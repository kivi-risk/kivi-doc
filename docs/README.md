# kivi

*本文档旨在说明风控建模数据、模型的加工计算流程，展示项目代码示例，维护风控建模数据、模型项目的工程拓展，
满足风控建模数据、模型建设过程中的任何环节的API查询。主要包含以下内容：*

- kivi: 算法源码文件
- docs: 算法工程文档
- kivi tutorials: kivi 教程文档

- **数据处理**：包含对原始数据的清洗、回溯、衍生、特征工程等。
- **样本建设**：包含对标签的处理，样本的构建等。
- **模型建设**：包含风控模型、营销模型等各类场景的模型建设。
- **数据输出**：旨在对风控建模中加工后的数据或模型进行规范化再输出，支撑数据、模型的生产应用。

> Installation kivi

```python
pip install kivi
```

```bash
# doc serve
1. docsify serve docs
2. nohup python -m http.server 3334 >>kivi-docs.log 2>&1 &
3. windows: python -m http.server

# kivi-pkg
1. python37 setup.py sdist bdist_wheel
```

> Email: chensy.cao@foxmail.com

> 知乎

- [专栏](https://www.zhihu.com/people/chensy-87/columns)

---

<div style="float:right;">Copyright &copy; 2019 - 2023 Chensy</div>
