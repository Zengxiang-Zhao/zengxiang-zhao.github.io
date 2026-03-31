---
layout: default
title: AI
date:   2021-09-11 20:28:05 -0400
categories: AI
nav_order: 101

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Understanding the transformer multiple head attention

```python
import torch
import torch.nn as nn
import torch.nn.functional as F


class MultiHeadAttention(nn.Module):
    """Complete multi-head attention with Q, K, V as linear layers"""
    
    def __init__(self, d_model=512, num_heads=8):
        super().__init__()
        self.d_model = d_model
        self.num_heads = num_heads
        self.d_k = d_model // num_heads

        print(f'd_k is: {self.d_k}')
        
        # Q, K, V are LINEAR LAYERS
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)

        self.W_o = nn.Linear(d_model, d_model)
        # self.W_o is a linear layer that:

        # 1. Takes the concatenated outputs from all attention heads
        # 2. Projects them back to the original model dimension (d_model)
        # 3. Mixes information across different attention heads
        # actually you can treat the W_o as another head that handle the information distilled from other heads. and output the usefull information you can use directly.
        
    def split_heads(self, x, batch_size):
        """Split the last dimension into (num_heads, d_k)"""
        x = x.view(batch_size, -1, self.num_heads, self.d_k)
        return x.transpose(1, 2)
    
    def forward(self, x, mask=None):
        batch_size, seq_len, _ = x.shape
        
        # 1. Compute Q, K, V using LINEAR LAYERS
        Q = self.W_q(x)  # (batch, seq_len, d_model)
        K = self.W_k(x)  # (batch, seq_len, d_model)
        V = self.W_v(x)  # (batch, seq_len, d_model)
        
        print(f'Before split Q: {Q.shape}')
        # 2. Split into multiple heads
        Q = self.split_heads(Q, batch_size)  # (batch, heads, seq_len, d_k)
        K = self.split_heads(K, batch_size)  # (batch, heads, seq_len, d_k)
        V = self.split_heads(V, batch_size)  # (batch, heads, seq_len, d_k)

        print(f'after split Q : {Q.shape}')
        #  K, Q shape is [2,8,10,64]. When K transpose the last two position, it becomes to [2,8,64,10]. 
        #  When multiple Q and K.transpose. you will get [2,8,10,10]. You can treat this as the relationship of the ten words each other.
        
        # 3. Compute attention scores
        scores = torch.matmul(Q, K.transpose(-2, -1)) / (self.d_k ** 0.5)

        print(f'scores shape: {scores.shape}')
        
        # 4. Apply mask (optional)
        if mask is not None:
            scores = scores.masked_fill(mask == 0, -1e9)
        
        # 5. Apply softmax
        attention_weights = F.softmax(scores, dim=-1) # Here the dim = -1 means the influence or attention that each word to other words.
        
        # 6. Apply attention to values
        # Here attention_weights is [2,8,10,10], V is [2,8,10,64]. 
        context = torch.matmul(attention_weights, V)  # (batch, heads, seq_len, d_k)

        print(f'context shape before transpose is: {context.shape}')
        
        # 7. Concatenate heads
        context = context.transpose(1, 2).contiguous()
        print(f'context shape after transpose is: {context.shape}')

        context = context.view(batch_size, -1, self.d_model)
        print(f'context shape after view is: {context.shape}')
        # 8. Final output projection
        output = self.W_o(context)
        
        return output, attention_weights

```

```python
# Example
mha = MultiHeadAttention(d_model=512, num_heads=8)
x = torch.randn(2, 10, 512)
output, weights = mha(x)

print(f"Input shape: {x.shape}")
print(f"Output shape: {output.shape}")
print(f"Attention weights shape: {weights.shape}")
```
```bash
# output

d_k is: 64
Before split Q: torch.Size([2, 10, 512])
after split Q : torch.Size([2, 8, 10, 64])
scores shape: torch.Size([2, 8, 10, 10])
context shape before transpose is: torch.Size([2, 8, 10, 64])
context shape after transpose is: torch.Size([2, 10, 8, 64])
context shape after view is: torch.Size([2, 10, 512])
Input shape: torch.Size([2, 10, 512])
Output shape: torch.Size([2, 10, 512])
Attention weights shape: torch.Size([2, 8, 10, 10])


```
