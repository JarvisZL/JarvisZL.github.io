# 组合数学


# 基本计数

<!--more-->

## **基本计数规则**
- 两个disjoint集合满足: $|S|+|T| = |S\bigcup T|$
- 两个集合的笛卡尔乘积: $|S\times T| = |S|\cdot |T|$
- 集合大小相等：如果$S \mathop{\Longleftrightarrow}\limits_{on-to}^{1-1} T$，则$|S| = |T|$

## **Twelvfold way**
| n balls | m bins | 没有限制 | 每个bin至多一个| 每个bin至少一个 |
| :-------: | :------: | :--------:| :--------------:| :----------: |
|   不同  |  不同   | $m^n$(<font color=red>tuple</font>)<font color=blue>1</font>  | $m_n(A_m^n)$(<font color=red>function</font>)<font color=blue>2</font>|$m!{n\brace m}$(<font color=red>先分为m类再排列</font>)<font color=blue>9</font> |
|   相同 |  不同   |$\left(\binom{m}{n}\right) = \binom{m+n-1}{n}$(<font color=red>弱整数分解,多重集</font>))<font color=blue>5</font>|$\binom{m}{n}$(<font color=red>fixed size subset</font>)<font color=blue>3</font>|$\binom{n-1}{m-1}$(<font color=red>隔板法,整数分解</font>)<font color=blue>4</font>|
| 不同|相同|$\sum_{k=1}^{m}{n\brace k}$(<font color=red>集合划分</font>)<font color=blue>8</font>|1  $m\geq n$ <br> 0 $m<n$<font color=blue>6</font>|${n\brace m}$(<font color=red>集合划分</font>)<font color=blue>7</font>|
| 相同|相同|$\sum_{k=1}^{m}P_k(n)$(<font color=red>整数无序划分</font>)<font color=blue>11</font>|1  $m\geq n$ <br> 0 $m<n$<font color=blue>12</font>|$P_m(n)$(<font color=red>整数无序划分</font>)<font color=blue>10</font>|


### **整数分解(隔板法)**
- 分钱问题，n块钱分给m个不同的人，**要求每个人至少有一块**
  $$x_1+...+x_m = n$$

### **弱整数分解,多重集**
- 分钱问题，n块钱分给m个不同的人
  $$x_1+...+x_m = n$$
  - 可以多给m块钱，每个人至少那一块，所以变成
    $$x_1^\prime+...+x_m^\prime = m+n$$
- 多重集：从S集合(m)里面选n元**多重集**即允许每个元素出现多次，n元多重集的个数：
  $$ \left(\binom{m}{n}\right)(多重集) = \binom{m+n-1}{n}(弱整数分解)$$

### 二项式系数
- n个不同的球放入k个不同的篮子并且第i个篮子中放$m_i$个球：$\binom{n}{m_1,m_2,...,m_k}$
  $$\binom{n}{m_1,m_2,...,m_k} = \dfrac{n!}{m_1!m_2!...m_k!}$$

### **集合划分**
- 规定划分的集合数：将n个人划分到m条相同的船上.
  $${n\brace m}(第二类斯特林数)$$
- 求所有划分集合数：
  $$\sum_{k = 1}^{m}{n\brace k}$$
- 第二类斯特林数：
  - ${n\brace k} = k{n-1 \brace k} + {n-1 \brace k-1}$ 根据n是不是单独一类判断。

### **整数无序划分**
- n块钱划分到m个相同的箱子里，和前面的整数分解不同的时，这里没有序的约束。
- 定义 $P_m(n)$表示该分解的种类即满足$\begin{cases}x_1+x_2+...+x_m = n\\ x_1 \geq x_2\geq ...\geq x_m\geq 1\end{cases}$
  - $P_m(n) = P_{m-1}(n-1) + P_m(n-m)$按照$x_m$是否为1.
- **Ferrers图**
  - ![](/images/documents/组合数学/ferrers1.png)
  - ![](/images/documents/组合数学/ferrers2.png)(k行每一行都删除一个)


