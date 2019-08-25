# Joint Extraction of Entities and Relations Based on a Novel Tagging Scheme【基于新标记方案的实体和关系的联合提取】

## 作者：Suncong Zheng, Feng Wang, Hongyun Bao, Yuexing Hao,Peng Zhou, Bo Xu

## 发表信息：Zheng, Suncong, et al. "Joint extraction of entities and relations based on a novel tagging scheme." arXiv preprint arXiv:1706.05075 (2017).
## 会议 ACL2017



## 摘要
&emsp;&emsp;作者首先提出了一种新颖的标签方案，该方案将联合提取任务转换为标签问题。然后基于此标签方案联合抽取实体和关系。

## 1 介绍
&emsp;&emsp;目前，信息抽取任务分为两种方法：1）基于流水线和2）联合抽取，信息抽取任务具体任务如下
![image](https://user-images.githubusercontent.com/54270073/63266544-b6edea00-c2c2-11e9-8316-589eff731a3b.png)

》》基于流水线的方法，将先进行实体识别，然后进行关系抽取，此方法存在以下几个缺点:

- 实体识别和关系抽取两个子任务并非没有关系，将分为两个模型来分别训练各自的任务
- 实体识别的结果会影响关系抽取任务的准确性和错误的传播

》》基于联合抽取的方法使用一个模型避免了以上缺点，现有的大部分联合抽取方法基于特征，这需要复杂的特征工程，以及依赖其他的NLP工具，而且还是先学习实体，再抽取关系。

&emsp;&emsp;为了联合抽取实体和关系，作者提出了一种标签方案并且与融合到端到端的模型以解决传统中方法的分离实体识别和关系抽取的问题。

&emsp;&emsp;一种监督学习方法，训练数据地址 https://github.com/shanzhenren/CoType 

&emsp;&emsp;本文主要贡献：
- **A novel tagging scheme is proposed to jointly extract entities and relations, which can easily transform the extraction problem into a tagging task.**
- **Based on our tagging scheme, we study different kinds of end-to-end models to settle the problem. The tagging-based methods are better than most of the existing pipelined and joint learning methods.**
- **Furthermore, we also develop an end-to-end model with biased loss function to suit for the novel tags. It can enhance the association between related entities.**

## 2 方法
### 2.1 The Tagging Scheme
&emsp;&emsp;一个单词被分配一个标签，标签“O”，代表和抽取结过无关的单词；除了标签“O”，其余的标签都由3个部分组成：词在实体中的位置，关系类型和关系的角色。
- “BIES”（Begin，Inside，End，Single）代表了一个词在实体中的位置；
- 关系类型信息包含在一个预先定义的集合中；
- 关系角色信息由数字“1”和“2”表示。   
具体的一个实例如下图所示：

![image](https://user-images.githubusercontent.com/54270073/63262516-c5cf9f00-c2b8-11e9-94c4-f32a78dce543.png)
### 2.2 From Tag Sequence To Extracted Results
&emsp;&emsp;从图2的标签序列中，我们可以知道“Trump”和“United States”共享关系类型“Country-President”,"Apple Inc”和“Steven Paul Jobs”共享关系“Company-Founder”。所以我们将有共享关系类型的实体装进一个三元组。
&emsp;&emsp;当然，如果一个句子中出现多个实体对共享一个关系类型，那么将按照“就近原则”，将实体对匹配为三元组对。
### 2.3 The End-to-end Model
![The End-to-End Model](https://user-images.githubusercontent.com/54270073/63311839-06b7ca00-c332-11e9-8a1c-66744da3b3f5.png)

本端到端模型包含了一个双向LSTM去编码输入的句子和一个基于偏见损失LSTM的解码层，偏见损失可以增强实体标签的相关性。
2.3.1 Bi-LSTM Encoding Layer

&emsp;&emsp;在标签序列问题中，Bi-LSTM编码层可以有效的捕获每个单词的语义信息。该层包含前馈LSTM层和后传播LSTM层和一个连接层。
&emsp;&emsp;词嵌入层将单词转换为一个向量，因此词序列可以被表示为W={w1，w2，...,wt，wt-1，...wn}，wt表示为一个句子中第t个单词的一个d维词向量，n为所给句子的长度。
&emsp;&emsp;LSTM层架构由一组循环连接的子网组成，称为内存快。
![Bi-LSTM Block](https://user-images.githubusercontent.com/54270073/63313408-e1798a80-c336-11e9-83cb-763a678588ff.png)

2.3.2 LSTM

![LSTM](https://user-images.githubusercontent.com/54270073/63313562-7ed4be80-c337-11e9-8356-c31f6a5274f7.png)

2.3.3 SoftMax

2.3.4 bias

### 3 实验
#### 数据集
使用了远程监督的NYT Dataset https://github.com/shanzhenren/CoType
训练集包含353k的三元组，测试机包含3880 三元组，关系集共有24

#### 评价方法
P-R曲线：
F1 score

#### 超参数
&emsp;&emsp;词嵌入层使用word2vec工具：https://code.google.com/archive/p/word2vec/，词嵌入的维度为d=300,并在嵌入层使用dropout来规范化网络，并且dropout率为0.5.
&emsp;&emsp;编码层的LSTM单元数量为300，解码层LSTM单元数量为600.
&emsp;&emsp;偏见参数α为10

#### 基线
- 基于流水线
- 传统联合抽取
- 端到端基于本文新标签方案

#### 实验结果
![image](https://user-images.githubusercontent.com/54270073/63332696-a1cc9600-c36a-11e9-9c3f-516e5831bd23.png)

![image](https://user-images.githubusercontent.com/54270073/63340368-700efb80-c379-11e9-9fa8-496a8db8f6de.png)

![image](https://user-images.githubusercontent.com/54270073/63342206-7e5f1680-c37d-11e9-82e5-791bf956f348.png)

![image](https://user-images.githubusercontent.com/54270073/63342237-8cad3280-c37d-11e9-9aa8-acb741cac90b.png)

![image](https://user-images.githubusercontent.com/54270073/63342261-9b93e500-c37d-11e9-9348-c817e880b445.png)

![image](https://user-images.githubusercontent.com/54270073/63342288-ababc480-c37d-11e9-9bee-4802335d8840.png)



### 4 分析和讨论

#### 错误分析

#### 偏见损失分析

#### 案例分析

### 5 结论