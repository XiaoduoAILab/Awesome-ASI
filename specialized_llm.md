# Awesome Specialized LLM

## 🧠 医疗领域

谷歌发布了针对医学领域微调的大型语言模型系列，如**Med-PaLM**（基于PaLM架构，Fine-tune），以及后续的**Med-PaLM 2**和多模态版本**Med-PaLM M**。这些模型在医学考试问答和实际诊断推理中表现出色，是首批通过美国医师执照考试（USMLE）及医学知识问答达到专家水平的LLM。Microsoft的**BioGPT**（GPT-2架构，在1500万篇PubMed文摘上预训练）专用于生物医学文本的生成与挖掘，在关系抽取和医疗问答任务上优于传统模型。斯坦福等提出的**BioMedLM**（2.7B GPT风格模型，仅用PubMed文本训练）在医学选择题问答上可与更大通用模型匹敌。新近出现的开源医学领域模型有**BioMistral-7B**（基于Mistral，预训练自PubMed全文）、**PMC-LLaMA-13B**（在480万篇医学论文基础上注入知识）和**MEDITRON**系列（7B/70B规模，基于LLaMA-2，扩充医学数据预训练）。

### 📌 代表性论文

- **Singhal et al., 2023** – *Large language models encode clinical knowledge*  作者介绍了Med-PaLM（在PaLM上进行医学领域提示调优）并评测其在多医学问答数据集上的效果，提出了人类评估框架，证明了LLM在医学领域的潜力。 
- **Luo et al., 2023** – *BioGPT: A Generative Pre-trained Transformer for Biomedical Text*  微软研究员提出BioGPT（GPT-2架构），在1500万篇PubMed文摘上预训练，用于生物医学文本生成和挖掘，在生物医学关系提取与问答上取得了优异性能。 
- **Bolton et al., 2024** – *BioMedLM: A 2.7B Parameter Language Model Trained on Biomedical Text*  来自斯坦福和Databricks，设计了一个2.7B的自回归医学LLM，仅使用PubMed语料训练，实验证明其在医学多选题问答（如MedMCQA等）上表现超过许多更大模型。
- **Wu et al., 2023** – *BloombergGPT* 尽管主要针对金融领域，此文中提到“BloombergGPT”模型以50B参数在3630亿金融特定词料上训练，本域模型的成功说明了行业定制LLM的潜力（此处金融领域参考，证明了领域预训练方式）。 
- **Labrak et al., 2024** – *BioMistral*  该工作介绍了BioMistral-7B（基于Mistral-7B，在PubMed Central上继续预训练的开放源医学模型），并在医学QA任务中优于现有开源模型。 
- **Wu et al., 2023** – *PMC-LLaMA*  该团队基于LLaMA-13B，通过大量医学文献和指南注入知识并进行指令调优，构建了PMC-LLaMA系列，结果在医学问答评测中超过了ChatGPT。 
- **Chen et al., 2023** – *MEDITRON-70B*  提出了MEDITRON-7B/70B，在LLaMA-2基础上进行医学专用预训练，使用PubMed和临床指南等数据，显著超过基线并接近GPT-4水平，开源了权重。 

---

## ⚖️ 法律领域

近期出现多款法律领域专用LLM。例如SaulLM-7B（Equall.ai团队，7B参数基于Mistral模型，对3.0亿篇法律文档微调）用于法律文本理解与生成，采用指令微调实现高效问答与文书写作。另一个是LawLLM（CIKM’24），在Gemma-7B基础上通过多任务训练适应美国法律场景，涵盖案件审查、判决预测、法律查询等任务，集成检索和翻译机制，性能优于多项基线。中国方面，最高人民法院会同清华大学构建了“法信法律大模型”，在通用100B模型上微调亿级法律文献（新闻报道）。厦大等提出LegalGPT（ICC会议），利用法律领域的链式思维（CoT）和多智能体框架改进法律推理，在法律考试与咨询任务上效果显著。


### 📌 代表性论文

- **Colombo et al., 2023** – *SaulLM-7B*  介绍SaulLM-7B（法律大模型），基于Mistral-7B，在30B法律语料上训练并进行指令微调，开源用于法律合规等场景。
- **Demetrey et al., 2024** – *LawLLM*  提出LawLLM，多任务多阶段训练适应美国法律问答，包括案例检索、判决预测等，结合领域检索与翻译方法，在各项法律任务上超过了通用LLM基线。 
- **石镌铭等, 2024** – *LegalGPT*  采用多步推理和多智能体机制将法律领域知识注入LLM，通过构造法律推理链显著提高了模型对法律考试题和咨询任务的回答准确率。 

---

## 💰 金融领域

BloombergGPT（50B参数，训练于Bloomberg内部金融数据集）是首个大规模金融专用LLM，使用3630亿财经词料训练，并与通用数据混合训练，可大幅提升金融问答与分析任务的表现 。开源方面，FinGPT（杨洪洋等，2023）构建了财务数据管道并基于低秩适配技术训练LLM，支持机器人投顾、算法交易等应用 。上述模型专门用于财务问答、市场分析等任务，已应用于证券分析与智能投研等系统中。


