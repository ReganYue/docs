.. _cn_api_static_save_inference_model:

save_inference_model
-------------------------------


.. py:function:: paddle.static.save_inference_model(path_prefix, feed_vars, fetch_vars, executor, **kwargs)




将模型及其参数保存到指定的路径。例如，``path_prefix="/path/to/modelname"``，在调用 ``save_inference_model(path_prefix, feed_vars, fetch_vars, executor)`` 之后，你可以在 "/path/to" 目录下找到两个文件，分别是 "modelname.pdmodel" 和 "modelname.pdiparams"，前者表示序列化之后的模型文件，后者表示序列化之后的参数文件。


参数
::::::::::::

  - **path_prefix** (str) – 要保存到的目录 + 模型名称（不包含后缀）。
  - **feed_vars** (Variable | list[Variable]) – 模型的所有输入变量。
  - **fetch_vars** (Variable | list[Variable]) – 模型的所有输出变量。
  - **executor** (Executor) –  用于保存预测模型的 ``executor``，详见 :ref:`api_guide_executor` 。
  - **kwargs** - 支持的 key 包括 'program'和 'clip_extra'。(注意：kwargs 主要是用来做反向兼容的)。

      - **program** - 自定义一个 program，默认使用 default_main_program。

      - **clip_extra** - 如果你想为每个算子附加额外的信息，则设置为True。


返回
::::::::::::

无。


代码示例
::::::::::::

COPY-FROM: paddle.static.save_inference_model