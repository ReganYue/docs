.. _cn_api_fluid_ParallelExecutor:

ParallelExecutor
-------------------------------


.. py:class:: paddle.static.ParallelExecutor(use_cuda, loss_name=None, main_program=None, share_vars_from=None, exec_strategy=None, build_strategy=None, num_trainers=1, trainer_id=0, scope=None)




``ParallelExecutor`` 是 ``Executor`` 的一个升级版本，可以支持基于数据并行的多节点模型训练和测试。如果采用数据并行模式，``ParallelExecutor`` 在构造时会将参数分发到不同的节点上，并将输入的 ``Program`` 拷贝到不同的节点，在执行过程中，各个节点独立运行模型，将模型反向计算得到的参数梯度在多个节点之间进行聚合，之后各个节点独立的进行参数的更新。

- 如果使用GPU运行模型，即 ``use_cuda=True``，节点指代GPU， ``ParallelExecutor`` 将自动获取在当前机器上可用的GPU资源，用户也可以通过在环境变量设置可用的GPU资源，例如：希望使用GPU0、GPU1计算，export CUDA_VISIBLEDEVICES=0,1；
- 如果在CPU上进行操作，即 ``use_cuda=False``，节点指代CPU，**注意：此时需要用户在环境变量中手动添加 CPU_NUM，并将该值设置为CPU设备的个数，例如：export CPU_NUM=4，如果没有设置该环境变量，执行器会在环境变量中添加该变量，并将其值设为1**。

参数
::::::::::::

    - **use_cuda** (bool) – 该参数表示是否使用GPU执行。
    - **loss_name** （str） - 该参数为模型最后得到的损失变量的名字。**注意：如果是数据并行模型训练，必须设置loss_name，否则计算结果可能会有问题。** 默认为：None。
    - **main_program** (Program) – 需要被执行的Program。如果未提供该参数，即该参数为None，在该接口内，main_program将被设置为paddle.static.default_main_program()。默认为：None。
    - **share_vars_from** (ParallelExecutor) - 如果设置了share_vars_from，当前的ParallelExecutor将与share_vars_from指定的ParallelExecutor共享参数值。
    需要设置该参数的情况：模型训练过程中需要进行模型测试，并且训练和测试都是采用数据并行模式，那么测试对应的ParallelExecutor在调用with_data_parallel时，需要将share_vars_from设置为训练所对应的ParallelExecutor。
    由于ParallelExecutor只有在第一次执行时才会将参数变量分发到其他设备上，因此share_vars_from指定的ParallelExecutor必须在当前ParallelExecutor之前运行。默认为：None。
    - **exec_strategy** (ExecutionStrategy) -  通过exec_strategy指定执行计算图过程可以调整的选项，例如线程池大小等。关于exec_strategy更多信息，请参阅 ``paddle.static.ExecutionStrategy``。默认为：None。
    - **build_strategy** (BuildStrategy)：通过配置build_strategy，对计算图进行转换和优化，例如：计算图中算子融合、计算图执行过程中开启内存/显存优化等。关于build_strategy更多的信息，请参阅  ``paddle.static.BuildStrategy``。默认为：None。
    - **num_trainers** (int) – 进行GPU分布式训练时需要设置该参数。如果该参数值大于1，NCCL将会通过多层级节点的方式来初始化。每个节点应有相同的GPU数目。默认为：1。
    - **trainer_id** (int) –  进行GPU分布式训练时需要设置该参数。该参数必须与num_trainers参数同时使用。trainer_id指明是当前所在节点的 “rank”（层级）。trainer_id从0开始计数。默认为：0。
    - **scope** (Scope) – 指定执行Program所在的作用域。默认为：paddle.static.global_scope()。

返回
::::::::::::
初始化后的 ``ParallelExecutor`` 对象。

.. note::
     1. 如果只是进行多卡测试，不需要设置loss_name以及share_vars_from。
     2. 如果程序中既有模型训练又有模型测试，则构建模型测试所对应的ParallelExecutor时必须设置share_vars_from，否则模型测试和模型训练所使用的参数是不一致。

代码示例
::::::::::::

.. code-block:: python

    import paddle
    import numpy
    import os

    use_cuda = True
    paddle.enable_static()
    place = paddle.CUDAPlace(0) if use_cuda else paddle.CPUPlace() 
    
    # 注意：如果你使用CPU运行程序，需要具体设置CPU_NUM，
    # 否则PaddlePaddle会把逻辑核的所有数目设为CPU_NUM，
    # 在这种情况下，输入的batch size应大于CPU_NUM，
    # 否则程序会异常中断。
    if not use_cuda:
        os.environ['CPU_NUM'] = str(2)

    exe = paddle.static.Executor(place)

    train_program = paddle.static.Program()
    startup_program = paddle.static.Program()
    with paddle.static.program_guard(train_program, startup_program):
        data = paddle.static.data(name='X', shape=[None, 1], dtype='float32')
        hidden = paddle.static.nn.fc(data, 10)
        loss = paddle.mean(hidden)
        test_program = paddle.static.default_main_program().clone(for_test=True)
        paddle.optimizer.SGD(learning_rate=0.01).minimize(loss)

    exe.run(startup_program)

    train_exe = paddle.static.ParallelExecutor(use_cuda=use_cuda,
                                               main_program=train_program,
                                               loss_name=loss.name) 
    # 注意：如果此处不设置share_vars_from=train_exe，测试过程中用的参数与训练使用的参数是不一致
    test_exe = paddle.static.ParallelExecutor(use_cuda=use_cuda,
                                              main_program=test_program,
                                              share_vars_from=train_exe)

    x = numpy.random.random(size=(10, 1)).astype('float32')
    loss_data, = train_exe.run(feed={"X": x},
                               fetch_list=[loss.name])

    loss_data, = test_exe.run(feed={"X": x},
                              fetch_list=[loss.name])

