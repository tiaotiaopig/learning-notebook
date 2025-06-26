# Onnxinfer

> 说明：
>
> **onnxinfer**是对微软的推理库**onnxruntime**的扩展，支持国产显卡，存在一些算子优化
>
> 算法模型导出的onnx时，需要修改为**opset12**，format支持v7，import支持v12
>
> 在C++端推理时，需要使用优化后的模型；且MLU和GPU需要分别优化，混用时可以推理成功，但速度特别慢
>
> 特性：
>
> 本仓库是对`onnxinfer`的简单封装，支持go调用`onnxinfer`，支持CPU、MLU、GPU推理
>
> 使用：
>
> 1. 目前集成了embedding模型和粗排模型
> 2. embedding使用的是bert,token是三输入(token_id, type_id, attentionmask) 粗排使用的是enie, token是两输入(token_id, type_id)
> 3. embedding的输入是query, 粗排的输入是QA对
> 4. libonnxinfer_wrapper.so的编译存在已知问题: ubuntu20中的g++9.4编译时，ldd libonnxinfer_wrapper.so找不到cnrt库，需要export LD_LIBRARY_PATH, 而在centos8中使用g++8.5编译则不会出现这个问题，-Wl,-rpath指定的路径能正常生效
> 5. run函数是非线程安全的，可以采用多实例单线程的方式

## python使用

安装python版本推理库，需要安装一下python包：

+ Onnx
+ Pybind11
+ protobuf
+ prettytable
+ tqdm
+ logger

### 说明：

**mlu的安装包和gpu安装包不兼容，只能安装其一**（ps：一台机器不会同时具备MLU和GPU）

### 安装：

```bash
# 安装mlu包
pip install onnxinfer-mlu/dist/onnxinfer-2.1.0.tar.gz
# 安装gpu包
pip install onnxinfer-gpu/dist/onnxinfer-2.1.0.tar.gz
```

### 示例：

```python
sessoption = onnxinfer.InferSessionOptions()
sessoption.InitializeSession([self.execution_provider_map[device_type]], device_id)

# is_fuse=True: 是否启动shape融合，用于c+端shape推理
# 如果是C++端集成模型，应该集成save_transform_model转换之后的模型，而不是原始模型
# save_transform_model='trans_model.onnx': python端会做一系列图变换，该选项用于保存图变换之后的模型，并用于C++端集成
onnxinfer_session = onnxinfer.InferSession(
    path=onnx_path,
    session_options=sessoption,
    is_fuse=True,
    save_transform_model=save_transform_model_path,
)
# 开始推理,input是字典，和onnx输入保持一致
res = onnxinfer_session.Run(data_in=input)[0].AsReadOnlyNumpy()
```

## Go使用

> Go使用的是C++端的onnxinfer，所以**使用的.onnx文件应该是使用python api 转换后的模型**

1. 加载环境：`source env-gpu.sh`
2. 编译.so库文件：`bash compile_gpu_lib.sh `
3. gobinding调用：`go run gobinding/onnxinfer_engine_test.go `

在go服务集成时，需要的资源文件：

+ 通用：fast_tokenizer、gobinding
+ mlu：neuware_home_1.16.0、onnxinfer-mlu
+ gpu：cuda-11.7.1、onnxinfer-gpu

## C++使用

> CMakeLists-gpu.txt 对应 main_gpu.cpp
>
> CMakeLists-mlu.txt 对应 main_mlu.cpp

## CICD部署

由于编译go可执行文件时，需要依赖大量`.so`库文件，有两种解决方法：

1. 修改cgo使用库文件的方式，使用c++的dl库，运行时加载库文件；（代码较复杂）
2. 本地编译可执行文件后上传（需要注意一致性）