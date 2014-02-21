## Replicated Softmax Model

Replicated Softmax model, 实质上是一个共享参数的RBM (Restricated Boltzmann Machine)，Replicated指的就是参数共享，Softmax指的是判别模型。

假设字典大小为$K$；文档中的词数为$N$；隐藏单元的为${\bf h} \in \{0, 1\}^F$；输入矩阵为${\bf V} \in \{0, 1\}^{N \times K}$，当第$i$个词和字典第$k$个词相等时，$v_{ik} = 1$. 

和一般的RBM相同，定义模型的能量，
$$
\begin{equation}
E({\bf V, h; \boldsymbol \theta}) = -\sum_{i=1}^N \sum_{j=1}^F \sum_{k=1}^K W_{ijk} h_j v_{ik} - \sum_{i=1}^N \sum_{k=1}^K v_{ik} b_{ik} - N \sum_{j=1}^F h_j a_j, 
\end{equation}
$$
其中$\bf \boldsymbol \theta = \{W, a, b\}$是模型参数。则输入$\bf V$的概率为
$$
\begin{equation}
P(\bf{V; \boldsymbol \theta}) = \frac{1}{Z(\boldsymbol \theta, N)} \sum_{\bf h} \exp (-E({\bf V, h; \boldsymbol \theta})), 
\end{equation}
$$
其中$Z(\boldsymbol \theta)$是归一化常数。

Replicated Softmax model中输入单元数和文档中的词数相同，并假设词序可忽略，同时使用共享参数的方式连接$\bf V$和$\bf h$，如图所示。参数共享，即$W_{ijk}$与$i$无关，能量函数可以化简为，
$$
E({\bf V, h}) = -\sum_{j=1}^F \sum_{k=1}^K W_{jk} h_j \hat{v}_k - \sum_{k=1}^K \hat{v_k} b_k - N \sum_{j=1}^F h_j a_j, 
$$
其中$\hat{v}_k = \sum_{i=1}^N v_i^k$。最后一项的$N$是一般RBM没有的，$N$的作用是使模型可以适用于不同长度的文档，因为输入单元的数量是与文档词数相等的，而隐藏单元数量是固定的。

模型的最终输出使用Softmax判决，
$$
\begin{equation}
P(h_j^{(1)} = 1) = \sigma (\sum_{k=1}^K W_{jk} \hat{v}_k + N a_j). 
\end{equation} 
$$
假如把输入单元看成是一个$K$项多项式，采样$N$次，则模型的输入是向量，而不是矩阵，可以看成是一个普通的RBM，可以使用CD算法训练。


## Over-Replicated Softmax Model 建模

Over-Replicated Softmax model是含有两个隐含层的DBM (Deep Boltzmann Machine)，也就是在Replicated Softmax model的基础上增加了深度，同时也使用了参数共享，但没有增加新的参数，即所谓Over-Replicated. 

如图所示，输入层$\bf V$是$N$个$K$维的Softmax单元，第二层隐含层$\bf H^{(2)}$是$M$个$K$维的Softmax单元。一个文档可以理解为由$(M + N)$个词组成，但只能够观测到$N$个词，即$\bf V$，余下的$M$个词即$\bf H^{(2)}$. 

DBM的能量函数定义为，
$$
\begin{equation}
\begin{aligned}
E({\bf V, h^{(1)}, H^{(2)}; \boldsymbol \theta}) = &-\sum_{i=1}^N \sum_{j=1}^F \sum_{k=1}^K W_{ijk}^{(1)} h_j^{(1)} v_{ik} - \sum_{i^\prime = 1}^M \sum_{j=1}^F \sum_{k=1}^K W_{i^\prime jk}^{(2)} h_j^{(1)} h_{i^\prime k}^{(2)} \\
&- \sum_{i=1}^N \sum_{k=1}^K v_{ik} b_{ik}^{(1)} - (M + N) \sum_{j=1}^F h_j^{(1)} a_j - \sum_{i=1}^M \sum_{k=1}^K h_{ik}^{(2)} b_{ik}^{(2)}, 
\end{aligned}
\end{equation}
$$
其中$\boldsymbol \theta = \{ \bf W^{(1)}, W^{(2)}, a, b^{(1)}, b^{(2)} \}$是模型参数，第一层隐含层即判决层的bias的系数$(M + N)$同样是为了适应不同长度的文档。和Replicated Softmax model相同，输入层的单元数$N$也是随着文档词数而改变的，但$M$对所有文档保持不变。同时$\bf V$和$\bf h^{(1)}$的连接也是采用参数共享的方式。不仅如此，输入层和第一层隐含层，与第一层和第二层隐含层的连接参数是相同的，即$W_{ijk}^{(1)} = W_{i^\prime jk}^{(2)} = W_{jk}, b_{ik}^{(1)} = b_{i^\prime k}^{(1)} = b_k. $则能量函数可以化简为，
$$
\begin{equation}
\begin{aligned}
E({\bf V, h^{(1)}, H^{(2)}; \boldsymbol \theta}) = &-\sum_{j=1}^F \sum_{k=1}^K W_{jk} h_j^{(1)} (\hat{v}_k + \hat{h}_k^{(2)}) \\
&-\sum_{k=1}^K (\hat{v}_k + \hat{h}_k^{(2)}) b_k - (M + N) \sum_{j=1}^F h_j^{(1)} a_j, 
\end{aligned}
\end{equation}
$$
其中$\hat{v}_k = \sum_{i=1}^N v_{ik}, \hat{h}_k^{(2)} = \sum_{i=1}^M h_{ik}^{(2)}$. 联合概率分布定义为，
$$
\begin{equation}
P({\bf V, h^{(1)}, H^{(2)}}; \boldsymbol \theta) = \frac{\exp (-E({\bf V, h^{(1)}, H^{(2)}; \boldsymbol \theta}))}{Z(\boldsymbol \theta, N)}. 
\end{equation}
$$

## Over-Replicated Softmax Model 学习