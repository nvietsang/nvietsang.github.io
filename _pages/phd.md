---
layout: page
title: PhD
permalink: /phd/
---

I completed my PhD at Université de Saint-Etienne under the supervison of <a href="https://scholar.google.be/citations?user=4osxa5gAAAAJ&hl=fr">Vincent Grosso</a> and <a href="https://cayrel.net">Pierre-Louis Cayrel</a>.

It is entitled **Physical Attacks against Symmetric Cryptography Standards** and was conducted between January 2023 and October 2025. It contains my works on correlation power analysis attacks and fault attacks, focusing mainly on the new lightweight standard Ascon-AEAD and partly on AES.

- <a href="/assets/docs/phd-manuscript.pdf">[Manuscript]</a>
- <a href="/assets/docs/phd-slides.pdf">[Slides]</a>

**Defense Information**:

- Time: 10h00
- Date: Friday, 19 December 2025
- Location : Room F021b, Laboratoire Hubert Curien, 18 rue Professeur Benoît Lauras, 42000 Saint-Étienne

**Abstract**: The rise of embedded devices in the era of Internet of Things (IoT) has increased the demand for secure cryptographic algorithms. While standardized algorithms are proven to be secure in the black-box model, where an adversary has access only to inputs and outputs, this model is not sufficient to capture real-world threats. In practice, the adversary may have physical access to the devices and recover the secret key used in the algorithms by physical attacks, which are generally classified as Side-Channel Attacks (SCA) and Fault Attacks (FA). This thesis explores the threats of such attacks on symmetric cryptography standards, with a focus on the long-standing Advanced Encryption Standard (AES) and the newly standardized lightweight authenticated encryption Ascon-AEAD.

In the first part of this thesis, we focus on a prominent type of SCA, namely Correlation Power Analysis (CPA), targeting Ascon-AEAD. We analyze different approaches for choosing the selection function, which is a core factor of CPA, to provide insights into when and why an attack succeeds or fails. Our analysis is validated with a second-order CPA attack against a masked software implementation, which also provides the first practical indication of the data and computational cost for full key recovery in a protected implementation. We further propose extending the selection function from one bit to multiple bits, thereby increasing success rates.

In the second part of this thesis, we focus on two prominent types of FA, namely Statistical Ineffective Fault Analysis (SIFA) and Persistent Fault Analysis (PFA), targeting Ascon-AEAD and AES. For SIFA, we provide a deeper analysis of a generic attack strategy applied to nonce-based authenticated encryption schemes. Our results show that, although SIFA is powerful, it may fail in practice under instruction-skip faults. These findings are supported by demonstrations on Ascon-AEAD. For PFA, we show that persistent faults can be induced by an instruction skip, which is simpler than memory faulting techniques reported in the literature. We also introduce the first PFA targeting a constant other than S-box elements. Specifically, we demonstrate that faulting a round constant of AES can lead to full key recovery. Our findings are supported by experiments on the AES implementation in the widely used MbedTLS library.

