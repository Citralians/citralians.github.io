# BASGD: Buffered Asynchronous SGD for Byzantine Learning 用于拜占庭学习的缓冲异步SGD

pdf source [here](https://arxiv.org/pdf/2003.00937).

- SGD: Stochastic Gradient Descent 随机梯度下降，是一种通过迭代的方式来最小化损失函数的优化算法。
- 拜占庭学习：此处与《计网》中的拜占庭将军问题一致，即在分布式系统中，有一部分节点是恶意的，它们会发送错误的信息，可能导致整个系统无法正常工作。拜占庭学习就是在这种情况下的机器学习问题。
- ABL: Asynchronous Byzantine Learning 异步拜占庭学习，客户端不需要在同一时间提交模型更新。
- BASGD: 此篇文章中新提出的抵抗拜占庭攻击的算法。

## 1. Introduction

现有的两种主要SBL方式：

- 将aggregate方式由简单平均替换为鲁棒性更强的方式，如中位数、trimmed mean Krum（基于欧氏距离的客户端可信度分析）、ByzantinePGD等。
- 在aggregate时筛选出可能的恶意梯度更新（离群值）并将其剔除。

SBL由于强制同步提交，对客户端异构的场景并不友好。

能解决上述问题的ABL的主流方式也有两种：

- Kardam：用两个filter来过滤可能的恶意梯度。不过在某些情况下它反而会留下所有恶意梯度，转而抛弃正常的梯度。
- Zeno++：使用存储在server上的干净训练样本辅助判断，但有隐私泄露风险，而且与FL本身的目标背道而驰。

《[FLTrust: Byzantine-robust Federated Learning via Trust Bootstrapping](https://arxiv.org/pdf/2012.13995v1)》这篇文章中提到，引发Kardam中的问题、以及Zeno++使用干净数据集辅助判断能缓解这一问题的原因是：**全局模型其实无从判断哪些梯度是恶意的**。因为在data-free的情况下，从客户端上传的更新就是全局模型已知的全部信息：它们只包含梯度信息而不包含原始数据，全局模型无法在这些经过处理的梯度与实际任务之间建立联系，进而无法先验地得知正常更新应该具有什么特征。另一方面，“符合多数客户端更新方向的梯度”并不一定就是正确的梯度，在较坏的情况下，攻击者可能控制大量客户端并让它们协同作弊，或者谎称拥有大量训练样本以增加权重，使得全局模型仍然收敛到错误的方向。

因此现在的主流方法大多需要一个先验的干净或毒化数据集作为标杆，以让全局模型在开始aggregate之前先知道正常梯度或有毒梯度的特征，从而有效地筛选掉毒化更新。

## 2. Preliminary

### 2.1 Distributed Learning Framework

许多机器学习模型，如逻辑回归和深度神经网络，可以被表述为以下的有限和优化（infinite sum optimization）问题：

$$
\min_{w \in \mathbb{R}^d} f(w) = \frac{1}{n} \sum_{i=1}^n f(w; z_i)
$$

其中 $w$ 是模型参数，$d$ 是参数维度，$n$ 是数据集大小，$f(w; z_i)$ 是基于 $z_i$ 的经验损失函数（应该就是损失函数，只不过换了个叫法。见[这里](https://www.zhihu.com/question/426518849)）。

PS(Parameter-Server) Framework： 参数服务器框架，有可能存在一个以上的server，而本文中貌似简略地将所有server视作了一个整体。server无法直接访问训练样本，只能接受来自客户端的梯度更新。每个客户端拥有的训练样本被视作相互独立（即便其中可能有一些完全一致的数据）。

在这种框架下，一个主流的方法是使用ASGD（Asynchronous Stochastic Gradient Descent）算法。在PS based ASGD中，服务器负责更新和维护最新的参数。服务器已经执行的迭代次数被用作服务器的全局逻辑时钟。在开始时，迭代次数为 t = 0。每次执行SGD步骤时，$t$ 会立即增加1。t次迭代后的参数记为 $w_t$。如果服务器在迭代 $t_0$ 时将参数发送给 worker $k$，那么在服务器下次在迭代 $t$ 时接收到 worker $k$ 的梯度之前，可能已经执行了一些SGD步骤。因此，我们定义 worker $k$ 在迭代 $t$ 时的延迟为 $\tau_t^k$ = t - t0。如果 $\tau_t^k$ > $\tau_{max}$ ，则 worker $k$ 在迭代 $t$ 时延迟严重，其中 $\tau_{max}$ 是一个预定义的非负常数。

### 2.2 Byzantine Worker

算法写的乱七八糟的，回头再抽时间看一下（而且貌似有好多种表述，只不过算法给的是主流表述之一）。比较high-level的表述是，在规定时间内向服务器提交了正确的更新、且被服务器收到的客户端称为正常客户端，否则称为拜占庭客户端。拜占庭客户端可能会发送错误的梯度更新，或者根本不发送梯度更新。