<br><br><br>

# 生成函数
## 例子
- 用来表示子集的个数
  - n个元素的k元子集：
    $$(x^0+x^1)^n = (1+x)^n$$
    求$x^k$的系数。
- 两幅扑克抽卡
  - 抽m张卡的情况：
    $$(1+x+x^2)^n$$
    求$x^m$的系数。
- 多重集的个数
  - n个元素k元多重子集:
    $$(1+x+x^2+...)^n = (\dfrac{1}{1-x})^n = \sum_{k\geq0}\left(\binom{n}{k}\right)x^k$$
- 斐波那契数列
  $$G(x) = F_0+F_1x+\sum_{k\geq 2}F_kx^k = 0+x+\sum_{k\geq 2}F_{k-1}x^k + \sum_{k\geq 2}F_{k-2}x^k$$ 
  $$\sum_{k\geq 2}F_{k-1}x^k = 0+x\sum_{l\geq 1}F_l x^l = xG(x)$$
  $$\sum_{k\geq 2}F_{k-2}x^k = x^2G(x)$$
  所以
  $$G(x) = x + xG(x) + x^2G(x)$$
  即
  $$G(x) = \dfrac{x}{1-x-x^2} = ... = \sum_{n\geq 0}\dfrac{1}{\sqrt{5}}(\phi^n-\hat{\phi^n})x^n$$

## 常用公式
- $$\dfrac{1}{1-\alpha x} = \sum_{k = 0}^{\infty}(\alpha x)^k$$
- $$\dfrac{a}{1-bx} = a\sum_{k=0}^{\infty}(bx)^k$$
- $$G(x) = \sum_{k = 0}^{\infty}\dfrac{G^{(k)}(0)}{k!}x^k$$
- $$(1+x)^n = \sum_{k = 0}^{n}\binom{n}{k}x^k$$

## 生成函数公式
- $$x^kG(x) = \sum_{n\geq k} g_{n-k}x^n$$
- $$F(x)+G(x) = \sum_{n \geq 0} (f_n+g_n)x^n$$
- $$G^\prime(x) = \sum_{n\geq 1} ng_{n}x^{n-1}$$
- $$F(x)G(x) = \sum_{n\geq 0}\sum_{k = 0}^{n}f_kg_{n-k}x^n$$

## 应用-Change Money
- 用一块钱换n块钱的生成函数： $G_1(x) = \sum_{n\geq 0}\alpha_nx^n = 1+x+x^2+.... = \dfrac{1}{1-x}$
- 用五块钱换n块钱的生成函数： $G_5(x) = \sum_{n\geq 0}\beta_nx^n = 1+x^5+x^{10}+.... = \dfrac{1}{1-x^5}$
- 则同时用一块和五块取换n块钱:  $\gamma_n = (\alpha,\beta)_n = \sum_{k = 0}^{n} \alpha_k \beta_{n-k}$,则
  $$F(x) = \sum_{n\geq 0}\gamma_nx^n =\sum_{n\geq 0}\sum_{k=0}^{n}\alpha_k\beta_{n-k}x^n\mathop{=}\limits^{卷积}G_1(x)\cdot G_5(x) = \dfrac{1}{(1-x)(1-x^5)}$$

## 卡特兰数
- 例子：
  - n对括号匹配(右括号的数量在任意时刻都不能超过左括号)
    - 使用$n\times n$的图中的路径来表示。
    - 每一条从(0,0)到(n,n)的路径和一种匹配方案一一对应。
  - 拥有n+1个叶子节点的满二叉树的数量
  - n+1凸多边形三角化的方案
- $$C_n = \dfrac{1}{n+1}\binom{2n}{n}$$

## 求解生成函数步骤
- 找出递推式
- 通过递推式使用 **生成函数常用公式** 构造G(x)关于自己的等式。
- 求解G(x)
- 将G(x)规范化(奇技淫巧....)

