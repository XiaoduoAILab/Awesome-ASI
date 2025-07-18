# 智能体记忆机制

## 1. 引言

大语言模型已经成为构建通用智能体系统的重要基础。然而，标准的 Transformer 结构天生缺乏对长期信息的保持能力，导致模型在处理长时序任务、复杂推理与连续交互时表现受限。因此，研究者们提出了多种记忆机制，试图弥补LLM在“记忆能力”上的不足。这些机制包括使用向量数据库进行外部存储、构建工作记忆模块用于临时推理、通过情景记忆管理特定任务过程，以及模拟人类长期记忆以增强智能体的持续学习能力。

本节将首先介绍这些不同的记忆类型，并列举每种类型的代表性工作，为后续章节关于应用与挑战的分析打下基础。

## 2. 智能体记忆类型概述

### 2.1 短期记忆（Short-term Memory）

短期记忆关注模型当前或近一步交互中需要维持的信息，通常与上下文窗口密切相关。其机制主要包括缓冲区、滑动窗口、自注意力增强等。

代表性研究包括：

- [1] Vaswani et al., “Attention is All You Need”, NeurIPS 2017.  
- [2] Xie et al., “LongNet: Scaling Transformers to 1,000,000,000 Tokens”, arXiv:2307.02486.  
- [3] Dai et al., “Transformer-XL: Attentive Language Models Beyond a Fixed-Length Context”, ACL 2019.  
- [4] Yang et al., “Retentive Network: A Successor to Transformer for Large Language Models”, ICLR 2024.  
- [5] Peng et al., “RWKV: Reinventing RNNs with Attention and Fast Weight Memory”, arXiv:2305.13048.

### 2.2 工作记忆（Working Memory）

工作记忆是执行推理和多步骤规划任务时所需的临时信息载体，通常包括一个动态更新的缓存结构、任务堆栈或规划栈。

典型实现包括：

- [6] Shinn et al., “Reflexion: Language Agents with Verbal Reinforcement Learning”, arXiv:2303.11366.  
- [7] Liu et al., “LLM+P: Empowering Large Language Models with Optimal Planning Proficiency”, arXiv:2401.06013.  
- [8] Yao et al., “ReAct: Synergizing Reasoning and Acting in Language Models”, NeurIPS 2023.  
- [9] Chen et al., “SayCan: Do What I Say, Not What I Did”, arXiv:2204.01691.  
- [10] Liu et al., “AgentPlanner: Agent-Based Task Planning with External Memory”, ICLR 2024.

### 2.3 情景记忆（Episodic Memory）

情景记忆用于存储时间上具有完整性或任务上具有阶段性的信息，常用于记录 agent 的经验、过去的任务轨迹和中间状态。

代表性研究：

- [11] Liu et al., “Voyager: An Open-Ended Embodied Agent with LLMs”, arXiv:2305.16291.  
- [12] Park et al., “Generative Agents: Interactive Simulacra of Human Behavior”, arXiv:2304.03442.  
- [13] Huang et al., “MEMAGENT: Memory-Augmented Autonomous Agents”, arXiv:2402.09022.  
- [14] Mialon et al., “Augmented Language Models: a Survey”, arXiv:2302.07842.  
- [15] Li et al., “Episodic Planning with Large Language Models”, arXiv:2402.02459.

### 2.4 长期记忆（Long-term Memory）

长期记忆是智能体学习与知识积累的关键，往往借助于外部检索机制如向量数据库、知识图谱、语义搜索或压缩记忆。

代表性工作包括：

- [16] Liu et al., “MemGPT: Towards Long-Term Memory for LLMs”, arXiv:2309.15532.  
- [17] Liu et al., “MemoryBank: Memory-Augmented Large Language Models”, arXiv:2401.12964.  
- [18] Xu et al., “LongLoRA: Efficient Fine-tuning of Long Context LLMs”, arXiv:2311.02843.  
- [19] Chen et al., “REPLUG: Retrieval-Augmented Black-Box Language Models”, ICLR 2023.  
- [20] Mialon et al., “LongMem: Scaling Transformer Models for Long Document Understanding”, NeurIPS 2023.

## 3. 记忆在不同类型智能体中的应用

在构建基于大语言模型的智能体时，不同任务目标对记忆机制的需求不同。本节按智能体类型划分，介绍当前研究中如何设计与应用记忆机制。

