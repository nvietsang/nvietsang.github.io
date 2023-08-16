---
layout: post
title: SIFA on Masked Computation
---

This post explains how to use SIFA to attack AES implementations protected with both masking and detection-based fault countermeasures. The nice idea of this attack was presented in the paper [[Dobraunig et al., ASIACRYPT 2018]](https://eprint.iacr.org/2018/357.pdf) that I enjoyed reading very much.

## 1. Main idea
First, we know the AES S-box: $$b = S[a]$$ (for instance, $$99 = S[0]$$, see the [S-box](https://en.wikipedia.org/wiki/Rijndael_S-box)). When applying masking, we compute the output shares $$b_0, ... ,b_d$$ given the input shares $$a_0, ...,a_d$$ such that $$b = b_0 \oplus ... \oplus b_d$$ and $$a = a_0 \oplus ... \oplus a_d$$. Here, $$d$$ is a security parameter called the order of masking.
This computation can be done by AND and XOR gates (see [[Rivain et al., CHES 2010]](https://eprint.iacr.org/2010/441.pdf), for example). The main idea is that faulting a single share during the S-box computation is sufficient to induce a bias in an unshared S-box output $$b$$.

## 2. Single Fault on Masked AND gate
Let's consider the unshared AND operation $$q = xy$$. From the following truth table, it is easy to see that the probability $$Pr[q=1] = 0.25$$ and $$Pr[q=0] = 0.75$$.

| $$x$$ | $$y$$ | $$q$$ |
| :-: | :-: | :-: |
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

In the shared computation of the AND gate $$q = xy$$, we have to compute $$q_0, ... ,q_d$$ given $$x_0, ... ,x_d$$ and $$y_0, ... ,y_d$$ such that $$q = q_0 \oplus ... \oplus q_d$$, $$x = x_0 \oplus ... \oplus x_d$$ and $$y = y_0 \oplus ... \oplus y_d$$. There are several designs for the AND computation on shares. In the paper, the authors exhibit the a single fault injection on the designs of [[Ishai et al., CRYPTO 2003]](https://people.eecs.berkeley.edu/~daw/papers/privcirc-crypto03.pdf) (ISW) and [[Nikova et al., ICICS 2006]](https://link.springer.com/content/pdf/10.1007/11935308_38.pdf?pdf=inline%20link) (TI - Threshold Implementation).

The main goal of SIFA is to obtaining a deviation in the distribution of $$q$$, which means $$Pr[q=1] \ne 0.25$$ and $$Pr[q=0] \ne 0.75$$, by faulting a single share in the AND computation. Then, we recover the key by exploiting this deviation.

For the sake of brevity, we just take the ISW scheme as an example. The computation of a 2-share AND gate ($$d = 1$$) is as the following:

$$
\begin{align}
q_0 &= x_0y_0 \oplus r_{0,1} \\
q_1 &= x_1y_1 \oplus (r_{0,1} \oplus x_0y_1 \oplus x_1y_0)
\end{align}
$$

We assume that the calculation of $$x_0y_0$$ in $$q_0$$ is skipped because of the fault's effect, then $$q_0' = r_{0,1}$$ and the unshared variable:

$$
q' = q_0' \oplus q_1 = x_1y_1 \oplus x_0y_1 \oplus x_1y_0
$$

We draw the following truth table to see the distribution of $$q'$$:

| $$x_0$$ | $$x_1$$ | $$y_0$$ | $$y_1$$ | $$q$$ | $$q'$$ | $$q' \overset{?}{=} q$$ |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| 0 | 0 | 0 | 0 | 0 | 0 | $$\checkmark$$ |
| 0 | 0 | 0 | 1 | 0 | 0 | $$\checkmark$$ |
| 0 | 0 | 1 | 0 | 0 | 0 | $$\checkmark$$ |
| 0 | 0 | 1 | 1 | 0 | 0 | $$\checkmark$$ |
| 0 | 1 | 0 | 0 | 0 | 0 | $$\checkmark$$ |
| 0 | 1 | 0 | 1 | 1 | 1 | $$\checkmark$$ |
| 0 | 1 | 1 | 0 | 1 | 1 | $$\checkmark$$ |
| 0 | 1 | 1 | 1 | 0 | 0 | $$\checkmark$$ |
| 1 | 0 | 0 | 0 | 0 | 0 | $$\checkmark$$ |
| 1 | 0 | 0 | 1 | 1 | 1 | $$\checkmark$$ |
| 1 | 0 | 1 | 0 | 1 | 0 |  |
| 1 | 0 | 1 | 1 | 0 | 1 |  |
| 1 | 1 | 0 | 0 | 0 | 0 | $$\checkmark$$ |
| 1 | 1 | 0 | 1 | 0 | 0 | $$\checkmark$$ |
| 1 | 1 | 1 | 0 | 0 | 1 |  |
| 1 | 1 | 1 | 1 | 0 | 1 |  |

We observe the following probabilities:

$$
\begin{align}
Pr[q'=1] &= 6/16  = 0.375  &(\ne Pr[q=1] = 0.25) \\
Pr[q'=0] &= 10/16 = 0.625  &(\ne Pr[q=0] = 0.75)
\end{align}
$$

and 

$$
\begin{align}
Pr[q'=1 | q'=q] &= 3/12 = 0.25  &(= Pr[q=1] = 0.25) \\
Pr[q'=0 | q'=q] &= 9/12 = 0.75  &(= Pr[q=0] = 0.75)
\end{align}
$$

### Applying SFA

We consider $$Pr[q'=1] = 0.375 (\ne Pr[q=1] = 0.25)$$. Note that, in this case, we are taking both correct and incorrect values of $$q'$$, corresponding both effective and ineffective faults, into account. Therefore, we should exploit the deviated distribution by using SFA.

### Applying SIFA

First, we ignore the above probabilities and assume that $$Pr[q'=1 \vert q'=q] \ne 0.25$$. Note that, in this case, we are only taking *incorrect* values of $$q'$$, into account, which means that $$q'=q$$. In other words, we only consider the ineffective faults. Since there is a deviation in the distribution of $$q'$$, we can apply SIFA.

Now, we come back to our above example. Here, $$Pr[q'=1 \vert q'=q] = 0.25$$ $$(= Pr[q=1] = 0.25)$$, there is no deviation that we can exploit. Therefore, SIFA is not applicable in this case. However, in the paper, the authors describe some other fault's effects from which we can use SIFA. For instance, a single fault that skipping the addition of $$r_{0,1}$$ in $$q_0$$ ($$q_0' = x_0y_0$$) gives us $$Pr[q'=1 \vert q'=q] \ne 0.25$$.

In the presence of fault countermeasures, for example, duplicating the computation and comparing their outputs, SFA is not applicable while SIFA still works. That's why SIFA is a very powerful attack.

## 3. Faulting Masked S-boxes

So far, we have just examined the deviation of distribution on the shared computation of an AND gate. With the same idea in mind, we scale up the problem to the shared computation of the S-box. We inject a single fault in the S-box computation, then draw the truth table and observe the distribution of the output to see if we can apply SFA or SIFA.