<br><br><br>

# 筛法
## 容斥原理
$$|\bigcup_{i = 1}^{n}A_i| = \sum_{I\subseteq [n],I\neq \emptyset} (-1)^{|I|-1}|\bigcap_{i\in I}A_i|$$
则可以定义$A_i$为坏事件，则 **坏事件都不发生**的情况有
$$|\bigcap_{i = 1}^{n}\bar{A_i}| = |U-\bigcup_{i=1}^{n}A_i| = |U| - |\bigcup_{i = 1}^{n}A_i| = |U| - \sum_{I\subseteq [n],I\neq \emptyset} (-1)^{|I|-1}|\bigcap_{i\in I}A_i|$$
定义$A_{\emptyset} = U，A_I = \bigcap_{i\in I}A_i$,则
$$|\bigcap_{i = 1}^{n}\bar{A_i}| = \sum_{I \subseteq [n]} (-1)^{|I|}|A_I|$$


## 应用：用来**计算第二类斯特林数**:<br>
  考虑twelvford way中n个不同元素到m个不同元素的满射，根据集合划分可以得到结果为$m!{n \brace m}$.<br>
  定义如下坏事件$A_{i} = [n] \longrightarrow [m] - \{i\}$,则满射的情况就是这些坏事件都不发生的情况即
  $$|\bigcap_{i = 1}^{m}\bar{A_i}| = \sum_{I \subseteq [m]} (-1)^{|I|}|A_I|$$
  而对于$A_I$而言，相当于一个$[n]\longrightarrow [m]-I$,根据twelvford way第一个$|A_I| = (m-|I|)^n$,所以
  $$|\bigcap_{i = 1}^{m}\bar{A_i}| = \sum_{I \subseteq [m]} (-1)^{|I|}(m-|I|)^n = \sum_{k = 0}^{m}(-1)^k\binom{m}{k}(m-k)^n$$
  也就是有$m!{n\brace m} = \sum_{k = 0}^{m}(-1)^k\binom{m}{k}(m-k)^n$,所以${n\brace m} = \dfrac{1}{m!}\sum_{k = 0}^{m}(-1)^k\binom{m}{k}(m-k)^n$

## Derangement
### 1. 原始版本：对[n]重排列，保证$\forall i,\pi_i \neq i$
  定义坏事件$A_i = \{\pi|\pi_{i} = i\}$,则
  $$|\bigcap_{i = 1} ^ n \bar{A_i}| = \sum_{I\subseteq [n]}(-1)^{|I|}|A_I|$$
  其中$|A_I| = (n-|I|)!$(确定了|I|其他全排)
  $$|\bigcap_{i = 1} ^ n \bar{A_i}|  = \sum_{k=0}^n (-1)^k\binom{n}{k}(n-k)! = n!\sum_{k = 0}^n\dfrac{(-1)^k}{k!}\approx \dfrac{n!}{e}$$
<br>

### 2. 一般性：对[n]重排列，保证$\pi_{i_1} \neq j_1,....$ 
棋盘(i,$\pi_i$),对于要求$\pi_{i_1} \neq j_1$，就在棋盘中$(i_1,j_1)$处打叉，最后在一个有叉的棋盘中保证每行每列只有一个棋子。<br>
根据容斥原理:
$$N = \sum_{k = 0}^n(-1)^kr_k(n-k)!$$
选定k个放在对应打叉的地方，其他重排，$r_k$为k个棋子放在打叉的地方的放法，k=0时是Universe. **关键在于求$r_k$**<br>
**n couple问题**:略

