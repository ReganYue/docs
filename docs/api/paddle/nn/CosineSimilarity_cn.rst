.. _cn_api_nn_CosineSimilarity:

CosineSimilarity
-------------------------------
.. py:class:: paddle.nn.CosineSimilarity(axis=1, eps=1e-8)

**CosineSimilarity**

计算x1与x2沿axis维度的余弦相似度。

参数
::::::::::::

  - **axis** (int) - 指定计算的维度，会在该维度上计算余弦相似度，默认值为1。
  - **eps** (float) - 很小的值，防止计算时分母为0，默认值为1e-8。

返回
::::::::::::
无

代码示例
::::::::::::

COPY-FROM: paddle.nn.CosineSimilarity