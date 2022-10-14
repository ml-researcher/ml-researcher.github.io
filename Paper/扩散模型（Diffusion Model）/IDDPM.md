# Improved Denoising Diffusion Probabilistic Models

本文需要的背景知识是扩散模型（Diffusion Model），如果你对扩散模型还不了解，可以参考我之前整理的教程：

<p style="text-align:center">https://github.com/ml-researcher/diffusion</p>

教程里有一个DDPM里的遗留问题需要解决：为什么$\beta_t$和$\tilde{\beta}_t$是两个特殊的$\sigma_t$的选择？

我们要拟合$p(x_{t-1}|x_t)$，具体来说我们想知道它的方差是多少。由贝叶斯公式：

$$
p(x_{t-1}|x_t) = \frac{p(x_t|x_{t-1})p(x_{t-1})}{p(x_t)}
$$

在这个式子里，$p(x_t|x_{t-1})=\mathcal{N}(x_t;\sqrt{1-\beta_t}x_{t-1},\beta_tI)$，分母是一个归一化常数，不好求的就是分子里的$p(x_{t-1})$。我们可以研究一些特殊的$x_0$的分布，求出$p(x_{t-1})$的表达式，从而求得方差，为我们选择$\sigma_t$做参考。这里举两个极端例子，分别是$x_0$是单点分布，以及$x_0$是标准正态分布。

## $x_0$服从单点分布

对于单点分布的$x_0$，即$p(x_0)=\delta(x_0)$，其中$\delta$是狄拉克函数。此时有$p(x_{t-1})=\int p(x_{t-1}|x_0)p(x_0)dx_0=p(x_{t-1}|x_0)$，我们已知$p(x_{t-1}|x_0)=\mathcal{N}(x_{t-1};\sqrt{\tilde{\alpha}_{t-1}}x_0,(1-\tilde{\alpha}_{t-1})I)$，因此：

$$
\begin{align*}
p(x_{t-1}|x_t) &= \frac{p(x_t|x_{t-1})p(x_{t-1}|x_0)}{\mathcal{Z}} \\
&= \frac{\exp(-\frac{(x_t-\sqrt{1-\beta_t}x_{t-1})^2}{2\beta_t})\exp(-\frac{(x_{t-1}-\sqrt{\tilde{\alpha}_{t-1}}x_0)^2}{2(1-\tilde{\alpha}_{t-1})})}{\mathcal{Z}Z'}
\end{align*}
$$

整理一下可得，这是一个方差为$\frac{1-\tilde{\alpha}_{t-1}}{1-\tilde{\alpha}_t}\beta_t$的正态分布。

## $x_0$服从标准正态分布

如果$x_0$是标准正态分布，那么有$x_t=\sqrt{\tilde{\alpha}_t}x_0+\sqrt{(1-\tilde{\alpha}_t)}\epsilon$，既然$x_0$和$\epsilon$都是$\mathcal{N}(0,I)$，可得$x_t$也是标准正态分布。因此$p(x_{t-1})=\mathcal{N}(x_{t-1};0,I)$。

$$
\begin{align*}
p(x_{t-1}|x_t) &= \frac{p(x_t|x_{t-1})p(x_{t-1})}{\mathcal{Z}} \\
&= \frac{\exp(-\frac{(x_t-\sqrt{1-\beta_t}x_{t-1})^2}{2\beta_t})\exp(-\frac{x_{t-1}^2}{2})}{\mathcal{Z}Z'}
\end{align*}
$$

整理可得，这是一个方差为$\beta_t$的正态分布。