## 欧拉函数
假设 $n = {p_1}^{k_1}{p_2}^{k_2}...{p_r}^{k_r}$ 
$$\phi(n) = n\prod_{i=1}^r(1-\dfrac{1}{p_i})$$
定义坏事件$A_i = \{a|1\leq a\leq n, p_i|a\},A_I = \{a|\forall i \in I,p_i|a\}$,$|A_I| = \dfrac{n}{\prod_{i\in I}p_i}$
则
$$|\bigcap_{i = 1} ^ r \bar{A_i}| = \sum_{k=0}^r \sum_{I\in \binom{[r]}{k}}(-1)^k\dfrac{n}{\prod_{i\in I}p_i} = n\prod_{i=1}^r(1-\dfrac{1}{p_i})$$

# 存在性
## 握手定理
$$\sum_{v\in V} d(v) = 2|E|$$
- 可由握手定理推出**sperner定理**: 任意一个合法的染色(大三角形三个顶点不同颜色，然后一条线中间的点只能用线段两端点的颜色染)，必然有一个三个顶点不同颜色的三角形。
## 鸽笼原理
- 迪利克雷近似.
  - 对于任意无理数x，任取正整数n,必然存在一个有理数$\dfrac{p}{q}$，满足$|x-\dfrac{p}{q}| < \dfrac{1}{nq}$
  - 鸽子: $\{kx\}$,$k$取$\{1,....,n+1\}$,$\{x\}$表示x的小数部分。
  - 笼子：$(0,1/n),(1/n,2/n),...,((n-1)/n,1)$
- 不可避免相除关系
  - 在[2n]中选择$N > n$个数，则必然有两个数是倍数关系。
  - 鸽子：N个数
  - 笼子：$C_m = 2^km, 2^km \leq 2n$,m取[2n]中所有奇数.
- 单调序列
  - 如果一个序列$L>mn$，那么在序列中一定有m+1的单调递增序列或者n+1的单调递减序列
  - 鸽子：L个数
  - 笼子：$m\times n$个格子，每个格子$(x_i,y_i)$表示以$a_i$结尾的最长单调递增序列长度或以$a_i$开始的最长单调递减序列长度。


