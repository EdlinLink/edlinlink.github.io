---
layout: post
title:  "Mining of Massive Datasets 读书笔记(update: Dec 3)"
date:   2013-10-21 12:00:00
tags:	tech
myTag:	data_mining machine_learning 

---

# Mining of Massive Datasets 

# Chapter 1 Data Mining

## 1.1 What is Data Mining?

数据挖掘最通常的定义就是：构造数据的一个模型。

*"The most commonly accepted definition of “data mining” is the discovery of “models” for data."*

### 1.1.1 Statistical Modeling

统计学家将数据挖掘看作是构造一个潜在于这些数据分布的统计模型。

*"statisticians view data mining as the construction of a statistical model, that is, an underlying distribution from which the visible data is drawn."*

### 1.1.2 Machine Learning

数据挖掘中会使用到一些机器学习的算法。机器学习的使用者会把数据当作训练集去训练一些算法，例如贝叶斯网络，支持向量机，决策树，隐马可夫模型...

机器学习在我们对数据没有太多想法的时候是一中很好的方法。但是对于挖掘结果有比较直接认识的情况下，并不太成功。例如WhizBang! Labs尝试使用机器学习的方法去找哪些网页是别人的简历。但是机器学习的方法并不优于人工设计的算法，因为会有一些特定的词句会出现在简历中，是不是简历可以通过很普通的方法鉴别出来，所以机器学习的方法并没有优势。

*"Since everyone who has looked at or written a resume has a pretty good idea of what resumes contain, there was no mystery about what makes a Web page a resume. Thus, there was no advantage to machine-learning over the direct design of an algorithm to discover resumes."*

### 1.1.3 Computational Approaches to Modeling

近些年，计算机科学家把数据挖掘看作是一个算法性的问题，即这些数据的模型是类似于回答一个对数据的复杂查询问题。

对数据建模有不同方法，除了统计建模(统计数据的平均值，方差符合什么分布之类)主要还有以下方法：

1. 简要和近似的总结数据，或者
2. 提取出数据中最重要的特征，其它不重要的部分则省略。

*"1. Summarizing the data succinctly and approximately, or 2. Extracting the most prominent features of the data and ignoring the rest."*

### 1.1.4 Summarization

PageRank: 一个网页的PageRank指的是在任意时间，一个在网页中随机游走的人停留在那个页面的概率。

Clustering: 在聚类中，数据被看作是高唯空间的一个点。越相近的点它们越有可能属于同一类。

*"This number, the “PageRank” of the page, is (oversimplifying somewhat) the probability that a random walker on the graph would be at that page at any given time."*

*"data is viewed as points in a multidimensional space. Points that are “close” in this space are assigned to the same cluster."*

### 1.1.5 Feature Extraction

典型的基于特征的模型是希望找到一些特别的例子能够表示整个数据。一个复杂的不同物品间的关系通常是找物品间的强依赖关系，通过这些关系表示所有关联。一些重要的特征提取的形式有：

1. Frequent Itemsets. 我们希望找到在一个篮子里经常同时出现的物品(例如：汉堡和番茄酱)。
2. Similar Items. 我们希望找到一对有许多相似之处的集合(例如：亚马逊上购买过许多相同物品的群体，亚马逊可以推荐这个群体其它客户购买的产品给这个群体中的人。这个过程也成为“协同过滤(collaborative filtering)”)

*"The typical feature-based model looks for the most extreme examples of a phe- nomenon and represents the data by these examples."*

*"a complex relationship between objects is represented by finding the strongest statistical dependencies among these objects and using only those in representing all statistical connections."*

*"Amazon can look for “similar” customers and recommend something many of these customers have bought. This process is called “collaborative filtering.”"*



## 1.2 Statistical Limits on Data Mining

数据挖掘希望解决的问题就是发现隐藏在大数据后面的一些特别的事。

*"A common sort of data-mining problem involves discovering unusual events hidden within massive amounts of data."*

### 1.2.1 Total Information Awareness

在2002年，乔治布什属下的机关提出一项叫Total Information Awareness(TIA)的项目，这项项目计划通过去挖掘市民大众的所有政府能拿到的信息，例如信用卡收据，酒店记录，旅游记录等等，去发现一些恐怖活动。这个项目最终被国会叫停了，但是这个事件引起人们对一个思考。政府尝试在这些活动中分析看有没有跟恐怖分子类似的活动，那么你的大部分合法的行为，或者一些不合法但绝非恐怖活动的行为会招致政府找你麻烦吗？答案是这得看政府把你做的这些事件定义得有多窄。

*"are you not going to find many innocent activities – or even illicit activities that are not terrorism – that will result in visits from the police and maybe worse than just a visit? The answer is that it all depends on how narrowly you define the activities that you look for."*

### 1.2.2 Bonferroni's Principle

我们希望在数据中找到一些特别的事件，但是如果这些事件发生的次数随着数据规模增大而增大，那么这些事件有可能只是臆造的("bogus")。Bonferroni's principle简单来说就是通过计算某件事情出现的数学期望，如果在数据中该事件出现的次数没有超过这个期望，则认为这个事件只是臆造的。

在上一小节中提到的找恐怖份子的例子中，Bonferroni's principle是想说我们应该仅仅通过那些概率极小，几乎不可能在随机数据中出现的事件来寻找恐怖分子。这样最能保证通过这些事件找到的恐怖分子是真的恐怖分子。

