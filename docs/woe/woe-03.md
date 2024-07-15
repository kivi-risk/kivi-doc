# 批量分箱计算与其他补充内容

## WOE分箱批量计算

> 参数：`WOEBatch`

- `:param df:` DataFrame 原始数据集。
- `:param bins[int, dict]:` [int]分箱数量，默认为 5；[dict(var_name: bins)]依据截断点分箱。
- `:param max_bin:` 允许最大分箱数量。
- `:param min_bin:` 允许最小分箱数量。
- `:param rebin:` 是否重新分箱，默认为 `True`。
- `:param woe_type:` 分箱方法，默认为决策树分箱 `TreeBins`。
- `:param woe_columns:` 需要进行分箱的字段，默认为全部字段。
- `:param target_name:` 标签名称，默认为 `target`。
- `:param observe_order:` 保留分箱单调性，默认为 `['单调上升','单调下降']`；即对不满足条件的数据进行重分箱。
- `:param abnormal_vals:` 需要剔除异常值，如[-9099, -4404]
- `:param protect_columns:` 需要剔除分箱的字段, 如不需要进行分箱的字段['uuid', 'target']。
- `:param logger:` 日志记录器，默认为 `None`。
- `:param verbose:` 是否打印分箱日志，默认为 `False`。

> 示例: 批量分箱

```python
from kivi.woe import *
from kivi.datasets import *

df_bank = Dataset.BankData()
df = df_bank.select_dtypes(include=['int64', 'float64']).copy()

batch = WOEBatch(df, woe_type=TreeBins, rebin=False)
df_woe = batch.fit()
```

*输出*

<img src="./img/woe/woe_batch_0.png" width="50%">

<img src="./img/woe/woe_batch_1.png" width="50%">

> 示例: 批量分箱并进行分箱的优化(` rebin=True `)

```python
batch = WOEBatch(df, woe_type=TreeBins, max_bin=5, min_bin=2, rebin=True)
df_woe = batch.fit()
```

*输出*

<img src="./img/woe/woe_batch_2.png" width="50%">

<img src="./img/woe/woe_batch_3.png" width="50%">

*可以看到，上面的过程共进行了4次分箱优化，最大分箱数为5，最小为2，分箱排序均为`单调上升、单调下降`的单调分箱。*

## WOE赋分方法

> 参数：`WOEScore`

- `:param df`: 原始数据
- `:param df_woe`: woe编码后的数据
- `:param id_name`: uuid
- `:param target_name`: 目标变量名称，默认为 target
- `:param dtype`: 数据类型，默认为float
- `:param batch_size`: 批量大小，默认为32
- `:param error`: 错误处理方式，默认为error
- `:param logger`: 日志记录器，默认为None
- `:param verbose`: 是否显示进度条，默认为True

> 示例

```python
import numpy as np
from kivi.woe import *
from kivi.datasets import *

df_bank = Dataset.BankData()
df_bank['uuid'] = np.arange(0, len(df_bank))

batch = WOEBatch(df_bank, verbose=False)
df_woe = batch.woe_batch_with_rebin()

woe_score = WOEScore(df=df_bank, df_woe=df_woe, batch_size=3, verbose=True)
df_score = woe_score.batch_run()
```

*输出*

<img src="./img/woe/woe_batch_4.png" width="50%">

<img src="./img/woe/woe_batch_5.png" width="50%">

## 手动调整分箱

### 手动分箱

在自动分箱满足不了实际需求的情况时，可以考虑手动指定分箱的截断点进行手动分箱操作。一般用于数据分析阶段，反复尝试不同的分箱截断点会有什么样的影响或效果。

> 参数`ManualBinsTool`

- `:param df`: 数据集
- `:param target_name`: 目标变量名
- `:param abnormal_vals`: 异常值
- `:param logger`: 日志
- `:param verbose`: 是否打印日志

> 示例

```python
import numpy as np
from kivi.woe import *
from kivi.datasets import *

df_bank = Dataset.BankData()
print(df_bank.shape)

batch = WOEBatch(df=df_bank, max_bin=5, min_bin=2, rebin=False)
df_woe = batch.fit()
print(df_woe.head(10))

# 手动分箱，bins为手动指定的分箱截断点
rebin_tool = ManualBinsTool(df=df_bank, verbose=True)
df_rebin_woe = rebin_tool.manual_rebin(column="age", bins=[-np.inf, 20, 22, 50, 70, np.inf])
```

*输出*

<center>
<img src="./img/woe/woe_batch_6.png" width="50%">
<h6>自动分箱结果</h6>

<img src="./img/woe/woe_batch_7.png" width="50%">
<h6>手动分箱结果</h6>
</center>

*可以看到，上面的手动分箱结果是按照手工指定的截断点(`[-np.inf, 20, 22, 50, 70, np.inf]`)进行的分箱计算。*

### 合并手动分箱结果

> `ManualBinsTool.append_rebin_woe`方法的作用是将手工分箱结果合并至全量分箱结果`df_woe`中。该过程会使用 `df_rebin` 中的分箱数据完全替换 `df_woe` 中的分箱数据。

> 参数：

- `:param df_woe`: 原始分箱
- `:param df_rebin`: 手工分箱

> 示例：`ManualBinsTool.append_rebin_woe`

```python
# 前置代码与上一小节一致
df_woe_manually = rebin_tool.append_rebin_woe(df_woe=df_woe, df_rebin=df_rebin_woe)
```

---
@2020/06/07 write
@2024/07/08 update
