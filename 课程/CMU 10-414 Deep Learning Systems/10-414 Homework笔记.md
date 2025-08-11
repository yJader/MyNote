## Homework1

### 运算
#### Broadcast
def broadcast_to(a, shape): broadcast an array to a new shape (1 input, `shape` - tuple)
广播, 复制张量的某些维度, 使得不同形状的数组进行逐元素运算时更加方便
- 向量加标量：把标量加到每个元素。
- 矩阵加列向量：把列向量加到矩阵每一列。
- 矩阵乘以标量：标量自动扩展到矩阵大小。