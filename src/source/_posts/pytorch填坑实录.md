---
title: pyTorch填坑实录
tags: []
id: '495'
categories:
  - - 深度学习
date: 2021-03-14 19:51:10
---

1.  pytorch: grad can be implicitly created only for scalar outputs:

```python
z.backward(torch.ones_like(x))
```

原因：backward必须使用标量来进行

2.  python本身看起来数据类型不敏感，但是pytorch极其敏感，int与double不能直接相加，出现相关错误后应查看数据类型：

```python
print(tx.dtype) 
tx=tx.to(dtype=torch.float64)
```

3.  torch.nn.functional.Softmax 和 torch.nn.softmax不是一个东西 前者本质是一个函数，用于最后的lost计算，后者是一个神经元节点的定义
    
4.  小心tensor的维度，用xxx.size()查看tensor的维度及范围
    

```python
print(out.size())
```