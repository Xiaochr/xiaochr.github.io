---
title: 'Literature Review: Diffusion Models'
date: 2022-02-10
permalink: /posts/2022/02/diffusion/
tags:
  - literature review
---

Diffusion Models 整理

---

## 早期研究

关于创新或信息传播的研究可追溯至上个世纪四五十年代。Coleman et al. (1957) 研究一种新药在市场中的使用情况，发现在引入新药7个月时，几乎所有“opinion leader”医生都会在处方中使用这种药，过了这个点之后，新药的接受率（adoption rate）在 opinion leader 中降低，但在 follower 人群中增加（图中 not nominated 曲线）。

另一个著名的 diffusion 研究是 Ryan and Gross (1950) 的爱荷华州农民对新型杂交玉米采纳情况的调查。作者记录了259户农民在13年间对杂交玉米的采纳，发现总的采纳率随时间呈现S形曲线。除了那些 early adopters，大多数农民从“听说”到“采纳”花费了大约9年的时间。

### Bass Model

Bass Model (Bass, 1969) 是最经典的传播模型之一，它能够解释上文提到的S形采纳率，对之后的研究起到重要影响。

模型假设每个个体有“采纳”和“未采纳”两个状态，个体可以从“未采纳”状态转变为“采纳”状态（但不能反过来），而这个转变过程可以是**自发的**，也可以是**受他人影响的**。

$$F(t+1) = F(t) + P(1 - F(t)) + Q(1 - F(t))F(t)$$

其中：

- F(t): the fraction of population who have adopted by time t.
- P: the rate of **spontaneous adoption**, due to external factors.
- Q: the rate of **imitation of adoption**.

将上面的公式变形，我们得到：

$$F(t) = \frac{1 - e^{-(P+Q)t}}{1 + \frac{Q}{P}e^{-(P+Q)t}}$$

所以当 **Q > P** 时，采纳率的曲线呈左图中的 **S 形**；而当 **Q < P**，大多数的采纳是自发采纳，速度将变得更慢很多，最终曲线呈右图中的形状。

## 几个经典的传播模型

### Independent Cascade Model

**以往的研究都是对于人群整体进行建模，没有考虑社交网络和网络结构**，且假设人群是 homogeneous 的。Goldenberg et al. (2001a) 在 Bass Model 的基础上进行**个体层面**的研究，并给出在人群 **heterogeneous** 情况下的仿真结果。同时，Goldenberg et al. (2001b) 在另一篇文章中进一步考虑进**网络结构**（network size, strong ties and weak ties 等）对信息传播的影响。Kempe et al. (2003) 将 Goldenberg et al. (2001a, 2001b) 提出的这一模型称为 **Independent Cascade Model**。

模型中有三类结点：

- **I**: nodes that **have not adopted** and **inactive**.
- **C**: nodes that are **contagious** and **active**.
- **U**: nodes that **have adopted** and **not contagious**.

在每个时间点t，每个I类结点有概率 p 自发激活；每个C类结点有概率 $\beta$ 激活网络中的I类邻居结点，使其变成C类结点，不管是否激活成功，所有C类结点都在 t+1 时间点变成U类结点（即被激活的结点只有一次机会进行传播）。

模型在此基础上可以进行一些微调，例如：是否考虑自发激活；结点是否 homogeneous，即各个结点的自发激活概率和传播概率是否相同等。

### Threshold Model

Granovetter (1978) 提出对于 collective behaviors（如 adoption of innovation, voting, ritos 等）的 Threshold Model。模型假设每个个体都有各自的决策阈值 $\theta$ ，作者想要通过人群中的初始决策阈值分布来预测最终做出决策的人数。

在数学上相当于找到一个动态过程的 equilibrium。模型的一些 notations：

- $\theta_i$ : the threshold of individual i
- $f(\theta)$ : the fraction of individuals with threshold $\theta$ . $F(\theta)$ : cumulative distribution of f.
- $x_t$ : the fraction of individuals who have adopted by time t.

