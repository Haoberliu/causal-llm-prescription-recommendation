# Causal LLM Prescription Recommendation

本仓库用于保存毕业设计项目 **“融合因果推断与大模型的处方推荐技术实现”** 的代码、实验配置、模型训练脚本、评估结果与说明文档。

> 注：如果论文题目中曾写作“厨房推荐”，建议统一修正为“处方推荐”。

## 项目简介

本项目面向临床处方推荐任务，结合 MIMIC-IV 数据、因果结构学习和大语言模型医学先验，构建具有一定可解释性的多标签药物推荐系统。整体思路包括：

1. 对住院记录进行清洗、特征构造和按患者维度划分训练集、验证集、测试集；
2. 使用 PC 算法从数据中发现特征与药物之间的候选因果关联；
3. 调用大语言模型生成医学先验，辅助筛选临床合理的特征—药物边；
4. 融合 PC 结果、LLM 医学先验和人工规则，形成最终因果掩码；
5. 构建 Causal Masked Linear / MLP 等模型进行多标签处方推荐；
6. 与传统模型、PC-only 模型、LLM prompt baseline 等方法进行对比实验。

## 建议目录结构

```text
causal-llm-prescription-recommendation/
├── data/                  # 数据目录，不建议上传原始 MIMIC-IV 数据
│   ├── raw/               # 原始数据，本仓库默认忽略
│   └── processed/         # 处理后的特征、标签、mask 等文件
├── docs/                  # 论文、答辩、系统说明相关文档
├── notebooks/             # 数据分析与实验探索 notebook
├── outputs/               # 模型、指标、图表等实验输出，本仓库默认忽略大文件
│   ├── causal/
│   ├── figures/
│   ├── metrics/
│   └── models/
├── prompts/               # LLM 医学先验与推荐理由生成提示词
├── scripts/               # 可直接运行的脚本
├── src/                   # 项目核心源码
│   ├── data/
│   ├── causal/
│   ├── llm/
│   ├── models/
│   ├── training/
│   └── evaluation/
├── tests/                 # 测试代码
├── requirements.txt       # Python 依赖
└── README.md
```

## 核心模块说明

### 1. 数据处理模块

负责从 MIMIC-IV 中提取患者基本信息、诊断特征、疾病状态、候选药物标签等内容，并按照 `subject_id` 进行数据划分，避免同一患者同时出现在训练集和测试集中。

### 2. 因果结构学习模块

使用 PC 算法进行候选因果边发现，通过 Bootstrap 多次采样统计边出现频率，得到较稳定的特征—药物关联结果。

### 3. LLM 医学先验模块

调用大语言模型 API，让模型基于医学知识判断某一疾病、体征、实验室指标与药物之间是否存在合理的临床关联，并输出保留理由。

### 4. 因果掩码融合模块

将 PC 算法发现的统计结构、LLM 医学先验和人工临床规则进行融合，生成最终的 `feature × drug` 因果掩码矩阵。

### 5. 推荐模型模块

重点实现 Causal Masked Linear 模型，通过因果掩码限制特征到药物输出层的连接，使模型具有更强的结构可解释性。

### 6. 实验评估模块

使用 Jaccard、micro Precision、micro Recall、micro F1 等指标评估多标签处方推荐效果，并与 Vanilla Linear、Vanilla MLP、PC-only Masked Linear、LLM prompt baseline 等方法对比。

## 注意事项

- 不要上传 MIMIC-IV 原始数据或任何包含患者隐私的数据。
- 不要上传 `.env`、API Key、模型大文件、训练日志大文件。
- 论文中使用 MIMIC-IV 数据集时，需要在参考文献中规范引用。
- 若使用 DeepSeek、Qwen 等大模型 API 或微调模型，需要在文档中说明模型版本、调用方式和实验设置。

## 当前状态

本仓库目前处于项目初始化阶段，后续可继续补充：

- 数据预处理脚本；
- PC 算法因果发现脚本；
- LLM 医学先验生成脚本；
- 因果掩码融合脚本；
- 模型训练与评估脚本；
- 毕业论文图表与答辩材料。
