---

> `WOE-IV` 简介

- `WOE(Weight of Evidence)`常用于特征变换
- `IV(Information Value)`则用来衡量特征的预测能力

> `WOE` 应用

- 处理缺失值：当数据源没有`100%`覆盖时，那就会存在缺失值，此时可以把`null`单独作为一个分箱。
这点在分数据源建模时非常有用，可以有效将覆盖率哪怕只有`20%`的数据源利用起来。
- 处理异常值：当数据中存在离群点时，可以把其通过分箱离散化处理，从而提高变量的鲁棒性（抗干扰能力）。
例如，`age`若出现`200`这种异常值，可分入`age > 60`这个分箱里，排除影响。
- 业务解释性：我们习惯于线性判断变量的作用，当`x`越来越大，`y`就越来越大。
但实际`x`与`y`之间经常存在着非线性关系，此时可经过`WOE`变换。

> [数据来源](./dataset/dataset.md)

```python
# 引入 kivi- 模块
from kivi import WOE
```

### 1. 无监督分箱

#### 1.1 等距分箱

> 参数：

- `:param variables:` 待分箱变量。
- `:param target:` 目标标签变量。
- `:param bins:` 等频分箱数目。
- `:param fill_bin:` 在各分箱中偶发性会出现 good 或 bad 为 0 的情况，默认 fill_pos 为 True ，为该分箱填充 0.5。

> 示例：

```python
woe = WOE.Distance(df_bank.age, df_bank.target, bins=5)
df_woe = woe.fit()
df_woe
```

#### 1.2 等频分箱

> 参数：

- `:param variables:` 待分箱变量。
- `:param target:` 目标标签变量。
- `:param bins:` 等频分箱数目。
- `:param fill_bin:` 在各分箱中偶发性会出现 good 或 bad 为 0 的情况，默认 fill_pos 为 True ，为该分箱填充 0.5。

> 示例：

```python
woe = WOE.Frequency(df_bank.age, df_bank.target, bins=5)
df_woe = woe.fit()
df_woe
```

#### 1.3 类别型分箱

> 参数：

- `:param variables:` 待分箱变量。
- `:param target:` 目标标签变量。
- `:param fill_bin:` 在各分箱中偶发性会出现 good 或 bad 为 0 的情况，默认 fill_pos 为 True ，为该分箱填充 0.5。

> 示例：

```python
woe = WOE.Category(df_bank.job, df_bank.target)
df_woe = woe.fit()
df_woe
```

#### 1.4 `Kmeans`分箱

> 参数：

- `:param variables:` 待分箱变量。
- `:param target:` 目标标签变量。
- `:param cutoffpoint:` 分箱截断点。
- `:param fill_bin:` 在各分箱中偶发性会出现 good 或 bad 为 0 的情况，默认 fill_pos 为 True ，为该分箱填充 0.5。

> 示例：

```python
import numpy as np

# 类别型分箱计算
woe = WOE.KmeansBins(df_bank.age, df_bank.target, bins=5)
df_woe = woe.fit()
df_woe
```

#### 1.5 手动指定分箱模式

> 参数：

- `:param variables:` 待分箱变量。
- `:param target:` 目标标签变量。
- `:param cutoffpoint:` 分箱截断点。
- `:param fill_bin:` 在各分箱中偶发性会出现 good 或 bad 为 0 的情况，默认 fill_pos 为 True ，为该分箱填充 0.5。

> 示例：

```python
import numpy as np

# 类别型分箱计算
woe = WOE.CutOffPoint(
    df_bank.age, df_bank.target,
    cutoffpoint=[-np.inf, 20, 22, 50, 70],)
df_woe = woe.fit()
df_woe
```

### 2. 有监督分箱

#### 2.1 Pearson 分箱

- 计算全部变量的最优分箱，依据`Pearson`相关系数合并最优分箱的优化过程。

> 参数：

- `:param df:` 需要计算的DataFrame。不需要进行字段数据类型区分，以及缺失处理。
- `:param targetName:` 标签名称，默认为'target'。
- `:param R:` 相关系数阈值。
- `:param ForceBin:` 最小分箱数。
- `:param MaxBin:` 最大分箱数。

> 示例：

```python
woe = WOE.Pearson(df_bank)
df_woe = woe.fit()
df_woe
```

