---
title: 'FEDERATED LEARNING: STRATEGIES FOR IMPROVING COMMUNICATION EFFICIENCY'
date: 2019-04-19 02:49:47
tags: [paper reading]
category: [论文阅读]
---
# 联合学习：提高通信效率的策略

# 摘要
<!--more-->
联合学习是一种机器学习设置，其目标是培训高质量的集中模型，同时培训数据仍然分布在大量客户端上，每个客户端都具有不可靠且相对较慢的网络连接。我们考虑针对此设置的学习算法，其中在每一轮中，每个客户端独立地基于其本地数据计算对当前模型的更新，并将该更新传送到中央服务器，其中聚合客户端更新以计算新的全局模式。此设置中的典型客户端是移动电话，通信效率至关重要。
在本文中，我们提出了两种降低上行链路通信成本的方法：**结构化更新，我们直接从使用较少数量的变量参数化的受限空间中学习更新**，例如：低级别或随机掩码;另外是sketch update，我们学习完整的模型更新，然后在将其发送到服务器之前使用**量化，随机旋转和子采样的组合进行压缩**。在卷积网络和循环网络上的实验表明，所提出的方法可以将通信成本降低两个数量级。

# 1 Introduction
随着数据集变得越来越大，模型越来越复杂，训练机器学习模型越来越需要在多台机器上分配模型参数的优化。现有的机器学习算法专为高度受控的环境（如数据中心）而设计，在这些环境中，数据以均衡的方式在i.i.d.中分布。时尚和高吞吐量网络可用。

最近，联邦学习（以及相关的分散方法）（McMahan＆Ramage，2017; Konecn'y等，2016; McMahan等，2017; Shokri＆Shmatikov，2015）已​​被提议作为替代设置：**共享全球模型在中央服务器的协调下训练，来自参与设备的联合**。参与设备（客户端）通常数量很大并且具有缓慢或不稳定的互联网连接。当培训数据来自用户与移动应用程序的交互时，就会出现联合学习的主要动机示例。联合学习使移动电话能够协作学习共享预测模型，同时将所有培训数据保留在设备上，从而将机器学习的能力与在云中存储数据的需求脱钩。训练数据在本地保存在用户的移动设备上，并且设备用作对其本地数据执行计算的节点，以便更新全局模型。通过对设备进行模型培训，这不仅仅是使用可以在移动设备上进行预测的本地模型。上述框架不同于传统的分布式机器学习（Reddi等，2016; Ma等，2017; Shamir等，2014; Zhang＆Lin，2015; Dean等，2012; Chilimbi等，2014 ）由于客户数量非常多，高度不平衡和非iid每个客户端上可用的数据，以及相对较差的网络连接。在这项工作中，我们的重点是最后一个约束，因为这些不可靠和不对称的连接对实际的联邦学习提出了特殊的挑战。

为简单起见，我们考虑联合学习的同步算法，其中典型的一轮包括以下步骤：
1.选择现有客户端的子集，每个客户端都下载当前模型。 
2.子集中的每个客户端都根据其本地数据计算更新的模型。 
3.模型更新从选定的客户端发送到服务器。 
4.服务器聚合这些模型（通常通过平均）来构建改进的全局模型。

上述框架的简单实现要求每个客户端在每轮中将完整模型（或完整模型更新）发送回服务器。对于大型模型，由于多种因素，这一步骤很可能成为联邦学习的瓶颈。一个因素是互联网连接速度的不对称属性：上行链路通常比下行链路慢得多。美国平均宽带速度下载为55.0Mbps，而上传速度为18.9Mbps，一些互联网服务提供商明显更加不对称，例如，X网络速率低于125Mbps，低于15Mbps（speedtest.net，2016）。另外，现有的模型压缩方案如Han等人。 （2015）可以减少下载当前模型所需的带宽，并建立加密协议，以确保在平均数百或数千个其他更新（Bonawitz等，2017）之前不会检查个别客户的更新（进一步增加）需要上传的位数。

因此，研究可以降低上行链路通信成本的方法很重要。在本文中，我们研究了两种通用方法：
•结构化更新，我们直接从受限空间学习更新，可以使用较少数量的变量进行参数化。
•素描更新，我们学习完整模型更新，然后在发送到服务器之前对其进行压缩。