### 📌 代表性论文

- **Wu et al., 2023** – *BloombergGPT*  介绍BloombergGPT，50B参数金融专用模型，在金融和通用巨量数据上训练，显著提升财务领域任务性能。 
- **Yang et al., 2023** – *FinGPT*  提出FinGPT框架，强调数据中心的流水线和LLM适配方法，并演示了在量化交易、投顾等场景的应用。 

---

## 🎓 教育领域

在教育领域，主要是利用现有大型模型进行定制化服务。例如OpenAI推出的ChatGPT Education（校园版），面向高校用户开放GPT-4o，增加分析、浏览、文件总结等工具，支持生成课堂教程、个性辅导等。虽然目前尚无公开的“教育专用LLM”模型论文，但有研究探讨将LLM作为教学助手或多智能体系统应用于教育。例如Chen等（2025）综述了基于LLM的教育代理，提出将LLM用于课程答疑与个性化学习。这些工具已在高校试点用于学习辅导、答疑解惑和编程教育。


### 📌 代表性论文

- **OpenAI, 2024** – *ChatGPT Education*  描述了面向大学的ChatGPT校园版服务，扩展了GPT-4o的使用限额和功能，为师生提供辅导、文本分析和多语言支持。 
- **Zhendong et al., 2025** – *LLM Agents for Education*  综述性论文，讨论了LLM在教育中作为智能教学代理的最新进展，包括在课程教学和学习分析中的应用案例。 

---

## 💻 编程领域

众多专门针对编程任务的LLM相继出现：Codex（OpenAI，基于GPT-3在公开代码库上微调）可根据注释生成代码，用于GitHub Copilot等编程助手；StarCoder（BigCode开源组织，15.5B参数）在GitHub许可代码上预参数）在Llama-2模型上进一步训练了500B代码数据，引入中间填充能力，并提供专业的Python和指令版本；CodeGeeX（清华与华为，13B参数）在850B多语言代码数据上预训练，支持24种编程语言，并在HumanEval-X等多语种基准上优于同规模模型 ；AlphaCode（DeepMind，采用Transformer超大模型和大量采样+筛选策略）在竞赛编程题中可解决相当比例的问题；CodeGen（Salesforce，ICLR’23）提供多个规模（最高16.1B）的开源代码生成模型，并提出分步合成范式以提高复杂程序的生成质量 。这些模型可用于自动补全、代码解释、算法生成等，并已集成到开发工具与在线IDE中（如GitHub Copilot、Meta的IDE插件等）。


### 📌 代表性论文

- **Chen et al., 2021** – *Codex*  首次介绍了OpenAI的Codex，通过GitHub代码微调GPT模型，在合成程序生成任务HumanEval中性能远超GPT-3，并讨论了采样策略的重要性。 
- **Li et al., 2023** – *StarCoder*  BigCode团队介绍了StarCoder（15.5B），使用1T开源代码训练，并通过Python微调，展示了其在多语言代码生成任务中的领先性能。 
- **Meta News, 2023** – *Code Llama*  官方公告，描述了Code Llama（7B/13B/34B）的训练细节，500B代码数据、填充能力及专门的Python和指令版本，用于增强开发者的编码效率。 
- **Zheng et al., 2023** – *CodeGeeX*  清华等提出CodeGeeX（13B），在850B多语种代码上训练，公测了HumanEval-X基准，证明其在代码生成和翻译任务上的竞争力，同时开源了模型和工具链。 
- **Nijkamp et al., 2023** – *CodeGen*  介绍Salesforce的CodeGen模型（最高16.1B），提出多轮提示方式提升复杂程序生成，并发布相应基准和开源代码。

---

## 🔬 科研领域

科学专用LLM如**ChemCrow**（结合GPT-4与化学工具）、**MolecularGPT**（用于分子属性预测）、**Galactica**（Meta出品，训练于科学知识）、**GeoGalactica**（地球科学方向）等，展现了LLM在科研自动化中的巨大潜力。

### 📌 代表性论文

- **Bran et al., 2024** – *ChemCrow*  介绍了ChemCrow框架，将GPT-4与专业化学工具整合，用于有机合成和材料发现任务，并验证了其自动化化学问题解决能力。 
- **Liu et al., 2024** – *MolecularGPT*  提出通过分子指令微调LLM的方法来预测分子性质，展示了MolecularGPT在新分子任务上的通用性和优秀性能。 
- **Taylor et al., 2022** – *Galactica*  Meta公布Galactica模型，训练于海量科学论文和知识库，在数学、化学问答及PubMedQA等任务上超过GPT-3和BLOOM等基线，展示了专用科学LLM的潜力。 
- **Lin et al., 2023** – *GeoGalactica*  在Galactica基础上，进一步预训练和微调地球科学领域数据，构建30B参数地质学模型，验证其在地质考试和知识查询任务中的优势。 

---

## 📚 BibTeX参考文献（部分）

