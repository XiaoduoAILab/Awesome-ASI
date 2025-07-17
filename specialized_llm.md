#!/usr/bin/env python3
# hpo_megatron.py - Hyperparameter Optimization for Megatron-LM using Bayesian methods

import os
import subprocess
import argparse
import logging
import signal
from datetime import datetime
from pathlib import Path
import optuna
from optuna.pruners import HyperbandPruner
from optuna.samplers import TPESampler

# 配置日志
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('hpo_megatron.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger('megatron-hpo')

class MegatronHPO:
    def __init__(self, megatron_dir, data_path, output_dir, gpus_per_trial=8):
        """
        初始化Megatron超参优化器
        
        :param megatron_dir: Megatron-LM源码目录
        :param data_path: 训练数据路径
        :param output_dir: 输出目录
        :param gpus_per_trial: 每个试验分配的GPU数量
        """
        self.megatron_dir = Path(megatron_dir).resolve()
        self.data_path = Path(data_path).resolve()
        self.output_dir = Path(output_dir).resolve()
        self.gpus_per_trial = gpus_per_trial
        
        # 创建输出目录
        self.output_dir.mkdir(parents=True, exist_ok=True)
        logger.info(f"Output directory: {self.output_dir}")
        
        # 验证Megatron目录
        if not (self.megatron_dir / "pretrain_gpt.py").exists():
            raise FileNotFoundError("Megatron-LM directory is invalid")

    def _build_train_command(self, trial, trial_dir):
        """
        构建Megatron训练命令
        
        :param trial: Optuna试验对象
        :param trial_dir: 试验输出目录
        :return: 训练命令列表
        """
        # 超参数采样
        params = {
            "learning_rate": trial.suggest_float("learning_rate", 1e-5, 0.1, log=True),
            "min_lr": trial.suggest_float("min_lr", 1e-7, 1e-4, log=True),
            "lr_warmup_fraction": trial.suggest_float("lr_warmup_fraction", 0.01, 0.2),
            "weight_decay": trial.suggest_float("weight_decay", 1e-5, 1.0, log=True),
            "adam_beta1": trial.suggest_float("adam_beta1", 0.8, 0.999),
            "adam_beta2": trial.suggest_float("adam_beta2", 0.9, 0.999),
            "adam_eps": trial.suggest_float("adam_eps", 1e-9, 1e-6, log=True),
            "micro_batch_size": 12,
            "global_batch_size": 1024,  # 根据实际需求调整
            "clip_grad": trial.suggest_float("clip_grad", 0.5, 2.0),
            "seq_length": 2048,
            "train_iters": 10000,  # 完整训练迭代次数
            "eval_iters": 100,
            "eval_interval": 1000,  # 早停检查点
            "save_interval": 1000,
        }
        
        # 构建命令
        cmd = [
            "torchrun",
            "--nproc_per_node", str(self.gpus_per_trial),
            str(self.megatron_dir / "pretrain_gpt.py"),
            "--tensor-model-parallel-size", "2",
            "--pipeline-model-parallel-size", "4",
            "--num-layers", "24",
            "--hidden-size", "2048",
            "--num-attention-heads", "16",
            "--seq-length", str(params["seq_length"]),
            "--max-position-embeddings", str(params["seq_length"]),
            "--micro-batch-size", str(params["micro_batch_size"]),
            "--global-batch-size", str(params["global_batch_size"]),
            "--lr", str(params["learning_rate"]),
            "--min-lr", str(params["min_lr"]),
            "--lr-decay-style", "constant",
            "--lr-warmup-fraction", str(params["lr_warmup_fraction"]),
            "--train-iters", str(params["train_iters"]),
            "--eval-iters", str(params["eval_iters"]),
            "--eval-interval", str(params["eval_interval"]),
            "--clip-grad", str(params["clip_grad"]),
            "--weight-decay", str(params["weight_decay"]),
            "--adam-beta1", str(params["adam_beta1"]),
            "--adam-beta2", str(params["adam_beta2"]),
            "--adam-eps", str(params["adam_eps"]),
            "--data-path", str(self.data_path),
            "--vocab-file", str(self.megatron_dir / "vocab.json"),  # 根据实际情况调整
            "--merge-file", str(self.megatron_dir / "merges.txt"),   # 根据实际情况调整
            "--save", str(trial_dir / "checkpoints"),
            "--load", str(trial_dir / "checkpoints"),
            "--log-interval", "10",
            "--save-interval", str(params["save_interval"]),
            "--exit-on-missing-checkpoint"
        ]
        
        # 添加分布式参数
        cmd += [
            "--distributed-backend", "nccl",
            "--DDP-impl", "torch"
        ]
        
        return cmd, params

    def _parse_validation_loss(self, log_file):
        """
        从日志文件中解析验证损失
        
        :param log_file: 日志文件路径
        :return: 验证损失值或None
        """
        try:
            with open(log_file, 'r') as f:
                for line in f:
                    if "validation loss" in line:
                        parts = line.split()
                        # 查找损失值（格式可能为："validation loss at step X: Y"）
                        for i, part in enumerate(parts):
                            if part == "loss:" and i + 1 < len(parts):
                                return float(parts[i+1])
        except Exception as e:
            logger.error(f"Error parsing log file: {e}")
        return None

    def _run_trial(self, trial):
        """
        运行单个超参数试验
        
        :param trial: Optuna试验对象
        :return: 验证损失
        """
        # 创建试验目录
        trial_id = f"trial_{trial.number}"
        trial_dir = self.output_dir / trial_id
        trial_dir.mkdir(exist_ok=True)
        
        # 构建训练命令
        cmd, params = self._build_train_command(trial, trial_dir)
        logger.info(f"Starting trial {trial.number} with params: {params}")
        
        # 设置日志文件
        log_file = trial_dir / "train.log"
        
        try:
            # 运行训练
            with open(log_file, 'w') as log:
                process = subprocess.Popen(
                    cmd,
                    stdout=log,
                    stderr=subprocess.STDOUT,
                    cwd=self.megatron_dir,
                    preexec_fn=os.setsid  # 创建新进程组
                )
            
            # 定期检查日志以支持早停
            while process.poll() is None:
                # 检查是否需要早停
                if trial.should_prune():
                    logger.info(f"Pruning trial {trial.number}")
                    os.killpg(os.getpgid(process.pid), signal.SIGTERM)
                    raise optuna.TrialPruned()
                
                # 检查日志中的验证损失
                val_loss = self._parse_validation_loss(log_file)
                if val_loss is not None:
                    trial.report(val_loss, step=self._get_current_step(log_file))
                
                # 每30秒检查一次
                optuna_timeout = 30
                process.wait(optuna_timeout)
            
            # 获取最终验证损失
            final_val_loss = self._parse_validation_loss(log_file)
            if final_val_loss is None:
                raise RuntimeError(f"Failed to parse validation loss for trial {trial.number}")
            
            return final_val_loss
        
        except Exception as e:
            logger.error(f"Trial {trial.number} failed: {e}")
            # 返回一个较大的损失值表示失败
            return float('inf')

    def _get_current_step(self, log_file):
        """
        从日志文件中获取当前训练步数
        
        :param log_file: 日志文件路径
        :return: 当前步数
        """
        try:
            with open(log_file, 'r') as f:
                lines = f.readlines()
                if lines:
                    last_line = lines[-1]
                    if "iteration" in last_line:
                        parts = last_line.split()
                        for i, part in enumerate(parts):
                            if part == "iteration" and i + 1 < len(parts):
                                return int(parts[i+1].strip('/'))
        except:
            pass
        return 0

    def optimize(self, n_trials=50, timeout=None, n_jobs=1):
        """
        运行超参数优化
        
        :param n_trials: 最大试验次数
        :param timeout: 最大运行时间（秒）
        :param n_jobs: 并行作业数
        """
        # 创建Optuna study
        sampler = TPESampler(n_startup_trials=10, multivariate=True)
        pruner = HyperbandPruner(min_resource=1, reduction_factor=3)
        
        study = optuna.create_study(
            direction="minimize",
            sampler=sampler,
            pruner=pruner,
            study_name="megatron_hpo"
        )
        
        # 运行优化
        study.optimize(
            self._run_trial,
            n_trials=n_trials,
            timeout=timeout,
            n_jobs=n_jobs,
            show_progress_bar=True
        )
        
        # 输出最佳结果
        logger.info("\n===== Optimization Completed =====")
        logger.info(f"Best trial: {study.best_trial.number}")
        logger.info(f"Best validation loss: {study.best_trial.value:.4f}")
        logger.info("Best parameters:")
        for key, value in study.best_trial.params.items():
            logger.info(f"  {key}: {value}")
        
        # 保存结果
        results_file = self.output_dir / "hpo_results.csv"
        study.trials_dataframe().to_csv(results_file, index=False)
        logger.info(f"Results saved to {results_file}")


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Hyperparameter Optimization for Megatron-LM")
    parser.add_argument("--megatron-dir", required=True, help="Path to Megatron-LM source directory")
    parser.add_argument("--data-path", required=True, help="Path to training data")
    parser.add_argument("--output-dir", default="hpo_results", help="Output directory for results")
    parser.add_argument("--gpus-per-trial", type=int, default=1, help="GPUs per trial")
    parser.add_argument("--trials", type=int, default=50, help="Number of trials")
    parser.add_argument("--timeout", type=int, default=None, help="Timeout in seconds")
    parser.add_argument("--jobs", type=int, default=1, help="Number of parallel jobs")
    
    args = parser.parse_args()
    
    # 初始化并运行优化器
    hpo = MegatronHPO(
        megatron_dir=args.megatron_dir,
        data_path=args.data_path,
        output_dir=args.output_dir,
        gpus_per_trial=args.gpus_per_trial
    )
    
    hpo.optimize(
        n_trials=args.trials,
        timeout=args.timeout,
        n_jobs=args.jobs
    )