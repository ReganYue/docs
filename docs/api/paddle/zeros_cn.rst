.. _cn_api_tensor_zeros:

zeros
-------------------------------

.. py:function:: paddle.zeros(shape, dtype=None, name=None)



创建形状为 ``shape`` 、数据类型为 ``dtype`` 且值全为0的Tensor。

参数
::::::::::::

    - **shape** (tuple|list|Tensor) - 输出Tensor的形状，``shape`` 的数据类型为int32或者int64。
    - **dtype** (np.dtype|str，可选) - 输出Tensor的数据类型，数据类型必须为bool、float16、float32、float64、int32或int64。若为None，数据类型为float32，默认为None。
    - **name** (str，可选) - 具体用法请参见 :ref:`api_guide_Name`，一般无需设置，默认值为 None。

返回
::::::::::::
值全为0的Tensor，数据类型和 ``dtype`` 定义的类型一致。


代码示例
::::::::::::

COPY-FROM: paddle.zeros