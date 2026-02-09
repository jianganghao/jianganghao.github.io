---
layout: post
title: PyTorch in 5 Minutes
date: 2026-02-09 09:00:00-0400
description: A fast-start PyTorch tutorial for Python users
categories: data-science
tags: pytorch, deep-learning, tutorial
giscus_comments: false
related_posts: false
related_publications: true
---

This quick tutorial was originally drafted by prompting ChatGPT in 2023 and lightly edited by me. It is intended for readers who know Python and want a fast start with PyTorch. For a systematic guide, see the official [PyTorch Tutorials](https://pytorch.org/tutorials/).

### 1. Installation

```bash
pip install torch torchvision
```

### 2. PyTorch Basics

#### Tensors

```python
import torch

# Create a 1D tensor
x = torch.tensor([1, 2, 3])
print(x)

# Create a 2x3 tensor (matrix) filled with ones
y = torch.ones(2, 3)
print(y)
```

#### NumPy and Tensor Interoperability

```python
import numpy as np
import torch

# NumPy -> PyTorch
numpy_arr = np.array([1, 2, 3])
tensor_from_numpy = torch.from_numpy(numpy_arr)
print(tensor_from_numpy)

# PyTorch -> NumPy
tensor = torch.tensor([1, 2, 3])
numpy_from_tensor = tensor.numpy()
print(numpy_from_tensor)
```

### 3. Automatic Differentiation

```python
import torch

x = torch.tensor(2.0, requires_grad=True)
y = x ** 2

y.backward()
print(x.grad)  # 4.0 (dy/dx = 2*x at x=2)
```

### 4. Neural Network Basics

```python
import torch
import torch.nn as nn
import torch.optim as optim

class SimpleNN(nn.Module):
    def __init__(self):
        super(SimpleNN, self).__init__()
        self.fc = nn.Linear(1, 1)

    def forward(self, x):
        return self.fc(x)

model = SimpleNN()
loss_fn = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=0.01)

inputs = torch.tensor([[1.0], [2.0], [3.0]])
outputs = torch.tensor([[2.0], [4.0], [6.0]])

for epoch in range(100):
    preds = model(inputs)
    loss = loss_fn(preds, outputs)

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    if (epoch + 1) % 10 == 0:
        print(f"Epoch [{epoch+1}/100], Loss: {loss.item():.4f}")
```

The loop above trains a simple network to approximate `y = 2x` using forward pass, loss computation, and backpropagation.

### 5. Tensor Operations

#### Reshaping

```python
import torch

x = torch.randn(2, 3)
print(x)

y = x.view(3, 2)
print(y)
```

#### Element-wise Operations

```python
import torch

x = torch.tensor([1, 2, 3])
y = torch.tensor([4, 5, 6])

z = x + y
print(z)
```

#### Matrix Multiplication

```python
import torch

x = torch.randn(2, 3)
y = torch.randn(3, 2)

z = torch.mm(x, y)
print(z)
```

#### Reductions

```python
import torch

x = torch.tensor([[1, 2], [3, 4]])

sum_x = torch.sum(x)
mean_x = torch.mean(x)
print(sum_x, mean_x)
```

### 6. Autograd Basics

```python
import torch

# Track gradients
x = torch.tensor(2.0, requires_grad=True)
y = x ** 2
y.backward()
print(x.grad)

# Stop tracking
x = torch.tensor(2.0, requires_grad=True)
with torch.no_grad():
    y = x ** 2
print(y.requires_grad)  # False
```

### 7. Chain Rule Example (Autograd)

We define:

- `f(u, v) = u^2 + 3uv + 5v^2`
- `u(x, y) = xy + sin(x)`
- `v(x, y) = y^2 - cos(y)`

At `x = π`, `y = 1`, we compute `df/dx` and `df/dy` with autograd:

```python
import torch
from math import pi

x = torch.tensor(pi, requires_grad=True)
y = torch.tensor(1.0, requires_grad=True)

u = x * y + torch.sin(x)
v = y**2 - torch.cos(y)

f = u**2 + 3*u*v + 5*v**2
f.backward()

print(f"df/dx: {x.grad.item()}")
print(f"df/dy: {y.grad.item()}")
```

PyTorch builds a computation graph and applies the chain rule automatically when you call `.backward()`.

### 8. Device (CPU/GPU) Quickstart

#### 1) Check your CPU architecture

```python
import platform

platform.platform()
```

#### 2) Check your GPU backend

```python
import torch

torch.has_mps   # Apple Silicon (Metal)
torch.has_cuda  # NVIDIA CUDA
```

#### 3) Set the device

```python
import torch

device = torch.device("mps")  # or "cuda" / "cpu"
device
```

#### 4) Move computation to GPU and bring results back to CPU

```python
import numpy as np
import torch
from transformers import GPT2LMHeadModel, GPT2TokenizerFast

device = torch.device("mps")  # or "cuda"

tokenizer = GPT2TokenizerFast.from_pretrained("gpt2")
model = GPT2LMHeadModel.from_pretrained("gpt2").eval().to(device)

sentence = "Hello world"
tokenize_input = tokenizer.encode(sentence)
tensor_input = torch.tensor([tokenize_input]).to(device)

loss = model(tensor_input, labels=tensor_input).loss
loss_cpu = np.exp(loss.detach().cpu().numpy())
loss_cpu
```

### Summary

PyTorch gives you NumPy-like tensors with GPU acceleration and a powerful autograd system. If you already know Python and NumPy, you can start quickly and scale to more advanced workflows without changing tools.
