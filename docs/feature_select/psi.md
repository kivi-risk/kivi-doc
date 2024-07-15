# PSI

> PSI (Population Stability Index)

## 理论分析

- PSI 反映了验证样本在各分数段的分布与建模样本分布的稳定性。在建模中，我们常用来筛选特征变量、评估模型稳定性。
- 预期分布: 在建模时通常以训练样本（In the Sample, INS）作为预期分布，
- 实际分布: 验证样本通常作为实际分布; 包括样本外（Out of Sample，OOS）和跨时间样本（Out of Time，OOT）。
- 业务含义：PSI数值越小，两个分布之间的差异就越小，代表越稳定。

|PSI 范围|稳定性|建议事项|
|:----:|:----:|:----:|
|0 - 0.1|好| 没有变化或很少变化|
|0.1 - 0.25|略不稳定|有变化，继续监控后续变化|
|大于 0.25|不稳定|发生很大变化，进行特征项分析|

$$
psi = \sum^{n}_{i=1}(Actucal\\% - Expected\\%) \times ln(\frac{Actucal\\%}{Expected\\%})
$$

**虽然PSI可以用来衡量分数分布的变化，但是不能反映分数分布变化的走向（整体往高分段偏移还是往低分段偏移）**

> 相对熵散度 KL(Kullback-Leibler divergence)

- 相对熵可以衡量两个随机分布之间的"距离“。
  - 1）当两个随机分布相同时，它们的相对熵为零；当两个随机分布的差别增大时，它们的相对熵也会增大。
  - 2）注意⚠️：相对熵是一个从信息论角度量化距离的指标，与数学概念上的距离有所差异。数学上的距离需要满足：非负性、对称性、同一性、传递性等；而相对熵不满足对称性。

$$
KL(P||Q) = - \sum_{x \in X} P(X) log\frac{1}{P(X)} + \sum_{x \in X} P(x) log\frac{1}{Q(x)}\\ 
$$
$$
KL(P||Q) = \sum_{x \in X} P(X) log\frac{P(x)}{Q(X)}
$$

> KL 与 PSI

$$
psi = \sum_{i=1}^{n}(A_i - E_i) \times ln\frac{A_i}{E_i}
$$
$$
psi = \sum_{i=1}^{n}A_i \times ln\frac{A_i}{E_i} + \sum^{n}_{i=1}E_i \times ln\frac{E_i}{A_i}
$$
$$
psi = KL(A||E) + KL(E||A)
$$

## 实现示例

> 参数

- `:param expected:` 期望数据。
- `:param actual:` 实际数据。
- `:param data_type:` 数据类型，continue 或 category。
- `:param bins:` 分箱数目。
- `:param cut_type:` 分箱类型 distance 或 frequency。
- `:param cutpoint:` 分箱截断点。

> 示例

```python
psi = PSI()
df_psi = psi.fit(expected, actual, data_type='continue', bins=10, cut_type='distance')
```

> 结果说明

- `index`: `PSI` 分箱贡献。
- `psi`: 指标`PSI`值。

---

- [1] [风控模型—群体稳定性指标(PSI)深入理解应用](https://zhuanlan.zhihu.com/p/79682292)
- [2] [评分卡模型监控（一）PSI & CSI](https://zhuanlan.zhihu.com/p/94619990)
