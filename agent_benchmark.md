# Agent Benchmark

## 概述
大型语言模型（LLM）作为"智能体"（Agent）参与现实任务的研究兴起，催生了新的评测需求。**Agent Benchmark** 指用于衡量智能体在复杂多轮任务中表现的标准测试集合，其产生源于传统静态输入输出评测的不足——难以评估智能体在连续交互、工具调用等动态场景的能力 \[1\]。  
例如：  
- **AgentBench**（Liu, 2023）：检验LLM在多环境、多轮次开放式任务中的推理和决策能力，发现商用LLM虽表现优秀，但在**长期推理**和**连续指令跟随**上与开源模型存在显著差距 \[1\]。  
> **核心意义**：为智能客服等应用提供客观的能力评价指标。

---

## 应用领域：电商客服场景
电商客服场景要求智能体处理多轮对话、订单检索、知识库调用等任务。代表性评测框架包括：  
| 基准名称       | 核心贡献                                      | 关键发现                                  |
|----------------|---------------------------------------------|-----------------------------------------|
| **ECom-Bench**（Wang等, 2025） | 首个电商客服多模态智能体基准，引入动态用户画像对话模拟与真实多模态任务 | GPT-4.0通过率仅10~20%，暴露复杂电商对话的挑战 \[3\] |
| **ALMITA**（Arcadinho等, 2024） | 评测工具调用型对话智能体在客户支持任务中的表现          | 工具增强LLM单步调用表现良好，但多轮对话易出错 \[4] |

**评测重点**：对话连贯性、上下文一致性、工具调用正确率及业务效果模拟。

---

## 多轮对话与长期记忆
### 多轮对话一致性
| 基准名称          | 核心设计                                      | 关键结论                                  |
|-------------------|---------------------------------------------|-----------------------------------------|
| **MT-Bench-101**（Bal等, 2024） | 构建"感知-互动-适应性"三层级任务体系              | 常规对齐方法对多轮能力提升有限 \[5]         |
| **MT-Eval**（Kwan等, 2024） | 基于1170条多轮查询（回忆/扩展/细化/跟进等类型）      | 多数模型在多轮情境下性能显著下降 \[6]          |
| **BotChat**（Wang等, 2023） | LLM生成对话并自评，对比单轮/多轮协议              | GPT-4随对话轮次增加衰减较慢，小型模型衰减严重 \[7] |

### 长期记忆评测
- **LoCoMo**（Maharana等, 2024）：模拟300回合长期对话，设计问答/事件总结等任务 \[8]。  
- **发现**：即使采用长上下文模型或RAG策略，LLM在**跨会话一致性**和**长期因果理解**上仍远落后于人类 \[9]。  
> **挑战**：需通过上下文管理、记忆架构优化和多轮训练改进。

---

## 工具调用评估
### 评测基准
| 名称               | 特点                                      | 关键发现                                  |
|--------------------|-----------------------------------------|-----------------------------------------|
| **ToolBench**（Qin等, 2023/24） | 多任务工具调用测试集                        | 覆盖动态多轮交互与长期推理 \[10]             |
| **BFCL**（Patil等, 2025） | 千级调用任务排行榜                          | 顶级模型单次调用优异，但记忆与决策链能力不足 \[11] |
| **ToolScan**（Kokane等, 2024） | 错误诊断导向，总结常见错误类型                | 现有基准忽略错误模式分析 \[17]              |

### 优化技术
- **EASYTOOL**（Yuan等, 2024）：将复杂工具文档提炼为统一简明说明，降低调用token数并提升性能 \[12]。  
> **评估维度**：正确性、效率、错误安全性。

---

## 人类反馈对齐
### 方法演进
- **MTPO**（Shan等, 2024）：在多轮对话中利用用户反馈优化模型 \[13]。  
- **分层强化学习**（如REFUEL, MT-Arch）：引导模型长期规划，提升对话质量与目标完成率 \[13]。  

### 人工评测机制
- **LLM-as-Judge**：GPT-4等作为评价者与人类评测一致性达80%+，可替代部分人工评估 \[14]。  
- **Chatbot Arena**：用户对比模型回答 + LLM自动评价，兼顾质量与成本 \[14]。  

---