#### 2.2 决策树分箱

> 参数：

- `:param variables:` 待分箱变量。
- `:param target:` 目标标签变量。
- `:param bins:` 分箱数量，默认为 5 。
- `:param fill_bin:` 在各分箱中偶发性会出现 good 或 bad 为 0 的情况，默认 fill_pos 为 True ，为该分箱填充 0.5。

> 示例：

```python
woe = WOE.TreeBins(df_bank.age, df_bank.target, bins=5, fill_bin=True)
df_woe = woe.fit()
df_woe
```

#### 2.3 `Best-KS`分箱

> 参数：

- `:param variables:` 待分箱变量。
- `:param target:` 目标标签变量。
- `:param bins:` 分箱数量。
- `:param fill_bin:` 在各分箱中偶发性会出现 good 或 bad 为 0 的情况，默认 fill_pos 为 True ，为该分箱填充 0.5。

> 示例：

```python
# 类别型分箱计算
woe = WOE.KSMerge(df_bank.age, df_bank.target, bins=5)
df_woe = woe.fit()
df_woe
```

#### 2.4 卡方分箱

> 什么是卡方分箱

卡方分箱正是一种基于卡方检验的分箱方法，使用卡方独立性检验来实现核心分箱功能的。

> 卡方统计量

$$\chi^2 = \sum_{i=1}^{m}\sum_{j=1}^{k}\frac{(A_{ij} - E_{ij})^2}{E_{ij}}$$

- $m=2$：表示相邻的两个分箱数目
- $k$：表示目标变量的类别数，比如目标是网贷违约的好和坏，那么$k=2$。$k$也可以是多类，大于2。
- $A_{ij}$：实际频数，即第i个分箱的j类频数
- $E_{ij}$：期望频数

其中，期望频数的公式如下，可根据$P(AB)=P(A)P(B)$推导出来：

$$E_{ij} = \frac{R_i}{C_k}{N}$$

- $R_i$&$C_j$：分别是实际频数整列和整行的加和。

> **假设有以下数据，分为Bins-1，Bins-2两组。频数如下表：**

|组别|标签：0|标签：1| R|
|---|-------|-------|---|
|Bins-1|100|     9|109  |
|Bins-2|101|     8|109  |
|C|201|17|218|

> 卡方分箱步骤

1. 初始化步骤
   1. 根据连续变量值大小进行排序 
   2. 构建最初的离散化，即把每一个单独的值视为一个箱体。这样做的目的就是想从每个单独的个体开始逐渐合并。
   3. 计算所有相邻分箱的卡方值：也就是说如果有1,2,3,4个分箱，那么就需要绑定相邻的两个分箱，共三组：12,23,34。然后分别计算三个组的卡方值。

2. 合并：
   1. 从计算的卡方值中找出最小的一个，并把这两个分箱合并，比如，23是卡方值最小的一个，那么就将2和3合并，本轮计算中分箱就变为了1,23,4。
   2. 低卡方值表明它们具有相似的类分布。直到满足停止条件。

3. 停止条件：
   1. 卡方停止的阈值
   2. 分箱数目的限制

*只要当所有分箱对的卡方值都大于阈值，并且分箱数目小于最大分箱数时，计算就会继续，直到不满足。*

> 参数：

- `:param variables:` 待分箱变量
- `:param target:` 目标标签变量
- `:param init_bins:` 初始化分箱数量，在此分箱数量基础上进行等频分箱，再依据卡方检验进行分箱的合并
- `:param min_bins:` 最小的分箱数量
- `:param max_bins:` 最大的分箱数量
- `:param m:` 目标变量类别数
- `:param fill_bin:` 在各分箱中偶发性会出现 good 或 bad 为 0 的情况，默认 fill_bin 为 True ，为该分箱填充 0.5。
- `:param confidence_level:` 卡方检验置信度

> 示例：

```python
# 类别型分箱计算
woe = WOE.Chi2Merge(df_bank.age, df_bank.target, bins=5)
df_woe = woe.fit()
df_woe
```

### 3. 分箱的一些补充说明

> `.fit()` 说明

