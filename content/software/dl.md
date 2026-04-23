---
Title: Deep Learning
showTableOfContents: true
---

## Transformers

### MHA

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import math

class MHA(nn.Module):
    def __init__(self, d_model, num_heads):
        super().__init__()
        assert d_model % num_heads == 0
        
        self.d_model = d_model
        self.num_heads = num_heads
        self.d_k = d_model // num_heads
        
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)

    def forward(self, x, mask=None):
        b, l, d = x.shape
        Q = self.W_q(x)  # (b,l,d)
        K = self.W_k(x)
        V = self.W_v(x)

        # 2. multi-head
        Q = Q.view(B, L, self.num_heads, self.d_k).permute(0, 2, 1, 3)  # (b, h, l, d)
        K = K.view(B, L, self.num_heads, self.d_k).permute(0, 2, 1, 3)
        V = V.view(B, L, self.num_heads, self.d_k).permute(0, 2, 1, 3)

        # 3.attention
        scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_k)  # (b, b, l, l)

        if mask is not None:
            scores = scores.masked_fill(mask == 0, -1e9)

        attn = F.softmax(scores, dim=-1)
        context = torch.matmul(attn, V)  # (b, h, l, d)

        # 5. conbining multi head
        context = context.permute(0, 2, 1, 3).contiguous().view(b, l, d)
        out = self.W_o(context)

        return out
```

