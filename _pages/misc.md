---
layout: page
title: Misc
permalink: /misc/
---



Below is a list of links that I found interesting:
<ul>
<li>
    2025/03/27: 
    <a href="https://www.youtube.com/watch?v=6qD-T1gjtKw">Understanding and Explaining Post-Quantum Crypto with Cartoons</a>
    by Klaus Schmeh
    <br>
    I love the explanation by cartoons. It introduces PQC in a super nice way!
</li>

<li>
    2025/03/02: 
    <a href="https://crypto.junod.info/posts/recursive-hash/">Hashing Apples, Bananas and Cherries</a>
    by Pascal Junod
    <br>
    In theoretical cryptography, the message to be hashed (for signing in a signature) is always referred to as a sequence of bytes. But in practice, what information should we include in the message? How to structure the information? This is also important in security, but left as the responsibility of the engineers. 
</li>

<li>
    2025/02/15: 
    <a href="https://www.youtube.com/watch?v=dyYAJENLwIo">15 Tips And Tricks On Learning Really Hard Things</a>
    by David Wong
    <br>
    I know David Wong from his book, Real-World Cryptography, and <a href="https://www.cryptologie.net">his blog</a> His explanations are always clear and easy to understand. From this video, I learned that well-known experts sometimes have difficulty understanding things, but they find ways to overcome it.
</li>

<li>
    2025/01/10: 
    <a href="https://www.youtube.com/watch?v=C4ATDMIz5wc">AES: How to Design Secure Encryption</a>
    by Spanning Tree
    <br>
    We know how AES work, but we don't know what makes it a secure algorithm. Why are there Key Schedule, SubBytes, ShiftRows, MixColumns, AddRoundKey? This video explain the AES from a designers' perspective.
</li>

<li>
    2024/11/28: 
    <a href="https://www.spiceworks.com/tech/tech-general/articles/big-endian-vs-little-endian/#:~:text=In%20a%20big%2Dendian%20machine,in%20case%20of%20multiple%20bytes.">Big Endian vs. Little Endian: Key Comparisons</a>
    by Hossein Ashtari
    <br>
    This is a simple explanation of big endian and little endian.
</li>

<li>
    2024/11/01: 
    <a href="http://wiki.newae.com/Tutorial_B8_Profiling_Attacks_(Manual_Template_Attack)">Tutorial of Template Attack</a>
    by NewAE Technology
    <br>
    This tutorial provides a step-by-step template attack on AES.
</li>

<li>
    2024/10/13: 
    <a href="https://www.youtube.com/watch?v=w5ebcowAJD8">How do QR codes work?</a>
    by Veritasium
    <br>
    This video presents the history and the techniques behind QR codes.
</li>

<li>
    2024/10/13: 
    <a href="https://www.youtube.com/watch?v=YuYxdMQVY2U">Test-of-Time Award for paper: Correlation Power Analysis with a Leakage Model</a>
    by Francis Oliver
    <br>
    This presentation is very informative: the stories behind the paper, many new research directions after the paper, what's going on this industry, what's next...
</li>

<li>
    2024/10/12: 
    <a href="https://brokencloudstorage.info/#overview">End-to-End Encrypted Cloud Storage in the Wild</a>
    by Jonas Hofmann and Kien Tuong Truong
    <br>
    This paper presents different interesting attacks on 5 encrypted cloud storage providers.
</li>

<li>
    2024/09/12: 
    <a href="https://www.amazon.science/blog/better-performing-25519-elliptic-curve-cryptography">Better-performing “25519” elliptic-curve cryptography</a>
    by Torben Hansen and John Harrison
    <br>
    This blog explains how they accelerate x25519 and Ed25519 in x86_64 and Arm64 micro-architectures.
</li>

<li>
    2024/09/10: 
    <a href="https://hackmd.io/@brozorec/pairings-for-the-rest-of-us-1">Pairings for the Rest of Us</a>
    by Boyan Barakov
    <br>
    This is a gentle introduction about parings for beginners. I love the author's shares about his learning journey.
</li>

<li>
    2024/09/03: 
    <a href="https://ninjalab.io/eucleak/">EUCLEAK: Side-Channel Attack on the YubiKey 5 Series</a>
    by Thomas Roche
    <br>
    This interesting attack shows why constant-time implementation is important in crypto.
</li>

<li>
    2024/08/26: 
    <a href="https://blog.cryptographyengineering.com/2024/08/25/telegram-is-not-really-an-encrypted-messaging-app/">Is Telegram really an encrypted messaging app?</a>
    by Matthew Green
    <br>
    This is an excellent blog about the encryption feature in Telegram. I quote my favorite paragraph: "My strong suspicion is that many people who join Telegram for its social media features also end up using it to communicate privately. And I think Telegram knows this, and tends to advertise itself as a “secure messenger” and talk about the platform's encryption features precisely because they know it makes people feel more comfortable."
</li>

<li>
    2024/08/26: 
    <a href="http://wiki.newae.com/Template_Attacks">Template Attacks</a>
    by NewAE Technology
    <br>
    This a gentle introduction about template attacks. It provides detailed steps of the attack concept and explains the intuition behind.
</li>

<li>
    2024/08/25: 
    <a href="https://pure.tue.nl/ws/portalfiles/portal/211137599/Custers_F.pdf">Soft Analytical Side-Channel Attacks on the Number Theoretic Transform for Post-Quantum Cryptography</a>
    by Frank Custers
    <br>
    This is a master thesis. I enjoyed the step-by-step explanation with a toy example about SASCA.
</li>

<li>
    2024/08/22: 
    <a href="https://link.springer.com/chapter/10.1007/978-3-540-30574-3_24">Side-Channel Leakage of Masked CMOS Gates</a>
    by Stefan Mangard, ThomasPopp and Berndt M. Gammel
    <br>
    This paper explained what glitches are and how they affect the DPA security. This is a fundamental paper leading to the invention of modern masking schemes such as Threshold Implementation. The explanations are very easy to understand.
</li>

<li>
    2024/08/01: 
    <a href="https://eprint.iacr.org/2015/260.pdf">Computational Aspects of Correlation Power Analysis</a>
    by Paul Bottinelli and Joppe W. Bos
    <br>
    This paper presented many approaches of computing Pearson correlation in side-channel analysis. The authors started from a naïve computation algorithm, then pointed out the limitation in terms of time and/or memory, especially when a large number of traces is used for the computation. From that, they proposed many improved versions for the computation algorithm and  thoroughly compared their efficiency.
</li>

<li>
    2024/07/01: 
    <a href="https://ileanabuhan.github.io/general/2021/05/07/SNR-tutorial.html">Computing the Signal-to-Noise Ratio (SNR) for SCA</a>
    by Ileana Buhan
    <br>
    This is an easy-to-understand introduction about the concept of SNR. The author started by explaining SNR from a statistical point of view, then linked it to the context of side-channel analysis (SCA). 
</li>
</ul>