- `score=True`: 是否增加 WOE 分数，默认为 `True`。
- `order=True`: 是否增加分箱的排序判断，默认为 `True`。
- `origin_border=False`: 是否增加统计分箱中的最大值与最小值，默认为 `False`。

> 其他变量方法：

- `woe.cutoffpoint`: 分箱截断点。

> 数据中存在异常值

1. 当数据中存在异常值：`-1111`,`-9999`。
2. 当比率型指标，分子不存在，或分母不存在时需要进行分类讨论；比如资产负债率指标，
资产不存在与负债不存在可能是完全不同的状况，需要在分箱时分类讨论。

**在以上情况下，** 可以指定`abnormal_vals`参数。示例如下：

```python
woe = TreeBins(df_bank.age, df_bank.target, bins=5, abnormal_vals=[19, 31, -9999])
woe.fit()
```

> 结果数据的介绍

```markdown
|    | var_name   |   missing_rate |   min_bin |   max_bin |   total |   bad |   bad_rate |        woe |         iv |   iv_value | order    |   score |
|---:|:-----------|---------------:|----------:|----------:|--------:|------:|-----------:|-----------:|-----------:|-----------:|:---------|--------:|
|  0 | duration   |              0 |      -inf |        75 |     693 |     1 |  0.001443  | -4.50129   | 0.770083   |  1.85743   | 单调上升 |     100 |
|  1 | duration   |              0 |        75 |       211 |    1855 |    72 |  0.038814  | -1.17109   | 0.360173   |  1.85743   | 单调上升 |      50 |
|  2 | duration   |              0 |       211 |       383 |    1071 |   146 |  0.136321  |  0.192112  | 0.00940973 |  1.85743   | 单调上升 |      30 |
|  3 | duration   |              0 |       383 |       645 |     543 |   122 |  0.224678  |  0.799688  | 0.103092   |  1.85743   | 单调上升 |      20 |
|  4 | duration   |              0 |       645 |       inf |     359 |   180 |  0.501393  |  2.04387   | 0.614673   |  1.85743   | 单调上升 |       0 |
```

- `var_name`: 指标的名称。
- `missing_rate`: 指标的缺失率。
- `min_bin`: 分箱的左边界，左开右闭。
- `max_bin`: 分箱的右边界，左开右闭。
- `total`: 该箱样本总量。
- `bad`: 该箱坏样本量。
- `bad_rate`: 该箱违约率。
- `woe`: `woe` 值。
- `iv`: 该箱 `iv` 贡献。
- `iv_value`: 该变量的`IV`值。
- `order`: 变量单调性`['单调上升', '单调下降', '数据不足', '未知',]`。
- `score`: 分箱的分值，值域为`[0, 100]`分，违约越低分值越大,依据以下公式进行计算：

*step 1:*

$$
woe_{neg} = - woe
$$

*step 2:*

$$
woe_{score} = \frac{woe_{neg} - min(woe_{neg})}{max(woe_{neg}) - min(woe_{neg})}
$$

*step 3:*

对 $woe_{score}$ 按照 `5` 进行取整。如：
1. `24.5` 取整为 `25`；
2. `25.5` 取整为 `25`；
3. `28.5` 取整为 `30`；


> 指标选取的一般原则

1. 各分箱的样本数量应相对均衡。
2. 分箱应尽量保证单调性。
3. ...

### 4. 不同分箱方式的结果比较

> 无监督分箱

无监督分箱一般只取决于数据字段自身的分布，不同的分箱方式有的时候好有的时候差。

> 有监督分箱

决策树分箱与卡方分箱一般优于`Best-KS`；决策树分箱比卡方分箱效率会高一些。

### 5. 加权分箱分析

#### 5.1 加权分箱的基本思想

> 模型迭代导致的一些问题：

1. **模型迭代导致样本有偏**。在模型的迭代过程中，业务上会不断拒绝综合决策不通过的客户，导致随着信贷业务的开展后续的入模样本是有偏的（缺失了拒绝客户的信贷表现）。
2. **有偏样本导致分箱失准**。在有偏的样本上进行分箱的分析，可能会导致分箱的截断点有偏，严重时可能会导致之前有效指标的失效。

> 加权分箱的基本思想：

