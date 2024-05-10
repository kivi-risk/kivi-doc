---

> [数据来源](./dataset/dataset.md)

```python
# 引入 kivi- 模块
from kivi import WOE
```

### 1. `WOE` 计算批量化

> 参数：`WOE.WOEBatch`
- `:param df:` DataFrame 原始数据集。
- `:param columns_name:` 需要进行分箱的字段。
- `:param target_name:` 标签名称，默认为 `target`。
- `:param WOEFun:` 分箱方法，默认为决策树分箱 `Frequency`。
- `:param bins[int, dict]:` `[int]`分箱数量，默认为5；`[dict(var_name: bins)]`依据截断点分箱。

> 示例：`WOE.WOEBatch`

```python
df_woe, faulted_cols = WOE.WOEBatch(
    df, columns_name=df.drop('target', axis=1).columns)
```

### 2. `WOE` 计算批量化并进行自动合并分箱

> 参数：`WOE.WOEBatchWithRebin`
- `:param df:` DataFrame 原始数据集。
- `:param target_name:` 标签名称，默认为 `target`。
- `:param WOEFun:` 分箱方法，默认为决策树分箱 `TreeBins`。
- `:param bins_type:` 保留分箱单调性，默认为 `['单调上升','单调下降']`；即对不满足条件的数据进行重分箱。
- `:param max_bin:` 允许最大分箱数量。
- `:param min_bin:` 允许最小分箱数量。
- `:param columns_name:` 需要进行分箱的字段。
- `:param drop_columns:` 需要剔除分箱的字段。

> 示例：`WOE.WOEBatchWithRebin`

```python
# 选择数值型变量
df = df_bank.select_dtypes(include=['int64', 'float64']).copy()
df_woe, fault_cols = WOE.WOEBatchWithRebin(df, drop_columns=['target'])
```

### 3. Woe 赋分方法

> 参数：`WOE.SaveWOEVal`
- `:param df:` DataFrame 原始数据集。
- `:param df_woe:` DataFrame WOE 分箱表。
- `:param columns:` 需要转换的指标名称。
- `:param join_origin:` 保留原始数据中的字段信息，默认为样本 `uid` 以及标签 `target`。
- `:param values:` `['woe', 'score']` 转换的值，默认为 `woe` ;也可以是分数 `score`。
- `:param disp:` 是否展示转换进度信息，默认为 `True`。

> 示例：`WOE.SaveWOEVal`

```python
df_val_woe, df_val_score = WOE.SaveWOEVal(
    df, df_woe, columns=columns, values=['woe', 'score'], disp=False)
```

### 4. Woe-Batch 赋分方法

> 参数：`WOE.TransToWOEVal`
- `:param df:` DataFrame 原始数据集。
- `:param df_woe:` DataFrame WOE 分箱表。
- `:param values:` 转换的值，默认为 `woe` ;也可以是分数 `score`。
- `:param batch:` 每个批次进行指标WOE分数转换的数量，默认为50。

> 示例：`WOE.TransToWOEVal`

```python
df_val = WOE.TransToWOEVal(df, df_woe, values='score', batch=3)
```

### 5. 手动分箱尝试

**一般用于数据分析阶段，反复尝试不同的分箱截断点会有什么样的影响或效果。**

> 参数

- `:param df:` DataFrame 数据。
- `:param col_name:` 需要进行重分箱的指标名称。
- `:param bins:` 分箱的截断点。
- `:param target_name:` 目标变量名称。

> 示例

```python
WOE.Rebins('age', bins=[-np.inf, 50, np.inf], df=df_bank, target_name='target')
```

> 结果

<img src="./img/woe_rebins.png">

### 6. 合并手动分箱结果

**在一些情况下，需要根据业务经验进行手动分箱操作。
该函数作用是合并手动分箱结果 `df_rebin` 至全量分箱结果 `df_woe`。
该过程会使用 `df_rebin` 中的分箱数据完全替换 `df_woe` 中的分箱数据**

> 参数：

- `:param df_woe:` 全量分箱。
- `:param df_rebins:` 补充分箱。

> 示例：`WOE.AppendRebinWoe`

```python
df_woe = WOE.AppendRebinWoe(df_woe, df_rebin)
```

---