# 概率法
- 形式一： 如果$P(x) < 1$,则存在某种情况$\bar{x}$会出现。(如 Ramsey's theorem)
- 形式二： **平均值原理**:这里有一个...最少(最多)有k个...。(如 Hamiltonian paths in tournament)
  - 即求期望$E[X] = k$。
  - 常用随机指示变量来表示随机Sample的一个图里面满足或不满足条件的边或者其他。
  - **一个n个点m条边的图，存在一个至少为$\dfrac{n^2}{4m}$的独立集**
    - 随机Sample一个图，然后删除其中的不符合条件(边在图里)的点，求期望.
  - **作业题：n结点d-正则图，存在一个至多为$\dfrac{n(1+\ln(d+1))}{d+1}$的点支配集**
    - 随机sample一个图，然后加入那些自身和邻居都不在图中的点，求期望.

## Lovász Local Lemma
- 定义坏事件$A_1,...,A_n$.
- 构建依赖图，如果事件$A_i,A_j$不是相互独立的，那么在图中加一条边(i,j)
- 条件：d:max degree of dependency graph
  $$ \forall i, Pr[A_i] \leq p$$ 
  $$ ep(d+1) \leq 1$$
- 结论(存在坏事件都不会发生的情况)： 
  $$Pr\{\bigcap_{i=1}^n \bar{A_i}\} > 0$$
  

# 极值图论
- 极值图：如果图G满足条件T，则图G**至多**有多少条边?

## Cyclic-free
- $|E| = n-1$
- ex: Spanning tree
## Triangle-free
- ex: Bipartite graph
- **Mantel's Theorem**
  - 图G(V,E)，|V| = n，并且满足triangle-free，则$|E|\leq \dfrac{n^2}{4}$
- 证明：TBD
## Turan's Theorem
- Turan's Theorem推广了Mantel's Theorem到$K_r$-free.
- 图G(V,E)，|V| = n，并且满足$K_r$-free，则$|E|\leq \dfrac{(r-2)}{2(r-1)}n^2$
- ex: Complete multipartite graph $K_{n_1,n_2,...,n_r}$
- Turan graph: $T(n,r) = K_{n_1,n_2,...,n_r}$
- **Turan's Theorem(independent set)**:
  - 图G(V,E)，$|V| = n, |E| = m$，则图G存在一个**独立集**的大小满足$>\dfrac{n^2}{2m+n}$
- 定义ex(n,H): 表示图G(V,E)，$|V| = n$，且$H \nsubseteq G$，G能有的最大边数，所以Turan's Theorem 可以改写为$ex(n,K_r) \leq \dfrac{r-2}{2(r-1)}n^2$


# 极值集合论
## Sunflower Lema
- 向日葵：$\mathcal{G}$是一个大小为**r**的向日葵，则$|\mathcal{G}| = r，\forall S,T \in \mathcal{G}, S\bigcap T = C$
- 若$\mathcal{F} \subseteq \binom{[n]}{k}$,如果$|\mathcal{F}| > k!(r-1)^k$,则在$\mathcal{F}$中存在一个大小为r的**向日葵**。
- **数学归纳法**证明：
  - k = 1 时显然成立。
  - 假设$k-1$之前成立，则$k$时，取$\mathcal{F}$中最大的不相交的集合$\mathcal{G}$
    - 如果$|\mathcal{G}| \geq r$,则成立。
    - 如果$|\mathcal{G}| \leq r-1$,则找一个**popular-x**,令$H = \{S\backslash \{x\}|x \in S \land S\in \mathcal{F}\}$,只要证明$|H| > (k-1)!(r-1)^{k-1}$即可。
    - 令$Y = \bigcup S,S\in \mathcal{G}$,则$|Y| \leq k*(r-1)$则可以知道$Y$覆盖$\mathcal{F}$,否则仍然存在一个集合A，使得$A\bigcap Y = \emptyset$,和$\mathcal{G}$是最大不相交集合矛盾
    - 再根据**平均值原理**，则一定有一个值$x\in Y$满足包含x的集合数$> \dfrac{|\mathcal{F}|}{|Y|} = \dfrac{k!(r-1)^k}{k(r-1)} = (k-1)!(r-1)^{k-1}$,得证。

## The Erdős–Ko–Rado Theorem
- 令$\mathcal{F} \subseteq \binom{[n]}{k}$且$n > 2k$，则如果$\mathcal{F}$是相交的，那么
  $$|\mathcal{F}| \leq \binom{n-1}{k-1}$$

## Sperner system
- **反链**：集族$\mathcal{F}\subseteq 2^[n]$是一个反链，则对于任意$S,T\in \mathcal{F},S\neq T$有$S\nsubseteq T$ 
- Theorem (Sperner 1928):
  - 如果$\mathcal{F}\subseteq 2^{[n]}$是一个反链，那么$|\mathcal{F}| \leq \binom{n}{\left\lfloor n/2\right\rfloor}$ 

# Ramsey's Theorem
- $\cdots$

# Hall's Theorem
- 一系列集合(可能相交)，如果能在每个集合中挑选一个代表元素，且这些代表元素不重复，则称存在一个SDR(不同集合代表系统)
- Hall's Theorem:
  - 如果$S_1,...,S_m$存在一个SDR**当且仅当**$\forall I\subseteq [m],|\mathop{\bigcup}\limits_{i\in I}S_i| \geq |I|$
  - 证明：分$S_1,...,S_m$中有没有critical family:$|\mathop{\bigcup}\limits_{i = 0}^{k}S_i| = k$
    - 无：则取一个$x_m$作为$S_m$的代表元，剩下一个子问题，根据数归得证
    - 有：不妨设为最后k个即$S_{m-k+1},...,S_{m}$,则令$X = \mathop{\bigcup}\limits_{i = 0}^{k}S_i = \{x_1,...,x_k\}$,令$S^\prime_i = S_i\backslash X$,则剩下$S^\prime_1,...,S^\prime_{m-k}$,根据数归得证。
