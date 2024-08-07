

> 数据

```python
from kivi.Dataset import *
from kivi.FeatureAnalysis import VIF
# 测试数据
df_bank = Dataset.BankData()
df_bank = df_bank.select_dtypes(['int64', 'float64']).copy()
```

### 1. 相关系数 `spearman`

> 说明

Spearman 秩相关系数是两个数据集之间关系单调性的非参数度量。
与 Pearson 相关性不同，Spearman 相关性不假设两个数据集都是正态分布的。
Spearman 系数在 -1 和 +1 之间变化，0 表示没有相关性。
-1 或 +1 的相关性意味着精确的单调关系。正相关意味着随着 x 的增加，y 也会增加。
负相关意味着随着 x 增加，y 减少。

p 值粗略地表示不相关系统生成具有 Spearman 相关性的数据集的概率，
p 值并不完全可靠，但对于大于 500 左右的数据集可能是合理的。

> 参数

- `:param df:` `DataFrame` 需要统计相关系数的数据。
- `:param columns:` 需要统计相关系数的数据列名。

> 示例

```python
df_corr = spearmanr(df, columns)
```

### 2. 多重共线性检测与处理

> 理论分析

- 不论是一般回归，逻辑回归，或生存分析，都要同时考虑多个预测因子，如果各个自变量x之间有很强的线性关系，就无法固定其他变量，
也就找不到x和y之间真实的关系了，因此多重共线性是不可避免需要面对的。
- 在构造预测模型时如何处理多重共线性是一个比较微妙的议题。既不能不加控制，又不能一刀切，认为凡是多重共线性就应该消除。

假设有$k$个自变量的多元线性回归模型: 
$$
\begin{aligned}
y & = \theta_0 + \theta_1 x_1 + ... + \theta_k x_k +\varepsilon \\\\
& = X\theta + \varepsilon \\\\
\varepsilon & = N(0, \sigma^2) \\\\
\hat{\theta} & = (X^T X)^{-1}X^Ty 
\end{aligned}
$$
该求解公式唯一的条件是矩阵$X$是列满秩的，不然会有无穷多解，
当各变量之间存在共线性问题，即各变量之间存在部分线性相关时，例如：
$$x_3 = x_2 + x_1 + \varepsilon$$
易知此时$X$近乎是不满秩的，$X^TX$近乎是奇异的，$X$的最小奇异值会非常小。

> 多重共线性解决手段

多重共线性是普遍存在的，通常情况下，如果共线性情况不严重（VIF<5），不需要做特别的处理。
如存在严重的多重共线性问题，可以考虑使用以下几种方法处理：

**1. 手动移除出共线性的变量**

先做相关分析，如果发现某两个自变量$X$（解释变量）的相关系数值大于$0.7$，则移除掉一个自变量（解释变量），然后再做回归分析。
此方法是最直接的方法，但有的时候我们不希望把某个自变量从模型中剔除，这样就要考虑使用其他方法。

**2. 逐步回归法**

让系统自动进行自变量的选择剔除，使用逐步回归将共线性的自变量自动剔除出去。此种解决办法有个问题是，
可能算法会剔除掉本不想剔除的自变量，如果有此类情况产生，此时最好是使用岭回归进行分析。

**3. 增加样本容量**

增加样本容量是解释共线性问题的一种办法，但在实际操作中可能并不太适合，原因是样本量的收集需要成本时间等。

**4. 岭回归**

上述第1和第2种解决办法在实际研究中使用较多，但问题在于，如果实际研究中并不想剔除掉某些自变量，某些自变量很重要，
不能剔除。此时可能只有岭回归最为适合了。岭回归是当前解决共线性问题最有效的解释办法。

> 其他问题

**多重共线性与相关性的区别**

相关性，是指两个变量的关联程度。一般地，从散点图上可以观察到两个变量有以下三种关系之一：两变量正相关、负相关、不相关。
如果一个变量高的值对应于另一个变量高的值，相似地，低的值对应低的值，那么这两个变量正相关。

**多重共线性不需要处理的场景**

1. 多重共线性是普遍存在的，轻微的多重共线性问题可不采取措施，如果`VIF`值大于`10`说明共线性很严重，这种情况需要处理，
如果`VIF`值在`5`以下不需要处理，如果`VIF`介于`5~10`之间视情况而定。

2. 如果模型仅用于预测，则只要拟合程度好，可不处理多重共线性问题，存在多重共线性的模型用于预测时，往往不影响预测结果。
-----

