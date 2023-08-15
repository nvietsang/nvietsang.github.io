---
layout: post
title: SIFA on Masked AES with Fault Countermeasures
---

This post explains how to use SIFA to attack AES implementations protected with both masking and detection-based fault countermeasures. The nice idea of this attack was presented in the paper [[Dobraunig et al., ASIACRYPT 2018]](https://eprint.iacr.org/2018/357.pdf) that I enjoyed reading very much.

## 1. Main idea
First, we know the AES S-box: $$b = S[a]$$ (for instance, $$99 = S[0]$$, see the [S-box](https://en.wikipedia.org/wiki/Rijndael_S-box)). When applying masking, we compute the output shares $$b_0, ... ,b_d$$ given the input shares $$a_0, ...,a_d$$ such that $$b = b_0 \oplus ... \oplus b_d$$ and $$a = a_0 \oplus ... \oplus a_d$$. Here, $$d$$ is called the order of masking.
This computation can be done by AND and XOR gates (see [[Rivain et al., CHES 2010]](https://eprint.iacr.org/2010/441.pdf), for example). The main idea is that faulting a single share on an AND gate during the S-box computation is sufficient to induce a bias in an unshared S-box output $$b$$.

## 2. Single Fault on Masked AND gate
Let's consider the unshared AND operation $$q = xy$$. From the following truth table, it is easy to see that the probability $$Pr[q=1] = 0.25$$ and $$Pr[q=0] = 0.75$$.

| $$x$$ | $$y$$ | $$q$$ |
| :-: | :-: | :-: |
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

In the shared computation of the AND gate $$q = xy$$, we have to compute $$q_0, ... ,q_d$$ given $$x_0, ... ,x_d$$ and $$y_0, ... ,y_d$$ such that $$q = q_0 \oplus ... \oplus q_d$$, $$x = x_0 \oplus ... \oplus x_d$$ and $$y = y_0 \oplus ... \oplus y_d$$. There are several designs for the AND computation on shares. In the following, we exhibit the a single fault injection on the designs of [[Ishai et al., CRYPTO 2003]](https://people.eecs.berkeley.edu/~daw/papers/privcirc-crypto03.pdf) (ISW) and [[Nikova et al., ICICS 2006]](https://link.springer.com/content/pdf/10.1007/11935308_38.pdf?pdf=inline%20link) (TI - Threshold Implementation).

The main goal of SIFA is to obtaining a deviation in the distribution of $$q$$, which means $$Pr[q=1] \ne 0.25$$ and $$Pr[q=0] \ne 0.75$$, by faulting a single share in the AND computation. Then, we recover the key by exploiting this deviation.

### Faulting on ISW
The computation of a 2-share AND gate ($$d = 1$$) is as the following:

$$
\begin{align}
q_0 &= x_0y_0 \oplus r_{0,1} \\
q_1 &= x_1y_1 \oplus (r_{0,1} \oplus x_0y_1 \oplus x_1y_0)
\end{align}
$$

We examine the effect of a single fault in 2 different positions:

- The fault skips the calculation of $$x_0y_0$$ in $$q_0$$, then $$q_0 = r_{0,1}$$ and the unshared variable:

$$
q = q_0 \oplus q_1 = x_1y_1 \oplus x_0y_1 \oplus x_1y_0
$$

We draw the following truth table to see the change in the distribution of $$q$$:

| $$x_0$$ | $$x_1$$ | $$y_0$$ | $$y_1$$ | $$q$$ |
| :-: | :-: | :-: | :-: | :-: |
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 0 | 1 | 0 |
| 0 | 0 | 1 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 1 |
| 0 | 1 | 1 | 0 | 1 |
| 0 | 1 | 1 | 1 | 0 |
| 1 | 0 | 0 | 0 | 0 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | 0 | 1 | 0 | 0 |
| 1 | 0 | 1 | 1 | 1 |
| 1 | 1 | 0 | 0 | 0 |
| 1 | 1 | 0 | 1 | 0 |
| 1 | 1 | 1 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

It is clear that $$Pr[q=1] = 6/16 = 0.375 \ne 0.25$$ and $$Pr[q=0] = 10/16 = 0.625 \ne 0.75$$. 

- The fault skips the addition of $$r_{0,1}$$ in $$q_0$$, then $$q_0 = x_0y_0$$ and the unshared variable:

$$
q = q_0 \oplus q_1 = x_0y_0 \oplus x_1y_1 \oplus x_0y_1 \oplus x_1y_0 \oplus r_{0,1}
$$

We draw the following truth table to see the change in the distribution of $$q$$:

| $$x_0$$ | $$x_1$$ | $$y_0$$ | $$y_1$$ | $$r_{0,1}$$ | $$q$$ |
| :-: | :-: | :-: | :-: | :-: |
| 0 | 0 | 0 | 0 | 0 | 0 |
| ... | ... | ... | ... | ... | ... |
| 1 | 1 | 1 | 1 | 1 | 0 |

Now, $$Pr[q=1] = 0.5$$ and $$Pr[q=0] = 0.5$$. 

### Faulting on TI

$$
\begin{align}
q_0 &= (x_2 \oplus x_3)(y_1 \oplus y_2) \oplus y_1 \oplus y_2 \oplus y_3 \oplus x_1 \oplus x_2 \oplus x_3 \\
q_1 &= (x_0 \oplus x_2)(y_0 \oplus y_3) \oplus y_0 \oplus y_2 \oplus y_3 \oplus x_0 \oplus x_2 \oplus x_3 \\
q_2 &= (x_1 \oplus x_3)(y_0 \oplus y_3) \oplus y_1 \oplus x_1 \\
q_3 &= (x_0 \oplus x_1)(y_1 \oplus y_2) \oplus y_0 \oplus x_0
\end{align}
$$

We examine the effect of a single fault in 2 different positions:

- The first addition is skipped in $$q_0$$, then $$q_0 = (x_2)(y_1 \oplus y_2) \oplus y_1 \oplus y_2 \oplus y_3 \oplus x_1 \oplus x_2 \oplus x_3$$. Similarly, we draw the truth table to see the change in the distribution of $$q$$. Then, we get $$Pr[q=1] = 0.625$$ and $$Pr[q=0] = 0.375$$

- $$x_0$$ is fixed to $$0$$. Then, we have $$Pr[q=1] = 0.25$$ and $$Pr[q=0] = 0.75$$. We note that this distribution is the same as the original one.

## 3. Applying SFA and SIFA
We note that we are taking both correct and incorrect values of $$q$$, corresponding both effective and ineffective faults, into account. Therefore, we should apply SFA to recover the key (SIFA only considers ineffective faults).

## 4. Faulting Masked S-boxes