# GBDT实现
## GBDT 结构定义
```python
    def __init__(self, n_estimators, learning_rate, min_samples_spilt, min_impurity, max_depth, regression):
        self.n_estimators = n_estimators
        self.learning_rate = learning_rate
        self.min_samples_spilt = min_samples_spilt
        self.min_impurity = min_impurity
        self.max_depth = max_depth
        self.regression = regression

        self.bar = progressbar.ProgressBar(widgets=bar_widgets)
        self.loss = SquareLoss()
        if not self.regression:
            self.loss = SoftMaxLoss()

        self.trees = []
        for i in range(self.n_estimators):
            self.trees.append(RegressionTree(min_samples_spilt = self.min_samples_spilt,min_impurity = self.min_impurity,max_depth = self.max_depth))
```
- n_estimator: 序列树个数
- learning_rate: 学习率
- min_sample_split: 叶节点最少的样本数量
- min_impurity: 最小纯度, 小于它之后不再切割
- max_depth: 子树的最大层数
- regressiong: 是不是回归问题
- loss: 回归树, 二次损失(对分类问题相同处理)
- trees: 先初始化一系列的序列树


## 拟合
```python
def fit(self, X, y):
    self.trees[0].fit(X, y)
    #让第一棵树进行第一次拟合
    y_pred = self.trees[0].predict(X)
    for i in self.bar(range(1, self.n_estimators)):
        gradient = self.loss.gradient(y, y_pred)
        self.trees[i].fit(X, gradient)
        y_pred -= np.multiply(self.learning_rate, self.trees[i].predict(X))
```
拟合的过程就是让下一棵树不断拟合上一棵树和真实值的残差(梯度)

## 预测
```python
def predict(self, X):
    y_pred = self.trees[0].predict(X)
    for i in range(1, self.n_estimators):
        y_pred -= np.multiply(self.learning_rate, self.trees[i].predict(X))
 
    if not self.regression:
        # Turn into probability distribution
        y_pred = np.exp(y_pred) / np.expand_dims(np.sum(np.exp(y_pred), axis=1), axis=1)
        # Set label to the value that maximizes probability
        y_pred = np.argmax(y_pred, axis=1)
    return y_pred
```
预测就是不断向下计算残差, 和当前的预测值相减
