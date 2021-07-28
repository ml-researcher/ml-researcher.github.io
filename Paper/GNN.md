# GCN的数学原理详解——谱图理论和傅立叶变换初探

这是一篇试图通过数学的逻辑推导来理解GCN的文章，虽然有一些操作确实只是empirical的，不过大部分还是可以用逻辑串起来的。

这份文字版只是我做视频的大纲，如果要看完整的讲解可以移步b站视频：https://www.bilibili.com/video/BV1Vw411R7Fj

## GCN简介

GNN：$H^{(l+1)} = f(A,H^{(l)})$。

## Spectral Graph Theory

谱图理论其实就是在研究与图邻接矩阵A相关的一些矩阵与给定feature相乘产生的一些性质。

这里我们只考虑两个矩阵：$L=D-A$和$L_{\text{sym}}=D^{-\frac{1}{2}}LD^{-\frac{1}{2}}$。当然完整的谱图理论会涉及到不止这两个矩阵，但是这里我们研究GCN相关的就先用这两个就够了。而且这两个都有很好的性质：都是半正定的实对称阵。（无向图）

我们定义$A_{\text{sym}}=D^{-\frac{1}{2}}AD^{-\frac{1}{2}}$，那么$L_{\text{sym}}=I-A_{\text{sym}}$。


* 实对称阵的一些性质
    * 有n个实数特征值
    * 特征向量之间正交

我们可以证明，L和Lsym都是半正定的。

证明很简单：$L=\sum_{(i,j)\in E}G_{(i,j)}$。顺着就推出来了。

需要知道xLx的结果，以及特征值和二次型之间的关系。

我们考虑Rayleigh quotient，$\frac{x^T M x}{x^Tx}$，x关于M的二次型和x关于I的二次型的比值。如果x是M的特征向量，那么有：

$$\frac{x^T M x}{x^Tx}=\frac{x^T (\lambda x)}{x^Tx}=\frac{\lambda x^Tx}{x^Tx} = \lambda$$

（Courant-Fischer Theorem这个暂时好像用不到，先不管了）另外还有很重要的一点，最小的特征值就是这个二次型的最小值（仍然限定x是单位向量），最大的特征值就是这个二次型的最大值。

现在要证明$0\le \lambda_1^{\text{(sym)}} \le \lambda_2^{\text{(sym)}} \le...\le\lambda_n^{\text{(sym)}} \le 2$。

所以我们只要证明瑞雷熵整个都在这个范围内，特征值自然也就在这个范围内了。

这里会用到两个辅助的矩阵$L_{\text{pos}}=D+A$和$L_{\text{pos-sym}}=I+A_{\text{sym}}$。我们证明这个矩阵也是半正定的，就可以证明$L_{\text{sym}}$的特征值不超过2了。





## Fourier Transformation

首先，什么是傅立叶变换。

* FFT
* 拟合一个函数（音波）

那么接下来，图的傅立叶变换怎么理解？仍然是域之间的变换！

需要知道Lx的结果是什么。我们又已知L是实对称阵，所以可以分解。


## GCN

首先，我们要定义频域上的卷积操作。

我们已经知道了，拉普拉斯矩阵与某个x相乘以后，就可以达到聚合邻居的效果，即

$$(Lx)_i=\sum_{(i,j)\in E}(x_i-x_j)$$

而我们又知道$L=U\Lambda U^T$。

$$g_{\theta}\star x = U g_\theta(\Lambda) U^T x$$


为什么这样是频域上的卷积操作，需要在上一节傅立叶变换补充intuition。另外为什么L可以分解，需要在上上一节补充谱图理论的知识。

ChebyNet，将$g_\theta(\Lambda)$限定在多项式函数里。（这里思考一个问题，为什么不用普通的多项式？——为了防止梯度爆炸。）

$$g_\theta(\Lambda)=\sum_{k=0}^{K}\theta_kT_k(\Lambda)$$

接下来就可以对我们上面定义的卷积进行推导变形了

$$\begin{align*}

g_{\theta}\star x &= U g_\theta(\Lambda) U^T x \\

&=U \left( \sum_{k=0}^{K}\theta_kT_k(\Lambda) \right) U^T x \\

&=\sum_{k=0}^K \theta_k U T_k(\Lambda) U^T x \\

&=\sum_{k=0}^K \theta_k T_k(U\Lambda U^T) x \\

&=\sum_{k=0}^K \theta_k T_k(L) x

\end{align*}$$

这里存在一个问题就是关于$\Lambda$限制在-1到1之间，这个操作需要理解normalized L他的特征值的范围。

其实讲到这里我们就会发现，任何一个特征值在-1到1之间的实对称阵都可以做，为什么偏偏是L呢？答案是没有限制！你如果能尝试出来一个比这个L效果好的矩阵，你也可以发一篇论文了。