方法
::::::::::::
run(fetch_list, feed=None, feed_dict=None, return_numpy=True)
'''''''''

该接口用于运行当前模型，需要注意的是，执行器会执行Program中的所有算子，而不会根据fetch_list对Program中的算子进行裁剪。

**参数**

    - **fetch_list** (list) – 该变量表示模型运行之后需要返回的变量。
    - **feed** (list|dict) – 该变量表示模型的输入变量。如果该参数类型为 ``dict`` ，feed中的数据将会被分割(split)并分送给多个设备（CPU/GPU）；如果该参数类型为 ``list``，则列表中的各个元素都会直接分别被拷贝到各设备中。默认为：None。
    - **feed_dict** – 该参数已经停止使用。默认为：None。
    - **return_numpy** (bool) – 该变量表示是否将fetched tensor转换为numpy。默认为：True。

**返回**

返回fetch_list中指定的变量值。

.. note::
     1. 如果feed参数为dict类型，输入数据将被均匀分配到不同的卡上，例如：使用2块GPU训练，输入样本数为3，即[0, 1, 2]，经过拆分之后，GPU0上的样本数为1，即[0]，GPU1上的样本数为2，即[1, 2]。如果样本数少于设备数，程序会报错，因此运行模型时，应额外注意数据集的最后一个batch的样本数是否少于当前可用的CPU核数或GPU卡数，如果是少于，建议丢弃该batch。
     2. 如果可用的CPU核数或GPU卡数大于1，则fetch出来的结果为不同设备上的相同变量值（fetch_list中的变量）在第0维拼接在一起。

**代码示例**

.. code-block:: python
    import paddle
    import numpy
    import os

    use_cuda = True
    paddle.enable_static()
    place = paddle.CUDAPlace(0) if use_cuda else paddle.CPUPlace()
     
    # 注意：如果你使用CPU运行程序，需要具体设置CPU_NUM，
    # 否则PaddlePaddle会把逻辑核的所有数目设为CPU_NUM，
    # 在这种情况下，输入的batch size应大于CPU_NUM，
    # 否则程序会异常中断。
    if not use_cuda:
        os.environ['CPU_NUM'] = str(2)

    exe = paddle.static.Executor(place)

    train_program = paddle.static.Program()
    startup_program = paddle.static.Program()
    with paddle.static.program_guard(train_program, startup_program):
        data = paddle.static.data(name='X', shape=[None, 1], dtype='float32')
        hidden = paddle.static.nn.fc(data, 10)
        loss = paddle.mean(hidden)
        paddle.optimizer.SGD(learning_rate=0.01).minimize(loss)

    exe.run(startup_program)

    train_exe = paddle.static.ParallelExecutor(use_cuda=use_cuda,
                                               main_program=train_program,
                                               loss_name=loss.name)
    # 如果feed参数是dict类型：
    # 图像会被split到设备中。假设有两个设备，那么每个设备将会处理形为 (5, 1)的图像
    x = numpy.random.random(size=(10, 1)).astype('float32')
    loss_data, = train_exe.run(feed={"X": x},
                               fetch_list=[loss.name])

    # 如果feed参数是list类型：
    # 各设备挨个处理列表中的每个元素
    # 第一个设备处理形为 (10, 1) 的图像
    # 第二个设备处理形为 (9, 1) 的图像
    #
    # 使用 exe.device_count 得到设备数目
    x2 = numpy.random.random(size=(9, 1)).astype('float32')
    loss_data, = train_exe.run(feed=[{"X": x}, {"X": x2}],
                               fetch_list=[loss.name])

drop_local_exe_scopes()
'''''''''

立即清除scope中的临时变量。模型运行过程中，生成的中间临时变量将被放到local execution scope中，为了避免对临时变量频繁的申请与释放，ParallelExecutor中采取的策略是间隔若干次迭代之后清理一次临时变量。ParallelExecutor在ExecutionStrategy中提供了num_iteration_per_drop_scope选项，该选项表示间隔多少次迭代之后清理一次临时变量。如果num_iteration_per_drop_scope值为100，但是希望在迭代50次之后清理一次临时变量，可以通过手动调用该接口。

**返回**

无。

**代码示例**

.. code-block:: python

    import paddle
    import numpy
    import os
    
    use_cuda = True
    # 注意：如果你使用CPU运行程序，需要具体设置CPU_NUM，
    # 否则PaddlePaddle会把逻辑核的所有数目设为CPU_NUM，
    # 在这种情况下，输入的batch size应大于CPU_NUM，
    # 否则程序会异常中断。
    if not use_cuda:
        os.environ['CPU_NUM'] = str(2)

    paddle.enable_static()
    train_program = paddle.static.Program()
    startup_program = paddle.static.Program()
    with paddle.static.program_guard(train_program, startup_program):
        data = paddle.static.data(name='X', shape=[None, 1], dtype='float32')
        hidden = paddle.static.nn.fc(data, 10)
        loss = paddle.mean(hidden)

    place = paddle.CUDAPlace(0) if use_cuda else paddle.CPUPlace()
    exe = paddle.static.Executor(place)
    exe.run(startup_program)

    parallel_exe = paddle.static.ParallelExecutor(use_cuda=use_cuda,
                                                  main_program=train_program,
                                                  loss_name=loss.name)

    x = numpy.random.random(size=(10, 1)).astype('float32')
    loss_data, = parallel_exe.run(feed={"X": x},
                                  fetch_list=[loss.name])

    parallel_exe.drop_local_exe_scopes()