```bibtex
@article{singhal2025,
author = {Karan Singhal and Tao Tu and Joey Gottweis and Catherine T. Kelleher and ...},
title = {Toward expert-level medical question answering with large language models},
journal = {Nature Medicine},
volume = {31},
pages = {943--950},
year = {2025},
doi = {10.1038/s41591-024-03423-7}
}
@article{singhal2023,
author = {Karan Singhal and Shreya R. Mahdavi and ...},
title = {Large language models encode clinical knowledge},
journal = {Nature},
volume = {620},
pages = {172--180},
year = {2023},
doi = {10.1038/s41586-023-06291-2}
}
@article{luo2023,
author = {Renqian Luo and Yanyu Zhang and Jian An and ...},
title = {BioGPT: A Generative Pre-trained Transformer for Biomedical Text Generation and Mining},
journal = {arXiv preprint arXiv:2304.15142},
year = {2023}
}
@article{bolton2024,
author = {Elliot Bolton and Abhinav Venigalla and Sanjay Rama and ...},
title = {BioMedLM: A 2.7B Parameter Language Model Trained on Biomedical Text},
journal = {arXiv preprint arXiv:2403.01410},
year = {2024}
}
@article{wu2023,
author = {Shijie Wu and Ozan Irsoy and Steven Lu and Vadim Dabravolski and Mark Dredze and
Sebastian Gehrmann and Prabhanjan Kambadur and David Rosenberg and Gideon Mann},
title = {BloombergGPT: A Large Language Model for Finance},
journal = {arXiv preprint arXiv:2303.17564},
year = {2023}
}
@article{yang2023,
author = {Hongyang Yang and Xiao-Yang Liu and Christina Dan Wang},
title = {FinGPT: Open-Source Financial Large Language Models},
journal = {arXiv preprint arXiv:2306.06031},
year = {2023}
}
@article{chen2021,
author = {Mark Chen and Jerry Tworek and Heewoo Jun and ...},
title = {Evaluating Large Language Models Trained on Code},
journal = {arXiv preprint arXiv:2107.03374},
year = {2021}
}
@article{li2022,
author = {Yujia Li and Hanyu Liu and Daria Tsvyashchenko and ...},
title = {Competition-Level Code Generation with AlphaCode},
journal = {Science},
volume = {379},
pages = {270--274},
year = {2022}
}
@article{nijkamp2023,
author = {Erik Nijkamp and Bo Pang and Hiroaki Hayashi and ...},
title = {CODEGEN: An Open Large Language Model for Code with Multi-Turn Program Synthesis},
journal = {Proceedings of the International Conference on Learning Representations (ICLR)},
year = {2023}
}
@article{li2023starcoder,
author = {Raymond Li and Loubna Ben Allal and Yangtian Zi and ...},
title = {StarCoder: may the source be with you!},
journal = {arXiv preprint arXiv:2305.06161},
year = {2023}
}
@article{zhou2024,
author = {Zhouhan Lin and Cheng Deng and Le Zhou and ...},
title = {GeoGalactica: A Scientific Large Language Model in Geoscience},
journal = {arXiv preprint arXiv:2401.00434},
year = {2024}
}
@article{bran2024,
author = {Andres M. Bran and Sam Cox and Oliver Schilter and ...},
title = {Augmenting large language models with chemistry tools},
journal = {Nature Machine Intelligence},
volume = {6},
pages = {525--535},
year = {2024}
}
@article{liu2024,
author = {Yuyan Liu and Sirui Ding and Sheng Zhou and ...},
title = {MolecularGPT: Open Large Language Model (LLM) for Few-Shot Molecular Property
Prediction},
journal = {arXiv preprint arXiv:2406.12950},
year = {2024}
}
@article{taylor2022,
author = {Ross Taylor and Marcin Kardas and Guillem Cucurull and ...},
title = {Galactica: A Large Language Model for Science},
journal = {arXiv preprint arXiv:2211.09085},
year = {2022}
}
@article{colombo2023,
author = {Pierre Colombo and Sergey Bobenko and Etienne Bergeron and ...},
title = {SaulLM-7B: A pioneering large language model for law},
journal = {arXiv preprint arXiv:2312.15175},
year = {2023}
}
@inproceedings{demetrey2024,
author = {Arthur Demetrey and Chuancheng Du and Minghuang Zhang and ...},
title = {LawLLM: Law Large Language Model for the US Legal System},
booktitle = {Proceedings of the 33rd ACM International Conference on Information and Knowledge
Management (CIKM)},
year = {2024}
}
@article{zhang2023,
author = {Quanyu Zhang and Han Xu and Yuan Liu and ...},
title = {LegalGPT: Legal Chain of Thought for the Legal Large Language Model Multi-Agent
Framework},
journal = {Proceedings of the International Conference on Intelligent Computing (ICC)},
year = {2024}
}
@misc{openai2024,
author = {OpenAI},
title = {ChatGPT Education},
howpublished = {\url{https://openai.com/chatgpt/education/}},
year = {2024}
}

```