在第2节和第3节中详细解释的这些方法可以组合，例如，首先学习结构化更新并绘制草图;我们不会在这项工作中尝试这种组合。

在下文中，我们正式描述了这个问题。联邦学习的目标是从存储在大量客户端的数据中学习具有包含在实矩阵中的参数的模型1W∈Rd1×d 2。我们首先提供了一个未通信的联邦学习版本。在t≥0的轮次中，服务器将当前模型W t分配给n t个客户端的子集S t。这些客户端根据其本地数据独立更新模型。让更新的局部模型为W t 1，W t 2，.... 。 。 ，W t n t，因此对于i∈St，客户i的更新可写为H t i：= W t i -W t。这些更新可以是在客户端上计算的单个梯度，但通常是更复杂计算的结果，例如，在客户端的本地数据集上采用的多步骤随机梯度下降（SGD）。在任何情况下，每个选定的客户端然后将更新发送回服务器，其中通过聚合所有客户端更新来计算全局更新：


# 2. STRUCTURED UPDATE

第一种类型的通信有效更新限制了更新H t i具有预先指定的结构。本文考虑了两种类型的结构：低秩和随机掩模。重要的是要强调我们直接训练这种结构的更新，而不是用一个特定结构的对象来近似/描绘一般更新 - 这将在第3节中讨论。

低等级。我们将对局部模型H ti∈Rd 1×d 2的每次更新强制为最多为k的秩的低秩矩阵，其中k是固定数。为此，我们将H t i表示为两个矩阵的乘积：H t i = A t i B t i，其中A ti∈Rd 1×k，B ti∈Rk×d 2。在随后的计算中，我们随机生成A t i并在局部训练过程中考虑常数，并且我们仅优化B t i。注意，在实际实现中，在这种情况下，可以以随机种子的形式压缩，并且客户端仅需要将训练的B t i发送到服务器。这种方法立即在通信中节省了d 1 / k的因子。我们在每轮中重新生成矩阵A t i，并为每个客户端独立生成矩阵。

我们还尝试了固定B t i和训练A t i，以及训练A t i和B t i;也没有表现得那么好。我们的方法似乎与Denil等人所考虑的最佳技术一样。 （2013年），无需任何手工制作的功能。这种观察的直观解释如下。我们可以将B t i解释为投影矩阵，并将A t i解释为重建矩阵。修复A t i并优化B t i类似于询问“给定给定的随机重建，什么是将恢复大多数信息的投影？”。在这种情况下，如果重建是全等级，则存在恢复由前k个特征向量跨越的空间的投影。然而，如果我们随机地对投影进行搜索并搜索重建，我们可能是不幸的，并且可能已经投射出重要的子空间，这意味着没有尽可能好的重建，或者很难找到。

随机面具。我们将更新H t i限制为稀疏矩阵，遵循预定义的随机稀疏模式（即，随机掩码）。在每轮中重新生成模式并且为每个客户端独立地生成模式。与低秩方法类似，稀疏模式可以由随机种子完全指定，因此仅需要发送H t i的非零条目的值以及种子。

# 3. SKETCHED UPDATE
第二种类型的更新解决了通信成本，我们称之为草绘，首先在没有任何约束的情况下计算本地训练期间的全部H t i，然后在发送到服务器之前以（有损）压缩形式对更新进行近似或编码。服务器在进行聚合之前解码更新。这种草绘方法在许多领域都有应用（Woodruff，2014）。我们尝试使用多个工具来执行草图绘制，它们是相互兼容的，可以联合使用：

子采样。每个客户端仅传送矩阵H，而不是发送H ti，矩阵H由Hti的（缩放的）值的随机子集形成。然后，服务器对二次采样的更新进行平均，产生全局更新H t。这可以这样做，使得采样更新的平均值是真实平均值的无偏估计：E [H t] = H t。与随机掩码结构更新类似，掩码针对每一轮中的每个客户端独立随机化，并且掩码本身可以存储为同步种子。

概率量化。压缩更新的另一种方法是量化权重。
我们首先描述了将每个标量量化为一位的算法。考虑更新Hti，令h =（h 1，...，h d 1×d 2）= vec（H t i），并且令h max = max j（h j），h min = min j（h j）。由~h表示的h的压缩更新生成如下：