## 代表性基准与评估框架
| 类别               | 基准名称                     | 核心能力评测方向                          |
|--------------------|----------------------------|-----------------------------------------|
| **基础能力**       | AGIEval（Zhong等, 2023）    | 人类标准化考试反映理解与推理 \[2] |
| **跨环境任务**     | CAMEL/CRAB（Xu等, 2024-25） | 多界面任务完成率（GPT-4单代理最高38%）\[15] |
| **工具调用**       | ToolBench, BFCL            | API调用准确率与鲁棒性 \[10][11]             |
| **多轮对话**       | MT-Bench-101, MT-Eval      | 对话细节与交互模式划分 \[5][6]              |
| **工业框架**       | LangSmith, Agent-Evals     | 记录生成过程并提供辅助评判                  |

---
## 基准对比表
| 基准             | 任务场景                   | 核心能力维度                | 评估指标/特点                                     |
| :------------- | :--------------------- | :-------------------- | :------------------------------------------ |
| AgentBench     | 多环境、多任务（网页、游戏、数据库等）    | 多步推理、规划、交互决策          | 环境成功率、整体得分；覆盖网络操作、算术推理等复杂任务                 |
| ECom-Bench     | 电子商务对话（虚拟购物网站）         | 多轮对话理解、工具调用（搜索、筛选、购买） | Pass\@K 通过率（K=1,2,3）；评价模型完成指定购物流程的准确性       |
| MT-Bench-101   | 多轮对话（教育/情境对话任务，共13类任务） | 语境记忆、推理、歧义处理、提问能力     | 每轮对话评分（GPT-4 作为评审，满分 10）；报告模型在细粒度多轮能力上的综合得分 |
| ALMITA         | 客服对话（带流程指引，需调用功能API）   | 对话完整性、回复准确度、API调用正确性  | 回复正确率、API调用正确率、完整对话正确率（test correctness）    |
| ToolBench      | API工具使用（多步骤调用外部API）    | 工具使用规划、多步思考           | Pass率、Win率：评估模型在限定资源内完成任务的概率，偏向于“可解”任务      |
| BFCL           | 编程函数调用（含多函数、多语言场景）     | 函数/工具调用准确率            | 函数调用成功率：包含抽象语法树正确性和实际执行正确性等，采用“整体正确率”指标     |
| ToolScan【27†】  | 诊断性错误分析（10类环境，30+任务）   | 错误类型识别、调用失败分析         | 详细标注各种失败模式，用以诊断模型工具使用中易错环节                  |
| CRAB           | 跨平台（桌面、移动）多环境任务（120任务） | 通用推理、跨环境适应性           | 任务完成率：评估模型在不同设备界面环境中完成任务的能力                 |
| AGIEval        | 标准化考试（SAT、法律等人类考试题）    | 综合知识、逻辑推理、计算能力        | 准确率：GPT-4 在SAT数学（95%）等考试中超过人类平均水平           |
| LangSmith【30†】 | 评测平台（定制数据集与工作流评估）      | 基于LangChain的评估框架      | 灵活支持用户定义评估任务和指标，用于比较模型在复杂Agent应用中的整体表现      |

## 总结与展望
### 当前瓶颈
1. 对话规划与长期一致性不足  
2. 动态环境下工具调用易出错  
3. 人类偏好与任务目标对齐待加强  

### 未来方向
| 方向         | 具体路径                                      |
|--------------|---------------------------------------------|
| **可扩展性** | 动态更新大规模测试集，跟进模型升级               |
| **通用性**   | 构建跨任务/领域评估体系，提升泛化能力            |
| **推理能力** | 引入复杂场景与层级目标，考核长期决策能力          |
| **评估机制** | 模型自检验增强可靠性                            |

> **核心目标**：结合迭代反馈、强化学习与多指标框架，推动智能体在真实场景中的稳健运行。

---

