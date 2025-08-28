## Basic
> https://docs.vllm.ai/en/latest/getting_started/examples/basic.html

路径: `examples/offline_inference/basic`

argument parser: 
- `Namespace`类存储ArgumentParser解析结果
- `LLM(**vars(args))`: 
	- vars方法将Namespace解析为字典, 
	- `**`: 在函数定义中，`**kwargs` 用来接受任意数量的关键字参数。在函数调用时，`**` 可以用来解包字典，将字典的键值对作为关键字参数传递给函数。

**关于`task`**
- 不同模型支持不同的task? 
	- 使用`facebook/opt-125m`进行classify, 结果返回均为0.5,0.5
- classify返回的各个维度的意义是什么? 应该去哪里看?
- 如果task=auto, vLLM如何推测应该执行哪个任务
	- `LLM(model="facebook/opt-125m")` -> 自动generate?
	- 答案在log中
```shell
❯ uv run examples/offline_inference/basic/basic.py
INFO 03-31 13:10:12 [__init__.py:239] Automatically detected platform cuda.
INFO 03-31 13:10:51 [config.py:593] This model supports multiple tasks: {'embed', 'reward', 'classify', 'score', 'generate'}. Defaulting to 'generate'.
```

sampling parameters
- `max_tokens`: 最大输出长度
- `temperature`: 输出随机性(0~1), 越低越保守
- `top_p` : 从概率最高的输出中选择, 直到概率和达到top_p, 保证不会生成概率过低的token
- `top_k`: 只从概率最高的k个单词中选择

### LLM类说明
> vllm/entrypoints/llm.py

该类用于从给定的提示（prompt）和采样参数生成文本。

此类包括一个分词器（tokenizer）、一个可能分布在多个 GPU 上的语言模型（language model），以及用于存储中间状态（即 KV 缓存）的 GPU 内存空间。给定一批提示和采样参数，该类通过智能批处理机制和高效的内存管理生成文本。
- tokenizer: 将输入prompt->token ID, 在LLM输出的token ID转换为文本

**参数**

- model：HuggingFace Transformers 模型的名称或路径。
- tokenizer：HuggingFace Transformers 分词器的名称或路径。
- tokenizer_mode：分词器模式。"auto"（自动）模式会使用可用的快速分词器，而 "slow"（慢速）模式始终使用慢速分词器。
- skip_tokenizer_init：如果为 True，则跳过分词器和去分词器的初始化。这种情况下，输入的 prompt_token_ids 需要是有效的，而 prompt 则应为 None。
- trust_remote_code：下载模型和分词器时，是否信任远程代码（例如 HuggingFace 上的代码）。
- allowed_local_media_path：允许 API 请求从服务器文件系统指定的目录中读取本地图片或视频。这存在安全风险，仅应在受信任的环境中启用。
- tensor_parallel_size：用于张量并行（tensor parallelism）分布式执行的 GPU 数量。
- dtype：模型权重和激活值的数据类型。目前支持 "float32"、"float16" 和 "bfloat16"。如果设置为 "auto"，则使用模型配置文件中指定的 torch_dtype。但如果 torch_dtype 指定为 float32，则会改用 float16。
- quantization：模型权重的量化方法。目前支持 "awq"、"gptq" 和 "fp8"（实验性）。如果设置为 None，首先会检查模型配置文件中的 quantization_config，如果该配置也为 None，则默认认为模型权重未量化，并使用 dtype 作为权重的数据类型。
- revision：要使用的模型版本，可以是分支名称、标签名称或提交 ID。
- tokenizer_revision：要使用的分词器版本，可以是分支名称、标签名称或提交 ID。
- seed：用于初始化随机数生成器的种子，以控制采样过程的随机性。
- gpu_memory_utilization：用于存储模型权重、激活值和 KV 缓存的 GPU **内存比例**（范围 0 到 1）。较高的值可以增加 KV 缓存大小，提高模型吞吐量。但如果值过高，可能会导致显存不足（OOM）错误。
- swap_space：每块 GPU 可使用的 CPU **内存交换空间（单位：GiB）**。当 best_of 采样参数大于 1 时，这部分内存用于临时存储请求状态。如果所有请求的 best_of=1，可以安全地将此值设为 0。值得注意的是，best_of 仅在 V0 版本支持。如果此值过小，可能会导致 OOM 错误。
- cpu_offload_gb：用于卸载模型权重的 CPU 内存大小（单位：GB）。这样可以虚拟扩展可用于存储模型权重的 GPU 内存，但会增加每次前向传播的 CPU-GPU 数据传输开销。
- enforce_eager：是否强制使用 eager 执行模式。如果设置为 True，将禁用 CUDA 图（CUDA graph），并始终以 eager 模式执行。如果设置为 False，则会混合使用 CUDA 图和 eager 模式。
- max_seq_len_to_capture：CUDA 图支持的最大序列长度。当序列的上下文长度超过此值时，将回退到 eager 模式。此外，对于编码-解码（encoder-decoder）模型，如果编码器输入的序列长度超过此值，也会回退到 eager 模式。
- disable_custom_all_reduce：详见 vllm.config.ParallelConfig。
- disable_async_output_proc：是否禁用异步输出处理。如果启用，可能会导致性能下降。
- hf_overrides：
	- 如果是字典，则包含要传递给 HuggingFace 配置的参数。
	- 如果是可调用对象（callable），则用于更新 HuggingFace 配置。
- compilation_config：可以是整数或字典：
	- 如果是整数，则表示编译优化级别。
	- 如果是字典，则可以指定完整的编译配置。
- **kwargs：传递给 vllm.EngineArgs 的参数（详见 engine-args）。

**备注**
该类适用于**离线推理**（offline inference）。对于**在线推理**（online serving），请使用 vllm.AsyncLLMEngine 类。