# 一种新型的,基于上下文的词嵌入犯法: ELMo
来自论文Deep contextualized word representations, 2018年发表
## 背景
作者认为, 好的词向量表征应该兼顾两个问题, 
1. 词语用法在语义上和语法上的复杂特点
2. 随着语言环境和上下文的改变,这些用法也应该随之改变
<br>
ELMo 算法的特点是, 每个词语的表征都是整个输入语句的函数,具体做法就是现在大语料上用 language model 来训练处 bi-LSTM 模型,然后利用 LSTM 产生词语的表征. 同时可以采用迁移学习, 在下游任务的应用中,忽略监督标签,利用语料来做 LM 的微调, 相当于一种 domain transfer; 然后利用 label 的信息进行 supervised learning.
