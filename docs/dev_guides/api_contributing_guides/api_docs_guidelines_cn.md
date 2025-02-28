# 飞桨API文档书写规范

1. **至关重要:** **API文档对该API的描述，一定要与API的行为保持一致。中英文文档的内容要严格一致。**
2. **API文档的字段:** API名称、API功能描述、API参数、API返回、API代码示例、API属性(class)、API方法(methods)等。是否写API抛出异常的情况，不做强制要求。
3. **API功能描述:** 请注意，看文档的用户没有和开发同学一样的知识背景。因此，请提示用户在什么场景下使用该API。请使用深度学习领域通用的词汇和说法。([深度学习常用术语表](https://github.com/PaddlePaddle/FluidDoc/wiki/%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E5%B8%B8%E7%94%A8%E6%9C%AF%E8%AF%AD%E8%A1%A8))。
4. **API参数:** 写清楚对输入参数的要求，写清楚在不同情况下的行为区别（例默认值时的行为）。同类性质的参数（如：输入tensor `x`，每个API中的`name` 参数），可以直接从这里copy内容：**[常用文档写法](https://github.com/PaddlePaddle/FluidDoc/blob/develop/doc/templates/common_docs.py)**。
5. **API代码示例:** 中英文文档当中的代码示例完全一致(means identical, comments可不用翻译)。代码示例使用2.0版本中的API，可运行。尽量不用random输入，注释形式给出输出值。
6. **其他:** 2.0 中的API，对于``Variable、LodTensor、Tensor``，统一使用``Tensor``。`to_variable`也统一改为`to_tensor`。
7. 构造输入数据时，尽量使用paddle提供的API，如:`paddle.zeros, paddle.ones, paddle.full, paddle.arange, paddle.rand, paddle.randn, paddle.randint, paddle.normal, paddle.uniform`。
8. 对于`Linear`, `Conv2D`, `L1Loss` 这些class形式的API，需要写清楚当这个`callable`被调用时的输入输出的形状。（i,e.`forward`函数的参数）。位置放在现在的Parameters/参数这个block后面，具体为：

中文时：

    形状:
          - input: 形状为（批大小, 通道数，高度，宽度），即，HCHW格式的4-D Tensor。
          - output: 形状为（批大小, 卷积核个数，输出图像的高度，输出图像的高度）的4-D Tensor。

英文时：

    Shape:
          - input: 4-D tensor with shape: (batch, num_channels, height, width), i.e.: HCHW.
          - output: 4-D tensor with shape: (batch, num_filters, new_height, new_width).


## 典型案例
[`paddle.concat`](https://github.com/PaddlePaddle/Paddle/blob/develop/python/paddle/tensor/manipulation.py#L54-L110)
[`paddle.split`](https://github.com/PaddlePaddle/Paddle/blob/develop/python/paddle/tensor/manipulation.py#L369-L425)
[`paddle.squeeze`](https://github.com/PaddlePaddle/Paddle/blob/develop/python/paddle/tensor/manipulation.py#L430-L496)
[`paddle.full_like`](https://github.com/PaddlePaddle/Paddle/blob/develop/python/paddle/tensor/creation.py#L58-L92)
[`paddle.ones`](https://github.com/PaddlePaddle/Paddle/blob/develop/python/paddle/tensor/creation.py#L122-L164)
[`paddle.ones_like`](https://github.com/PaddlePaddle/Paddle/blob/develop/python/paddle/tensor/creation.py#L170-L209)


## 英文模板

    def add(x, y, name=None):
        """

        Add two tensors element-wise. The equation is:

        .. math::
            out = x + y

        **Note**:
        ``paddle.add`` supports broadcasting. If you want know more about broadcasting, please refer to :ref:`user_guide_broadcasting` .

        Args:
            x (Tensor): the input tensor, it's data type should be float32, float64, int32, int64.
            y (Tensor): the input tensor, it's data type should be float32, float64, int32, int64.
            name (str, optional): For details, please refer to :ref:`api_guide_Name`. Generally, no setting is required. Default: None.

        Returns:
            N-D Tensor. A location into which the result is stored. It’s dimension equals with $x$.

        Examples:
            .. code-block:: python

                import paddle
                import numpy as np

                paddle.enable_imperative()
                np_x = np.array([2, 3, 4]).astype('float64')
                np_y = np.array([1, 5, 2]).astype('float64')
                x = paddle.imperative.to_variable(np_x)
                y = paddle.imperative.to_variable(np_y)

                z = paddle.add(x, y)
                np_z = z.numpy()
                # [3., 8., 6. ]

                z = paddle.add(x, y, alpha=10)
                np_z = z.numpy()
                # [12., 53., 24. ]
        """

## 中文模板

    .. _cn_api_tensor_add:
    add
    -------------------------------

    .. py:function:: paddle.add(x, y, name=None)

    逐元素相加算子，输入 ``x`` 与输入 ``y`` 逐元素相加，并将各个位置的输出元素保存到返回结果中。计算公式为：

    .. math::
        out = x + y

    .. note::
       ``paddle.add`` 遵守broadcasting，如您想了解更多，请参见 :ref:`cn_user_guide_broadcasting` 。
    ..
      说明：以上为API描述部分，只需要尽可能简单的描述出API的功能作用即可，要让用户能快速看懂。这个case可以拆解为3个部分，功能作用 + 计算公式 + 注解部分；

    参数
    :::::::::
        - **x** (Tensor) - 输入的Tensor，数据类型为：float32、float64、int32、int64。
        - **y** (Tensor) - 输入的Tensor，数据类型为：float32、float64、int32、int64。
        - **name** (str，可选) - 具体用法请参见 :ref:`api_guide_Name`，一般无需设置，默认值为 None。
    ..
      说明：API参数可优先copy常用文档写法中的参数，参数的描述要准确，还要重点描述参数的功能作用及使用场景。

    返回
    :::::::::
    ``Tensor``，维度和数据类型都与 ``x`` 相同，存储运算后的结果。
    ..
      返回为 返回类型 + 描述的格式即可。

    代码示例
    ::::::::::

    .. code-block:: python

        import paddle
        import numpy as np

        paddle.enable_imperative()
        np_x = np.array([2, 3, 4]).astype('float64')
        np_y = np.array([1, 5, 2]).astype('float64')
        x = paddle.imperative.to_variable(np_x)
        y = paddle.imperative.to_variable(np_y)

        z = paddle.add(x, y)
        np_z = z.numpy()
        # [3., 8., 6. ]

        z = paddle.add(x, y, alpha=10)
        np_z = z.numpy()
        # [12., 53., 24. ]

    ..
      尽量不使用random的输入；
      优先使用动态图，在一个代码示例中给出多个使用场景；

## API文档各模块写作说明

### API 名称
API名称直接写API的名字即可，不需要将全路径写全；

**如 paddle.add **

    add
    ---------

### API 声明
API的声明部分，要给出API的声明信息；

**function: 如 paddle.add**

    .. py:function:: paddle.add(x, y, name=None)

   **class: 如 paddle.nn.Conv2d**

    .. py:class:: paddle.nn.Conv2d(num_channels, num_filters, filter_size, padding=0, stride=1, dilation=1, groups=None, param_attr=None, bias_attr=None, use_cudnn=True, act=None, name=None, data_format="NCHW", dtype="float32")

### API 功能描述
API功能描述部分只需要尽可能简单的描述出API的功能作用即可，要让用户能快速看懂。如``paddle.add[https://www.paddlepaddle.org.cn/documentation/docs/zh/api/paddle/tensor/math/add_cn.html#add]``:

可以拆解为3个部分，功能作用 + 计算公式 + 注解部分，其中：

 - 功能作用：描述该API文档的功能作用；**由于用户没有对应的背景**，所以需要补充必要的细节，比如是不是element_wise的，如 ``paddle.add``：

        逐元素相加算子，输入 ``x`` 与输入 ``y`` 逐元素相加，并将各个位置的输出元素保存到返回结果中。

 - 计算公式：给出该API的计算公式，由于公式中每个变量都对应API的参数，所以不需要做额外的说明，如 ``paddle.add``:

        计算公式为：

        .. math::
            out = x + y


 - 注解部分：加入API有需要特殊说明的部分，可以在注解部分给出，比如：该API与其他API功能相似，需要给出该API与另一个API的使用上的区别。

**注意事项：**
1. 写作API文档中，请使用深度学习领域通用的词汇和说法。([深度学习常用术语表](https://github.com/PaddlePaddle/FluidDoc/wiki/%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E5%B8%B8%E7%94%A8%E6%9C%AF%E8%AF%AD%E8%A1%A8))。
2. 文档中的**前后说明要一致**，比如维度的说明，统一使用 4-D Tensor的格式，不确定的写“多维”，示例如下：
![图片](https://agroup-bos-bj.cdn.bcebos.com/bj-836a496baa91882d77fde5ad1259a098d408afce)
3. 文档相互引用的方式：[如何让文档相互引用](https://github.com/PaddlePaddle/FluidDoc/wiki/%E9%A3%9E%E6%A1%A8%E6%96%87%E6%A1%A3%E7%9B%B8%E4%BA%92%E5%BC%95%E7%94%A8)
4. 功能描述中涉及到的专有数据结构如``Tensor``、``LoDTensor``或``Variable``，中英文都统一使用``Tensor``无需翻译。
5. 如果涉及到一些通用的知识，如broadcasting，可以以Note的方式写出来，示例如下：

中文：

    .. note::
        ``paddle.add`` 遵守broadcasting，如您想了解更多，请参见 :ref:`cn_user_guide_broadcasting`。
英文：

    **Note**:
        ``paddle.add`` supports broadcasting. If you want know more about broadcasting, please refer to :ref:`user_guide_broadcasting` .


**总结：paddle.add的描述如下**

    逐元素相加算子，输入 ``x`` 与输入 ``y`` 逐元素相加，并将各个位置的输出元素保存到返回结果中。计算公式为：

    .. math::
        out = x + y
    .. note::
        ``paddle.add`` 遵守broadcasting，如您想了解更多，请参见 :ref:`cn_user_guide_broadcasting` 。


### API 参数（重要）
**注意:** 一些通用的参数说明，直接copy[**常用文档写法**](https://github.com/PaddlePaddle/FluidDoc/blob/develop/doc/templates/common_docs.py 'common_args')中的描述即可。


API参数部分，要解释清楚每个参数的意义和使用场景。需要注意以下两点：

**1、**对于有**默认值**的参数，至少要讲清楚在默认值下的逻辑，而不仅仅是介绍这个参数是什么以及默认值是什么；

如``stop_gradient``的对比，要添加默认值为True的行为，即**表示停止计算梯度** 。

    stop_gradient (bool，可选) - 提示是否应该停止计算梯度，默认值为False。
    # wrong

    stop_gradient (bool，可选) - 提示是否应该停止计算梯度，默认值为True，表示停止计算梯度。
    # right

或者如``return_numpy``的对比：

    return_numpy (bool) – 该变量表示是否将fetched tensor转换为numpy。默认为：True。
    # wrong

    return_numpy (bool) – 该参数表示是否将返回的计算结果（fetch list中指定的变量）转化为numpy；如果为False，则每个变量返回的类型为Tensor，否则返回变量的类型为numpy.ndarray。默认为：True。
    # right

可以看出，第二行的return_numpy的描述更为清晰，分别描述了True和False的两种情况。而第一行的说明过于简单。

**2、** 在讲清楚每个API参数是什么的同时，还需要描述清楚每个参数的具体作用是什么。如再如``feeded_var_names``：

    feeded_var_names (list[str]) – 字符串列表，包含着Inference Program预测时所需提供数据的所有变量名称（即所有输入变量的名称）。
    target_vars (list[Variable]) – Variable （详见 基础概念 ）类型列表，包含着模型的所有输出变量。通过这些输出变量即可得到模型的预测结果。
用户看了描述以后，完全不知道这两个参数可以用来做**网络裁剪**，所以需要将一些参数的功能作用描述出来。

**总结：paddle.add：**

    参数
    :::::::::
        - x (Tensor) - 输入的Tensor，数据类型为：float32、float64、int32、int64。
        - y (Tensor) - 输入的Tensor，数据类型为：float32、float64、int32、int64。
        - name (str，可选) - 具体用法请参见 :ref:`api_guide_Name`，一般无需设置，默认值为 None。


### API 返回
 先描述API 返回值的类型，然后描述API的返回值及其含义。

**如 paddle.add**

    返回
    :::::::::

    ``Tensor``，维度和数据类型都与 ``x`` 相同，存储运算后的结果。

### API 抛出异常
API抛出异常部分，不做强制要求，可以在一些特殊的场景下给出抛出异常的信息，如：

**场景1：API使用有先后依赖关系**
如：[paddle.fluid.layers.DynamicRNN.update_memory()](https://www.paddlepaddle.org.cn/documentation/docs/zh/api_cn/layers_cn/DynamicRNN_cn.html#update-memory-ex-mem-new-mem 'update_memory')框中的部分:
![图片](https://agroup-bos-bj.cdn.bcebos.com/bj-2a1cbab0eb4c76208a5f1e0a0cc6a19bb0d85f1a)

**场景2：多个参数相互影响**
如[paddle.layers.pool2d](https://www.paddlepaddle.org.cn/documentation/docs/zh/api_cn/layers_cn/pool2d_cn.html#id4 'pool2d')框出的部分:
![图片](https://agroup-bos-bj.cdn.bcebos.com/bj-ce7f6e6f0bc3ccae564b47ff47007f4d518204a0)

**场景3：该API的使用场景有特殊限制**
如[paddle.fluid.layers.DynamicRNN.step_input()](https://www.paddlepaddle.org.cn/documentation/docs/zh/api_cn/layers_cn/DynamicRNN_cn.html#step-input-x-level-0 'step_input')框出的部分：
![图片](https://agroup-bos-bj.cdn.bcebos.com/bj-5fef53592710f7638d0902b2a816570d8ac6beea)

### API 代码示例（重要）
代码示例是API文档的核心部分之一，毕竟talk is cheap，show me the code。所以，在API代码示例中，应该对前文描述的API使用中的各种场景，尽可能的在一个示例中给出，并用注释给出对应的结果。
如

**注意事项**

 - 示例代码需要与当前版本及推荐用法保持一致：**develop分支下fluid namespace 以外的API，不能再有fluid关键字，只需要提供动态图的示例代码。**
 - 尽量不用random输入，需要以注释形式给出输出值。
 - 中英文示例代码，不做任何翻译，保持相同（means identical）。
 - 原则上，所有提供的API都需要提供示例代码，对于``class member methods, abstract API, callback``，等情况，可以在提交PR时说明相应的使用方法的文档的位置或文档计划后，通过白名单审核机制通过CI检查。
 - 对于仅为GPU环境提供的API，当该示例代码在CPU上运行时，运行后给出含有”Not compiled with CUDA”的错误提示，也可认为该API行为正确。

英文API代码示例格式规范如下：

    def api():
      """
      Examples:
        .. code-block:: python

            示例代码位置

        .. code-block:: python

            示例代码位置 (存在多个示例代码)
      """

英文格式如 ``paddle.multiply``：

    def multiply(x, y, axis=-1, name=None):
      """
      Examples:
        .. code-block:: python

                import paddle

                x = paddle.to_tensor([[1, 2], [3, 4]])
                y = paddle.to_tensor([[5, 6], [7, 8]])
                res = paddle.multiply(x, y)
                print(res) # [[5, 12], [21, 32]]

                x = paddle.to_tensor([[[1, 2, 3], [1, 2, 3]]])
                y = paddle.to_tensor([2])
                res = paddle.multiply(x, y)
                print(res) # [[2, 4, 6], [2, 4, 6]]]

中文格式如``paddle.add``：

    代码示例
    ::::::::::

    ..  code-block:: python

            import paddle
            x = paddle.to_tensor([2, 3, 4], 'float64')
            y = paddle.to_tensor([1, 5, 2], 'float64')
            z = paddle.add(x, y)
            print(z)  # [3., 8., 6. ]

### API 属性
API的属性用来描述API所包含的属性。如果API有属性，每个属性需要分为四个部分描述：

 - 名称：属性名称直接写属性的名字即可，不需要将全路径写全；
 - 注意：列举出使用该属性时应注意的一些问题，如果没有可以不填；如不同的版本、是否是只读属性、使用的一些tricks等等，如``Program``的``rand_seed``：

        .. note::
        必须在相关OP被添加之前设置。

 - 描述：API功能描述部分要求一致；
 - 返回：API返回部分要求一致；
 - 代码示例：与API代码示例部分要求一致；

**总结：paddle.Program.random_seed**

    random_seed
    ''''''''''''

    ..note:
    必须在相关OP被添加之前设置。

    程序中随机运算符的默认随机种子。0意味着随机生成随机种子。

    **返回**
    int64，返回该Program中当前正在使用的random seed。。

    **代码示例**

    .. code-block:: python

        import paddle
        prog = paddle.default_main_program()
        random_seed = prog.random_seed
        x_var = paddle.data(name="X", shape=[3,3], dtype="float32")
        print(random_seed)
        # 0
        # default random seed is 0
        # Here we need to set random seed before we use dropout
        prog.random_seed = 1
        z_var = paddle.nn.functional.dropout(x_var, 0.7)
        print(prog.random_seed)
        # 1
        # the random seed is change to 1


### API 方法
API的方法用来描述API所包含的方法，一些类的API会有这个内容，没有方法的API可以不写此模块。如果有，每个方法需要分为六个部分描述：

 - 名称：方法名称直接写方法的名字即可，不需要将全路径写全；
 - 声明：与API声明的要求一致。
 - 参数：与API参数的要求一致。
 - 描述：与API功能描述的要求一致。
 - 返回：与API返回的要求一致。。
 - 代码示例：与API代码示例的要求一致。

**总结：paddle.Program.parse_from_string**

    parse_from_string
    ''''''''''''

    .. py:function:: paddle.Program.parse_from_string(binary_str_type)
    通过对 protobuf 的反序列化，转换成``Program``

    **参数**
    binary_str_type (**str**) – protobuf 二进制字符串

    **返回**
    ``Program``，反序列化后的 ``Program``

    **代码示例**

    .. code-block:: python

        import paddle

        startup_prog = paddle.Program()
        main_prog = paddle.Program()
        with paddle.program_guard(startup_prog, main_prog):
            x = paddle.data(name='X', shape=[1000, 784], dtype='float32')
            y = paddle.data(name='Y', shape=[784, 100], dtype='float32')
            z = paddle.mul(x=x, y=y)

            binary_str = paddle.default_main_program().desc.serialize_to_string()
            prog_restored = paddle.default_main_program().parse_from_string(binary_str)

            print(paddle.default_main_program())
            print(prog_restored)

            # The two Programs printed here should be same

### 注解
注解部分描述一些用户使用该API时需要额外注意的一些注意事项，可以出现在任意需要提示用户的地方；可以是当前版本存在的一些问题，如例1；也可以是该API使用上的一些注意事项，如例2；

**例1 paddle.fluid.cuda.cuda_places:**
中文：

    .. note::
    多卡任务请先使用 FLAGS_selected_gpus 环境变量设置可见的GPU设备，下个版本将会修正 CUDA_VISIBLE_DEVICES 环境变量无效的问题。

英文：

    **Note**:
    For multi-card tasks, please use `FLAGS_selected_gpus` environment variable to set the visible GPU device.
    The next version will fix the problem with `CUDA_VISIBLE_DEVICES` environment variable.

**例2 paddle.sqrt**
中文：

    .. note::
    请确保输入中的数值是非负数。

英文：

    **Note**:
    input value must be greater than or equal to zero.

### 警告
警告部分需要慎重使用，一般是不推荐用户使用的API方法（例1），或者是已经有计划要废弃的API（例2），需要用警告来说明。

**例1 paddle.fluid.layers.DynamicRNN**
中文：

    .. warning::
    目前不支持在DynamicRNN的 block 中任何层上配置 is_sparse = True 。
英文：

    Warning:
        Currently it is not supported to set :code:`is_sparse = True` of any
        layers defined within DynamicRNN's :code:`block` function.

**例2 paddle.fluid.clip.set_gradient_clip**
中文：

    .. warning::
    此API对位置使用的要求较高，其必须位于组建网络之后， ``minimize`` 之前，因此在未来版本中可能被删除，故不推荐使用。推荐在 ``optimizer`` 初始化时设置梯度裁剪。 有三种裁剪策略： ``GradientClipByGlobalNorm`` 、 ``GradientClipByNorm`` 、 ``GradientClipByValue``。 如果在 ``optimizer`` 中设置过梯度裁剪，又使用了 ``set_gradient_clip`` ，``set_gradient_clip`` 将不会生效。

英文：

    Warning:  
        This API must be used after building network, and before ``minimize`` ,
        and it may be removed in future releases, so it is not recommended.
        It is recommended to set ``grad_clip`` when initializing the ``optimizer`` ,
        this is a better method to clip gradient. There are three clipping strategies:
         :ref:`api_fluid_clip_GradientClipByGlobalNorm` , :ref:`api_fluid_clip_GradientClipByNorm` ,
         :ref:`api_fluid_clip_GradientClipByValue` .

## 文档测试

 - 中文文档、英文文档齐全，内容一一对应。
 - 文档清晰可读，易于用户使用
 - 给出易于理解的api介绍，文字描述，公式描述。
 - 参数命名通俗易懂无歧义，明确给出传参类型，对参数含义以及使用方法进行详细说明，对返回值进行详细说明。
 - 异常类型和含义进行详细说明。
 - 示例代码需要做到复制粘贴即可运行，并且需要明确给出预期运行结果（如果可以）。
 - 阅读无障碍：无错别字、上下文连贯、内容清晰易懂、链接可正常跳转、图片公式显示正常。
