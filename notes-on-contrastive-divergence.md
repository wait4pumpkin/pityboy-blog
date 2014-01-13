### 参考资料

Oliver Woodford：Notes on Contrastive Divergence

CD（Contrastive Divergence）算法由Geoffrey Hinton提出，用于求ML（Maximum-Likelihood）问题的近似解。

### 问题描述

假设模型为`$f(x;\Theta)$`，`$\Theta$`为模型参数，则`$x$`的概率可以表示为

```mathjax
\begin{equation}
p(x;\Theta) = \frac{1}{Z(\Theta)} f(x;\Theta), \label{equ:prob}
\end{equation}
```

其中`$Z(\Theta)$`是归一化函数，因为概率和等于1。

假设训练集是`${x_1, x_2, ..., x_K}$`，根据最大似然原则，要使得训练集的联合概率最大，即最大化

```mathjax
\begin{equation}
p(X; \Theta) = \prod_{k=1}^{K} \frac{1}{Z(\Theta)}f(x_k; \Theta). \label{equ:max}
\end{equation}
```

若对上式取负对数，则问题就相当于最小化能量

```mathjax
\begin{equation}
E(X; \Theta) = \log(Z(\Theta)) - \frac{1}{K} \sum_{k=1}^{K} \log f(x_k; \Theta). \label{equ:energy}
\end{equation}
```

这就是ML问题的描述，下面以几个例子说明如何求解。

### ML问题求解

假如模型为单高斯分布，即`$f(x;\Theta)\sim N(x;\mu,\delta)$`，则`$\Theta=\{\mu,\delta\}$`. 显然，此时`$Z(\Theta)=1$`。分别对（`$\ref{equ:energy}$`）求`$\mu$`和`$\delta$`求偏导，由导数等于零可以求出解析解，概率统计或者统计信号的书都会有详细的求解。

若模型为多个高斯分布的和（混合高斯模型），即`$f(x;\Theta)=\sum_i^N N(x;\mu_i,\delta_i)$`. 此时，`$Z(\Theta)=N$`. 同样可以通过求偏导得出解析解，但是模型参数之间是互相依赖的（偏导中包含了其他参数），并不能简单地通过导数等于零解出参数。这时候可以用GD（Gradient Descent）和Line Search找到局部最小点。

若模型为多个高斯分布的乘积，即`$f(x;\Theta)=\prod_{i=1}^N N(x;\mu_i,\delta_i)$`. 此时`$Z(\Theta)$`不再是常数（取几个特殊指即可知）。因此能量值只能通过积分计算，然后再通过GD寻找最小值。但对于高维的数据或者参数空间来说，计算这个积分显然是相当耗时的。CD算法的作用就在于此，即使对于无法准确计算的能量函数，CD也可以提供有效的梯度计算方法，用于求解模型参数。

### CD算法原理

对能量函数（`$\ref{equ:energy}$`）求偏导

```mathjax
\begin{equation}
\begin{aligned}
\frac{\partial E(X;\Theta)}{\partial \Theta} &= \frac{\partial Z(\Theta)}{\partial \Theta} - \frac{1}{K} \sum_{i=1}^K \frac{\partial \log f(x_i; \Theta)}{\partial \Theta} \\ 
&= \frac{\partial Z(\Theta)}{\partial \Theta} - \left \langle \frac{\partial \log f(x_i; \Theta)}{\partial \Theta} \right \rangle_X, 
\end{aligned}
\end{equation}
\label{equ:grad}
```

其中，最后一项即为给定`$X$`的期望值，该项显然是可以直接求出的，因为`$X$`是给定的训练集。而第一项可以进一步展开，

```mathjax
\begin{equation}
\begin{aligned} 
\frac{\partial \log Z(\Theta)}{\partial \Theta} &= \frac{1}{Z(\Theta)} \frac{\partial Z(\Theta)}{\partial \Theta} \\ 
&= \frac{1}{Z(\Theta)} \frac{\partial}{\partial \Theta} \int f(x;\Theta) dx \\ 
&= \frac{1}{Z(\Theta)} \int \frac {\partial f(x;\Theta)}{\partial \Theta} dx \\ 
&= \frac{1}{Z(\Theta)} \int f(x;\Theta) \frac {\partial \log f(x;\Theta)}{\partial \Theta} dx \\ 
&= \int p(x;\Theta) \frac {\partial \log f(x;\Theta)}{\partial \Theta} dx \\ 
&= \left \langle \frac {\partial \log f(x;\Theta)}{\partial \Theta} \right \rangle_{p(x;\Theta)} 
\label{equ:compute}
\end{aligned}
\end{equation}
```

即分布`$p(x;\Theta)$`的期望值。但该分布的样本无法直接获取，因为`$Z(\Theta)$`无法直接计算，但可以通过MCMC（Markov Chain Monte Carlo）采样，把训练集的样本转换为指定的分布。关于MCMC和Gibbs Sampling可参阅统计之都的文章，[LDA-math-MCMC 和 Gibbs Sampling](http://cos.name/2013/01/lda-math-mcmc-and-gibbs-sampling/)

假设`$X^{n}$`是MCMC的第`$n$`步转换，`$X^0$`是输入训练集，则`$X^{\infty}$`即为所需分布`$p(x;\Theta)$`. 综上所述，即可得

```mathjax
\begin{equation}
\frac{\partial E(X;\Theta)}{\partial \Theta} = \left \langle \frac {\partial \log f(x;\Theta)}{\partial \Theta} \right \rangle_{X^\infty} - \left \langle \frac {\partial \log f(x;\Theta)}{\partial \Theta} \right \rangle_{X^0}
\end{equation}
```

实际使用中MCMC并不需要无限步，因为随着不断迭代，输入数据的分布与目标分布会越来越接近。Hinton进一步指出，即使每次只做一步MCMC，ML问题同样可以收敛。因此，使用Gradient Descent，每一次迭代，参数更新为

```mathjax
\begin{equation}
\Theta_{t+1} = \Theta_t - \alpha (\left \langle \frac {\partial \log f(x;\Theta)}{\partial \Theta} \right \rangle_{X^1} - \left \langle \frac {\partial \log f(x;\Theta)}{\partial \Theta} \right \rangle_{X^0})
\end{equation}
```

其中，`$\alpha$`是GD的步长。

