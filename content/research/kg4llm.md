---
title: "KG for LLM"
ShowTableOfContents: true
---

## Introduction & Background

LLM和KG各有利弊：LLM富含通用知识、擅长语言处理、具有泛化性；KG具有结构化的精确知识，可解释性强，知识专业性强，可演化；

### LLM

![image-20240704172235679](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407041722595.png)

Encoder-only、Decoder-only、Encoder-Decoder的LLM发展路线图

![image-20240704172732196](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407041727026.png)

Encoder和Decoder之间的异同，以及Self-Attention的内部结构

### KG

![image-20240704173050791](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407041730828.png)

各种类型的KG，包括百科全书、常识、领域知识和多模态的知识图谱

### LLM与KG的结合

![image-20240704173249398](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407041732894.png)

LLM和KG交互的3种方式：KG增强LLM，LLM增强KG，两者协同

![image-20240704173432357](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407042314893.png)

目前LLM和KG的结合应用较少，知名的有ERNIE3.0、Bard和Doctor.ai

重点在于如何将LLM与KG结合，一些可能的技术手段有Prompt工程、GNN、神经符号推理、表征学习、上下文学习、少样本学习。

![image-20240704175915996](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407041759399.png)

## KG-ENHANCED LLMS 

### KG-enhanced LLM Pre-training

#### Integrating KGs into Training Objective

##### 增强KG实体

| Method |                                                             |
| ------ | ----------------------------------------------------------- |
| GLM    | 基于KG来计算mask概率，n-hop的实体被赋予更高的mask概率       |
| E-BERT | 基于token和entity的loss大小来动态调整各自的权重             |
| SKEP   | 基于PMI来挖掘表达情感倾向的词语，然后赋予这些词语更高的概率 |

##### 利用KG和文本之间的连接

| Method            |                                                             |
| ----------------- | ----------------------------------------------------------- |
| ERNIE             | 对齐文本token和KG实体                                       |
| KALM              | 整合实体嵌入，预测实体                                      |
| KEPLER            | 同时训练KG嵌入和Mask token预测                              |
| Deterministic LLM | 遮盖事实性关系的决定性token，并设置相应的预测任务和对比任务 |
| WKLM              | 替换同类型的实体，让LLM鉴别是否被替换                       |

![image-20240704184012226](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407041840990.png)

ERNIE：将对齐文本和KG作为训练目标

#### Integrating KGs into LLM Inputs

| Method    |                                                                                       |
| --------- | ------------------------------------------------------------------------------------- |
| ERNIE3.0  | 将KG三元组与句子拼接，同时预测masked token和三元组中的relation；会引入Knowledge Noise |
| K-BERT    | 引入visible matrix，仅句子中的实体token与三元组有交互                                 |
| COLAKE    | 将输入token建立成子图，从而拼接相应的KG子图                                           |
| DkLLM     | 检测长尾实体，用pseudo token替代                                                      |
| Dict-BERT | 将长尾实体用字典中的定义描述替换，并将长尾概念与描述对齐                              |

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407041842853.png" alt="image-20240704184209237"  />

Colake：输入文本子图拼接KG子图

#### KG指令微调

| Method     |                                                                     |
| ---------- | ------------------------------------------------------------------- |
| KPPLM      | 将结构化KG子图构建成文本prompt，并进一步自监督微调LLM利用这些Prompt |
| OntoPrompt | 将KG实体放在LLM的上下文中，并进一步微调                             |
| ChatKBQA   | 训练LLM在KG上进行逻辑查询                                           |
| RoG        | 训练LLM进行规划-检索-推理，在KG上产生可信的推理路径                 |

### KG-enhanced LLM Inference

主要解决LLM无法更新知识的问题，多用于QA。

#### Retrieval-Augmented Knowledge Fusion

