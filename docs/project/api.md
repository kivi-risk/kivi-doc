# API

*注：该页面综合汇总了整个`page`的基本内容。（现在还不全）*

## 1. 数据分析

1. [pyspark 相关表操作](./SparkSupport/SparkSupport): 表的增删改查，获取`spark session`等。
1. [pyspark 列操作](./descriptive_statistics/data_analysis?id=_1-pyspark中的列操作): `pyspark dataframe` 的各类列运算，列操作，列混合操作。
1. [分布分析](./descriptive_statistics/data_analysis?id=_2-分布分析): 分位数计算、排名计算、分布统计量。
1. [空值分析](./descriptive_statistics/data_analysis?id=_3-空值分析): `Nan`与`Null`的区别，空值的一般注意事项，时间数据操作过程中产生空值的原因。
1. [表转置操作](./descriptive_statistics/data_analysis?id=_4-表转置操作): 长表宽表的互相转换。

## 2. 样本的分析与建设

1. [样本建设的概念](./score_card/sample): 样本建设的基本概念，需要注意的事项，样本的存储等。
1. [](): 。
1. [](): 。
1. [](): 。

## 3. 分箱分析 `WOE`

```python
from kivi import WOE
```

### 3.1 `WOE` 分箱

1. [`WOE.Distance`](./woe_iv/woe_iv_api?id=_11-等距分箱): 等距分箱
1. [`WOE.Frequency`](./woe_iv/woe_iv_api?id=_12-等频分箱): 等频分箱
1. [`WOE.Category`](./woe_iv/woe_iv_api?id=_13-类别型分箱): 类别型分箱
1. [`WOE.KmeansBins`](./woe_iv/woe_iv_api?id=_14-Kmeans分箱): `Kmeans`分箱
1. [`WOE.Pearson`](./woe_iv/woe_iv_api?id=_21-Pearson-分箱): Pearson 分箱
1. [`WOE.TreeBins`](./woe_iv/woe_iv_api?id=_22-决策树分箱): 决策树分箱
1. [`WOE.KSMerge`](./woe_iv/woe_iv_api?id=_23-Best-KS分箱): `Best-KS`分箱
1. [`WOE.Chi2Merge`](./woe_iv/woe_iv_api?id=_24-卡方分箱): 卡方分箱
1. [`WOE.CutOffPoint`](./woe_iv/woe_iv_api?id=_15-手动指定分箱模式): 手动指定分箱模式

### 3.2 批量分箱及其他辅助方法

1. [`WOE.WOEBatch`](./woe_iv/woe_embedding?id=_1-woe-计算批量化): `WOE` 计算批量化
1. [`WOE.WOEBatchWithRebin`](./woe_iv/woe_embedding?id=_2-WOE-计算批量化并进行自动合并分箱): `WOE` 计算批量化并进行自动合并分箱
1. [`WOE.SaveWOEVal`](./woe_iv/woe_embedding?id=_3-Woe-赋分方法): `Woe` 赋分方法
1. [`WOE.TransToWOEVal`](./woe_iv/woe_embedding?id=_4-Woe-Batch-赋分方法): Woe-Batch 赋分方法
1. [`WOE.Rebins`](./woe_iv/woe_embedding?id=_5-手动分箱尝试): 手动分箱尝试
1. [`WOE.AppendRebinWoe`](./woe_iv/woe_embedding?id=_6-合并手动分箱结果): 合并手动分箱结果

## 4. 回归诊断

### 4.1 相关系数

> [`spearmanr`](./feature_select/mutivar?id=_1-相关系数-spearman): 相关系数 `spearman`

### 4.2 共线性诊断

1. [`VIF.vif`](http://localhost:3000/#/feature_select/mutivar?id=_212-vifvif-计算某变量与其他变量的vif): 计算某变量与其他变量的`VIF`
1. [`VIF.StatsTableVIF`](http://localhost:3000/#/feature_select/mutivar?id=_213-vifstatstablevif-依次计算全表vif): 依次计算全表`VIF`
1. [`VIF.LowVIFFeatures`](http://localhost:3000/#/feature_select/mutivar?id=_214-viflowviffeatures-依据vif设定阈值，选择最小vif特征组合): 依据`VIF`设定阈值，选择最小`VIF`特征组合

## 5. 指标与模型的评估

### 5.1 区分效果评估

1. [`LIFT`](./model_select/model_eval?id=_1-lift): LIFT，评估指标或者分数的提升度
1. [`lift_compare`](./model_select/model_eval?id=_2-lift_compare): lift_compare，评估指标或者不同数据集分数的lift
1. [`绘制ROC图`](./model_select/model_eval?id=_3-绘制ROC图): 绘制`ROC`图，并标注`AUC`与`KS`
1. [`AUC`和`KS`](./model_select/model_eval?id=_4-AUC-和-KS): 输出预测值与真实值的 `AUC` 和 `KS`
1. [`PR曲线`]()

### 稳定性评估

1. [`PSI`](./feature_select/PSI-CSI?id=群体稳定性指标-psi)
1. [`CSI`](./feature_select/PSI-CSI?id=特征稳定性指标-csi) 

### 评分卡分数转换

> 

-----

> 待`开发/补充`的计划

- 分数的计算。
- 拒绝推断（截断法）。
- 考虑历史指标的入模方式？