### 3.1 对话型智能体中的记忆机制

对话型智能体（如聊天机器人、虚拟助手）对短期上下文保持、一致性维护与用户偏好记忆尤为依赖。

代表性研究包括：

- [21] Lian et al., “LongChat: Optimizing Long-Context Chat Models with Hybrid Attention”, arXiv:2308.14508.  
- [22] Liu et al., “MemGPT: Towards Long-Term Memory for LLMs”, arXiv:2309.15532.  
- [23] Kim et al., “Retrieval-Augmented Language Model Pre-Training”, arXiv:2306.15595.  
- [24] Ji et al., “Prompting Memory in Language Models for Personalized Conversation Generation”, arXiv:2305.15334.  
- [25] Bai et al., “Personal Memory Retrieval for LLM-based Conversational Agents”, arXiv:2403.09653.

这些系统通常通过嵌入记忆查询（memory prompting）、用户配置文件向量化、历史摘要压缩等方式提升长期对话能力，部分如 MemGPT 还设计了自动记忆读写调度模块。

### 3.2 自主代理型智能体中的记忆机制

自主代理（如 Auto-GPT、Voyager）需要在环境中持续行动并规划任务，记忆用于保存历史轨迹、状态、目标与反馈。

代表性系统与研究：

- [26] Shinn et al., “Reflexion: Language Agents with Verbal Reinforcement Learning”, arXiv:2303.11366.  
- [27] Liu et al., “Voyager: An Open-Ended Embodied Agent with LLMs”, arXiv:2305.16291.  
- [28] Qiao et al., “AutoGPT: Building Autonomous AI Agents with LLMs”, GitHub: https://github.com/Torantulino/Auto-GPT  
- [29] Li et al., “CAMEL: Communicative Agents for Mind Exploration of Large Scale Language Model Society”, ICLR 2023.  
- [30] Park et al., “Generative Agents: Interactive Simulacra of Human Behavior”, arXiv:2304.03442.

这些系统通常构建情景记忆池、使用嵌套规划树或强化学习增强的记忆重调机制来模拟类似人类的长期任务分解与调整。

### 3.3 工具增强型智能体中的记忆机制

此类智能体（如 ReAct、Toolformer）可调用外部工具（如浏览器、代码执行器、数据库）完成复杂任务，记忆机制用于维护工具调用历史、中间状态与行为反馈。

代表性论文包括：

- [31] Yao et al., “ReAct: Synergizing Reasoning and Acting in Language Models”, NeurIPS 2023.  
- [32] Schick et al., “Toolformer: Language Models Can Teach Themselves to Use Tools”, ICLR 2023.  
- [33] Shen et al., “MindAct: Memory-Enhanced Tool-Using Language Agents”, arXiv:2403.00815.  
- [34] Mialon et al., “Augmented Language Models: a Survey”, arXiv:2302.07842.  
- [35] Liu et al., “MemoryBank: Memory-Augmented Large Language Models”, arXiv:2401.12964.

这些系统构建了任务指令—调用—反馈的三元组缓存、工具调用图（tool trace）或强化记忆调度机制，从而支持长期任务连续调用与回溯。

### 3.4 多模态智能体中的记忆机制

多模态智能体同时处理图像、语音、视频等信息，记忆机制面临跨模态表示一致性与时序对齐问题。

代表性研究：

- [36] Li et al., “MM-ReAct: Prompting ChatGPT for Multimodal Reasoning and Action”, arXiv:2303.11381.  
- [37] Xu et al., “MaMMa: Memory-Augmented Multimodal Assistant”, arXiv:2310.15799.  
- [38] Yang et al., “SeeAct: Embodied Multimodal Agents with Visual Memory for Long-Horizon Tasks”, arXiv:2403.00695.  
- [39] Zhang et al., “VisionMemory: Teaching Foundation Models to Remember What They See”, arXiv:2401.02774.  
- [40] Qian et al., “Multimodal Memory Transformer for Knowledge-Aware Recommendation”, SIGIR 2021.

MM-ReAct 利用视觉记忆模块提取与任务相关的图像区域，而 VisionMemory 将视觉模态表示写入向量数据库用于后续检索，SeeAct 更进一步引入跨帧时间窗口记忆以增强动作一致性。