*"If this number is signifi- cantly larger than the number of real instances you hope to find, then you must expect almost anything you find to be bogus, i.e., a statistical artifact rather than evidence of what you are looking for. This observation is the informal statement of Bonferroni’s principle."*

### 1.2.3 An Example of Bonferronis's Principle

假设在人群中有一伙坏人，这些换人会经常聚在酒店商讨他们的计划，那么现在有以下假设：

1. 一共有十亿(10^9)个人他们有可能是这些坏人。
2. 每天每100个人中就有一个去住酒店的。
3. 一间酒店能住100人，一共有100,000间酒店(每天所有酒店一共能够容纳一千万人)。
4. 我们能够获得1000天所有酒店的记录。

为了在这些记录数据中找到这些坏人，我们尝试找一下有没有哪两个人有两次在同一天同时住在同一间酒店的。我们认为这些人就是可疑的坏人。现在我们来分析一下这样的假设究竟正不正确。假如每个人住酒店这个行为都是随机的，那么某个人在任意一天住酒店的概率是0.01。那么特定的两个人在同一天住酒店的概率就是0.0001。那么他们在同一天住同一酒店的概率就是0.0001再除以酒店数10^5，也就是10^-9。如果他们出现两次同一天住同一酒店的概率就是10^-18。

我们现在想一下，在所有人群中出现两人同一天住同一酒店的期望是多少。从十亿人中选两人，C(2,n)大概是n^2/2，即5*10^17。在1000天的记录中选两天的选法有C(2,1000)，即5*10^5。所以在这1000天的记录中会出现大概

<center> 5\*10^17 \* 5\*10^5 \* 10^-18 = 250,000 </center>

这个多组可疑的坏人。假设真正的坏人只有10组，那么去调查这250,000组人是没有效果的。显然，通过在1000天的数据中找两次两人同一天住同一酒店的方法是不可行的。



## 1.3 Things Useful to Know

### 1.3.1 Importance of Words in Documents

在数据挖掘的应用中，我们常常会遇到文档分类问题。典型的像一片关于棒球的文章会频繁的出现"接球"，"击球"，"跑垒"，"挥杆"等词。我们可以根据这些词，把该文档分到棒球类。但是在我们知道这篇文章是讲什么之前，我们并不知道要去找哪一些词。所以文档分类的方法同先去找文章中的一些重要词。很容易我们会认为文章最经常出现的词就是重要词，但是最经常出现的词有可能是"的"，"是"，"和"这些没有具体意思的词，所以在找重要词之前，我们会把这些最常见的词给去掉。

事实上，一篇文章的主题是跟它文章中最不常见的词相关的，然而不是所有不常见的词都于主题相关。例如文中的人名是个不常见的词，但是我们获知这个人名对文章主题没有什么帮助。但是如果文章出现了"马球"这样的不常见的词，我们可以大概估计文章讲的是和马球相关的内容。我们猜想像人名这种不常见，但不是文章主题的词在文中不会频繁出现，但是像马球这种和文章主题相关的词，可能在该文会频繁出现。

我们对给定词语在文档中出现和文章主题之间关系度量的方法称为TF.IDF(Term Frequency times Inverse Document Frequency)。假设我们有N个文档，定义fij是词组i在文档j中出现的频率。则可再定义词频TFij:

$$TF_ij = f_ij / max_k F_kj$$

即是词组i在文档j中出现的频率再根据在该文档词频最大的词正则化。文档中最大词频的词(不包括无意词"的","和"...)的TF值是1。

一个词的IDF定义如下。如果一个词i在N个文档中，有ni个都包含这个词，那么IDFi = log2(N/ni)。词i在文档j的TF.IDF值值的就是TFij\*IDFi。TF.IDF值越高约能代表这篇文章的主题。 

#### Example 1.3 

假设有2^20个文档。词组w在2^10个文档中出现了。那么IDFw = log2(2^20/2^10) = 10。假设在文档j中w这个词出现了20词是出现次数最多的词(派出掉无意词后)。那么TFwj = 1。词组w在文档j的TF.IDF值是1\*10 = 10。

假设在文档k中，w只出现了一次，出现最多次数的词有20次，那么TFwk = 1/20，词组w在文档k的TF.IDF值为1/2。

*"The terms with the highest TF.IDF score are often the terms that best characterize the topic of the document."*

### 1.3.2 Hash Functions

哈希函数是我们常用的方法通过该函数将数据计算一个哈希值，一个哈希值对应一个桶，从而将数据放入B个不同的桶内。我们希望每个桶的负载均匀，但是实际上这是不可能实现的，例如我们定义哈希函数h(x) = x mod B。如果B是偶数，那么所有偶数桶内的数据会比奇数桶多，所以我们常常将B定为素数个桶。以达致各个通尽量负载均衡。

在哈希值不是整数的情况下，我们可以通过将哈希值写成bits的形式，这样就可以通过bits的形式转变成整数。例如输入的数据是字符，我们可以将他们转成ASCII值，相加再模除B。如果B是一个很大的数，那么我们可以将几个字符的ASCII连起来，看成一个大数，相加再模除B。

对于其他复杂的数据类型也可以用类似的方法：

+ 假设输入类型是record，可以对每一个部分转成整型，全部相加再模除B。
+ 假设输入类型是数组，可以对每一个数组内的元素转成整型，相加再模除B。


