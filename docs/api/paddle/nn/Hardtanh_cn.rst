.. _cn_api_nn_Hardtanh:

Hardtanh
-------------------------------
.. py:class:: paddle.nn.Hardtanh(min=-1.0, max=1.0, name=None)

Hardtanh激活层（Hardtanh Activation Operator）。计算公式如下：

.. math::

    Hardtanh(x)=
        \left\{
        \begin{aligned}
        &max, & & if \ x > max \\
        &min, & & if \ x < min \\
        &x, & & if \ others
        \end{aligned}
        \right.

其中，:math:`x` 为输入的 Tensor

参数
::::::::::
    - min (float，可选) - Hardtanh激活计算公式中的min值。默认值为-1。
    - max (float，可选) - Hardtanh激活计算公式中的max值。默认值为1。
    - **name** (str，可选) - 具体用法请参见 :ref:`api_guide_Name`，一般无需设置，默认值为 None。

形状：
::::::::::
    - input：任意形状的Tensor。
    - output：和input具有相同形状的Tensor。

代码示例
:::::::::

COPY-FROM: paddle.nn.Hardtanh