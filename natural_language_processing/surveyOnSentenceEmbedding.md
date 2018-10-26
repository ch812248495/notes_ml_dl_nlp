# 对于句子向量研究情况的一些调研
词向量(word embedding)是自然语言的基石, 可以说自然语言处理的快速发展开始于 word embedding 思路的引入, 只要是涉及到深度学习的自然语言处理, 无不涉及到由词向量引申出的句向量甚至段落向量, 文档向量的获取.
在实际生产生活中, 句向量的应用更加广泛, 比如基于索引的问答系统, 问题搜索匹配等等.

## 一些经典的解决方案
- bag of words
    这是最最直接的一种方案, 对出现词语建立字典, 忽略语序, 利用count, tfidf, 等方式进行编码, 或者简单求平均, 这是一套非监督的算法, 可迁移性好, 缺点是忽略了语序, 比如not good, not bad分开来之后, 无法分辨到底是怎样的情感态度,因此在一些情感分析的场景下效果不佳
- RNN(LSTM, GRU)
    利用 RNN 族的网络对句子进行编码, 在监督任务的场景下端到端对句子进行编码是一种很好的方法, RNN 引入了时序的特征, 在特定任务上表现良好, 但是这是一种监督的算法, 因此迁移性差, 不同的任务需要从头开始训练模型, 同时 LSTM 的小号很大, 而且不能并行计算, 速度很慢
- Sentence2vec
    利用了 word2vec 中的思路, 初始化一个句向量, 以这个句子中的词语作为目标, 进行训练, 这是一种非监督的算法, 可以有一定的迁移性, 其效果在 skip-gram 中也有非常有效的证明, 但是训练时间太长了, 而且大多数词语重复,但是关键词差别大的句子, 比如 what is this, how is it going.
    <div align=center>
    <img width=300 height=200 src="images/1.png">
    </div>
- skip-thought vector
    同样借鉴了 skip-gram, 通过训练一个 LSTM 编码器, 给句子编码, 通过周围句子向量的相似性学习模型, 和上面 sentence2vec 不同的是, 这种方法是以上下文的向量来训练本句的句向量的.
从机器学习的类型上来看, 

| 方法 | 类型 |
|-|-|
bag of words | 无监督
RNN | 监督
paragraph2vec | 无监督
skip-thought | 无监督

## 一些新的研究进展
- SIF
    来自论文one simple but tough-to-beat basline for sentence embeddings, 这种方法非常简单, 是 bag of words 的扩展<br>
    方法是这样的, 句子首先表示为词语向量的加权平均, 然后寻找这个向量的主成分(向量在主轴上的投影), 然后向量减去这个主成分就行了
$$v_s = \frac{1}{s}\sum \frac{a}{a+p(w)} \tag{1}$$
$$v_s = vs-uu^Tv_s\tag{2}$$

SIF算是句向量学习深度学习当道的现在的一股清流了, 在实验中, similarity 和 entailment 任务这种方法远远好于 LSTM,DAN,skip-thought 等等,只在 sentiment 任务中弱于 LSTM, 猜测原因是downweight 了诸如 not, good 这样重要的高频词