1. 在`KGB样本`中加入`IGB样本`，但是在具体计算分箱截断点时，分别考虑两类样本的权重。
2. 在计算分箱的`WOE`时，需要统计各个分箱的好坏客户量，这时也分别考虑两类样本对于好坏客户量的贡献。
3. 总体来说，加权分箱方法考虑了`IGB样本`一定的数据分布特性，补充在`KGB样本`上，从一定程度上降低了样本的偏离程度，复原了样本的总体情况，从而使得分箱更具鲁棒性。

*注：`KGB`指的是已知标签的样本，即放款的客群。`IGB`为未知标签的客群，即未放款的客群。*

#### 5.2 加权分箱的基本做法

> 依据拒绝客户进行加权分箱，步骤如下：

- `Step 1`: 在**KGB样本**上进行自动化决策树分箱，依据分箱转换指标值为`WOE`值，并依据`WOE`值做一个`logistic`模型。
- `Step 2`: 在**IGB样本**上使用`Step 1`模型进行`PD`计算，以`PD`值为**IGB样本**的权重；复制一份**IGB样本**并以`1 - PD`为权重。
- `Step 3`: 在**KGB样本**以`1`为样本权重，并组合两个**IGB样本**与一个**KGB样本**，共同构成新的样本。
- `Stpe 4`: 在新样本上，进行样本加权决策树分类，得到分箱的截断点。
- `Step 5`: 在`Stpe 4`的分箱截断点的基础上，进行`WOE/IV`的计算。其中好客户、坏客户的计算以加权求和方式进行。

$$
\sum_{bucket} good = \sum_{bucket} weight_i \times good_i
$$

$$
\sum_{bucket} bad = \sum_{bucket} weight_i \times bad_i
$$

> 实现：

*Step 1: 样本的划分*

```python
from kivi.Dataset import Dataset

# 个人信贷违约数据集
df_bank = Dataset.BankData()

# 样本量划分
good = (df_bank.target.count() - df_bank.target.sum())
bad = df_bank.target.sum()
n_good = int(good * 0.7)
n_bad = int(bad * 0.7)

# KGB 通过决策的样本，好客户占总量 70% 坏客户占总量的 30%
df_kgb_good = df_bank[df_bank.target == 0][: n_good]
df_kgb_bad = df_bank[df_bank.target == 1][n_bad: ]
df_kgb = df_kgb_good.append(df_kgb_bad)

# IGB 未通过决策的样本，好客户占总量 30% 坏客户占总量的 70%
df_rej_good = df_bank[df_bank.target == 0][n_good: ]
df_rej_bad = df_bank[df_bank.target == 1][: n_bad]
df_rej = df_rej_good.append(df_rej_bad)

print(f'已通过样本：{df_kgb.shape} 违约率：{df_kgb.target.mean():.3f}')
print(f'未通过样本：{df_rej.shape} 违约率：{df_rej.target.mean():.3f}')

# 选择数值型变量
columns = ['campaign', 'duration', 'previous', 'age', 'balance']
df_kgb['uuid'] = np.arange(0, len(df_kgb))
df_kgb = df_kgb[columns + ['target', 'uuid']].copy()
df_rej['uuid'] = np.arange(1e4, 1e4+len(df_rej))
df_rej = df_rej[columns + ['target', 'uuid']].copy()
```

*Step 2: 在`KGB`上进行分箱，并进行`KGB`模型拟合*

```python
# 在`KGB`上进行分箱，并将数据转换为指标分数
df_woe, fault_cols = WOE.WOEBatchWithRebin(df_kgb, drop_columns=['uuid', 'target'])
df_woeval = WOE.TransToWOEVal(df_kgb, df_woe, values='score', batch=3)

# `KGB`模型拟合
model = StatsLogit(df_woeval[columns], df_woeval.target)
metrics = RocAucKs(df_woeval.target, model.predict())
print(f"KS = {metrics.get('ks'): .3f}, AUC = {metrics.get('auc'): .3f}")

>>> KS =  0.629, AUC =  0.870
```

*Step 3: 样本权重计算，以及拼接样本*

