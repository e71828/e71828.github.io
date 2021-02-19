---
layout: default
title: 傅里叶变换浅析
description: 如何理解频域的傅里叶级数与时域的傅里叶变换。
---
<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            // show equation numbers
            TeX: {
                 equationNumbers: {  autoNumber: "AMS"  },
                 extensions: ["AMSmath.js"]
            },
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

# 从Fourier 级数讲起。
## 引子
    - 无穷个连续函数相加是否连续？
    - 无穷个可导函数相加是否可导？
    
## Fourier 级数展开的条件
### Dirichlet条件： 
    - 连续或有限个第一类间断点（可去或跳跃间断点）；
    - 有限个极值点。
    
### 奇偶延拓：
    -  单个`sin`函数的频谱在虚轴（仍奇函数）；
    -  单个`cos`函数的频谱在实轴（依然是偶函数）。
    
### 三角函数的正交性：

$$ f_T(t)={\frac {a_{0}}{2}}+\sum _{n = -\infty}^{\infty }\left[a_{n}\cos (n\omega t)+b_{n}\sin (n\omega t)\right] $$

$ \int _{-\pi }^{\pi }\cos(mx)\,\cos(nx)\,dx=\pi \delta _{mn},\quad m,n\geq 1,$  

$\int _{-\pi }^{\pi }\sin(mx)\,\sin(nx)\,dx=\pi \delta _{mn},\quad m,n\geq 1$  

若利用乘积定理：

$$\int _{-\infty }^{+\infty }f_{1}(t)\,f_{2}(t)\,\mathrm{d}t=\frac{1}{2\pi} \int _{-\infty }^{+\infty }f_{1}(\omega)\,\overline{f_{2}(\omega)}\,\mathrm{d}\omega$$

Dirac-函数平方的无穷积分怎么算，是否存在？  

又得$\frac{T}{2} \delta _{mn}$。  
## Fourier 系数

$a_{0}= \frac {2}{T} \int^{ \frac {T}{2}} _{ -\frac {T}{2}} f_T(t) \; \mathrm{d}t$

$a_{n}= \frac {2}{T} \int^{ \frac {T}{2}} _{ -\frac {T}{2}} f_T(t)\cos (n\omega t) \; \mathrm{d}t , \quad n \in \mathbb {N}$

$b_{n}= \frac {2}{T} \int^{ \frac {T}{2}} _{ -\frac {T}{2}} f_T(t)\sin (n\omega t) \; \mathrm{d}t , \quad n \in \mathbb {N^{+}}$

## Euler 公式对应的Fourier 级数复指数形式。
- 不受奇偶延拓限制。
- 非奇非偶。
- 整合了余弦级数和正弦级数。
- $c_{-n} = \overline{c_{n}} $

## 周期无穷大

- 当n取一切整数时，离散的频谱便会均匀地分布在整个数轴上。
- 当时域函数地周期无穷大时，离散的频谱便会变得连续。
- 时域周期函数，如周期方波（定义过原点），偶数倍谐频的傅里叶系数为零，基波为正弦波，频谱分量为离散值。
- 单个偶函数方波的频谱函数为 `sinc`函数，是连续的，非周期的。
    
验证傅里叶变换对：$2\pi\delta(\omega)$。  

验证单位阶跃函数；$\frac{1}{2} + \frac{1}{\pi} \int^{+\infty}_{0} \frac{\sin (\omega t)}{\omega} \mathrm{d} \omega$

## 结论
- 频域由连续（包络）变成离散值， 说明时域信号是周期的，时域信号的出现了重复。
- 时域由连续（包络）变成离散（采样），则频谱在频域轴上周期性搬运，或发生混叠（与时域频率成分有关）。
- 单个余弦函数的频谱对应两个频域轴上的点，由傅里叶变换的线性可知，无穷个余弦函数的和，可以表示频域上的周期脉冲。
- 无穷个余弦函数累加，无法想象，但是借助Dirac-函数，予以表述，就是时域上的周期脉冲。
- 狄拉克疏是其自身的傅里叶变换。
- 傅里叶级数与傅里叶变换不应该分开理解。
- 数学机器区分音频的不同频率，利用[缠绕频率](https://www.youtube.com/watch?v=spUNpyF58BY)。
- 傅里叶变换与逆变换是对称的，为什么工程把$\frac{1}{2\pi}$给了逆变换？ 
   - 在量子力学等领域，将$\frac{1}{\sqrt{2\pi}}$各分给正逆变换。
   
# [HOME](../index.md)
