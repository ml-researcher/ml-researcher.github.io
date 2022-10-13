# Diffusion Model

机器学习问题的解决一般都要引入人工给定的先验条件，扩散模型也不例外。

第一个假设，$q(x_t|x_{t-1})=\mathcal{N}(x_t;\alpha_tx_{t-1},\beta_t^2I)$，其中$\alpha_t^2+\beta_t^2=1$。用重参数的技巧表示就是$x_t=\alpha_t x_{t-1}+\beta_t\epsilon_{t-1}$，$\epsilon_{t-1}\sim\mathcal{N}(0,I)$。

第二个假设，马尔可夫假设。$q(x_{1:T}|x_0)=\prod_{t=1}^T q(x_t|x_{t-1})$。基于这个假设可以得到：

$$
\begin{align*}
x_t &= \alpha_tx_{t-1}+\beta_t\epsilon_{t-1} \\
&= \alpha_t(\alpha_{t-1}x_{t-2}+\beta_{t-1}\epsilon_{t-2}) + \beta_t\epsilon_{t-1} \\
&= \dots \\
&= \alpha_t\alpha_{t-1}\dots\alpha_1 x_0 + \alpha_t\alpha_{t-1}\dots\alpha_2\beta_1\epsilon_0 + \alpha_t\alpha_{t-1}\dots\alpha_3\beta_2\epsilon_1 \\
&+ \dots + \alpha_t\beta_{t-1}\epsilon_{t-2} + \beta_t\epsilon_{t-1} \\
&= \alpha_t\alpha_{t-1}\dots\alpha_1 x_0 + \sqrt{1-(\alpha_t\alpha_{t-1}\dots\alpha_1)^2}\epsilon
\end{align*}
$$

其中，$\epsilon\sim \mathcal{N}(0, I)$。令$\bar\alpha_t =\alpha_t\alpha_{t-1}\dots\alpha_1$，则有$q(x_t|x_0) = \mathcal{N}(x_t;\bar\alpha_tx_0, (1-\bar\alpha_t^2)I)$。

基于上面的结果我们来求$q(x_{t-1}|x_t,x_0)$。根据贝叶斯公式，

$$
\begin{align*}
q(x_{t-1}|x_t,x_0)&=\frac{q(x_t|x_{t-1},x_0)q(x_{t-1}|x_0)}{q(x_t|x_0)}\\
&= \frac{q(x_t|x_{t-1})q(x_{t-1}|x_0)}{q(x_t|x_0)} \\
&= \frac{\mathcal{N}(x_t;\alpha_tx_{t-1},(1-\alpha_t^2)I)\mathcal{N}(x_{t-1};\bar\alpha_{t-1}x_0, (1-\bar\alpha_{t-1}^2)I)}{\mathcal{N}(x_t;\bar\alpha_tx_0, (1-\bar\alpha_t^2)I)}
\end{align*}
$$

(To be continued...)