```python
# 在`KGB`样本上，样本权重都是 1
df_kgb['pd'] = 1

# 使用上面拟合的 `KGB` 模型对 `IGB` 样本进行 `PD` 拟合
df_woeval_rej = WOE.TransToWOEVal(df_rej, df_woe, values='score', batch=3)
rej_predict = model.predict(sm.add_constant(df_woeval_rej[columns]))
df_rej['pd'] = rej_predict.tolist()

# 权重为 1-PD 的 `IGB` 样本
df_rej_a = df_rej.copy()
df_rej_b = df_rej.copy()
df_rej_a.target = 1
df_rej_b.target = 0
df_rej_b.pd = 1 - df_rej_b.pd
df_rej_b.uuid = df_rej_b.uuid + 3e4

# 组合 `IGB` 和 `KGB` 成为新的有权重的样本
df_rej_new = df_rej_a.append(df_rej_b)
df_data = df_rej_new.append(df_kgb)
df_data.reset_index(drop=True, inplace=True)
```

*Step 4: 进行加权分箱*

```python
# 加权分箱
df_woe, fault_cols = WOE.WOEBatchWithRebin(
    df_data, drop_columns=['uuid', 'target', 'pd'], 
    dtc_weight=df_data.pd, weight=df_data.pd)

# 使用加权分箱，将 KGB 样本指标转换为指标分数
df_woeval = WOE.TransToWOEVal(df_kgb, df_woe, values='score', batch=3)
```

*Step 5: 模型拟合，效果评估*

```python
# 新的 KGB 样本模型拟合
model = StatsLogit(df_woeval[columns], df_woeval.target)

# 模型评估
metrics = RocAucKs(df_woeval.target, model.predict())
print(f"KS = {metrics.get('ks'): .3f}, AUC = {metrics.get('auc'): .3f}")

>>> KS =  0.629, AUC =  0.871
```

#### 5.3 加权分箱的结果对比

> 分箱结果对比

*在 `KGB样本` 上拟合截断点，在全量样本上分箱评估对比：指标`IV`由`KGB`样本的`1.820`到全量样本`1.833`。*

<img src="./img/woe_dtc_weight_0.png">

*在 `KGB样本` 上拟合截断点，并进行权重修正，然后在全量样本上分箱评估对比：指标`IV`由权重修正的`1.803`提升到全量样本`1.839`。*

<img src="./img/woe_dtc_weight_1.png">

**可以看到，在样本上，修正后的样本`IV`低于权重修正前，但是在全量样本上，修正后`IV`会更高。**

> 模型结果对比

1. 加权修正前：KS = 0.629, AUC = 0.870
2. 加权修正后：KS = 0.629, AUC = 0.871
3. 上面的过程只是个示例，实际上上述的样本`IGB样本` 与 `KGB样本`源于同一个样本且数据量相对较少，可以看到加权修正后，模型略有提升。
4. 可以预期，在`IGB样本` 与 `KGB样本`差异更大在的大样本上，修正后的分箱截断点更接近全量样本的分箱截断点，从而使得稳定性和有效性略优于权重优化前。

> 成因分析

**如下图，在值域较大的`bucket`中，权重修正后有更高的违约率，也就一定程度上提高了指标的区分能力。**

```python
(df_woe_wt.bad_rate - df_woe_wt_origin.bad_rate).abs().plot(label='wt')
(df_woe_kgb.bad_rate - df_woe_kgb_origin.bad_rate).abs().plot(label='kgb')
plt.legend()
```

<img src="./img/woe_weight_compare.png">

> 建议的建模一般操作

1. 按照通常步骤选择指标建模。
2. 按照上述操作重新优化指标的分箱截断点。
3. 重新拟合模型参数。

*注意：在实际业务开展过程中，应保存未通过客户的各类信息，用以复原`IGB样本`。*

----

> 📚 Reference

- [1] [风控模型—群体稳定性指标(PSI)深入理解应用](https://zhuanlan.zhihu.com/p/79682292)
- [2] [评分卡模型监控（一）PSI & CSI](https://zhuanlan.zhihu.com/p/94619990)
- [3] [Scorecard-Bundle](https://github.com/Lantianzz/Scorecard-Bundle)
- [4] [从论文分析，告诉你什么叫 “卡方分箱”？](https://toutiao.io/posts/q7i3ki/preview)
- [5] [ChiMerge: Discretization of Numeric Attributes](https://www.aaai.org/Papers/AAAI/1992/AAAI92-019.pdf)

> Editor&Coding: Chensy
----

