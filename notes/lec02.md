# Lecture 02: Imitation Learning

[lecture video](https://www.youtube.com/watch?v=tbLaFtYpWWU&list=PL_iWQOsE6TfVYGEGiAOMaOzzv41Jfm_Ps&index=4&pp=iAQB)

[slides](../handouts/slides/lec-2.pdf)

[homework](../homework/hw1/hw1.pdf)

## Introduction

**模仿学习**（imitation learning）一种强化学习方式，旨在学习所提供的专家行动。

虽然与监督学习相同的是两者都在学习过程中提供了参考的解决方案，并以与其相偏离的程度作为成本函数，但是模仿学习有时会在与环境交互的过程中遇上没有参考解的陌生境地。

## Terminology

强化学习将系统的运行过程简化为两个部分的循环：**观察**（observation）与**行动**（action）。

观察从环境的真实**状态**（state）中获取数据，而行动则通过**代理**机器（agent）的**策略**（policy）基于观察做出，并影响环境状态。

如果用马尔可夫决策过程（Markov decision process）描述这一过程，则可有定义：
$$
\begin{align*}
    &s_t\ -\ state \\
    &o_t\ -\ observation \\
    &a_t\ -\ action \\
    &\pi_\theta(a_t\mid o_t)\ -\ policy \\
    &\pi_\theta(a_t\mid s_t)\ -\ policy(fully\ observed) \\
\end{align*}
$$
系统的行动过程则可描述为：
$$
\begin{align*}
    &P(O_t=o_t\mid s_t) = o_t(s_t) \\
    &P(A_t=a_t\mid o_t)=\pi_\theta(a_t\mid o_t) \\
    &P(S_{t+1}=s_{t+1}\mid s_t,a_t)=p(s_{t+1}\mid s_t,a_t) \\
\end{align*}
$$

## Training

We'll use some kind of training data and some kind of training model$\pi_\theta$ to gain the parameter \theta, which will be used for implement the system later.

More specifically, the model parameter is gained from the supervised learning problem using a MLE method:
$$
\max_\theta E_{o_t \sim p_{data}(o_t)}[log\pi_\theta(a_t\mid o_t)]
$$
> PS: still not clear why there's using expected log likelihood for maximization

> or can I just interpret this maximization problem into a similar form like the following
>$$
    \max_\theta \sum_t[log\pi_\theta(a_t\mid o_t)]
>$$
>this's reasonalbe if $\{o_t\}$ is a discrete time series and $p_{data}(o_t)$ equals to the frequency of $o_t$.

## Problems

The distributional shift problem

### **e.g.** *self driving car*

Suppose we trained a agent using human experts' driving data $o_t \sim p_{data}(\cdot)$ and $a_t \sim \pi^\star(\cdot\mid o_t)$ and gained a model action distribution $\pi_\theta(\cdot\mid o_t)$.
>PS: Though action is our agent's own decision, it's still not deterministic and follows a conditioned distribution $\pi_\theta(\cdot\mid o_t)$.

But agent's driving trajectory may be different from expert's, which in turn cause the difference in distribution of $o_t$. And this is the distribution shift problem.

### Analysis

The distribution drift may come from many reasons: 
But the true problem is, this drift tends to increase over time.

If we define cost function as:
$$
c(s_t,a_t):=I(a_t=\pi^\star(s_t))
$$
and set our goal as:
$$
\min E_{s_t\sim p_{\pi_\theta}(s_t)}[c(s_t,a_t)]
$$

## Solutions

- Be smart about how we collect (and augment) our data
- Use very powerful models that make very few mistakes
- Use multi-task learning
- Change the algorithm (DAgger)

### Be smart about how we collect (and augment) our data

Collect more data to cover mistakey situations and solutions to these situations so that we could make it harder for agents to be in unfamiliar situations.

- Intentionally add mistakes and corrections
- Use data augmentation

### Use very powerful models that make very few mistakes

Here we are to address two different problems that might cause trajectory drifts.

1. Non-Markovian behavior
2. Multimodal behavior

#### Non-Markovian behavior

Under circumstances of Non-Markovian behavior, the distribution of $a_t$ is no longer just conditioned on $o_t$ and but on all of the history observations $o_1, ... , o_t$

#### Multimodal behavior

As for multimodal behavior, we can use corresponding models, such as:

1. mixture of Gaussians
2. latent variable models
3. diffusion models

### Use multi-task learning

不太懂

### Change the algorithm (DAgger)

DAgger(Dataset Aggregation) refers to an algorithm that aims to collect human actions under agent trajecories.
