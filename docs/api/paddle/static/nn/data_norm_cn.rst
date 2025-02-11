.. _cn_api_fluid_layers_data_norm:

data_norm
-------------------------------


.. py:function:: paddle.static.nn.data_norm(input, act=None, epsilon=1e-05, param_attr=None, data_layout='NCHW', in_place=False, name=None, moving_mean_name=None, moving_variance_name=None, do_model_average_for_mean_and_var=False)




**数据正则化层**

可用作conv2d和fully_connected操作的正则化函数。此层所需的数据格式为以下之一：

1. NHWC [batch, in_height, in_width, in_channels]
2. NCHW [batch, in_channels, in_height, in_width]

:math:`input` 为一个mini-batch上的特征：

.. math::
        \mu_{\beta} &\gets \frac{1}{m} \sum_{i=1}^{m} x_i \qquad &//\
        \ mini-batch\ mean \\
        \sigma_{\beta}^{2} &\gets \frac{1}{m} \sum_{i=1}^{m}(x_i - \
        \mu_{\beta})^2 \qquad &//\ mini-batch\ variance \\
        \hat{x_i} &\gets \frac{x_i - \mu_\beta} {\sqrt{\
        \sigma_{\beta}^{2} + \epsilon}} \qquad &//\ normalize \\
        y_i &\gets \gamma \hat{x_i} + \beta \qquad &//\ scale\ and\ shift

参数
::::::::::::

  - **input** （Tensor） - 输入变量。
  - **act** （string，可选） - 激活函数类型，线性| relu | prelu | ...，默认值为 None。
  - **epsilon** （float，可选） - 指明在计算过程中是否添加较小的值到方差中以防止除零。默认值：1e-05。
  - **param_attr** （ParamAttr，可选） - 参数比例的参数属性。默认值为None。
  - **data_layout** （str，可选） -  指定输入的数据格式，输出的数据格式将与输入保持一致，可以是"NCHW"和"NHWC"。N是批尺寸，C是通道数，H是特征高度，W是特征宽度。默认值："NCHW"。
  - **in_place** （bool，可选） - 是否使data_norm的输入和输出复用同一块内存，默认值为False。
  - **name** (str，可选) - 具体用法请参见 :ref:`api_guide_Name`，一般无需设置，默认值为 None。
  - **moving_mean_name** （string，可选） - 存储全局Mean的moving_mean的名称。默认值为None。
  - **moving_variance_name** （string，可选） - 存储全局Variance的moving_variance的名称。默认值为None。
  - **do_model_average_for_mean_and_var** （bool，可选） - 是否为mean和variance进行模型平均。默认值为False。
  - **slot_dim** （int，可选） -  一个slot的embedding维度，slot用来表征一类特征的集合，在pslib模式下，通常我们通过slot区分特征id，并从参数服务器（pslib）中提取它们的embedding。embedding的第一维是历史上这个embedding展示的次数。如果本op的输入是由这样的embedding连接而来，那么当这个特征id是新的或空的，则正则化结果可能不实际。为了避免这种情况，我们添加了slot_dim来定位并判断这一维是否为零。如果是的话，我们选择跳过正则化。默认值为 -1。
  - **summary_decay_rate** （float，可选） - 更新summary信息时的衰减率。默认值为 0.9999999。
  - **sync_stats** （bool，默认值False） - 在多GPU卡的场景下可以使用，用来同步多卡间的summary信息。
  - **enable_scale_and_shift** (bool，默认值False) - 在分布式全局正则化后是否做像batchnorm一样做scale&shift的操作。

返回
::::::::::::
Tensor，是对输入数据进行正则化后的结果。


代码示例
::::::::::::

COPY-FROM: paddle.static.nn.data_norm