### 参考文献
1. Liu et al. AgentBench: Evaluating LLMs as Agents. arXiv:2308.03688 (2023)  
2. Zhong et al. AGiEval: A Human-Centric Benchmark for Evaluating Foundation Models (2023)  
3. Wang et al. ECom-Bench: Can LLM Agent Resolve Real-World E-commerce Customer Support Issues? (2025)  
4. Arcadinho et al. Automated test generation to evaluate tool-augmented LLMs as conversational AI agents (2024)  
5. Bai et al. MT-Bench-101: A Fine-Grained Benchmark for Evaluating LLMs in Multi-Turn Dialogues. ACL 2024  
6. Kwan et al. MT-Eval: A Multi-Turn Capabilities Evaluation Benchmark for LLMs. EMNLP 2024  
7. Wang et al. BotChat: Evaluating LLMs' Capabilities of Having Multi-Turn Dialogues (2023)  
8. Maharana et al. Evaluating Very Long-Term Conversational Memory of LLM Agents. arXiv:2402.17753 (2024)  
9. Maharana et al. (同上)  
10. Guo et al. ToolBench: A Benchmark Suite for Assessing LLM Tool Use. ICLR 2024  
11. Patil et al. Berkeley Function Calling Leaderboard (BFCL): From Tool Use to Agentic Evaluation of LLMs. ICML 2025  
12. Yuan et al. EASYTOOL: Enhancing LLM-based Agents with Concise Tool Instruction. arXiv:2401.06201 (2024)  
13. Shan et al. MTPO (Multi-turn Preference Optimization) (2024)  
14. Zheng et al. Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena. NeurIPS 2023  
15. Xu et al. CRAB: Cross-environment Agent Benchmark for Multimodal Language Model Agents. ACL Findings 2025  
16. Yao et al. WebShop: Towards Scalable Real-World Web Interaction with Grounded Language Agents. NeurIPS 2022    
17. Kokane et al. ToolScan: A Benchmark for Characterizing Errors in Tool-Use LLMs. arXiv:2411.13547 (2024)  
18. Yu et al. Multi-Mission Tool Bench: Assessing the Robustness of LLM Agents. arXiv:2504.02623 (2025)  
19. Ma et al. AgentBoard: Evaluating LLMs in Human-Machine Collaboration. arXiv:2401.13178 (2024)  
20. Wang et al. MintBench: A Benchmark for Multi-Agent Interactive Tasks. arXiv:2309.10691 (2023)  
21. Liu et al. AgentLite: Lightweight Agent Benchmark for LLMs. arXiv:2402.15538 (2024)  

---

### 附录：参考文献链接表
| 编号 | 标题/作者 | 链接 |
|------|-----------|------|
| [1]  | AgentBench: Evaluating LLMs as Agents | [arXiv:2308.03688](https://arxiv.org/abs/2308.03688) |
| [2]  | AGiEval: A Human-Centric Benchmark | [ResearchGate](https://www.researchgate.net/publication/370000870_AGiEval_A_Human-Centric_Benchmark_for_Evaluating_Foundation_Models) |
| [3]  | ECom-Bench | [arXiv:2507.05639v1](https://arxiv.org/html/2507.05639v1) |
| [4]  | Automated test generation... | [ACL Anthology](https://aclanthology.org/2024-genbench-1.4/) |
| [5]  | MT-Bench-101 | [ACL Anthology](https://aclanthology.org/2024.acl-long.401/) |
| [6] | MT-Eval | [ACL Anthology](https://aclanthology.org/2024.emnlp-main.1124/) |
| [7] | BotChat | [arXiv:2310.13650](https://arxiv.org/html/2310.13650) |
| [8] | Evaluating Very Long-Term... | [arXiv:2402.17753](https://arxiv.org/abs/2402.17753) |
| [10] | ToolBench | ICLR 2024 Spotlight |
| [11] | Berkeley Function Calling... | [OpenReview](https://openreview.net/forum?id=2GmDdhBdDk) |
| [12] | EASYTOOL | [arXiv:2401.06201](https://arxiv.org/abs/2401.06201) |
| [14] | Judging LLM-as-a-Judge... | [arXiv:2306.05685](https://arxiv.org/abs/2306.05685) |
| [15] | CRAB | [arXiv:2407.01511](https://arxiv.org/abs/2407.01511) |
| [16] | WebShop | NeurIPS 2022 |
| [17] | ToolScan | [arXiv:2411.13547v2](https://arxiv.org/html/2411.13547v2) |
| [18] | Multi-Mission Tool Bench | [arXiv:2504.02623](https://arxiv.org/abs/2504.02623) |
| [19] | AgentBoard | [arXiv:2401.13178](https://arxiv.org/abs/2401.13178) |
| [20] | MintBench | [arXiv:2311.XXXXX](https://arxiv.org/abs/2309.10691) |
| [21] | AgentLite | [arXiv:2405.XXXXX](https://arxiv.org/abs/2402.15538) |


