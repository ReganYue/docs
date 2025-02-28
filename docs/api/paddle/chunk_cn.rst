.. _cn_api_tensor_cn_chunk:

chunk
-------------------------------

.. py:function:: paddle.chunk(x, chunks, axis=0, name=None)

将输入Tensor分割成多个子Tensor。

参数
:::::::::

       - **x** (Tensor) - 输入变量，数据类型为bool, float16, float32，float64，int32，int64的多维Tensor。
       - **chunks** (int) - ``chunks`` 是一个整数，表示将输入Tensor划分成多少个相同大小的子Tensor。
       - **axis** (int|Tensor，可选) - 整数或者形状为[1]的Tensor，数据类型为int32或int64。表示需要分割的维度。如果 ``axis < 0``，则划分的维度为 ``rank(x) + axis``。默认值为0。
       - **name** (str，可选) - 具体用法请参见 :ref:`api_guide_Name`，一般无需设置，默认值为 None。

返回
::::::::::::
分割后的Tensor列表。


代码示例
::::::::::::

COPY-FROM: paddle.chunk