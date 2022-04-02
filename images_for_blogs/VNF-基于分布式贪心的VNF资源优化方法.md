# VNF-基于分布式贪心的VNF资源优化方法

## Title：分布式的VNF Chain Deployment/orchestration  with 资源重用在边缘网络

**备选题目**：**Distributed virtual network function chain orchestration with resource reuse/Guarantees for 5G Edge Networks**

Distributed VNF Chain Deployment with resource reuse at Network Edge

**Key words**: Service Function Chains, Distributed Service Orchestration, Resource Reuse, Network Edge，5G 



## 1、**Introduction**
**NFV**：为了在减少资本和运营成本的同时提供灵活的服务，网络功能虚拟化(NFV)的技术已经被服务提供商广泛提倡。在 MEC 环境下，可以在网络边缘和后端云(核心网)使用虚拟网络功能(VNFs)灵活地提供网络服务，同时以按需方式为用户服务。

**NFV特点**：作为 NFV 中的一个典型应用，网络服务链NFS通常由==一系列有序==的 VNFs 组成。在实际应用中，VNFs 不仅可以被不同的==服务需求共享（引出本文idea—资源重用）==，而且可以部署在不同的网络设备上。因此，计算和带宽资源都可以得到有效利用。

**挑战**：

* 服务功能VNFs程序异构性（VNF具有不同的==资源消耗==，因此需要考虑到VNF部署的计算资源，存储资源以及服务流路由占用的通信资源）
* 大规模边缘网络的系统复杂性，网络环境的高度动态性（集中式算法复杂度高，执行的延迟大，因此需要==分布式==网络功能链编排方案以提高网络运行效率）
* 边缘资源的有限性（网络资源受限，VNFs 可以部署在不同的网络设备上且可以被不同的服务需求—服务链请求共享，因此可以考虑资源重用的服务功能链编排方案以降低边缘网络的资源消耗，尽可能地满足边缘的服务链请求，最大化边缘网络效益）

**主要问题**：
如何在满足计算、存储、通信资源的约束（bound），以及服务时延的约束下，通过考虑VNF实例的部署（重用）来降低资源消耗，尽可能地满足边缘服务链请求，最大化边缘网络效益？

（1）在哪个MEC服务器放置哪种VNF实例，例如：在边缘服务器$v_j$中是否部署了第$k$种服务类型的VNF

（2）由哪个MEC服务器来响应某个服务链对于第n个VNF需求，例如：服务功能链$s_i$的第$n$个VNF是由边缘服务器$v_j$提供

（3）如何路由服务链的流量，例如：$s_i$的子链$l_i^{n,n'}$是否经过链路$e_{j,j'}$

**目标**：设计分布式的高效服务链编排方案，有效地利用边缘设备的计算资源，存储资源 ==（资源重用）==，同时减少链路的带宽消耗（VNF布局和路由问题），尽可能满足边缘服务链请求，最大化边缘网络效益。

==PS：由于延迟对VNF的重要性==，引出增加延迟约束（延迟需求）

==Idea（这也是新加的区别于原论文的地方）==：将时延也作为一个bound，即在重用资源的时候，考虑再添加一个路径是否会越时延约束的界，如果不越时延界，再判断资源界，并将最大效益的服务请求（==分布式贪心==）加入到==边缘服务集==中，并将该服务编排方案加入到==服务编排集==中。

**主要方法**：

**分布式服务功能链动态编排与资源重用算法**：==注意我们要把资源向量、投票向量、时间向量等一系列重复描述都隐去，重点描述我们算法的思想==

(1) 构建服务链请求集合，每个服务链请求运行一个请求接入代理，根据局部信息探索所请求服务链的最佳编排方案。

**探索阶段—算法1**：==区别于原论文，这里是以整个服务链为单位，所以需要一个完整的服务链方案探索过程，这里也是我们的一个重要的工作==：每个服务请求代理尝试性地探索适合于自身的服务链编排方案，具体的，服务链的每个VNF可以放置在哪个物理的MEC服务器，对应的VNF需求由哪个MEC服务器进行响应，以及服务链的流量如何路由以完成服务链完整功能的实现。针对上面多个联合决策问题，探索可行方案。此外，服务链的时延约束也放在这里面进行处理。

**分布式编排阶段—算法2**：MEC节点代理对用户的服务链编排方案进行评价，在满足bound的情况下，按照效益/占用资源比的条件，选择最大效益/资源的服务请求，加入到==有效服务集$\hat S$==，并将该服务编排方案加入到==服务编排集$(X,Y,Z)$== 中，更新该服务链对系统带来的资源消耗（包括计算，存储和带宽）。

**协议阶段**：进行协议服务链部署，基于异步的局部共识，编排服务功能链（步骤相对比较简单，加在第一个算法末尾即可）

在MEC中边缘部署VNF，考虑延迟约束，联合优化路由和服务功能链部署，实现边缘服务器资源（VNF）重用和提高链路的资源利用率。
将 VNF 链部署问题表述为混合整数线性规划(MILP) ，以==最大化运营商效益/最小化总资源消耗==。

<div align="center" ><img src="https://cdn.jsdelivr.net/gh/wlchengg/PicBed@main/images_for_blogs/20220402155410.png" alt="20220402155410" width="75%" style="box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);border-radius:10px;"/><br><div style="color:orange; border-bottom: 1px solid #d9d9d9; display: inline-block; color: #777; font-size: 90%; padding: 1px;">图1. 系统架构图</div></div>

如图1所示，$u_0$所需服务功能链是（VNF1，VNF2，VNF3），当选择路径为（d1,d2）时，没有VNF3，需要在d2处部署VNF3，会占用d2的存储资源；当选择路径为（d1，d2，d3）时，则不需要额外部署VNF，此时虽然增加了传输时延，但也提升了VNF重用率，降低了部署VNF的存储资源占用。


**下面内容参考PPT进行修改的，需要修改调整表达方式**

#### A. Background

第五代移动网络(5G)预计将满足不同垂直行业的不同使用案例的性能需求。海量机器类型通信(mMTC)、增强的移动宽带(eMcal)和超可靠的低延迟通信(URLLC)是处理用例的类别。为了满足这些不同用例的需求，需要在无线访问端和联网(核心)端进行创新和增强。在这两方面，网络功能虚拟化(NFV)、软件定义网络(SDN)和边缘计算起着主要作用。==边缘计算==是一种新兴技术，它将云计算能力引入到无线接入侧，以克服传统云计算的固有问题，如高延迟和安全性不足。此外，==由于网络边缘的存储和计算设施的存在，以及为终端用户减少了延迟，边缘计算范式使服务提供商能够提供大量的新应用程序==。 



在基于云的环境(如5G核心网络)中，服务的部署通常委托给一个名为Orchestrator的集中式组件，该组件通常采用一种适用于所有人的策略(如==节能、使用的节点数量、负载均衡==)来决定(i)==将服务组件放置在哪里，(ii)需要分配多少资源给他们—**在这里我们考虑了资源重用，即VNFs在满足最大处理流量约束的条件下可以重用**==。然而，这种集中的方法可能不是最优的，或者不适用于网络的边缘。首先，这种环境的特点可能是高流失率，网络拓扑的不可预测的变化，甚至临时的网络分区。其次，在网络边缘运行的大量异构应用程序集可能有不同的和不可预测的目标，更不用说对相同事件(如负载增加)做出不同反应的必要性了。在上述情况下，一些应用程序可能会向上/向外扩展，其他的可能会修改服务行为(例如，选择一个更激进的视频转码器)，其他的可能会 ==**迁移，下一篇文章可以做服务功能链的迁移，然后用深度强化学习来做，模式非常适合**== ，等等;上述行为很难用一个通用的协调器==这里引出分布式服务功能链部署==实现。



==分布式内容交付和缓存==、物联网、灾难响应、车辆到一切的汽车和视频加速，只是可以从网络边缘部署中受益的各种应用程序中的一部分。然而，在不依赖于集中式协调器的情况下，协调如此多的应用程序(每个应用程序都具有不同的策略和部署方法)带来了几个挑战。多个应用程序请求(每个请求都以不同的目标和策略操作)如何在共享的边缘基础设施上聚合成==全局最优的服务编排==?我们如何==避免违反全局策略或多个共存服务请求的可行性约束==?鉴于服务安置问题的NP-hard性质，我们如何保证汇聚到==分布式服务编排协议和性能==?



#### B. **Contributions**

为了回答这些问题，我们提出了一种==分布式服务功能链动态编排与资源重用算法==。算法利用最大共识和子模块函数理论，在一组用户请求之间就如何分配边缘服务器资源(临时)达成一致，而不需要一个集中的协调器。本文的贡献如下:

==满足多维异构资源要求==

==低复杂度的分布式共识动态服务编排方案==

==资源重用，最大化边缘响应服务链请求，提高系统效益==

1)利用混合整数规划建立请求-服务-资源多层服务编排模型。其目标是在满足边缘服务器容量约束的情况下，使总服务效用最大化。

2)设计分布式贪心算法，详细描述了局部投票、选举和异步协议的迭代过程。

3)利用次模定理分析证明了算法的收敛性和性能保证。



## 2、Related work

TODO：后补充即可 



## 3、System Model and Formulation
#### A.System Model



将用户$u_i$的服务请求所需服务链表示为：
$$s_i = (o_{i,j},\mathcal{F}_i, \mathcal{L}_i, \rho_i, \varpi_i, d_i)$$

