---
layout: post
title:  "[Unpublished] Gradient-based optimizers"
math: true
comments: false
published: false
---

Here are some high-performing grandient-based optimizers that are widely used in deep learning. $dW$ denotes the gradient matrix $\frac{\partial J}{\partial W}$ where $J$ is the cost function, $\alpha$ is the learning rate, and $\epsilon$ is a small positive number added to denominators for numerical stability, e.g. $\epsilon = 10^{-8}$.

1. GD with momentum \\
$$v_{dW} := 0$$ \\
$$v_{dW} = \beta v_{dW} + (1 - \beta) dW$$ \\
$$W := W - \alpha v_{dW}$$ \\
To make this gradient estimate unbiased we can use $\frac{1}{1 - \beta^t} v_{dW}$ instead of $v_{dW}$ (at the $t$-th gradient update), but in practice this doesn't matter too much.

2. RMSProp \\
$$s_{dW} := 0$$ \\
$$s_{dW} = \beta s_{dW} + (1 - \beta) dW^2$$ \\
$$W := W - \alpha \frac{dW}{\sqrt{s_{dW}} + \epsilon}$$ \\
https://www.coursera.org/learn/neural-networks/lecture/YQHki/rmsprop-divide-the-gradient-by-a-running-average-of-its-recent-magnitude
https://www.cs.toronto.edu/~tijmen/csc321/slides/lecture_slides_lec6.pdf

3. Adam (essentially GD with momentum plus RMSProp)
$$v_{dW} := 0,~ s_{dW} := 0$$ \\
$$v_{dW} = \beta_1 v_{dW} + (1 - \beta_1) dW$$ \\
$$s_{dW} = \beta_2 s_{dW} + (1 - \beta_2) dW^2$$ \\
$$v_{dW}^{unbiased} = \frac{v_{dW}}{1 - \beta_1^t}$$ \\
$$s_{dW}^{unbiased} = \frac{s_{dW}}{1 - \beta_2^t}$$ \\
$$W := W - \alpha \frac{v_{dW}^{unbiased}}{\sqrt{s_{dW}^{unbiased}} + \epsilon}$$ \\
Recommended parameter choice: $\beta_1 = 0.9$, $\beta_2 = 0.99$.
https://arxiv.org/abs/1412.6980

4. Learning-rate Decay
Here are some options for decaying the learning rate:
* $\alpha_t = \frac{1}{1 + (\text{decay-rate}) * t} \alpha_0$
* $\alpha_t = 0.95^t \alpha_0$
* $\alpha_t = \frac{k}{\sqrt{t}} \alpha_0$
* Manual decay
