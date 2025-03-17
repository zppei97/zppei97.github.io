---
title: 👩🏼‍🏫 Teach academic courses
summary: Embed videos, podcasts, code, LaTeX math, and even test students!
date: 2023-10-24
math: true
authors:
  - admin
---

在了解置信区间之前，先来区分两个不同的概念。

## 1. **置信区间**（Confidence Interval）
置信区间用于估计**回归系数**或**预测值的均值**的范围，反映了模型参数的不确定性。它考虑的是**模型参数**（如回归系数）的不确定性，但不包括未来观测值的随机性。


## 2. **预测区间**（Prediction Interval）
预测区间用于估计**新的单个观测值**的范围，它不仅考虑了模型参数的不确定性，还考虑了新的观测值中的随机性（即数据中的固有噪声）。因此，预测区间通常比置信区间宽。

## 3. **线性回归模型回顾**

考虑一个线性回归模型：

$$
Y = X\beta + \epsilon
$$

其中：
- $ Y $ 是 $ n \times 1 $ 的响应变量向量。
- $ X $ 是 $ n \times (p+1) $ 的设计矩阵（包含截距项）。
- $ \beta$ 是 $ (p+1) \times 1 $ 的回归系数向量。
- $ \epsilon $ 是 $ n \times 1 $ 的误差向量，假设 $ \epsilon \sim \mathcal{N}(0, \sigma^2 I) $。