其中，$o_{i,j}$是用户$u_i$发出请求的接入节点是否为$v_j$；$\mathcal{F}_i$是服务请求所需要VNF的集合，$\mathcal{L}_i$是服务链中的链路集合，两者共同确定了服务请求所需的服务功能链；式中$\rho_i$是该条服务链的流速率（即所需带宽，我们假设其在不同链路段是相等的）；$\varpi_i$是流量负载（即所需传输的数据量，我们也假设其在不同链路段相等）；$d_i$是该服务链的时延约束，服务必须在$d_i$时间内完成。

物理网络表示为无向图：
$$G=(\mathcal{V},\mathcal{E})$$

其中，$\mathcal{V}$和$\mathcal{E}$分别是物理节点（即边缘服务器）集合，以及物理链路的集合。

**符号清单：**
TODO：整理符号，统一符号类型，并去重。==已完成==
常量参数：
变量：大写字母、希腊字母
集合：花体字（\mathcal）
角标：小写英文单词或字母
序号：小写英文字母

|符号|意义|
|--|--|
|$u_i \in \mathcal{U}$|用户，用户集合|
|$v_j \in \mathcal{V}$|边缘服务器，边缘服务器的集合|
|$e_{j,j'} \in \mathcal{E}$|$v_j$和$v_{j'}$间的物理链路，物理链路的集合|
|$C_j,R_j$|边缘服务器$v_j$的计算和存储资源容量|
|$B_{j,j'}$|物理链路$e_{j,j'} \in \mathcal{E}$的带宽容量|
|$d_{j,j'}$|物理链路$e_{j,j'}$的通信时延|
|$\mathcal{N}_j$|$v_j$的邻居节点集合|
|$s_i \in \mathcal{S}$|$u_i$的服务请求对应的服务链，所有服务链的集合
|$d_i$|$s_i$的时延约束|
|$\rho_i$|服务请求$e_i$所需的流速率|
|${\varpi _i}$|服务请求$s_i$的流量负载|
|$f_k \in \mathcal{F}$|第$k$类VNF，所有不同类型VNF的集合|
|$c_k,r_k$|第$k$类VNF的计算和存储资源需求|
|$p^k$|第$k$类VNF的流处理能力|
|$f^n_i \in \mathcal{F}_i$|服务链$s_i$中的第$n$个VNF,服务链$s_i$所需服务功能的集合|
|$l_i^{n,n'} \in \mathcal{L}_i$|$f_i^n$ 和 $f_i^{n'}$ 之间的链路，所有链路的集合|
|$\eta_i^{n,n'} \subseteq \mathcal{F}_i$|$f_i^n$ 和 $f_i^{n'}$ 之间的子链，$1≤n<n'≤\lvert \mathcal{F}_i\rvert$|
|$o_{i,j}$|靠近用户$u_i$且与边缘服务器$v_j$就近部署的AP|
|$\rho_j$|$v_j$处AP的流处理能力|
|$\lambda _{i,k}^n$|0-1二值变量，服务链 $s_i$ 的第 $n$个 VNF 是否为类型-$k \in K$|
|$x_{j,k}$|0-1二值变量，表示$v_j$中是否部署了第$k$种服务类型的VNF|
|$y^n_{i,j}$|0-1二值变量，表示服务链$s_i$的第$n$个VNF是否由$v_j$提供|
|$z_{i(j,j')}^{n,n'}$|0-1二值变量，表示$s_i$的链路$l_i^{n,n'}$是否经过物理链路$e_{j,j'}$|
|$p_i,P$|服务链$s_i$对应物理链路的有序序列，以及服务链对应物理路径的集合|
|$q_i$|用户$i$服务请求的排队时延|
|$u_{\varpi}$|单位流量给系统带来的效益|
|$\alpha,\beta,\gamma$|计算、存储和带宽资源占用对成本的贡献权重|
|$(X,Y,Z)$|一个完整的编排方案，$X,Y,Z$分别对应$x,y,z$三个决策变量的向量|


假设不同的用户有不同的服务需求，每个用户都需要一个由==有序序列==的VNFs组成的服务链来满足服务需求。

用 $\mathcal{F}$和$\mathcal{K}$分别表示不同类型VNF的集合以及VNF类型的索引集合，$\mathcal{S}$表示所有请求服务链的集合。

对于每个用户 $u_i \in \mathcal{U}$，我们将 $s_i \in \mathcal{S}$ 表示为用户$u_i$请求的服务链，$\mathcal{F}_i$是其所需服务功能的集合，$\left|\mathcal{F}_i\right|$ 是服务链的长度（即该服务功能链中VNF的数量）。 我们接下来记 $f_i^n \in \mathcal{F}_i$ 是服务链 $s_i$  的第 $n$个 VNF，$\eta _i^{nn'}$ 是 $f_i^n$ 和 $f_i^{n'}$ 之间的子链 ($1 ≤ n < n' ≤ \left|\mathcal{F}_i\right|$).

服务链请求 ==${s_i} = ({o_{i,j}}, \mathcal{F}_i, \mathcal{L}_i, {\rho _i},{\varpi _i})$==，表示接入节点，所需服务功能集合，所需服务功能间的链路集合，流速率，==流量负载${\varpi _i}$（与效益相关，类似计算卸载中的数据量大小）==

具体来说，$o_{i,j}$ 是靠近用户$u_i$ 并且与边缘服务器$v_j$就近部署的AP，这意味着用户$u_i$请求的服务功能链$s_i$ 可以从$v_j$处的AP访问网络。${\rho _j}$表示$v_j$处的AP的流处理能力。

我们假设服务链的流量在遍历不同的服务器时始终保持不变，因为在 [28] 中已经研究了流量变化的影响，我们的公式可以很容易地扩展到变化的情况。

[28] W. Ma, O. Sandoval, J. Beltran, D. Pan et al., “Traffic aware placement of interdependent nfv middleboxes,” in IEEE INFOCOM, 2017, pp. 1–9.

具体来说，我们引入$\lambda _{i,k}^n$来表示服务链 $s_i$ 的第 $n$个 VNF 是否为类型-$k \in \mathcal{K}$。在现实中，一个用户可能需要多个服务链，这时可以认为是同一物理位置上的不同用户需要不同的服务链。

$$
\lambda_{i, k}^{n}=\left\{\begin{array}{l}
1, \text { 如果服务链 } s_i \text { 的第 } n \text { 个VNF的类型是 } k\in\mathcal{K}, \\
0, \text { otherwise. }
\end{array}\right.
$$

最后，设 $P$ 是服务链沿着路由的物理路径的集合，其中的每个 $p_i \in P$ 由服务链 $s_i$ 的物理链路的有序序列组成。例如图 1 中的 ${p_1} = ({e_{1,2}},{e_{2,3}},{e_{3,4}})$表示服务链 $s_i$ 的路由路径经过四个服务器节点 $v_1$,$v_2$,$v_3$,和$v_4$。



#### B. Formulation

**决策变量：**

为了建立服务编排问题模型，首先定义以下变量：

* $x_{j,k}$：表示在边缘服务器$v_j$中是否部署了第$k$种服务类型的VNF ~~==数量==  (有可能部署2个)~~（这个是否用来表示数量还可以再考虑考虑，因为如果部署了多个某类VNF的话，多个请求就能够 ==同时== 进行重用了）

$$
x_{j, k}=\left\{\begin{array}{l}
1, \text { 如果边缘服务器 } v_j \text { 上部署了 } k \in \mathcal{K} \text { 类VNF, } \\
0, \text { otherwise. }
\end{array}\right.
$$

* $y_{i,j}^n$：表示服务功能链$s_i$的第$n$个VNF $f_i^n$是否由边缘服务器$v_j$提供

$$
y_{i, j}^{n}=\left\{\begin{array}{l}
1, \text { 如果服务链 } s_i \text { 的第 } n \text {个VNF由} v_{j} \text { 提供, } \\
0, \text { otherwise. }
\end{array}\right.
$$

* $z_{i(j,j')}^{n,n'}$：表示$s_i$的链路$l_i^{n,n'}$是否经过链路$e_{j,j'}$。

$$
z_{i(j,j')}^{n,n'}=\left\{\begin{array}{l}
1, \text { 如果服务链 } s_i \text { 的链路 } l_{n,n'}^{i} \text { 是经过物理链路 } e_{j,j'}, \\
0, \text { otherwise. }
\end{array}\right.
$$
==关于$z_{i(j,j')}^{n,n'}$，以下两种特殊情形需要特别注意：==

  1. 服务链中的连续多个VNF（即一个功能 ==子链==）由同一个边缘服务器$v_{j'}$提供服务时，仅仅功能子链的第一个VNF和其前一个VNF之间（其由$v_j$提供服务）占用了$e_{j,j'}$的带宽，而之后的若干VNF之间因为是在同一个服务器内部直接传递信息，因此不消耗链路带宽，在计算带宽时需要注意；

  2. 当一个VNF链路$l_i^{n,n+1}$经过了服务器的转发通信（一条物理路径，按我们的假设最多经过一次中间转发），那么该VNF链路需要消耗经过的物理路径上所有链路的带宽，在计算带宽时需要注意。


**约束：**

==约束可以拓展到具体的多维资源约束==

<font color=red>考虑一下，计算和存储要不统一为一个资源，或者分开？</font>

1. 计算资源约束：
   每台边缘服务器 $v_j$ 的总计算资源消耗量不超过其计算资源容量 $C_j$
$$\sum_{s_i \in \mathcal{S}}{\sum_{f_i^n \in \mathcal{F}_i}{\sum_{k \in \mathcal{K}} y_{i,j}^n \cdot x_{j,k} \cdot c_{k}}} \leq C_{j},\quad  \forall v_{j} \in \mathcal{V}$$

2. 存储资源约束：
   每台边缘服务器 $v_j$ 的总存储资源消耗量不超过其存储资源容量 $R_j$
$$\sum_{s_i \in \mathcal{S}}{\sum_{f_i^n \in \mathcal{F}_i}{\sum_{k \in \mathcal{K}} y_{i,j}^n \cdot x_{j,k} \cdot r_{k}}} \leq R_{j},\quad \forall v_{j} \in \mathcal{V}$$

3. 带宽资源约束：
   沿着链路 $e_{j,j'}\in \mathcal{E}$ 的所有业务流的吞吐量之和不应超过其带宽容量 $B_{j,j'}$
$$\sum_{s_{i} \in \mathcal{S}} \sum_{l_i^{n,n'} \in \mathcal{L}_{i}} z_{i(j,j')}^{n,n'} \cdot {\rho_i} \leq B_{j,j'},\quad \forall e_{j,j'} \in \mathcal{E}$$


4. 服务功能独占性约束：
   即服务链上的每个VNF只由一个边缘服务器提供
  $$\sum_{v_{j} \in \mathcal{V}} y_{i, j}^{n}=1, \quad \forall s_{i} \in \mathcal{S}, \forall n \in\left[1,\left|\mathcal{F}_{i}\right|\right] \cap \mathbb{N}$$

5. 时延约束：
    **排队时延：** 定义为接入AP $v_j$的总流量处理能力减去接入$v_j$的所有服务链的总流量的倒数
$$q_{i}=\frac{1}{{\rho _j}-\sum_{s_i \in \mathcal{S}} o_{i, j} \cdot \rho_{i}}$$

    **传输时延：** 定义为服务链经过的物理路径上所有链路的传输时延总和：
$$t_i = \sum_ {l_i^{n,n'} \in \mathcal{L}_{i}} {\sum_{e_{j,j'} \in \mathcal{E}}{z_{i(j,j')}^{n,n'} \cdot d_{{j,j'}}^i}}$$

    编排后的服务链在物理路径上的**总时延**（包括排队时延$q_i$和传输时延$t_i$）不能超过服务请求的时延约束$d_i$，即
$$\sum_{v_{j} \in \mathcal{V}} o_{i, j} \cdot q_{i} + \sum_ {l_i^{n,n'} \in \mathcal{L}_{i}} {\sum_{e_{j,j'} \in \mathcal{E}}{z_{i(j,j')}^{n,n'} \cdot d_{{j,j'}}^i}} \leq d_{i}, \quad \forall s_{i} \in \mathcal{S}$$

6. 服务完整性约束：
   即$s_i$中的所有VNF都能得到服务 

$$\sum_{f_{i}^{n} \in \mathcal{F}_{i}} \sum_{v_{j} \in \mathcal{V}} y_{i,j}^n=\left|\mathcal{F}_{i}\right|, \quad \forall s_{i} \in \mathcal{S}$$
<font color=red> 不一定所有的服务链请求都能满足，我们讨论的是在满足资源约束的情况下，尽可能地满足更多的服务链请求，最大化满足的服务链的负载。</font>

构造最大效益编排集时根据以下**效益成本比**进行添加（<font color=orange>此处计算和存储资源应该为链中所有VNF所需资源总和，已修改）</font>：
$$\frac{u({s_i})}{\theta ({s_i})} \propto \frac{\varpi_i}{\left(\sum_{k\in\{\mathcal{K}|f_i^n\in\mathcal{F}_i,{\lambda_{i, k}^{n}=1} \}}{\left(\alpha \cdot {c_k} + \beta  \cdot {r_k}\right)} + \gamma  \cdot {\rho _i}\right)}$$

其中，效益与服务流量负载成正比例关系，分母$\theta(s_i)$是$s_i$占用计算、存储、带宽资源带来的成本，$\alpha,\beta,\gamma$是三种资源的成本权重值。

**目标函数**

* 流量负载${\varpi _i}$（与效益相关，类似计算卸载中的数据量大小）

  为服务功能链请求$s_{i}$提供一个单位流量所获得的收入表示为$u_\varpi$，它允许支持任意收入场景。则服务链请求$s_i$带来的效益为：
  $$u(s_i)={\varpi_i} \cdot u_\varpi$$

设在边缘网络$G=\{\mathcal{V},\mathcal{E}\}$上，对用户请求集合$\mathcal{S}$中的所有服务链进行编排得到的完整编排方案为$(X,Y,Z)$。

则我们的目标是寻找编排方案，最大化系统效益，即让边缘网络的服务链请求负载总和最大：
$$\max_{(X,Y,Z)} U({x_{j,k}},y_{i,j}^n,z_{i(j,j')}^{n,n'}) = \max_{(X,Y,Z)}\sum\limits_{{s_i \in \mathcal{S}}} {u({s_i})} = \max_{(X,Y,Z)}\sum\limits_{{s_i \in \mathcal{S}}} {{{\varpi _i}} \cdot u_\varpi }$$

在添加效益集的时候，按照 ==服务链的流量效益/占用资源== （注意<font color=red>这里的占用资源为新增的资源，重用的资源不算，这样就能提高效益/资源成本，在算法和实验中要注意考虑到）</font>

我们讨论的是在满足资源约束的情况下，尽可能地满足更多的服务链请求，最大化满足总的服务链的负载（每个服务链请求的数据大小都不一样，我们要尽可能满足更大的数据量的请求—类似计算卸载下的卸载数据量，这也是不同服务链请求效益差异化的地方）。添加效益编排集时按照下式进行:

$$\frac{u({s_i})}{\theta ({s_i})} = \frac{\varpi_i \cdot u_\varpi}{\left(\sum_{k\in\{\mathcal{K}|f_i^n\in\mathcal{F}_i,{\lambda_{i, k}^{n}=1} \}}{\left(\alpha \cdot {c_k} + \beta  \cdot {r_k}\right)} + \gamma  \cdot {\rho _i}\right)}$$

总的资源有计算、存储、带宽，VNF部署的主要开销是服务部署的**计算**和存储资源和服务链流量传输的通信资源。

通过VNF重用节省存储资源的同时可能会增加带宽资源（为了重用而选择更长的路径），因此需要权衡和优化。

**问题$P1$：**

$$\begin{array}{rllr}
\max\limits_{(X,Y,Z)} &U({x_{j,k}},y_{i,j}^n,z_{i(j,j')}^{n,n'}) = \sum\limits_{{s_i \in \mathcal{S}}} {{{\varpi _i}} \cdot u_\varpi } & &(1)\\
\text { s.t. }  \\
&  \sum_{s_i \in \mathcal{S}}{\sum_{f_i^n \in \mathcal{F}_i}{\sum_{k \in \mathcal{K}} y_{i,j}^n \cdot x_{j,k} \cdot c_{k}}} \leq C_{j},
  &\forall v_{j} \in \mathcal{V} &(1.a) \\
&  \sum_{s_i \in \mathcal{S}}{\sum_{f_i^n \in \mathcal{F}_i}{\sum_{k \in \mathcal{K}} y_{i,j}^n \cdot x_{j,k} \cdot r_{k}}} \leq R_{j},
  &\forall v_{j} \in \mathcal{V} &(1.b) \\
&  \sum_{s_{i} \in \mathcal{S}} \sum_{l_i^{n,n'} \in \mathcal{L}_{i}}  z_{i(j,j')}^{n,n'} \cdot {\rho_i} \leq B_{j,j'},
  &\forall e_{j,j'} \in \mathcal{E}  &(1.c) \\
&  \sum_{v_{j} \in \mathcal{V}} y_{i, j}^{n}=1,
  &\forall s_{i} \in \mathcal{S}, \forall n \in\left[1,\left|\mathcal{F}_{i}\right|\right] \cap \mathbb{N} &(1.d) \\
&  \sum_{v_{j} \in \mathcal{V}} o_{i, j} \cdot q_{i} + \sum_ {l_i^{n,n'} \in \mathcal{L}_{i}} {\sum_{e_{j,j'} \in \mathcal{E}}{z_{i(j,j')}^{n,n'} \cdot d_{{j,j'}}^i}} \leq d_{i},  & \forall s_{i} \in \mathcal{S} &(1.e)\\
& \sum_{f_{i}^{n} \in \mathcal{F}_{i}} \sum_{v_{j} \in \mathcal{V}} y_{i,j}^n=\left|\mathcal{F}_{i}\right|,
&\forall s_{i} \in \mathcal{S} &(1.f) \\
& x_{j,k},y_{i,j}^{n},z_{i(j,j')}^{n,n'} \in \{0,1\}.& &(1.g)
\end{array}$$

<font color=red>**目标和约束**，对应到分布式贪心算法中去，在满足的bound的情况下，分布式基于贪心的思想加入效益集合中</font>

1. bound：资源约束，原文也有多维资源，这里包括计算，存储，通信资源，应该没问题，考虑一下带宽约束是否有必要存在——==有必要的，在构建单个VNF链的时候需要考虑。==

2. 目标：最好是系统效益，系统效益的来源是 ==为服务功能链请求$s_{i}$提供一个单位流量所获得的收入表示为$u_\varpi$== ，它允许支持任意收入场景，即不同的服务链请求的效益与该请求的流量大小正相关，即运营商为用户提供服务的效益。接纳的服务功能链的请求数量 ==尽可能多，响应的请求流量尽可能大。==

3. 在分布式贪心添加效益集过程中，资源重用率——在满足资源Bound的情况下，尽可能接纳更多的服务请求链，响应的请求流量尽可能大。此时资源重用可以减少资源的消耗，因此可以在添加效益集的时候，利用异步分布式贪心思想，按照 ==服务链的流量效益/占用资源== （注意这里的占用资源为新增的资源，重用的资源不算，这样就能提高效益/资源成本）



## 4、Distributed dynamic orchestration of service function chain with resource reuse  分布式服务功能链动态编排与资源重用算法

现有的大多数文献都提出了集中式方法，这意味着单个节点可以计算所有资源分配。集中式方法存在扩展性问题，此外，在某些情况下，例如多个提供者和多个管理域，集中式算法可能不可行。因此，分布式方法似乎是克服这些挑战的可行解决方案。

**方法思路——分布式服务功能链动态编排与资源重用**：
（1）每个服务链请求探索服务链编排可行方案
（2）按效益/资源比构建==有效服务集${\hat S}$==，并将该服务编排方案加入到==服务编排集$(X,Y,Z)$== 中
（3）基于异步通信动态实现各代理对服务链编排方案的共识

### **算法1 Explore the service chain orchestration 探索服务链编排方案**

**考虑：**

1. 在进行共识通信时，采用全域广播，还是邻居通信？

---
**算法1**

输入：服务链请求集合$S$，物理网络$G$，VNF部署状态$X$;

for ${s_i} \in S$	// 遍历所有用户请求
&emsp;// 初始化
&emsp;$(X,Y,Z) \gets (X, null, null), d_i^{cur}\gets 0, n \gets 1, v_j \gets v_0, p_i \gets \emptyset $，其中$v_0$是接入节点$o_{i,j}$的一个假想的前驱节点，仅做占位用，便于简化遍历算法。
&emsp;// 顺序地对$\mathcal{F}_i$中所有所需的VNF，宽度优先搜索顺序从接入点开始遍历$\mathcal{V}$中的节点，找能重用VNF的边缘服务器。为了权衡通信时延成本和就近存储成本，我们限制连续两个VNF间最多经过两跳节点转发，如果两跳内没有可重用VNF，则选一个节点部署所需VNF(占用存储资源)。
​&emsp;while $d_i^{cur} < d_i$ && $n \leq \left| \mathcal{F}_i \right|$
&emsp;&emsp;// 如果时延超过约束或者已经遍历完服务链的所有VNF，则直接退出探索。
&emsp;&emsp;for $v_{j'}$ in $\mathcal{N}_j$ do
&emsp;&emsp;// 遍历邻居节点，判断是否已部署所需VNF。已部署则不会额外占用存储资源
&emsp;&emsp;&emsp;if VNF $f^n_i$在$v_{j'}$上已经有可用的部署（即$x_{j',k}=1$，$\lambda_{i, k}^{n}=1$）  then
&emsp;&emsp;&emsp;&emsp;从$n$开始统计当前节点$v_{j'}$上的连续可重用VNF数目记为$m^n_{i,j'}$
&emsp;&emsp;&emsp;else
&emsp;&emsp;&emsp;&emsp;continue; //当前邻居节点不可重用，则继续遍历下一邻居
&emsp;&emsp;&emsp;// end if
&emsp;&emsp;// end for
&emsp;&emsp;if 存在$m^n_{i,j'} \neq 0$ then
​&emsp;&emsp;&emsp;更新节点 $j'\gets \argmax_{j'\in\mathcal{N}_j}{m^n_{i,j'}}$，和最多可重用到的VNF的后继的序号 $n' \gets n+m^n_{i,j'}+1$;
&emsp;&emsp;&emsp;对所有可重用VNF $a \in [n,n')\cap\mathbb{N}$，更新服务变量 $y^a_{i,j'}\gets 1$，更新链路变量$z^{n-1,n}_{i(j,j')} \gets 1$以及$z^{a',a'+1}_{i(j',j')} \gets 1$（其中$a'\in [n+1,n'-1)$），更新物理路径$p_i \gets p_i \cup \{e_{j,j'}\}$;
&emsp;&emsp;else
&emsp;&emsp;// 如果当前节点的所有邻居节点都没有部署当前所需VNF，则先探索两跳节点。
&emsp;&emsp;&emsp;for $v_{j'}$ in $\mathcal{N}_j$ do
&emsp;&emsp;&emsp;&emsp;for $v_{j''}$ in $\mathcal{N}_{j'}$ do
&emsp;&emsp;&emsp;&emsp;// 遍历两跳节点，判断是否已部署所需VNF。已部署则不会额外占用存储资源
&emsp;&emsp;&emsp;&emsp;&emsp;if VNF $f^n_i$在$v_{j''}$上已经有可用的部署（即$x_{j'',k}=1$，$\lambda_{i, k}^{n}=1$）  then
​&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;从$n$开始统计当前节点$v_{j''}$上的连续可重用VNF数目记为$m^n_{i,j''}$
&emsp;&emsp;&emsp;&emsp;&emsp;else
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;continue; //当前邻居节点不可重用，则继续遍历下一邻居
&emsp;&emsp;&emsp;&emsp;&emsp;// end if
​&emsp;&emsp;&emsp;&emsp;// end for j''
&emsp;&emsp;&emsp;// end for j'
​&emsp;&emsp;&emsp;if 存在$m^n_{i,j''} \neq 0$ then
​&emsp;&emsp;&emsp;&emsp;更新节点 $j'\gets \argmax_{j''\in\mathcal{N}_j}{m^n_{i,j''}}$，进一步更新最多可重用到的VNF的后继的序号 $n' \gets n+m^n_{i,j'}+1$;
&emsp;&emsp;&emsp;&emsp;对所有可重用VNF $a \in [n,n')\cap\mathbb{N}$，更新服务变量 $y^a_{i,j'}\gets 1$，更新链路变量$z^{n-1,n}_{i(j,j')} \gets 1$以及$z^{a',a'+1}_{i(j',j')} \gets 1$（其中$a'\in [n+1,n'-1)$），更新物理路径$p_i \gets p_i \cup \{e_{j,j'_{pre}},e_{j'_{pre},j'}\}$;
&emsp;&emsp;&emsp;else
&emsp;&emsp;&emsp;&emsp;随机选择一个一跳邻居$v_{j'} \in \mathcal{N}_j$，更新$x_{j',k} \gets 1$，$y^n_{i,j'} \gets 1$，$z^{n-1,n}_{i(j,j')} \gets 1$，其中$k$满足$\lambda_{i, k}^{n} = 1$.
&emsp;&emsp;&emsp;&emsp;$n' \gets n+1$
&emsp;&emsp;&emsp;// end if $m^n_{i,j''}$
&emsp;&emsp;// end else to 两跳邻居
&emsp;&emsp;if $n > \left| \mathcal{F}_i \right|$ then
&emsp;&emsp;&emsp;更新链路带宽资源：${{\tilde B}_{j,j'}^i} \gets\sum_{v_j,v_{j'} \in \mathcal{V}}{z_{i(j,j')}^{n-1,n} \cdot {\rho _i}}$, 或 ${{\tilde B}_{j,j'}^i} \gets \sum_{e_{j,j'} \in p_i}{\rho_i}$;
&emsp;&emsp;&emsp;更新时延：$d_i^{cur} \gets \sum_{v_{j} \in \mathcal{V}} o_{i, j} \cdot q_{i} + \sum_ {l_i^{n,n'} \in \mathcal{L}_{i}} {\sum_{e_{j,j'} \in \mathcal{E}}{z_{i(j,j')}^{n,n'} \cdot d_{{j,j'}}^i}}$，或 $d_i^{cur} \gets \sum_{v_{j} \in \mathcal{V}} o_{i, j} \cdot q_{i} + \sum_{e_{j,j'} \in p_i}{d_{{j,j'}}^i}$
&emsp;&emsp;&emsp;​// 执行分布式编排算法2，构建服务编排集
&emsp;&emsp;else if $d_i^{cur} > d_i$ then
&emsp;&emsp;&emsp;// 说明违背了时延约束，需要重新探索方案
&emsp;&emsp;&emsp;将当前方案的更改恢复初始化状态，重新进入while循环。
&emsp;&emsp;// end if $m^n_{i,j'}$
&emsp; // end while
// end for $s_i$
// 发送本地最新方案并接收其他代理的方案，选择最优方案，达成共识。
​send();
receive();
选择 $(X^*,Y^*,Z^*) \gets \max_{(X,Y,Z)} U({x_{j,k}},y_{i,j}^n,z_{i(j,j')}^{n,n'})$;
输出：$(X^*,Y^*,Z^*)$

---
**算法2**

输入：各节点计算资源容量 $C_j$，存储资源容量$R_j$，链路带宽资源容量$B_{j,j'}$
\WHILE{$\tilde{C}_j < C_j$ && $\tilde{R}_j < R_j$ && $\tilde{B}_{j,j'} < B_{j,j'}$ && $\hat{S} \neq \emptyset$}
&emsp;\STATE {// $\tilde{C}_j,\tilde{R}_j,\tilde{B}_{j,j'}$分别是当前消耗的计算、存储、带宽资源}
&emsp;\STATE {$\hat s = \max _{{s_i} \in \tilde S}{\varpi _i} \cdot {u_\varpi }/\left(\sum_{k\in\{\mathcal{K}|f_i^n\in\mathcal{F}_i,{\lambda_{i, k}^{n}=1} \}}{\left(\alpha \cdot {c_k} + \beta  \cdot {r_k}\right)} + \gamma  \cdot {\rho _i}\right)$; // 贪心获取最高增益的服务链}
&emsp;\STATE {$\tilde{C}_j \gets \tilde{C}_j + \tilde{C}_j^{\hat s}$;    // 更新消耗的计算资源}
&emsp;\STATE {$\tilde{R}_j \gets \tilde{R}_j + \tilde{R}_j^{\hat s}$;    // 更新消耗的存储资源}
&emsp;\STATE {$\tilde{B}_{j,j'} \gets \tilde{B}_{j,j'} + \tilde{B}_{j,j'}^{\hat s};$ // 更新消耗的带宽资源}
&emsp;\IF {$\tilde{C}_j \leq C_j$ && $\tilde{R}_j \leq R_j$ && $\tilde{B}_{j,j'} \leq B_{j,j'}$}
&emsp;&emsp;\STATE {$\tilde{S} \gets \tilde{S} \backslash \hat{s}$; // 从服务请求集去掉最高增益服务链}
&emsp;&emsp;\STATE {$\hat{S} \gets \hat{S} \cup \hat{s}$;   // 将最高增益服务链加入编排集 }
&emsp;&emsp;\STATE {$(X,Y,Z) \gets (X,Y,Z) \cup (X_{\hat{s}},Y_{\hat{s}},Z_{\hat{s}})$;   // 更新VNF部署方案}
&emsp;\ELSE
&emsp;&emsp;\STATE {$\tilde{C}_j \gets \tilde{C}_j - \tilde{C}_j^{\hat s}$;    // 撤销更新消耗的计算资源}
&emsp;&emsp;\STATE {$\tilde{R}_j \gets \tilde{R}_j - \tilde{R}_j^{\hat s}$;    // 撤销更新消耗的存储资源}
&emsp;&emsp;\STATE {$\tilde{B}_{j,j'} \gets \tilde{B}_{j,j'} - \tilde{B}_{j,j'}^{\hat s};$ // 撤销更新消耗的带宽资源}
&emsp;&emsp;\STATE {$\hat{S} \gets \hat{S} \cup \hat{s}$;   // 将最高增益服务链加入编排集}
&emsp;&emsp;\STATE {将$\hat s$的编排方案置为初始化值}
&emsp;\ENDIF
\ENDWHILE

---

## 5、Evaluation

（1） 系统效益

（2） 边缘服务的服务链请求数量

（3） 资源重用比

---

## 6、PPT备用

**下面内容为PPT中的一些总结，参考性不大，因为基本跟那篇论文一样！**

**下面内容参考PPT进行修改的—需确定内容的原创性，如果大部分借鉴别人的，需要修改调整表达方式**

#### A. Background

第五代移动网络(5G)预计将满足不同垂直行业的不同使用案例的性能需求。海量机器类型通信(mMTC)、增强的移动宽带(eMcal)和超可靠的低延迟通信(URLLC)是处理用例的类别。为了满足这些不同用例的需求，需要在无线访问端和联网(核心)端进行创新和增强。在这两方面，网络功能虚拟化(NFV)、软件定义网络(SDN)和边缘计算起着主要作用。==边缘计算==是一种新兴技术，它将云计算能力引入到无线接入侧，以克服传统云计算的固有问题，如高延迟和安全性不足。此外，由于网络边缘的存储和计算设施的存在，以及为终端用户减少了延迟，边缘计算范式使服务提供商能够提供大量的新应用程序。

The fifth generation of mobile networks (5G) is expected to address performance requirements of diverse use cases in different vertical industries. Massive machine type communications (mMTC), enhanced mobile broadband (eMcal), and ultra-reliable low-latency communications (URLLC), are the categories of addressed use cases. To fulfill the requirements of these diverse use cases, innovations and enhancements are needed on the radio access side and the networking (core) side. On both sides, network function virtualization (NFV), software-defined networking (SDN), and edge computing are to play a principal role.

Edge computing is an emerging technology that enables the evolution to 5G by bringing cloud capabilities near to the radio access side in order to overcome the intrinsic problems of the traditional cloud, such as high latency and the lack of security. Moreover, Edge Computing paradigm has enabled service providers to supply a large variety of new applications since ==the presence of storage and computing facilities at the edge of the network as well as reduced latency toward end users 这个内容跟Infocom完全重复==，需要重新写. 

在基于云的环境(如5G核心网络)中，服务的部署通常委托给一个名为Orchestrator的集中式组件，该组件通常采用一种适用于所有人的策略(如==节能、使用的节点数量、负载均衡==)来决定(i)==将服务组件放置在哪里，(ii)需要分配多少资源给他们—**在这里我们考虑了资源重用，即VNFs在满足最大处理流量约束的条件下可以重用**==。然而，这种集中的方法可能不是最优的，或者不适用于网络的边缘。首先，这种环境的特点可能是高流失率，网络拓扑的不可预测的变化，甚至临时的网络分区。其次，在网络边缘运行的大量异构应用程序集可能有不同的和不可预测的目标，更不用说对相同事件(如负载增加)做出不同反应的必要性了。在上述情况下，一些应用程序可能会向上/向外扩展，其他的可能会修改服务行为(例如，选择一个更激进的视频转码器)，其他的可能会==**迁移，下一篇文章可以做服务功能链的迁移，然后用深度强化学习来做，模式非常适合**==，等等;上述行为很难用一个通用的协调器==这里引出分布式服务功能链部署==实现。

In cloud-based environments (e.g. 5G Core network), the deployment of services is often delegated to a centralized component, named Orchestrator, that usually exploits a one-size fits-all policy (e.g., energy saving, number of used nodes, load balancing) to decide (i) where to place service components, (ii) how many resources have to be assigned to each of them.

However, such a centralized approach may be sub-optimal or not applicable at the edge of the network. First, such environment may be characterized by high churn rates, unpredictable changes in the network topology and even temporary network partitions. Second, the largely heterogeneous set of applications running at the edge of the network may have diverse and unpredictable objectives, not to mention the necessity to react differently to the same event, such as a load increase. In the above circumstance, some applications may scale up/out, other may modify the service behavior (e.g., choose a more aggressive video transcoder), other may migrate, and more; the above behaviors are difficult to achieve with a one-size fits-all orchestrator.

==分布式内容交付和缓存==、物联网、灾难响应、车辆到一切的汽车和视频加速，只是可以从网络边缘部署中受益的各种应用程序中的一部分。然而，在不依赖于集中式协调器的情况下，协调如此多的应用程序(每个应用程序都具有不同的策略和部署方法)带来了几个挑战。多个应用程序请求(每个请求都以不同的目标和策略操作)如何在共享的边缘基础设施上聚合成==全局最优的服务编排==?我们如何==避免违反全局策略或多个共存服务请求的可行性约束==?鉴于服务安置问题的NP-hard性质，我们如何保证汇聚到==分布式服务编排协议和性能==?

Distributed content delivery and caching, Internet of Things, disaster response, vehicle-to-everything automotive and video acceleration are only some of the varies of applications that can benefit from deployment at the edge of the network. However, coordinating such a plenty of applications, each one featuring different policies and deployment approaches, without relying on a centralized orchestrator, brings several challenges. How could several application requests, each operating with different goals and policies, converge to a globally optimal service orchestration over a shared edge infrastructure? How could we avoid violations of global policies or feasibility constraints of several coexisting service requests? How can we guarantee convergence to a distributed service orchestration agreement and performance given the NP-hard nature of the service placement problem?

#### B.**Contributions**

为了回答这些问题，我们提出了一种==分布式服务功能链动态编排与资源重用算法==。算法利用最大共识和子模块函数理论，在一组用户请求之间就如何分配边缘服务器资源(临时)达成一致，而不需要一个集中的协调器。本文的贡献如下:

==满足多维异构资源要求==

==低复杂度的分布式共识动态服务编排方案==

==资源重用，最大化边缘响应服务链请求，提高系统效益==

1)利用整数规划建立请求-服务-资源多层服务编排模型。其目标是在满足边缘服务器容量约束的情况下，使总服务效用最大化。

2)设计分布式贪心算法，详细描述了局部投票、选举和异步协议的迭代过程。

3)利用子模定理分析证明了算法的收敛性和性能保证。

To answer these questions, we present an asynchronous Distributed Greedy-based Service Orchestration algorithm (DGSO). DGSO leverages the max-consensus and the theory of submodular functions to reach an agreement between a set of user requests on how edge server resources have to be (temporary) assigned, without the necessity of a centralized orchestrator. The contributions of this paper are as follows:

1) Formulate the request-service-resource multi-layer service orchestration model using integer programming. The objective is to maximize the total serving utility with satisfying the capacity constraints of edge servers.

2) Design a distributed greedy algorithm with detailing the iteration procedure of local voting, election and asynchronous agreement.

3) Analysis and proof the convergence and performance guarantee by submodular theorem.



备用：

A. **目标函数** 我们的目标是最大化全局效用 U，同时找到允许部署每个请求的请求资源分配==并最大化资源重用率==。我们用整数规划对请求资源分配问题建模。其二元决策变量 xii 等于 1 如果服务函数 j 的实例已被分配给边缘服务器 n 上的请求 i，否则为零。目标公式如下，Nu Ns N max 二Uisn（xi）xi（1） i=1j=1b-1 其中 x；e{0，1}Ws×No 为请求 i 的赋值向量，其第 j 个 x bth 元素为 xib.I{1.…，Nu}，S{1，.，Ns} 和 B{1，.  .，No}分别是请求、服务功能和边缘服务器的索引集。

这个==目标函数有问题，i，j，n/b需要表示清楚==

A.Objective Function Our goal is to maximize a global utility U while finding a requests-resources assignment that allows the deployment of each request.We model the requests-resources assign-
ment problem with an integer programming.Its binary decision variable xii is equal to one if an instance of service function j has been assigned to request i on edge server n and to zero otherwise.The objective is formulated as follows，Nu Ns N max 二Uisn（xi）xi（1）
i=1j=1b-1
where x；e{0，1}Ws×No is the assignment vector for re-
quest i，whose jth x bth element is xib.I{1.…，Nu}，S{1，.，Ns}and B{1，..，No}are the index set of requests，sevice functions，and edge server，respectively.



B. **约束** 由于每台边缘服务器的资源有限，总的资源成本不能超过边缘服务器的资源容量，用 Nu N 表示。
   XSx6Gk S0k, VkEK, VWEB,(2) i=1j=1 其中pbk EN表示边缘服务器b上可用资源k的数量，KA{1,..., No}是资源类型的索引集。 此外，我们将 pb E NA 表示为边缘服务器 b 上资源的整体容量。 我们用 Cik EN 来表示服务函数 j 的资源 k 成本，并表示 c;E N，服务函数 jES 的成本向量。

B. Constraints As each edge server has limited resources, the total resources costs must not exceed the resources capacity of edge servers, which expressed as Nu N.
XSx6Gk S0k, VkEK, VWEB,(2)
i=1j=1
where pbk ENrepresents the amount of resource k available on edge server b, and KA{1,..., No} is the index set of resource types. Furthermore, we denote pb E NA the overall capacity of resources on edge server b. We use Cik EN to represent the cost of service function.j in terms of resource k, and denote c;E N, the cost vector of service functionjES.

B.约束条件考虑到功能的整体性，我们要求==用户请求的服务功能集中的任何单个功能只能分配在一个边缘服务器上==，可以表示为N，Zxj动≤1，Vi=I，  （3）b=1 最后，==由于请求的完整性，必须同时成功分配所有需要的功能，即一个请求的服务功能集不能部分分配==，可以表述为N。
   二xb=0u，vieI（4）b=1l=1 其中ou E{0，1}表示服务l是否为i所需的组件。

B.Constraints Considering the functional wholeness of a function，we require any single function in a service function set of an user request only can be assigned on one edge server，which can be expressed as N，Zxj动≤1，Vi=I，（3）b=1 Finally，since the wholeness of the request，all needed functions must be successfully assigned simultaneously，thatis，the service function set of a request can not be partial assigned，which can be formulated as N。
二xb=0u，vieI（4）b=1l=1 where ou E{0，1}represents whether service l is a component required by i.



***

备用：

我们将请求建模为一个集合，该集合的元素在嵌入到共享边缘网络基础设施上的n个(抽象的)服务函数中选择。 一个服务功能是一个物理功能的抽象实例，例如负载均衡器、视频转码器或内容缓存。  

网络中部署了多个异构边缘服务器，即每个服务器具有不同的物理能力。部署在服务器中的每个VNF消耗一定数量的资源，如CPU、存储、内存、网络带宽等，这些资源以N种不同类型建模。  

最后，我们考虑Nu请求，所有这些请求都同时要求共享边缘服务器提供资源，并且每个请求都遵循不同的服务功能集构建策略。 我们假设请求(用户) 自己会选择实现其组合服务功能所需的最佳(可行)函数，然后将它们分配到最合适的边缘服务器。  

We model a request as a set whose elements are selected among Ns(abstract) service functions to be embedded on the shared edge network infrastructure.A service function is an abstract instance of a physical function,e.g.,a load balancer,a video transcoder or a content cache.
There are No edge servers, each one with potentially different physical capacities. We assume that each service function consumes a given amount of resources such as CPU, storage, memory, network bandwidth, etc., which are modeled with N, different types.
Finally, we consider Nu requests, all of which simulta-neously demand resources from shared edge servers, and each one follows a potentially different service function set construction strategy. We assume that the request(user)
itself will select the best(feasible) functions that are required to implement its composing service functions, then allocate them in the most appropriate edge servers.



在本节中，我们将介绍提出的分布式服务功能链动态编排与资源重用算法。 每个用户i运行一个代理，该代理启动一个投票过程，目的是获取部署其分配向量xi所需的资源，并参与资源选举。 投票和选举是在用户级别执行的。 请求“当选”，即他们在分布式分配问题上获胜，获得在某个(物理)边缘服务器上执行其所需服务功能的权利。 在第一阶段，每个代理根据其本地状态意识在本地执行选举。 基于最大共识的分布式协议阶段保证了选举过程的收敛性。  

In this section we introduce the proposed DGSO(Distributed Greedy-based Service Orchestration). Each user i runs an agent, which starts a voting procedure with the aimof acquiring the resources needed to deploy its assignment vector xi, and participates to a resource election. Voting and elections are performed at the user level. Requests that are"elected",i.e., that they win the distributed assignment problem, gain the right to perform their required service functions on a certain (physical) edge server. In the first phase, each agent performs the election locally, based on its local state awareness. Then a max-consensus based distributed agreement phase guarantees the converge of the election process.



**A. 准备**

为了描述该算法，我们首先给出以下定义， (vote vector v) 给定一个分布式投票过程在一组 I 的 Nu 用户中，我们定义 v;E Rf" 为用户已知的当前获胜选票的向量 i E I. 每个元素 uf" 是一个正实数，表示用户 i 知道的 mE 我的投票，如果我认为 m 是选举阶段的获胜者。 否则，vn 为 0。
   （需求资源向量 ri）。 给定一组 I 的 Nu 用户对 N 个不同类型资源的投票过程，我们定义需求资源向量riE Nfu×，每个用户请求当前需求的总资源向量； 每个元素 r"E NNe 是用户 m e I 及其最近的投票 u 所需的资源量？
   再见我。
   （投票时间向量 ti）。 给定一组参与分布式投票过程的 Nu 用户，我们定义为投票时间向量 t;E Rf"，向量的元素 t?
   表示请求 mE I 的最后一次投票 v"knownbyiEI 的时间戳。

为了保持投票信息的一致性，我们定义了投票时间向量来提供信息更新的索引，

在每个用户i上，DSO代理运行一个业务流程阶段(Orchestration Phase)，在这个阶段中，如果有请求-服务-资源分配(request-service-resource assignment)，则会构建并投票以参与资源选举。在分布式选举过程中更新选票。如果投票向量vi的任何值改变，i发送它的向量vi, ri和t;到它的(第一跳)邻居，然后等待来自任意数量的响应。

On each user i, the DSO agent runs an Orchestration Phase where a request-service-resource assignment, if any, is builtand voted to participate in the resource election. Votes are updated in a distributed election process. If any value of the vote vector viis changed,i sends its vectors vi, ri and t; to its(first-hop) neighbors, then waits for a response coming from any number of them.



#### **A. Preparation** 



为了描述该算法，我们首先给出以下定义:

(投票向量v)给定一个分布式ν用户的投票过程在一组我,我们定义v; E射频”是当前赢得选票的向量由用户我E I佛罗里达大学每个元素”是一个正实数代表我我被用户的投票,如果我认为m是一个选举的赢家的阶段。否则，vn = 0。

(要求的资源向量ri)。给定一组I (Nu)用户对N个不同类型资源的投票过程，定义为当前每个用户请求所需要的资源向量Nfu×;每个元素r“E NNe是用户m E I最近投票u?

我知道byie。

(投票时间向量ti)。给定参与分布式投票过程的Nu用户集合I，定义投票时间向量t;E Rf"，其元素t?

表示请求mE I的最后一次投票v的时间戳。

我们还给出了邻居的以下定义，使用户之间能够进行信息交换，

(邻域I)。给定一个用户集合I，定义useriE I的邻域I;S I\{il，它是直接连接到I的用户的子集。



To describe the algorithm, we first give following definitions,
(vote vector v) Given a distributed voting process among a set I of Nu users, we define v;E Rf"to be the vector of current winning votes known by user i E I. Each element uf"is a positive real number representing the vote of mE I known by user i, if i thinks that m is a winner of the election phase. Otherwise, vn is 0.
(demanded resource vector ri). Given a voting process among a set I of Nu users on N, different types of resources, we define as demanded resource vectorriE Nfu×, the vector of total resources currently demanded by each user request; each element r"E NNe is the amount of resources demanded by user m e I with its most recent vote u?
known byie I.
(voting time vector ti). Given a set I of Nu users participating to a distributed voting process, we define as voting time vector t;E Rf", the vector whose element t?
represents the time stamp of the last vote v"knownbyiEI for request mE I.

We also give the following definition of neighborhood which enable the information exchanging between users,

(neighborhood I). Given a set I of users, we define neighborhood I;S I\{ il of useriE I, the subset of users directly connected to i.



#### **B. Algorithm Overview**

在每个用户i上，DSO代理运行一个业务流程阶段(Orchestration Phase)，在这个阶段中，如果有请求-服务-资源分配(request-service-resource assignment)，则会构建并投票以参与资源选举。在分布式选举过程中更新选票。如果投票向量vi的任何值发生变化，i都会将它的向量vi、ri和ti发送给它的(第一跳)邻居，然后等待来自它们任意数目的响应。



在协议阶段，将邻居i接收到的所有向量vv、riv、tiv结合局部值，与i达成协议。

注意赋值向量x;每个用户我都不需要交换。代理知道来自其同伴的资源需求，但不知道关于它们希望分配哪些功能的详细信息。







![image-20220323211334327](images\image-20220323211334327.png)

On each user i, the DSO agent runs an Orchestration Phase where a request-service-resource assignment, if any, is builtand voted to participate in the resource election. Votes are updated in a distributed election process. If any value of thevote vector vi is changed,i sends its vectors vi, ri and ti to its(first-hop) neighbors, then waits for a response coming from any number of them.



During the Agreement Phase, all vectors vv, riv and tiv received from neighbor i are used in combination with the local values, to reach an agreement with i.
Note that the assignment vector x; of each user i does not need to be exchanged. Agents are aware of the resources demand from their peers, but are unaware of the details regarding which functions they wish to allocate.



#### **C. Orchestration**

在对当前迭代t(算法2，第2行)初始化局部向量v(t)，r(t)， t(t)后，每个DSO agent使用算法2，第10行，根据上次迭代更新的已知选票，选出当前的获胜者。

如果agent i被击败(算法2，第6行)，算法开始迭代(i)一个嵌入程序(算法2，第7行)，该程序计算下一个合适的分配向量xi，(ii)一个投票程序(算法2，第6行)。(iii)选举例程(算法2，第10行)。

迭代继续进行，直到主体i不再被击败(算法2，第11行)。这可能发生在(i)所选的分配向量允许i赢得选举，或者(ii)没有更合适的分配x;(然后没有新的投票生成)。

嵌入例程:无论是在第一次迭代期间(t=0)，还是任何时候用户i被超过，DSO调用一个嵌入例程(算法2，第7行)，计算下一个合适的赋值向量轴。





![image-20220323211932000](images\image-20220323211932000.png)



After the initialization of local vectors v(t),r(t) and t(t) for the current iteration t(Algorithm 2, line 2), each DSO agent uses Algorithm 2, line 10 to elect the current winners according to the known votes updated at the last iteration.

If agent i has been outvoted(Al-gorithm 2, line 6), the algorithm starts to iterate among(i) an embedding rou-tine(Algorithm 2, line 7), which com-putes the next suitable assignment vector xi,(ii)a voting routine(Algo-rithm 2, line 8) where agent i votes for the resources that follow the last computed assignment vector and(iii)the election routine(Algorithm 2, line10).

The iteration continues until agent i does not get outvoted anymore(Algorithm 2, line 11). This may happen if either(i)the selected assignment vector allows i to win the election or(ii)there are no more suitable assignments x;(then no new vote has been generated).

Embedding Routine: Either dur-ing the first iteration(t=0), or anytime user i is outvoted, DSO invokes an embedding routine(Algorithm 2, line 7) that computes the next suitable assignment vector axi.



投票程序:在建立一个新的分配向量后，每个DSO代理运行一个投票程序，更新其最近的投票时间;如果赋值向量是有效的，所有需要的资源都会通过一个由全局工具(Algorithm3)派生的评分函数进行更新和投票。虽然原始的全局效用本身可以作为分数函数来计算选票，但我们给出了它的一个变体，以保证收敛性和最优逼近界。



![image-20220323212304721](images\image-20220323212304721.png)



Voting Routine: After a new assignment vector has been built, each DSO agent runs a voting routine, updating the time of its most recent vote; if the assignment vector is valid, all demanded resources are updated and voted, through a score function derived by the global utility(Algorithm3). Although the raw global utility itself may be used as score function to compute votes, we give a variant of it to guarantee convergence and optimal approximation bound.



选举程序:编制阶段的最后一步(算法2,第10行)是一个资源选举决定哪些用户能够分配要求资源共享主机的边缘服务器(4)算法。根据最近的已知选票v (t),相关资源需求的能力p r (t)和共享的节点,这个过程采用贪婪的方法来选择用户。

在每一步中，它(i)丢弃任何需求资源ri超过剩余节点容量的用户，(ii)选择对需求资源有较高层次投票的用户(算法4，第4-5行)。然后将选出的资源添加到赢家集合中，并从剩余集合中删除分配给新赢家的资源数量(算法4，第6-7行)。

当所有候选人都获胜，或者剩余的资源不够剩下的候选人时，贪婪的选举就结束了。最后，没有赢得选举的用户的投票被重置(算法4，第9行)。



![image-20220323212430952](images\image-20220323212430952.png)



Election Routine: The last step of the Orchestration Phase(Algorithm 2, line 10) is a resource election that decides which users are capable of allocating the demanded resources on the shared hosting edge server(Algorithm 4). Based on the most recent known votes v(t), the related resource demands r(t) and the capacity p of the shared node, this procedure selects users by mean of a greedy approach.

At each step, it (i) discards any user whose demanded resources ri exceed the residual node capacity and(ii) selects the one with the highestratio vote to demanded resources(Al-gorithm 4, lines 4-5). The elected oneis then added to the winners set and the amount of resources assigned tothe new winner are removed from the residual set(Algorithm 4, lines 6-7).

The greedy election ends when either all candidates result winners, or residual resources are not enough for any of those remaining. Finally, votes of users that did not win the election are reset(Algorithm 4, line 9).



一旦从邻近的每个用户i接收到矢量vv、rv、tu，每个代理就会运行一个协议阶段。在这一阶段，用户利用共识机制对自己的投票向量vi(算法5)达成一致。

为作业投票的分数函数是一种策略。许多政策在实践中可能很有效，但在某些情况下，它们可能导致武断的糟糕表现。我们的算法保证给定的性能下界，只要函数在选举程序是子模的最大化。



![image-20220323212829722](images\image-20220323212829722.png)



Once received vectors vv, rv, tu from every user i in its neighborhood, each agent runs an Agreement Phase. During this phase, users make use of a consensus mechanism to reach an agreement on their vote vector vi(Algorithm 5).

The score function to vote for assignments is a policy. Many policies may work well in practice, but in some cases they may lead to arbitrarily bad performance. Our algorithm guarantees a given performance lower bound as long as the function maximized during the election routine is submodular.



#### D. Score Function

接下来，我们给出评分函数V，每个用户在算法3中描述的投票例程中应该使用这个函数来满足这个属性。

让Uib (xi) =;Uijb(xi) xiib是节点b上useri的整体节点实用程序。我们定义分数函数:



其中Wo S I是节点b当前的赢家集合，即v,f0VwE Wi, Cib定义为



基于评分函数，DSO的选举程序相当于一个贪心算法，试图为每个节点b找到获胜用户W,C I的集合，使集合函数zb最大化。set函数定义为

在满足边缘服务器和请求的容量约束的情况下，我引入了公式化的优化问题来最大化总服务效用，然后从编排和协议步骤介绍了所提出的DSO（分布式服务编排）算法。



![image-20220324012437698](images\image-20220324012437698.png)



Next we give the score function V that each user should use during the voting routine described in Algorithm 3 to satisfy this property.

Let Uib(xi)=; Uijb(xi) xiib be the overall node utility of useri on node b. We define the score function:

where Wo S I is the current winner set for node b,ie,v,f0VwE Wi,and Cib is defined as

Based on the score function, the election routine of DSO is equiva-lent to a greedy algorithm attempting to find, for each node b, the set of winner users W,C I such that the set function zb is maximized.The set function is defined as

I introduced the formulated optimization problem to maximize the total serving utility with satisfying the capacity constraints of edge servers and requests, and then introduced the proposed DSO (Distributed Service Orchestration) algorithm from the orchestration and agreement steps.



#### **E.** Performance Analysis

##### **(1) Submodularity** **of the total gain function**

由于多个边缘服务器以分布式方式维护实用程序，这意味着每个用户在构建获胜的分配集时没有全局信息。因此，无法直接获得全局最大效用集。然而，通过我们的DSO算法中的通信过程和一致性协议，每个节点维护的局部最大效用集被传递，并根据增益函数的子模块性进行更新，可以证明该算法将在性能保证的情况下获得次优解。接下来，我们首先证明了增益函数的子模性和DSO的收敛性，然后详细证明了DSO的性能保证。

根据给定的每个边缘服务器b的总增益函数zb（W），我们给出了它的子模性，如引理1所示。

引理1：增益函数的子模性每个边缘服务器的总增益函数，即zb（Wa），是相对于用户集的子模。

子模性指的是边际效用递减定律，也就是说，将一个元素添加到任何集合A的增益低于将该元素添加到A的任何子集B的增益。

![image-20220324013323611](images\image-20220324013323611.png)

我们给出引理1的证明如下。

引理1的证明：假设W，cw，ci，wfwi和wei。根据分数函数Vi（xi，Wi，b）的定义，我们有不等式（9）

![image-20220324013514415](images\image-20220324013514415.png)

根据总增益zb（Wi）的定义，分数Vi（xi，Wo，b）是将i添加到Wo中的边际增益。在那里-

因此，我们有，

26（W%+i）-20（W%）=Vi（xi，W%，b）(11)

≤Vi（xi，Wi，b）=0（W%+i）-c6（W%），其中W，+i表示将i添加到集合W中，。因此，根据子模函数的定义，可以证明每个边缘服务器的总增益函数是相对于用户集的子模函数。

引理1已被证明。





Since multiple edge servers maintain utility in a distributed manner, which means that each user does not have global information when constructing the winning assignment set. Therefore, the global maximum utility set cannot be directly obtained. However, through the communication process and consensus agreement in our DSO algorithm, the local maximum utility set maintained by each node is passed and updated according to the submodularity of the gain function, it can be proved that the algorithm will obtain a suboptimal solution with a performance guarantee. Next, we first prove the submodularity of the gain function, the convergence of DSO, and then give detailed proof for the performance guarantee of DSO. 

According to the given total gain function zb(W,) of each edge server b, we give the submodularity of it as shown in Lemma 1.
Lemma 1: Submodularity of Gain Function The total gain function of each edge server, that is zb(Wa), is submodular with respect to user set.
The submodularity refers to the law of diminishing margain utility, that is, the gain by adding an element to any set A is lower than the gain by adding the element to any subset B of A.

We give the proof of Lemma 1 as follows.
Proof of Lemma 1: Suppose W,c W,c I,w f Wi, and w e I. According to the defination of score function Vi(xi, Wi,b), we have the inequality(9)

By the defination of total gain zb(Wi), the score Vi(xi, Wo,b) is the marginal gain of adding i to Wo. There-
fore, we have,
26(W%+i)-20(W%)=Vi(xi,W%,b)
(11)
≤Vi(xi, Wi,b)=0(W%+i)-c6(W%), where W,+imeans adding i to the set W,. Therefore, according to the definition of submodular function, it can be proved that the total gain function of each edge server is the submodular function with respect to user set.
Lemma 1 has been proved.



##### **(2) Convergence Guarantee**

DSO融合的一个必要条件是，所有用户都知道哪些是边缘服务器的得票人。该信息需要（至少）遍历通信网络中的所有用户一次。

网络中的用户可以相互通信，但他们只能在我们称之为邻居用户的通信范围内直接通信。超出通信范围的用户需要通过多个邻居转发进行通信。假设网络中两个最远的用户之间的通信需要*D*倍的邻居转发，称为网络直径。因此，一条消息最多必须转发*D*次才能为整个网络中的所有用户所知。

下面我们给出DSO的收敛定理。

定理1：算法收敛DSO算法迭代次数的上界为N2N，D。

定理1的证明：首先，我们给出所有用户最多需要kN，D次迭代才能就任何边缘服务器的第一个k获胜用户达成一致。接下来我们用归纳法来证明这个结论。

第一步（为了证明真实性fork=1）：由于我们算法的分布式性质，每个用户给出的编排决策是否能够获胜的信息必须经过所有用户的选择。从边缘服务器的总增益函数的子模块性可以看出，在第一次迭代中得分最高的编排决策将被最多N，-1个用户拒绝，也就是说，所有用户必须至少对该决策打分一次。

由于每个分数最多可以通过所有用户的D次迭代，然后所有用户都可以基于它进行获胜的集合选举。因此，所有用户最多需要一次又一次的迭代，才能就得分最高的编排决策达成共识。

第2步（假设k=h为真，以证明k=h+1为真）：假设结论为真时，即所有用户对之前的h达成一致-

通过hN、D迭代，最多只能使用ning用户。在之前的h共识获胜决定的基础上，下一个获胜决定也将被拒绝最多N，-1次，即最多N次投票。考虑到每个投票信息都需要经过D次迭代，以便所有用户都能知道，最多DN，需要迭代才能达成一致意见。因此，通过增加前一个h获胜决策的迭代次数，所有用户最多可以就前一个h+1获胜决策达成共识hN，D+NuD=（h+1）N，D次迭代。

第3步（入职培训）

从归纳法的属性可以证明，所有用户在最多进行kV，D次迭代的情况下，对之前的k个获胜决策达成一致。

总之，根据我们的算法，最坏的情况是有N，N个决策。因此，k=N，Ni，然后我们得出结论，所有用户在最多NaN，D次迭代中达成共识。这个定理已被证明。



A necessary condition for convergence in DSO is that all users are aware of which are the winning votes for an edge server. This information needs to traverse all users in the communication network (at least) once.

Users in the network can communicate with each other, but they can only communicate directly within the communication range in which we call them neighbor users. Users out of the communication range need to communicate through multiple neighbor forwarding. Assuming that the communication between two farthest users in the network needs *D* times of neighbor forwarding, called the diameter of the network. Therefore, a message must be forwarded by *D* times at most to be known to all users in the entire network. 

Below we give the convergence theorem of DSO.

Theorem 1: Algorithm Convergence The amont of DSO algorithm iterations has an upper bound N2N,D.
Proof of Theorem 1: First, we give that it takes at most kN,D iterations for all users to reach a consensus on the first k winning user of any edge server. Next we use induction to prove this conclusion.
Step 1(to prove trueness fork=1): Due to the distributed nature of our algorithm, the information that whether or not the orchestration decisiongiven by each user can win must go through the election of all users. From the submodularity of the edge server's total gain function, it can be seen that the orchestration decision with the highest score in the first iteration will be rejected by at most N,-1 users, that is, all users must have scored this dicision at least once.
And since each score can go through all users at most D iterations, and then all users can conduct winning set elections based on it. Therefore, it takes at most DN, iterations of iterations for all users to reach a consensus on the highest-scoring orchestration decision.

Step 2(assume trueness for k=h, to prove trueness for k=h+1): It is assumed that when the conclusion is true fork=h, that is, all users reach a consensus on the previous h win-
ning users at most through hN,D iterations. On the basis of the previous h consensus winning decisions, the next winning decision will also be rejected at most N,-1 times, that is, at most N, times of voting. Considering that each voting information needs to go through D iterations so that all users can know, at most DN, iterations is needed to reach a consensus of this decision. Therefore, by adding the iterations of the previous h winning decision, all users can reach a consensus on the previous h+1 winning decision at most hN,D+NuD=(h+1)N,D iterations.
Step 3(induction)
From the attribute of the induction method, it can be proved that all users reach a consensus on the previous k winning decision at most taking kV,D iterations.
In conclusion, according to our algorithm, the worst is that there are N,N, decisions. Therefore,k=N, Ni and then we conclude that all users reach consensus in at most NaN,D iterations. The theorem has been proved.



##### **(3) Performance Guarantee**

现在，我们用定理2给出性能保证。

定理2：算法性能通过我们的算法获得的边缘服务器b的效用z（Wc）以近似比率1-2逼近最优效用z（W*），即，

（我们）≥（1-2）=（w），12）

定理2的证明：在构造win的过程中-

算法DSO基于贪婪的思想将用户添加到初始集。根据带背包约束的greedy算法的性质，可以得到依赖于初始集的性能界。此外，我们还证明了该算法在定理2中具有常数因子逼近的性能界。

将边缘服务器b的最佳获胜集表示为W*=u，u…，u*，W*中的第一个i元素集表示为W*=u，u…，u，其中W*的元素按照以下公式排序，u=argmin zw，（u），（13）uEW*/W-1

式中，zw:，（u）表示将u添加到集合W1的边际增益。考虑INI时的情况

tial winning set是W*中第一个d元素的集合，表示为Wf。此时，DSO可以被视为一个带背包约束的贪婪算法。以I/WA为候选集，以边缘服务器剩余资源pd A pgax-r（WA）为资源约束，函数zw；作为边际收益。



Now, we give the performance guarantee with Theorem 2.
Theorem 2: Algorithm Performance The utility z(Wc) of edge server b obtained by our algorithm approximates the optimal utility z(W*) with an approximation ratio1-2, that is,
(We)≥(1-2)=(w),12)
Proof of Theorem 2: In the process of constructing win-
ning set, algorithm DSO adds users to an initial set based on a greedy idea. According to the properties of greedyalgorithm with knapsack constraint,a performance bound depending on the initial set can be obtained. Furthermore, we can prove that the algorithm has the performance bound with constant-factor approximation in Theorem 2.

Denote the optimal winning set of edge server b as W*=u,u…,u*, the set of the first i elements in W* as W*=u, us,…,u, where the elements of W* are ordered according to the following formula, u=argmin zw,(u),(13) uEW*/W-1
where zw:,(u) represents the marginal gain of adding u to the set W1. Consider the situation when the ini-
tial winning set is the set of the first d elements in W*, denoted as Wf. At this time, DSO can be regarded as a greedy algorithm with backpack constraint. It takes I/WAas the candidate set, the edge server remaining resource pd A pgax-r(WA) as the resource constraint, and the function zw; as marginal gain.

## 