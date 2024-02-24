---
title: Crypto入门-基础数论复盘
date: 2024-02-23 19:25:15
tags: Crypto
mathjax: true
---

# Crypto入门-基础数论复盘

高中OI学过数论相关，都还给教练了，现在开始复盘，死去的记忆开始拷打我。

## RSA相关

### 欧拉函数

$\varphi(n)$表示小于$n$的正整数中与$n$互质的数的数目。基本性质：

1. 如果$n$为质数，则$\varphi(n)=n-1$。

2. 如果$p,q$为质数，则$\varphi(pq)=\varphi(p)\varphi(q)=(p-1)(q-1)$。

### 同余

1. 若$a,b,c,d\in\mathbb Z,m\in\mathbb N_+,a\equiv b\pmod m,c\equiv d\pmod m$，则有：

    $$
    a\pm c\equiv b\pm d\pmod m,\\ac\equiv bd\pmod m
    $$

2. 若$a,b\in\mathbb Z,k,m\in\mathbb N_+,a\equiv b\pmod m$，则有：$ak\equiv bk\pmod{mk}$。

### 中国剩余定理

求解一元线性同余方程组：

$$
\begin{cases}
    x\equiv a_1\pmod{n_1}\\
    x\equiv a_2\pmod{n_2}\\
    \ \ \ \ \vdots\\
    x\equiv a_k\pmod{n_k}
\end{cases}
$$

求解方法：

1. 计算$\displaystyle n=\prod_{i=1}^kn_k$。

2. 对于第$i$个方程：

    1. 计算$\displaystyle m_i=\frac{n}{n_i}$。

    2. 计算$m_i$在模$n_i$意义下的逆元$m_i^{-1}$。

    3. 计算$c_i=m_im_i^{-1}$，注意不要对$n_i$取模。

3. 解为：$\displaystyle x=\sum_{i=1}^ka_ic_i\pmod c$。

### 连分数

$$
\left[a_0\right]=\frac{a_0}{1}\\
\left[a_0,a_1\right]=a_0+\frac{1}{a_1}=\frac{a_0a_1+1}{a_1}\\
\left[a_0,a_1,a_2,\dots,a_n\right]=a_0+\frac{1}{\left[a_1,a_2,\dots,a_n\right]}=\left[a_0,\left[a_1,a_2,\dots,a_n\right]\right]
$$

例如：

$$
\begin{align*}
    \frac{89}{26}&=3+\frac{1}{2+\dfrac{1}{2+\dfrac{1}{1+\dfrac{1}{3}}}}\\
    &=\left[3,2,2,1,3\right]
\end{align*}
$$

使用辗转相除法将分数$\dfrac{x}{y}$转为连分数形式：

```python
def transform (x,y):
    res=[]
    while y:
        res.append(x//y)
        x,y=y,x%y
    return res
```

### 渐进分数

称$\left[a_0,a_1,\dots,a_m\right]$为$\left[a_0,a_1,\dots,a_n\right]$的$m(0\leqslant m\leqslant\ n)$级渐进分数。当$m$越接近$n$，误差越来越小。

求每个渐进分数：

```python
def continued_fraction(sub_res):
    numerator,denominator=1,0
    for i in sub_res[::-1]:
        denominator,numerator=numerator,i*numberator+denominator
    return denominator,numerator
def sub_fraction(x,y):
    res=transform(x,y)
    res=list(map(continued_fraction,res[0:i] for i in range(1,len(res))
    return res
```
