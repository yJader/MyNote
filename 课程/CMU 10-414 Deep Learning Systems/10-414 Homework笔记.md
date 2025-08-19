## overall

### colab导入库问题
引用挂载的needle库时, 修改了needle库文件内容后, 还需要重新导入

plan a: 启用autoreload, 设置为模式1: 在每次执行cell前, reload用 %aimport 标记过的模块
```python
%load_ext autoreload
%autoreload 1

%aimport needle
```

或者plan b: 
设置为模式2: 在每次执行cell前, reload所有已导入模块
```python
%load_ext autoreload
%autoreload 2
```

注: 重复导入可能会造成一些奇怪的bug, 可以尝试重启运行时
- bug例: 重复导入ndl库, 导致`isinstance(ndl.Tensor, ndl.autograd.Tensor)`结果为False ...非常抽象
## Homework1

### 运算
#### Broadcast
def broadcast_to(a, shape): broadcast an array to a new shape (1 input, `shape` - tuple)
广播, 复制张量的某些维度, 使得不同形状的数组进行逐元素运算时更加方便
- 向量加标量：把标量加到每个元素。
- 矩阵加列向量：把列向量加到矩阵每一列。
- 矩阵乘以标量：标量自动扩展到矩阵大小。


广播的梯度计算: 沿着广播的维度sum回去
- 广播的本质是变量复制, 广播后的元素参与了多次的计算, 反向传播时要累加这部分的梯度值