模型假设当 $x_t > \theta_i$ 时，即采纳的人数比例超过个人的决策阈值时，个体i将在下一时间点采纳。用数学形式表示即 $x_{t+1} = F(x_t)$ 。而传播停止的条件是采纳的人数比例不变，即 $x_{t+1} = x_t$ 。综上我们只需要解：

$$x^* = F(x^*)$$

$x^*$ 即为系统均衡时的采纳人数比例。下图为动态分析的直观示意图：

通过分析能够发现人群 threshold 的异质性（即分布的 standard deviation）对最终采纳的人群比例有很大影响，一般来说**更高的分布异质性能够带来更大的采纳比例**；但增加结点 degree 的分布异质性则会使得网络更加不易受感染 (Watts, 2002)。当 threshold 服从 U(0, 1) 的均匀分布时，模型称为 **Linear Threshold Model**；而上文所说的这种 threshold 分布不限的模型常被称为 **Generalized Threshold Model**。

### 传染病传播模型

传染病传播模型的原理实际上与上述几个模型大同小异（例如 Independent Cascade Model 可以认为是一种泛化的 SIR 模型 (Shakarian et al., 2015)），只不过在传染病传播的背景下增加了一些特定的规则。传染病模型中结点有三种状态：

- Susceptible: 未被感染的人
- Infected: 已被感染的病人
- Recovered: 已康复的人

按照不同的假设和传播规则，比较经典的模型有：

- SI 模型：模型中只有S和I两类结点，传播图像呈S形。
- SIR 模型：在 SI 模型的基础上增加R类结点，康复的结点不再被感染。
- SIS 模型：在 SIR 模型的基础上，康复的结点还会被再次感染。
- 还有 SIRS、SEIR 模型等等

每种模型都可以对人群整体进行分析，也可以结合网络结构分析，具体的公式不在此列出。

## 后续研究

之后对于 diffusion 的研究几乎都是基于 Independent Cascade Model、Threshold Model、SIR Model 等经典模型，在特定的研究背景下对模型进行修改完善。

根据**传播内容**的不同，有诸多不同的社会、文化、政治方面传播的文献，例如疾病传播、医疗健康 (Centola, 2010; Centola, 2011)、创新观点、政策、社交媒体上的信息传播等。不同的内容有时会形成不同的传播模式，例如 Vosoughi et al. (2018) 发现假新闻比起真新闻传播得更快更广泛。而在大多数情况下，尽管不同类型的传播内容对于传播过程和结果有影响，但适用于疾病传播的模型同样能够用于对其他内容传播的研究 (Guilbeault et al., 2018)。

传播人群的**社交网络结构特点**也受到高度重视，学者们将 small world、weak ties、scale-free、homophily 等网络特点融入 Threshold Model 等经典模型进行分析，在不同结构的网络中的传播过程往往有不同的特点。以 weak ties 为例，Granovetter (1973) 提出“the strength of weak ties”的观点，weak ties 这种与“远处”的人相连的“long ties”能够促进传播，Granovetter 认为“whatever is to be diffused can reach a larger number of people, and traverse a greater social distance, when passed through weak ties rather than strong.”。然而另外一些学者发现网络中的 dense clusters 和 local bridges 对于传播会起到阻碍作用，对于同质网络来说，只有在初始采纳者之外的 cluster density 都小于 $1 - \theta$ 时，才能产生一个 complete cascade。初始采纳者在网络中的位置也十分重要，他们必须很容易接触到易受传染的结点，且这些结点需要成功被传染，才能有更大的传播范围 (Watts&Dotts, 2007)。

