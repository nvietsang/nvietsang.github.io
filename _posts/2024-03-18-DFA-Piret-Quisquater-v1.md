---
layout: post
title: DFA - Differential Fault Attack on AES
---

This post explains the main idea of the Differential Fault Attack on AES with faults injected before the MixColumns in the 9-th round. For the original paper, we refer to [[Piret and Quisquater, CHES 2003]](https://link.springer.com/chapter/10.1007/978-3-540-45238-6_7).

## # Principle

Let us assume that a fault is injected into the first byte of the state before the MixColumns of the 9-th round. As a consequence, the value of this byte changes from $$a_0$$ to $$a_0'$$, and the difference is $$\epsilon = a_0 \oplus a_0'$$. Faulting the first byte also causes the changes of the 4 bytes in the first column after the computation of MixColumns.

<div style="text-align: center">
    <img src="/assets/figures/2024-03-18-DFA-PQ-v1.png" width=700 alt="drawing"/>
</div>

We recall the computation of MixColumns. Let $$\mathbf{a}$$ be the vector representing the first column of the state before the MixColumns:

$$
\mathbf{a} = 
    \begin{pmatrix}
        a_0 \\
        a_1 \\
        a_2 \\
        a_3
    \end{pmatrix},
\mathbf{a}' = 
    \begin{pmatrix}
        a_0 \oplus \epsilon \\
        a_1 \\
        a_2 \\
        a_3
    \end{pmatrix} 
$$

Denote $$\mathbf{b}$$ the result of this column after applying the MixColumns transformation, we have $$\mathbf{b} = \mathbf{M}\cdot \mathbf{a}$$, where

$$
\mathbf{M} = 
    \begin{pmatrix}
        2 & 3 & 1 & 1 \\
        1 & 2 & 3 & 1 \\
        1 & 1 & 2 & 3 \\
        3 & 1 & 1 & 2
    \end{pmatrix} 
$$

The differential vector after the MixColumns is thus:

$$
\mathbf{b} \oplus \mathbf{b'} = \mathbf{M} \cdot (\mathbf{a} \oplus \mathbf{a'}) = 
    \begin{pmatrix}
        2\epsilon \\
        \epsilon \\
        \epsilon \\
        3\epsilon
    \end{pmatrix}
$$

AddRoundKey transformation does not change this differential vector since it is a linear operation. However, the SubBytes in the 10-th round does change this differential vector. As a result, the differences of the corresponding 4 bytes (hereafter, we call a "quadruplet") in the ciphertext do not maintained the relation of $$\mathbf{b} \oplus \mathbf{b'}$$.

Now, we will see how to recover a quadruplet of the last round key (4 bytes in orange in the figure above), given several pairs of correct and faulty ciphertext. 

We denote such a pair $$(C, C')$$. We perform the following steps:

1. Extract the pair of quadruplets $$(C_q, C_q')$$. 
2. Make hypothesis for the corresponding quadruplet of key, denoted $$K_q^h$$.
3. From $$(C_q, C_q')$$ and $$K_q^h$$, compute backwards to obtain a pair of vectors representing the first column of the state before the last SubBytes in the 10-th round. We denote this pair $$(\mathbf{v}, \mathbf{v'})$$.
4. Check if $$\mathbf{v} \oplus \mathbf{v'}$$ has the relation as $$\mathbf{b} \oplus \mathbf{b'}$$. If yes, $$K_q^h$$ is a good candidate. If no, go back to step 2 and try with another hypothesis.
5. Repeat the steps from 1 to 4 with several different pairs of $$(C_q, C_q')$$ untill we obtain a unique quadruplet of key (the original paper claimed that 2 pairs is enough).

## # Conditions on Faults

The explanation above considers the fault injected in the first byte. In fact, this attack does not require the fault position to be fixed, and also does not require the fault effect to be identical. In order to recover a quadruplet of key, it just needs 2 faults in the same column to collect 2 pairs of correct and faulty ciphertexts.

From a pair $$(C, C')$$, we know the difference $$C \oplus C'$$ which must have 4 nonzero bytes. This determines a quadruplet so that we know which quadruplet of key to make hypothesis. Then, we compute backwards to obtain $$\mathbf{v} \oplus \mathbf{v'}$$.

For any other possible position of the fault, we can always compute derive a relation similar to $$\mathbf{b} \oplus \mathbf{b'}$$. Since we already know which quadruplet we are working with, we know which column of $$\mathbf{v}$$ and $$\mathbf{v'}$$ belong to. There are 4 possible relations for $$\mathbf{b} \oplus \mathbf{b'}$$ in this column. If $$\mathbf{v} \oplus \mathbf{v'}$$ matches one of these relations, it's good!

## # Implementation

Here is my implementation of the above attack: [https://github.com/nvietsang/dfa-on-aes](https://github.com/nvietsang/dfa-on-aes).
