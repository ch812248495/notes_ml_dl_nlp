# cart树的实现和源码分析
简单实现了 cart 分类树的代码, 这里做一个总结和备忘
## 树结构

```python
class Tree:
    def __init__(self, value=None, trueBranch=None, falseBranch=None, results=None, col=-1, summary=None, data=None):
        self.value = value
        self.trueBranch = trueBranch
        self.falseBranch = falseBranch
        self.results = results
        self.col = col
        self.summary = summary
        self.data = data
```
这就是我们树的结构(实际是树上的节点), 每个节点有如下几个属性:
- value: 分类值val
- trueBranch: 左子树(判断为真(或大于分类点)的子集)
- falseBranch: 右子树(判断为假(或小于分类点)的子集)
- results: 类别统计, 用于辅助计算 gini 值
- col: 特征所在索引(列号)
- summary: 存储中间变量, 后面详解
- data: 存在这个节点里面的数据[2*2]

## 类别统计:
```python
def calculateDiffCount(datas):
#datas = ['b','b','c','a','a']
#return ['a':2, 'b':2, 'c':1]
    results = {}
    for data in datas:
        if data not in results:
            results[data] = 1
        else:
            results[data] += 1
    return results

```
如注释所示, 统计类别数量, 建立字典

## gini 指数计算
```python
def gini(rows):
    length = len(rows)
    results = calculateDiffCount(rows)
    imp = 0.0
    for i in results:
        imp += results[i] / length * results[i] / length  ## Gini: 1- sum((count(i)/total)^2)
    return 1 - imp
```
GINI 指数的计算方式:
$$1 - \sum(\frac{count(i)}{totalSampleNumber})^2$$

## 分类
```python
def splitDatas(rows, value, column):
    # 输入[[3,4,5,6],[4,5,6,7]]
    #value 5.5
    #colums 1
    #返回[[3,4,5,6]] [[4,5,6,7]]
    list1 = []
    list2 = []
    if (isinstance(value, int) or isinstance((value, float))):
        for row in rows:
            if (row[column >= value]):
                list1.append(row)
            else:
                list2.append(row)
    else:
        for row in rows:
            if row[column] == value:
                list1.append(row)
            else:
                list2.append(row)
    return (list1, list2)
```
将所有样本根据某个特征(column)的某个切分点(value)分成两部分

## 递归建立树
```python

def buildDecisionTree(rows, evaluationFunction = gini):
    currentGain = evaluationFunction(rows)
    column_length = len(rows[0])
    rows_length = len(rows)
    best_gain = 0.0
    best_value = None
    best_set = None

    for col in range(column_length-1):
        col_value_set = set([x[col] for x in rows])
        for value in col_value_set:
            list1, list2 = splitDatas((rows,value, col))
            p = len(list1) / rows_length
            gain = currentGain -p*evaluationFunction(list1) + (1-p)*evaluationFunction(list2) # 和初始状态的 GINI(混乱度)相比,提高了多少
            if gain > best_gain:
                best_gain = gain
                best_value = (col, value)
                best_set = (list1, list2)

    dcY = {'impurity': '%3f' % currentGain, 'samples': '%d' % rows_length}
    if best_gain > 0:
        trueBranch = buildDecisionTree(best_set[0], evaluationFunction)
        falseBranch = buildDecisionTree(best_set[1], evaluationFunction)
        return Tree(col=best_value[0], value = best_value[1], trueBranch = trueBranch, falseBranch = falseBranch, summary=dcY)
    else:
        return Tree(results=calculateDiffCount(rows), summary=dcY, data=rows)

```
- 对每个特征( col, 即每一列) 中的每一个切分点(value)进行遍历, 如果可以得到更好的分类效果( GINI 更低, 相对最一开始的 GINI 下降更多), 记录下增益, 这个特征, 这个值, 以及两个子树的数据,
- 这个 dcY 是给后建立节点的 summary 做准备的, 记录了相对开始的 GINI 增益, 样本数量(行数)
    ```python
        dcY = {'impurity': '%3f' % currentGain, 'samples': '%d' % rows_length}
    ```
- 当增益依然大于零的时候, 对左子树和右子树递归建树, 之后返回父节点(后序遍历)
- 完全生成之后,直接返回,不需要对左子树右子树继续递归

## 剪枝
```python
def prune(tree, miniGain, evaluationFunction = gini):
    if tree.trueBranch.results == None:
        prune(tree.trueBranch, miniGain, evaluationFunction)
    if tree.falseBranch.results == None:
        prune(tree.falseBranch, miniGain,evaluationFunction)
    if tree.trueBranch.results != None and tree.falseBranch.results != None:
        len1 = len(tree.trueBranch.data)
        len2 = len(tree.falseBranch.data)
        len3 = len(tree.trueBranch.data + tree.falseBranch.data)
        p = float(len1) /(len1 + len2)
        gain = evaluationFunction(tree.trueBranch.data + tree.falseBranch.data) - p * evaluationFunction(tree.trueBranch.data) - (1-p)*evaluationFunction(tree.falseBranch.data)

        if(gain < miniGain):
            tree.data = tree.trueBranch.data + tree.falseBranch.data
            tree.results = calculateDiffCount(tree.data)
            tree.trueBranch = None
            tree.falseBranch = None
```
计算分裂当前节点得到的 GINI 增益, 如果分裂后的 GINI相对之前的 GINI 增益没有特别大,小于给定的 minGini 值, 则合并两个节点, 将左子树和右子树置为空