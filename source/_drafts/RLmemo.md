---
title: Reinforcement Learning Classroom
tags:
mathjax: true
---

# COMPGI13 Memo

## Lecture #1

Value-Based Policy-Based and Actor-Critic

Model-Based and Model-Free

Reinforcement Learning Problem vs Planning Problem

## Lecture #2

### MRP
Bellman Function and its Vectorized format

Solve bellman function directly by Linalg

Dense Large MRPs

$$v =(I-\gamma P)^{-1}R$$

Solvers

- Dynamic programming
- Monte-Carlo evalution
- Temporal-Difference learning

### MDP
Bellman E for $V^\pi$
$$v_\pi = \sum_{a\in A}\pi(a|s)q_\pi(s,a)$$

Bellman E for $Q^\pi$
$$q_\pi(s,a)=R_s^a+\gamma\sum_{s'\in S}P_{ss'}^av_\pi(s')$$

Concated $V^\pi$
$$v_\pi = \sum_{a\in A}\pi(a|s)(R_s^a+\gamma\sum_{s'\in S}P_{ss'}^av_\pi(s'))$$

Concated $Q^\pi$

$$q_\pi(s,a)=R_s^a+\gamma\sum_{s'\in S}P_{ss'}^a\sum_{a'\in A}\pi(a'|s')q_\pi(s',a')$$

## Lecture #3

### Policy Based

$$v_{k + 1}(s) \dot{=} \mathbb{E_\pi} [R_{t + 1} + \gamma v_k(S_{k+t})|S_t = s] \\\\
=\sum_a \pi(a | s) \sum_{s', r}p(s', r|s, a)[r + \gamma v_k(s')]$$
Steps:
1. Policy Evaluation
2. Policy Improvement

### Value Based

$$v_\star(s) = \max_a \mathbb{E}[R_{t+1} + \gamma v_\star(S_{t+1}) | S_t = s, A_t = a] \\\\
= \max_a \sum_{s', r} p(s', r|s, a)[r + \gamma v_\star(s')]$$

$$v_{k + 1}(s) \dot{=} \max_a \mathbb{E}[R_{t+1} + \gamma v_t (S_{t+1}) | S_t = s, A_t = a] \\\\
=\max_a \sum_{s', r} p(s', r|s, a)[r + \gamma v_k (s')]$$

## Lecture #4

### MC Learning

$$V(S_t) \dot{=} V(S_t) + \alpha(G_t - V(S_t))$$

where $G_t$ is actual measured value
### Temperal-Difference Learning

$$V(S_t) \dot{=} V(S_t) + \alpha(R_{t+1} + \gamma V(S_{t+1} - V(S_t)))$$

where $R_{t+1} + \gamma V(S_{t+1})$ is estimated value.

$R_{t+1} + \gamma V(S_{t+1})$ is called the TD target.

$\delta_t = R_{t+1} + \gamma V(S_{t+1}) - V(S_t)$ is called the TD error.

### Comparsion

- TD can learn before knowing the final outcome
    - TD can learn online after every step.
    - MC must wait until end of episode before return is known


- TD can learn without the final outcome
    - TD can learn from incomplete sequences
    - MC can only learn from complete sequences
    - TD works in continuing ( non-terminating ) environments
    - MC only works for episodic (terminating) environments


- MC has high var, zero bias
    - Good convergence properties
    - (even with function approximation)
    - Not very sensitive to intial value
    - Very simple to understand and use


- TD has low var, some bias
    - Usually more efficient than MC
    - TD(0) converges to $v_\pi (s)$
    - (but not always withfunction approximation)
    - More sensitive to intial value


- TD exploits Markov property
    - Usually more effective in Markov environments


- MC does not exploit Markov property
    - Usually more effective in non-Markov environments

## Lecture #5

### On and Off policy learning
判断on-policy和off-policy的关键在于，你所估计的policy或者value-function  和  你生成样本时所采用的policy  是不是一样。如果一样，那就是on-policy的，否则是off-policy的。[^1]
[^1]:http://blog.csdn.net/mmc2015/article/details/58021482

- On 
    - Learn on the job
    - Learn about policy $\pi$ from experience sampled from $\pi$


- Off
    - Look over someones' sholder
    - Learn about policy $\pi$ from experience sampled from $\mu$


### Sarsa (On-policy)

Updateting Action-Value function:
$$Q(S,A) \dot{=} Q(S,A) + \alpha (R + \gamma Q(S',A') - Q(S,A))$$

### Off-Policy Learning
- 通过观察人类或其他Agent的行为学习
- 利用旧的策略学习新的策略
- 在策略探索中学习到最优策略
- 在一个策略中学习到多种策略

#### 重要性采样

$$\mathbb{E}_{X\sim P} [f(X)]=\sum{P(X)f(X)} \\\\ = \sum{Q(X)\frac{P(X)}{Q(X)}f(X)} \\\\ = \mathbb{E}_{X\sim Q}[\frac{P(X)}{Q(X)}f(X)]$$

##### Off-Policy MC 重要性采样

- 利用从$\mu$采样的信息来评价$\pi$
- 利用$\mu$分布和$\pi$分布的相似性对得到的$G_t$加权

$$G^\frac{\pi}{\mu}_t=\frac{\pi(A_{t}|S_{t})}{\mu(A_{t}|S_{t})}\frac{\pi(A_{t+1}|S_{t+1})}{\mu(A_{t+1}|S_{t+1})}...\frac{\pi(A_{T}|S_{T})}{\mu(A_{T}|S_{T})}G_t$$ 

- 更新V函数

$$V(S_t) \dot(=) V(S_t) + \alpha (\color{brown}{G^\frac{\pi}{\mu}_t} - V(S_t))$$

- 如果$\mu$为0时 $\pi$不为零不可使用
- 会增加var

##### TD-Policy MC 重要性采样

- 对TD目标$R+\gamma V(S')$进行加权
- 只需要进行一次重要性采样

$$V(S_t) \dot{=} V(S_t) + \alpha (\color{brown}{\frac{\pi(A_t|S_t)}{\mu(A_t|S_t)}(R_{t+1}+\gamma V(S_{t+1}))} - V(S_t))$$

- 比MC的var要低
- 只需要评估一步的策略相似度

#### Q-Learning

- 不需要进行重要性采样
- 下一步操作从行为策略来采样 $A_{t+1} \sim \mu(\dot{}|S_t)$
- 认为存在一个备选的操作是从目标策略中采样的,记作$A' \sim \pi(\dot{}|S_t)$
- 利用这个假象备选的操作来更新Q函数

$$Q(S_t,A_t) \dot{=} Q(S_t,A_t) + \alpha (\color{brown}{R_{t+1} + \gamma Q(S_{t+1},A')} - Q(S_t,A_t))$$

我们可以同时提升行为策略和目标策略

现在假设$\pi$是在$Q(s,a)$上的$greedy$策略,即
$$\pi(S_{t+1}) = \mathop{argmax}_{a'}{Q(S_{t+1},a')}$$
行为策略是$\mu$是在$Q(s,a)$上的$\epsilon-greedy$策略

Q-Learning目标函数可以被简化为

$$R_{t+1} + \gamma Q(S_{t+1}, A') \\\\ = R_{t+1} + \gamma Q(S_{t+1}, \mathop{argmax}_{a'} Q(S_{t+1},a')) \\\\ = R_{t+1} + \mathop{max}_{a'} \gamma Q(S_{t+1},a')$$

## Lecture #6 Value Function Approximation

用抽象的函数来逼近离散的最优查找表函数,不仅可以极大的压缩存储量,提升效率,而且可以实现泛化:
$$\hat{v}(s,w) \simeq v_\pi(s)$$
$$\hat{q}(s,a,w) \simeq q_\pi(s,a)$$

可以选择的近似函数:
- 线性组合
- 神经网络
- 决策树
- 最近邻
- 傅立叶/小波变换
- ...

### DQN的记忆重放
DQN利用了经验重放和固定Q目标函数:
- 利用$\epsilon-greedy$策略选择一个动作$a_t$
- 将转移对$(s_t,a_t,r_{t+1},s_{t+1})$存储到记忆D中
- 从D中随机采样一个mini-batch的$(s,a,r,s')$数据
- 利用一个旧的拟合模型参数$w^-$计算Q的目标
- 优化Q和目标函数之间的MSE,Loss函数为
$$L_i(w_i) = \mathbb{E}_{s,a,r,s'\sim D_i} [(r + \gamma \mathop{max}_{a'} Q(s', a';w^-_i))^2]$$
- 随机梯度下降

### DQN玩Atari
- 端到端的学习, 像素$s$到函数$Q(s,a)$
- 状态$s$是最新4帧的堆叠(stack)
- 输出的$a$是手柄的18个值(摇杆和案件)
- Reward是分数的变化

## Lecture #7 Policy Gradient Methods
与Value-Based方法不同,直接对策略建模
$$\pi_\theta(s,a) = \mathbb{P}[a|s,\theta]$$

|     RL方法     |      学习特点    |
| ------------- | --------------- |
| Value-Based   | 学习值函数,隐含策略|
| Policy-Based  | 直接学习策略      |
| Actor-Critic  | 同时学习策略和值函数|


# CMU_10703

Skimming Lecture 1-5

## Lecture 6 Planning and learning: Dyna, Monte carlo tree search

### Model-based RL

优点:
- 方便迁移
- 在稀疏奖励信号的时候可以更好的利用经验来判断
- 类似人类的学习方法
- 有助于探索

缺陷:
- 首先学习模型,然后构造value function, 会导致学习时有两个误差源

### 模型
模型包括:
1. 转移函数
2. 奖励函数

分布模型:
1. 转移概率$T(s'|s,a)$

采样模型:
即模拟模型, 可以从给定的state产生经验数据, 当其他模型都给出合适的估计后,可以用来生成假设数据