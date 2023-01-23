# VAE

这应该是第三次总结VAE。第一次是在[b站](https://www.bilibili.com/video/BV1Z64y167iJ)用视频总结的，第二次是在[github](https://github.com/ml-researcher/VAE)整理了一份pdf。感觉自己断断续续学了很久VAE，可是每次学都会有新的疑问，看来VAE真可谓是常看常新了。

## 先验

在处理概率问题的时候我们需要去分清哪些是客观存在的（虽然本质上来说一切都是客观存在的），或者换一种说法，哪些是我们需要去构造的，哪些是我们需要去估计的。

$$
\begin{flalign*}
\log p(x) &= \log \int_\mathcal{Z} p(x|z)p(z) dz \\
&= \log \int_\mathcal{Z} q(z) \frac{p(x|z)p(z)}{q(z)} dz \\
&= \log E_{z\sim q(z)} [\frac{p(x|z)p(z)}{q(z)}] \\
&\ge E_{z\sim q(z)} [\log \frac{p(x|z)p(z)}{q(z)}] && \text{(Jensen inequity)} \\
&= E_{z\sim q(z)}[\log p(x|z)] - KL(q(z)\| p(z))
\end{flalign*}
$$

概率公式其实表达了数据是如何生成的，比如上面第一步，就表明了数据x是由隐变量z生成的，给定z以后，x就有一个对应的条件概率分布。

这里的$p(z)$和$p(x|z)$都是客观存在的，只是我们不知道它们到底是什么（除了上帝没人知道）。我们能做的只有两件事：猜想+统计估计。比如在VAE中，$p(z)$就是猜想的先验，一般来说是高斯分布，或者VQ-VAE里是离散词表均匀分布。对于$p(x|z)$，一般来说是用神经网络拟合的正态分布。

ELBO里最重要的一点是无中生有了一个$q(z)$出来，或者完整写出来是$q(z|x)$。

首先要接受这样一个事实：在所有可能的概率分布中，一定存在某个分布$q(z)$使得ELBO最大，只是我们不知道这个分布是什么。因此我们用神经网络去预测这个分布。

这里会涉及两个很本质的问题：我们期望神经网络预测的是什么？为什么优化ELBO就可以达到这个期望？

第一个问题：我们有两个神经网络Enc和Dec，Enc用来预测给定x以后最优的$q(z)$，Dec用来预测给定z以后x的条件概率分布。

第二个问题：这里用到一个很关键的思想——迭代。我们假设Enc已经实现了它的目标，那么优化ELBO就可以让Dec接近期望；我们假设Dec已经实现了它的目标，那么优化ELBO就可以让Enc接近期望。

## 分布的变换

这里又是一个很本质的问题：为什么ELBO的概率公式对应着数据分布的变换。

这里就必须引入ELBO的另一个推导过程：直接用等式推导，而不是用Jensen不等式推导。

$$
\begin{align*}
\log p(x) &= \int_{\mathcal{Z}} q(z)\log \frac{p(x,z)q(z)}{p(z|x)q(z)} dz \\
&= E_{z\sim q(z)}[\log \frac{q(z)}{p(z|x)}] - E_{z\sim q(z)}[\log \frac{q(z)}{p(x,z)}] \\
&= KL(q(z)\| p(z|x)) - KL(q(z)\| p(x,z))
\end{align*}
$$

等式的第一项是gap，第二项就是ELBO，于是有：

$$
ELBO = \log p(x) - KL(q(z)\| p(z|x))
$$

根据这个等式，让ELBO最大的最优的$q(z)$恰好就是$p(z|x)$。这也就意味着我们期望神经网络Enc学完以后，就是可以给定x预测z的条件概率分布（后验预测）。