.. _cn_api_nn_Softshrink:

Softshrink
-------------------------------
.. py:class:: paddle.nn.Softshrink(threshold=0.5, name=None)

Softshrink激活层

.. math::

    Softshrink(x)= \begin{cases}
                    x - threshold, \text{if } x > threshold \\
                    x + threshold, \text{if } x < -threshold \\
                    0,  \text{otherwise}
                    \end{cases}

其中，:math:`x` 为输入的 Tensor

参数
::::::::::
    - threshold (float，可选) - Softshrink激活计算公式中的threshold值，必须大于等于零。默认值为0.5。
    - **name** (str，可选) - 具体用法请参见 :ref:`api_guide_Name`，一般无需设置，默认值为 None。

形状：
::::::::::
    - input：任意形状的Tensor。
    - output：和input具有相同形状的Tensor。

代码示例
:::::::::

COPY-FROM: paddle.nn.Softshrink