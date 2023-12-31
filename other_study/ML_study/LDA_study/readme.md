# 【关于 主题模型(topic model) 】那些你不知道的事

> 作者：杨夕
> 
> 项目地址：https://github.com/km1994/nlp_paper_study
> 
> 个人介绍：大佬们好，我叫杨夕，该项目主要是本人在研读顶会论文和复现经典论文过程中，所见、所思、所想、所闻，可能存在一些理解错误，希望大佬们多多指正。
> 

## 目录

## 主要任务

主题模型是用来做文档建模的，**将文档转化为数值向量，数值向量的每个维度对应于一个主题**。因此，本质上说，主题模型的提出根本上是为了实现文本数据的结构化，而结构化的文档可以彼此进行比较和查询，并实现传统的机器学习任务。

## 两类问题

1. 文档聚类
2. 词汇聚类

## 介绍

基于主题模型，每一篇文档都是一个主题的多项分布，文档集合完成了一个软聚类（Soft Clustering）过程，各个主题就是聚类中心，文档在各个主题上的概率就是它对于这个聚类中心的隶属度指标；主题模型还得到了各主题下词汇的概率分布，将“主题-词汇”概率矩阵90°旋转，就可以类似地，得到”词汇在各个主题“的概率分布情况，也就是所谓的词汇的软聚类结果。

## 主要价值

1. 文档的结构化，相比于传统的词袋模型达到了降维的效果；
2. 完成了文档的聚类和词汇的聚类，实现文本信息的抽象化分析，帮助分析者探索隐含的语义内容

## 常用方法

### 短文本主题建模的利器 ---Biterm Topic Model

#### 动机

解决短文本上 的 主题建模问题

#### 思路

- 首先 采用 **去掉低频和stopword** 方法 抽取biterm词对；
- 对于短文本（如tweets或百度知道等），取一个doc中的任意两个词对 ；
- 对于长文本，需要两个词在一定的距离之内，这个窗长一般取到30~60效果较好.然后通过biterm对文档集合进行建模.

#### LDA 方法及问题

- 存在问题：稀释性问题。LDA中每个文档对应一个theta，每个词对应一个z。对于短文本，由于词少，每个文本单独对应于theta，使得 z-->theta 这一步的统计可能不具备统计意义，而且增加文本数量不能克服这种短文本带来的缺陷。
- 解决方法：
  - 方法一：将多个短文本聚合成一个长文本。（eg：LDA-U将一个user的多个发言聚合在一起减少稀疏影响）；【问题：需要有外部信息来辅助找到聚合的单位】
  - 方法二：利用一个长文档文本集作为辅助数据，将长文本的一些信息先以某种形式抽取反应到先验中，从而间接地提供给短文本。

#### mixture of unigram 方法及问题

- 优点：优于 LDA;
- 原因：它对于所有文档用同一个theta，克服了短文本稀疏性问题；
- 缺点：它假设了整个文档对应于一个z，这比较粗糙和strong了，会导致一个topic类内距离很大。（eg：因为对于长文本，较远的段落之间可能语义差异很大了。在长文本以及K较大的时候，这个模型效果会比较差，但在短文本上，这个缺陷倒是不那么明显，因此效果好于LDA系列）

#### BTM模型

- 特点：综合上面两种方法

1. BTM跟mix一样，利用了整个文本集合来估计一个theta，解决了稀疏问题（我们通常有海量数据）；
2. 放宽了mix中对整个doc必须同属于一个z的约束（相当于从整doc放松到了窗口长度类的两个词），加强了LDA中每个词对应于一个Z的假设（BTM中约束了窗长内的两个词组成一个biterm 对应于一个z）；

### 多主题文本建模的利器 --- Multi-Grain Topic Model

- 场景：有关于联想、戴尔、苹果三台笔记本电脑的若干评论数据
- 方法：基于全局和局部信息，既发现了宏观上的大主题（eg：评论对应的品牌 --- 联想、戴尔、苹果；微观上的小主题，苹果电脑对应的性能话题，联想电脑对应的性价比话题等，二者相互补充，比LDA返回的信息量要大很多。）

### 掺入少许先验知识的主题模型————Topic Modeling with Minimal Domain Knowledge

- 方法：通过关联解释（Correlation Explanation ）进行主题建模会产生丰富的主题，这些主题可以最大限度地提供一组文本数据的信息。这种方法优化了稀疏二进制数据（Sparse Binary Data）的Correlation Explanation框架，允许对大型语料库进行主题建模。

### 揭示不同作者写作主题分布的主题模型 --- Author-Topic Model

- 思路：能对某个语料库中作者的写作主题进行分析，找出某个作家的写作主题倾向，以及找到具有同样写作倾向的作家，它是一种新颖的主题探索方式。

### 分析话题热点变迁的主题模型 --- Dynamic Topic Models

- 动机：蕴含时间因素的主题，有些主题 包含的关键词会随着时间的变化而产生相应的变化
- 思路：如构成要素不断更新换代的“忒修斯之船（The Ship of Theseus）”一般，即使同一主题下的开端和末尾中的主题词没有一个是相同的，但还是原先的主题，保留有相同的语境。

## 要求

1. 文档足够多。如果文档少，就没用进行聚类的必要，主题个数比文档个数还多，没用现实的指导意义。
2. 文档足够长。短文本效果不好，这是共识！短文本包含的文本特征太少了，原始的基于词袋模型的文档向量太稀疏了。
3. 词汇特征足够多。与上述原因类似，文档集涉及的词汇个数若太少，也没有聚类价值。
4. 词频足够大。低词频的词汇必要时需要采用规则过滤掉或进行预处理转化为高频同义词或特殊符号（比如，同样格式的都用文本标签代替，如：<网页><手机号><邮政编码>等）。低频词本身很难准确聚类！准确度也没用可信性。

## 参考资料

1. [主题模型(topic model)到底还有没有用，该怎么用？](https://www.zhihu.com/question/34801598)
