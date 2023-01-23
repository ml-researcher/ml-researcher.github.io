# DENOISING DIFFUSION IMPLICIT MODELS (ICLR'21)

在[IDDPM](./IDDPM.md)里面我们介绍过了一种减少采样步骤的方法，实际上这一点可以进一步扩展，产生一个更加广义的方法，这就是DDIM。

这篇文章主要有两点需要理解：

* 加噪的过程扩展一下得到什么
* 训好的模型如何扩展到任意的T

DDIM在DDPM的基础上对这两点做了更一般的扩展，因此读这篇文章之前需要对DDPM的推导有较好的理解。

## 加噪过程扩展

DDPM里的加噪过程是$q(x_t|x_{t-1})=\mathcal{N}(x_t;\sqrt{1-\beta_t}x_{t-1},\beta_tI)$，经过推导可以得到$q(x_t|x_0)=\mathcal{N}(x_t;\sqrt{\tilde{\alpha}_t}x_0,(1-\tilde{\alpha}_t)I)$。在此基础上，根据公式$q(x_{t-1}|x_t,x_0)q(x_t|x_0)=q(x_t|x_{t-1})q(x_{t-1}|x_0)$，可以得到$q(x_{t-1}|x_t,x_0)=\mathcal{N}(x_{t-1};\frac{\sqrt{\tilde{\alpha}_{t-1}}\beta_t}{1-\tilde{\alpha}_t}x_0+\frac{\sqrt{\alpha_t}(1-\tilde{\alpha}_{t-1})}{1-\tilde{\alpha}_t}x_t,\frac{1-\tilde{\alpha}_{t-1}}{1-\tilde{\alpha}_t}\beta_tI)$。

DDIM的想法是，我们现在在不给定$q(x_t|x_{t-1})$的基础上进行推导。我们只固定下来$q(x_t|x_0)$的形式，即$q(x_t|x_0)=\mathcal{N}(x_t;\sqrt{\tilde{\alpha}_t}x_0,(1-\tilde{\alpha}_t)I)$。在此基础上推导$q(x_{t-1}|x_t,x_0)$的形式，由于我们不知道$q(x_t|x_{t-1})$，所以用待定系数法求解。设$q(x_{t-1}|x_t,x_0)=\mathcal{N}(x_{t-1};m_tx_0+n_tx_t,o_t^2I)$，我们来看一下$m_t,n_t,o_t$需要满足什么条件。

$$
\begin{align*}
x_t &= \sqrt{\tilde{\alpha}_t}x_0 + \sqrt{1-\tilde{\alpha}_t}\epsilon_1 \\
x_{t-1} &= \sqrt{\tilde{\alpha}_{t-1}}x_0 + \sqrt{1-\tilde{\alpha}_{t-1}}\epsilon_2 \\
x_{t-1} &= m_tx_0 + n_tx_t + o_t\epsilon_3 \\
    &= m_tx_0 + n_t(\sqrt{\tilde{\alpha}_t}x_0 + \sqrt{1-\tilde{\alpha}_t}\epsilon_1) + o_t\epsilon_3 \\
    &= (m_t+n_t\sqrt{\tilde{\alpha}_t})x_0 + n_t\sqrt{1-\tilde{\alpha}_t}\epsilon_1 + o_t\epsilon_3 \\
    &= (m_t+n_t\sqrt{\tilde{\alpha}_t})x_0 + \sqrt{n_t^2(1-\tilde{\alpha}_t)+o_t^2}\epsilon
\end{align*}
$$

因此有：

$$
\begin{cases}
m_t+n_t\sqrt{\tilde{\alpha}_t}=\sqrt{\tilde{\alpha}_{t-1}} \\
n_t^2(1-\tilde{\alpha}_t)+o_t^2=1-\tilde{\alpha}_{t-1}
\end{cases}
$$

解得：

$$
\begin{cases}
m_t = \sqrt{\tilde{\alpha}_{t-1}} - \sqrt{\frac{\tilde{\alpha}_t(1-\tilde{\alpha}_{t-1}-o_t^2)}{1-\tilde{\alpha}_t}} \\
n_t = \sqrt{\frac{1-\tilde{\alpha}_{t-1}-o_t^2}{1-\tilde{\alpha}_t}}
\end{cases}
$$

于是我们得到了$q(x_{t-1}|x_t,x_0)=\mathcal{N}(x_{t-1};(\sqrt{\tilde{\alpha}_{t-1}} - \sqrt{\frac{\tilde{\alpha}_t(1-\tilde{\alpha}_{t-1}-o_t^2)}{1-\tilde{\alpha}_t}})x_0+\sqrt{\frac{1-\tilde{\alpha}_{t-1}-o_t^2}{1-\tilde{\alpha}_t}}x_t,o_t^2I)$。如果代入$o_t^2=\frac{1-\tilde{\alpha}_{t-1}}{1-\tilde{\alpha}_t}(1-\frac{\tilde{\alpha}_t}{\tilde{\alpha}_{t-1}})$，就会得到DDPM的结果。

继续推导，可以得到$p_\theta(x_{t-1}|x_t)\approx q(x_{t-1}|x_t,x_0=\frac{1}{\sqrt{\tilde{\alpha}_t}}(x_t-\sqrt{1-\tilde{\alpha}_t}\epsilon_\theta)) = \mathcal{N}(x_{t-1};\frac{1}{\sqrt{\alpha_t}}(x_t-(\sqrt{1-\tilde{\alpha}_t}-\sqrt{\alpha_t(1-\tilde{\alpha}_{t-1}-o_t^2)})\epsilon_\theta),o_t^2I)$。其中，$\epsilon_\theta$是神经网络的预测输出，$\alpha_t=\frac{\tilde{\alpha}_t}{\tilde{\alpha}_{t-1}}$。这样，训练和预测过程都确定了。

## 扩展到任意时间T

给定一个$\tilde{\alpha}_1,\tilde{\alpha}_2,\dots,\tilde{\alpha}_T$序列，就可以得到对应的训练过程，实际上，训练得到的模型包括该序列的任意子序列对应的训练目标。实际操作的时候，为了方便常取前$k$个用于推理。

## 参考资料

* https://kexue.fm/archives/9181