在基础的模型中，一个 activated source 就足够进行传播，这种传播被称为 simple contagions；但现实中很多时候传播需要多个源的共同作用，如行为、信仰、态度等，即需要多次加强接触才能传播的 complex contagions。新近的研究很多关注的就是大规模复杂网络中的 complex contagions，例如 Centola et al. (2005)发现在 complex contagion (multiplex propagation) 的情况下，small world 网络中的 random links 反而会使得传播更不易实现。

Guilbeault et al.(2018) 的综述中很好地总结了 diffusion models 的近期研究进展。作者认为这些模型主要可以归类为两个方向：一是研究各种网络拓扑结构对传播的影响；二是考虑结点级别的不同特点，如个体结点的属性、阈值等。具体模型有很多，就不在此列出，详见文章的第三部分 Theoretical Advances。

同时，对于 diffusion 的研究也不再局限于构建刻画传播过程的模型，也引申出 influence maximization、发掘最有影响力的人、预测传播 cascade、对网络制定有效的攻击方案或对其进行预防等等有很大意义的问题。其中对于 (social) influence 在人群中传播的研究，除了上文提到的经典模型外，还经常用到 hazard model (Aral&Walker, 2011; Aral&Walker, 2012)。


---

## References

**Coleman, J., Katz, E., & Menzel, H. (1957). The diffusion of an innovation among physicians. *Sociometry*, *20* (4), 253-270.**

Ryan, B., & Gross, N. C. (1950). *Acceptance and diffusion of hybrid corn seed in two Iowa communities* (Vol. 372). Ames, IA: Agricultural Experiment Station, Iowa State College of Agriculture and Mechanic Arts.

**Bass, F. M. (1969). A new product growth for model consumer durables. *Management science*, *15* (5), 215-227.**

Goldenberg, J., Libai, B., & Muller, E. (2001a). Using complex systems analysis to advance marketing theory development: Modeling heterogeneity effects on new product growth through stochastic cellular automata. *Academy of Marketing Science Review*, *9* (3), 1-18.

Goldenberg, J., Libai, B., & Muller, E. (2001b). Talk of the network: A complex systems look at the underlying process of word-of-mouth. *Marketing letters*, *12* (3), 211-223.

**Kempe, D., Kleinberg, J., & Tardos, É. (2003, August). Maximizing the spread of influence through a social network. In *Proceedings of the ninth ACM SIGKDD international conference on Knowledge discovery and data mining* (pp. 137-146).**

**Granovetter, M. (1978). Threshold models of collective behavior. *American journal of sociology*, *83* (6), 1420-1443.**

**Watts, D. J., & Dodds, P. S. (2007). Influentials, networks, and public opinion formation. *Journal of consumer research*, *34* (4), 441-458.**

Shakarian, P., Bhatnagar, A., Aleali, A., Shaabani, E., & Guo, R. (2015). *Diffusion in social networks* (pp. 47-58). Cham, Switzerland: Springer International Publishing.

Watts, D. J. (2002). A simple model of global cascades on random networks. *Proceedings of the National Academy of Sciences*, *99* (9), 5766-5771.

**Guilbeault, D., Becker, J., & Centola, D. (2018). Complex contagions: A decade in review. *Complex spreading phenomena in social systems*, 3-25.**

Vosoughi, S., Roy, D., & Aral, S. (2018). The spread of true and false news online. *Science*
, *359*(6380), 1146-1151.

Centola, D. (2011). An experimental study of homophily in the adoption of health behavior. *Science*, *334* (6060), 1269-1272.

Centola, D. (2010). The spread of behavior in an online social network experiment. *science*
, *329*(5996), 1194-1197.

Centola, D., Macy, M. W., & Eguiluz, V. M. (2005). Cascade dynamics of multiplex propagation. *arXiv preprint physics/0504165*.

Aral, S., & Walker, D. (2011). Creating social contagion through viral product design: A randomized trial of peer influence in networks. *Management science*, *57*(9), 1623-1639.

Aral, S., & Walker, D. (2012). Identifying influential and susceptible members of social networks. *Science*, *337*(6092), 337-341.