| Method          |                                                                                   |
| --------------- | --------------------------------------------------------------------------------- |
| RAG             | 用非参数化的模块MIPS检索相关文档，然后编码成隐变量，作为LLM的上下文，做进一步生成 |
| Story-fragments | 添加额外模块来决定相关的知识实体，并添加到上下文                                  |
| EMAT            | 将外部知识编码成K-V Cache，加速推理                                               |
| REALM           | 在预训练阶段，加入知识检索器，训练模型检索和关注语料库中的相关知识                |
| KGLM            | 使用上下文从KG中选择事实                                                          |

![image-20240704184408363](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407041844595.png)

RAGPipeline

#### KG's Prompting

| Method   |                                 |
| -------- | ------------------------------- |
| Li       | 将三元组转化为模板短句          |
| MindMap  | 将图结构转化为思维导图          |
| ChatRule | 将Relation Path转化为文本Prompt |
| Cok      | 将三元组序列构成知识链条        |

## LLM-AUGMENTED KGS

### LLM-augmented KG Embedding

#### LLMs as Text Encoders

|              |                                             |
| ------------ | ------------------------------------------- |
| Pretrain-KGE | 将三元组中的文本用LLM编码，并计算KG损失函数 |
| KEPLER       | 统一KG embedding和预训练LM的embedding       |
| Nayyeri等    | 将篇章结构视为图结构，进一步整合文本的嵌入  |
| Huang等      | 多模态KG嵌入                                |
| CoDEx        | 提出新的损失函数，度量三元组的可能性        |

#### LLMs for Joint Text and KG Embedding

|          |                                |
| -------- | ------------------------------ |
| kNN-KGE  | 将实体和关系作为LLM的特殊token |
| LMKE     | 对比学习方法                   |
| LambdaKG | 添加KG中的1-hop邻居            |

![image-20240724204931369](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407242049233.png)

kNN-KGE

### LLM-augmented KG Completion

### LLM-augmented KG Construction

### LLM-augmented KG-to-text Generation

### LLM-augmented KG Question Answering

## SYNERGIZED LLMS + KGS

### Synergized Knowledge Representation

| Method    |                                                  |
| --------- | ------------------------------------------------ |
| ERNIE     | 文本-KG双编码器                                  |
| BERT-MK   | 额外引入相邻实体                                 |
| Coke-BERT | 基于GNN，利用输入文本滤除无关实体                |
| JAKET     | 在LLM中部融合实体信息                            |
| KEPLER    | 用LLM统一KG实体和文本的嵌入                      |
| JointGT   | 提出3个预训练任务来对齐KG实体和文本              |
| DRAGON    | 文本-KG交互的自监督预训练，掩码文本恢复+链路预测 |
| HKLM      | LLM结合KG来学习领域知识                          |

![image-20240705003913991](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407050039597.png)

ERNIE：文本-知识图谱双编码器结构

### Synergized Reasoning

#### LLM-KG Fusion Reasoning

| Method   |                                             |
| -------- | ------------------------------------------- |
| KagNet   | 编码输入KG，增强文本                        |
| MHGRN    | 用LLM输出来引导KG推理                       |
| QA-GNN   | 将文本嵌入成一个节点，与KG构成图，用GNN处理 |
| JointLK  | 文本和KG之间双向的细粒度注意力交互          |
| GreaseLM | 文本和KG在LLM的每一层发生交互               |

![image-20240705010327664](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407050103110.png)

JointLK：LLM的输出和KG Encoder的输出通过注意力交互，以修剪KG得到答案

#### LLMs as Agents Reasoning

![image-20240705010442972](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407050104591.png)

LLM在KG中检索到对应的事实路径，以给出正确的答案

| Method       |                                |
| ------------ | ------------------------------ |
| KD-CoT       | 迭代检索KG路径，获取事实       |
| KSL          | LLM检索事实、产生答案          |
| StructGPT    | LLM调用API获取KG知识           |
| ThinkonGraph | LLM进行Beam Search以获取KG知识 |
| AgentTuning  | 数据集，用于LLM的KG推理        |

