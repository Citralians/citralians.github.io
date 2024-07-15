# Towards Efficient Asynchronous Federated Learning in Heterogeneous Edge Environments 面向异构边缘环境的高效异步联邦学习

貌似是师姐的论文。source在[一个学术会议的官网](https://infocom.info/day/3/track/Track%20E)上，不过不让下载，只能看摘要。
这个标题就抛出来了一堆概念：

- 联邦学习：采用类似client-server的架构，但是server(global model)不会直接接触到client的数据，而是通过client的模型在本地进行训练，然后再将模型参数的更新传回全局模型。

- **异步联邦学习**：在联邦学习的基础上，每一个client都可以在任意时间点进行模型的更新，而不需要等待其他client都跑完。

- 异构边缘环境：边缘环境是指“数据计算更靠近网络边缘/数据源头的计算环境”。听上去有点绕，简而言之约等于上述架构中的client。而“异构”则是指各个client的数据组织方式/硬件/软件环境可能都不一样，最终导致的最主要问题就是迭代速度不一致。

而这篇文章的主要贡献就是提出了一种异步联邦学习的方法，可以在异构边缘环境中进行高效的模型训练。

## 摘要

- 在过去，对待异构边缘环境的方法主要是“陈旧性察觉”（原文为staleness awareness，我瞎翻的）机制：异构环境的收敛速度会不一致，那些收敛得比较慢的client整体贡献权重会降低，以减轻过时数据的影响；但这么做的问题是，这些client的输出相当于被部分忽略了，这对它们各自的数据源以及算力而言都是一种浪费。
- 这篇paper的解决方案是：
  1. 聚集，根据梯度相似度来把client里尽可能同构的模型分为相同的组；
  2. 基于组进行两步的半异步联邦学习，分别是基于“陈旧性察觉”的组间聚集和基于数据相似度的组内聚集。

## Introduction

涉及到的概念：

- 联邦学习、异构边缘环境：前述
- Not Identically and Independently Distributed (Non-IID) data：数据分布不同，即各个client中的数据异构。
- 半异步联邦学习：在异步联邦学习的基础上，每个client的更新不是完全独立的，而是根据一定的规则进行的。每一轮训练，server会选取k个用时最短的client发送来的参数进行模型更新，并将更新后的server模型参数广播给它们。
- 陈旧性察觉：在异步联邦学习中，有可能某个client的训练是基于很久以前更新的global model（换言之就是训练速度太慢，别人都开始做后面的题了它还在那做第1题），这样的数据是过时的，会影响整体的训练效果。

## Related Work
- system heterogeneity: 一般来说，异构系统的性能会受到最慢的那个client的影响，因此需要一种方法来平衡各个client的训练速度。
- data heterogeneity: 只说了non-IID data会导致local model drifting：随着训练的进行，各个client的模型与全局模型的差距会越来越大，这对于全局模型的性能有负面影响。但没说为什么，推测是因为数据分布不同导致的。一些解决方案是：1.先用本地数据“预热”一下全局模型（以较小的学习率进行训练）；2.根据不同client的数据特征建立通用的dataset。
- cluster-based FL: 一种解决异构系统的方法，将client分为不同的cluster，然后在cluster内进行模型的更新，最后再将cluster的结果合并。这种方法的问题是，cluster的划分可能会导致一些client的数据被忽略。

## System Model
用的clustering，每个cluster有一个head，head负责聚合类内数据以及和server通信。

后面讲的都是这篇论文的研究成果，没细看