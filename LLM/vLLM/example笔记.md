## Basic
> https://docs.vllm.ai/en/latest/getting_started/examples/basic.html

路径: `examples/offline_inference/basic`

argument parser: 
- `Namespace`类存储ArgumentParser解析结果
- `LLM(**vars(args))`: 
	- vars方法将Namespace解析为字典, 
	- `**`: 在函数定义中，`**kwargs` 用来接受任意数量的关键字参数。在函数调用时，`**` 可以用来解包字典，将字典的键值对作为关键字参数传递给函数。

**关于task**
- 不同模型支持不同的task? 
	- 使用`facebook/opt-125m`进行classify, 结果返回均为0.5,0.5
- classify返回的各个维度的意义是什么? 应该去哪里看?

sampling parameters
- `max_tokens`: 最大输出长度
- `temperature`: 输出随机性(0~1), 越低越保守
- `top_p` : 从概率最高的输出中选择, 直到概率和达到top_p, 保证不会生成概率过低的token
- `top_k`: 只从概率最高的k个单词中选择

