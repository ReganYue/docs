.. _cn_api_fluid_layers_acosh:

acosh
-------------------------------

.. py:function:: paddle.acosh(x, name=None)




Arccosh函数。

.. math::
    out = acosh(x)

参数
:::::::::
    - x (Tensor) - 输入的Tensor，数据类型为：float32、float64。
    - **name** (str，可选) - 具体用法请参见 :ref:`api_guide_Name`，一般无需设置，默认值为 None。

返回
:::::::::
输出Tensor，与 ``x`` 维度相同、数据类型相同。



代码示例
:::::::::

COPY-FROM: paddle.acosh