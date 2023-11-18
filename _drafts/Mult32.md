---
layout: post
title: Multiplying two 32-bit numbers without using 64-bit variable
---

In this post, we figure out how to multiplying two 32-bit positive numbers by only using 32-bit variables. Suppose that we are given two positive numbers $$x,y \in [0, 2^{32}-1]$$, the goal is to calculate $$z = x \times y$$ where $$z \in [0, 2^{64} - 1]$$ without using 64-bit any variable.

We use two variables, say $$z_1$$ and $$z_0$$, to represent $$z$$ where $$z_1$$ is 32 upper bits and $$z_0$$ is 32 lower bits of $$z$$, respectively. In other words, $$z = z_1 \times 2^{32} + z_0$$. So, our goal now is to find $$z_1$$ and $$z_0$$ using only calculations on 32-bit variables.

The first and naive idea is to decompose $$x$$ and $$y$$ into:

$$
x = x_1 \times 2^{16} + x_0,
$$

$$
y = y_1 \times 2^{16} + y_0,
$$

where $$x_0, x_1, y_0, y_1 \in [0, 2^{16}-1]$$ are 16-bit numbers. We then have:

$$
z = x_1y_1 \times 2^{32} + (x_0y_1 + x_1y_0) \times 2^{16} + x_0y_0
$$

When we see the factor $$2^{32}$$ here, we might say that let's take $$z_1 = x_1y_1$$ and $$z_0 = (x_0y_1 + x_1y_0) \times 2^{16} + x_0y_0$$ !?

Be careful! It is a bit more complicated than that. Why? Let's take a look on $$x_0y_0$$. Notice that $$x_0$$ and $$y_0$$ are 16-bit numbers. So their product is maximum 32 bits. It is similar to $$x_0y_1$$ and $$x_1y_0$$. Thus, $$(x_0y_1 + x_1y_0) \times 2^{16} + x_0y_0$$ does not fit 32 bits of $$z_0$$.

To deal with this, we consider $$z$$ as four 16-bit parts $$a_3, a_2, a_1, a_0 \in [0, 2^{16}-1]$$ such that:

$$
z = z_3 \times 2^{48} + z_2 \times 2^{32} + z_1 \times 2^{16} + z_0.
$$



```
#define UP(x) (x >> 16)      // 16 upper bits
#define LO(x) (x & 0xFFFF)   // 16 lower bits

/**
 * Given:
 *  x = x1 * 2^16 + x0
 *  y = y1 * 2^16 + y0
 * Compute:
 *  z = x * y
 * Return:
 *  z[1]: 32 upper bits
 *  z[0]: 32 lower bits
*/
void mult32(unsigned int *z, unsigned int x, unsigned int y) {
	unsigned int x1 = UP(x);
	unsigned int x0 = LO(x);
	unsigned int y1 = UP(y);
	unsigned int y0 = LO(y);

    unsigned int t;
    // z = z3 * 2^48 + z2 * 2^32 + z1 * 2^16 + z0
    // z[1] = z3 * 2^16 + z2 
    // z[0] = z1 * 2^16 + z0
    unsigned int z3, z2, z1, z0; 
    t = x0 * y0;
    z0 = LO(t);
    
    z1 = UP(t);
    t = x0 * y1 + z1;
    z1 = LO(t);
    z2 = UP(t);

    t = x1 * y0 + z1;
    z1 = LO(t);
    z2 = UP(t) + z2;
    t = x1 * y1 + z2;
    // z2 = LO(t);
    // z3 = UP(t);
    
    z[1] = t;
    // z[1] = (z3 << 16) | z2;
    z[0] = (z1 << 16) | z0;
}

```