---
title: （多头）自注意力机制的PyTorch实现
tags: 深度学习 注意力机制 代码模板
key: fda085967b4c79ba228972c7cb8e5611
---

用于学习和复习的两份自注意力机制实现代码。

## 自注意力

使用了缩放点积作为打分函数，因此key和query的维数是一样的，实现很简单。

```python
from math import sqrt

import torch
import torch.nn as nn

class SelfAttention(nn.Module):
    dim_in: int
    dim_k: int
    dim_v: int

    def __init__(self, dim_in, dim_k, dim_v):
        super(SelfAttention, self).__init__()
        self.dim_in = dim_in
        self.dim_k = dim_k
        self.dim_v = dim_v
        self.linear_q = nn.Linear(dim_in, dim_k, bias=False)
        self.linear_k = nn.Linear(dim_in, dim_k, bias=False)
        self.linear_v = nn.Linear(dim_in, dim_v, bias=False)
        self._norm_fact = 1 / sqrt(dim_k)

    def forward(self, x):
        # x: batch, n, dim_in
        batch, n, dim_in = x.shape
        assert dim_in == self.dim_in

        q = self.linear_q(x)  # batch, n, dim_k
        k = self.linear_k(x)  # batch, n, dim_k
        v = self.linear_v(x)  # batch, n, dim_v

        dist = torch.bmm(q, k.transpose(1, 2)) * self._norm_fact  # batch, n, n
        dist = torch.softmax(dist, dim=-1)  # batch, n, n

        att = torch.bmm(dist, v)
        return att
```

这里为简单起见没有实现mask，若要实现，只需要把`k`或者`q`逐点乘以`mask`（假设我们有一个`mask`，需要保留的地方是`1`，需要mask掉的地方是`-Inf`），这样两个`Tensor`进行矩阵乘法后，在需要mask掉的位置的分数就是负无穷，Softmax后的注意力分布就是0。

## 多头自注意力

上述自注意力机制的多头版本，思路是使用一个大矩阵把所有头的所有Q、K、V并行地计算出来，然后通过改变形状（reshape）、和交换维度（permute）把多个头的Q、K、V放到同一个batch中进行和单头注意力相同的计算，最后再把多个头的注意力向量拼接起来得到最后的值。

**这里并行计算多个头的trick要注意。**

```python
from math import sqrt

import torch
import torch.nn as nn


class MultiHeadSelfAttention(nn.Module):
    dim_in: int  # input dimension
    dim_k: int   # key and query dimension
    dim_v: int   # value dimension
    num_heads: int  # number of heads, for each head, dim_* = dim_* // num_heads

    def __init__(self, dim_in, dim_k, dim_v, num_heads=8):
        super(MultiHeadSelfAttention, self).__init__()
        assert dim_k % num_heads == 0 and dim_v % num_heads == 0, "dim_k and dim_v must be multiple of num_heads"
        self.dim_in = dim_in
        self.dim_k = dim_k
        self.dim_v = dim_v
        self.num_heads = num_heads
        self.linear_q = nn.Linear(dim_in, dim_k, bias=False)
        self.linear_k = nn.Linear(dim_in, dim_k, bias=False)
        self.linear_v = nn.Linear(dim_in, dim_v, bias=False)
        self._norm_fact = 1 / sqrt(dim_k // num_heads)

    def forward(self, x):
        # x: tensor of shape (batch, n, dim_in)
        batch, n, dim_in = x.shape
        assert dim_in == self.dim_in
        nh = self.num_heads

        dk = self.dim_k // nh  # dim_k of each head
        dv = self.dim_v // nh  # dim_v of each head
        dims_perm = (0, 2, 1, 3)
        q = self.linear_q(x).reshape(batch, n, nh, dk).permute(dims_perm)  # (batch, nh, n, dk)
        k = self.linear_k(x).reshape(batch, n, nh, dk).permute(dims_perm)  # (batch, nh, n, dk)
        v = self.linear_v(x).reshape(batch, n, nh, dv).permute(dims_perm)  # (batch, nh, n, dv)

        q, k = q.reshape(-1, nh, dk), k.reshape(-1, nh, dk)  # batch * nh, n, dk
        v = v.reshape(-1, nh, dv)  # batch * nh, n, dv

        dist = torch.bmm(q, k.transpose(1, 2)) * self._norm_fact  # batch * nh, n, n
        dist = torch.softmax(dist, dim=-1)  # batch * nh, n, n

        att = torch.bmm(dist, v)  # batch * nh, n, dv
        att = att.reshape(batch, nh, n, dv).permute(0, 2, 1, 3)\
            .reshape(batch, nh, self.dim_v)  # batch, n, dim_v
        return att
```

mask的实现方式同上。
