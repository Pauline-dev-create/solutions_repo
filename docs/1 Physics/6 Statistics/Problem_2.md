# Problem 2

## Estimating Pi using Monte Carlo Methods
Motivation

Monte Carlo simulations leverage randomness to estimate values, with estimating π being a classic example using geometric probability. This approach connects probability, geometry, and computation, offering insights into convergence and efficiency applicable in physics, finance, and computer science.

Part 1: Estimating π Using a Circle

## Theoretical Foundation

The circle-based Monte Carlo method estimates π by generating random points within a square that bounds a unit circle (radius $r = 1$). The area of the unit circle is $\pi r^2 = \pi$, and the area of the square is $2r \times 2r = 4$. The ratio of points inside the circle to the total points approximates the ratio of their areas:$$\frac{\text{Number of points inside circle}}{\text{Total number of points}} \approx \frac{\pi}{4}$$Thus, the estimate for π is:$$\pi \approx 4 \cdot \frac{\text{Number of points inside circle}}{\text{Total number of points}}$$


## Simulation
We generate random points $(x, y)$ uniformly in the square $[-1, 1] \times [-1, 1]$ and count those satisfying $x^2 + y^2 \leq 1$ (inside the unit circle).

Visualization
A scatter plot distinguishes points inside (e.g., blue) and outside (e.g., red) the circle.

Analysis
Accuracy improves with more points, as the estimate converges to π. The error decreases as $O(1/\sqrt{N})$, where $N$ is the number of points, due to the Central Limit 

## Theorem.
Part 2: Estimating π Using Buffon’s Needle

Theoretical Foundation

Buffon’s Needle problem involves dropping a needle of length $l$ onto a plane with parallel lines spaced $d$ apart ($l \leq d$). The probability a needle crosses a line depends on its orientation. Consider the needle’s center at $(x, 0)$ and its angle $\theta$ (0 to $\pi$) with the horizontal. The needle crosses a line if its vertical span exceeds the distance to the nearest line.
The vertical span is $l \sin\theta$. The needle crosses if $l \sin\theta > d - (d \mod x)$, but for simplicity with $l \leq d$ and uniform dropping, the probability is derived as follows:

The distance from the center to the nearest line is uniform on $[0, d/2]$.
The crossing condition is $l \sin\theta > \text{distance to line}$.
Averaging over $\theta$ (uniform on $[0, \pi]$) and the center position, the probability of crossing is $\frac{2l}{\pi d}$.

Thus, the estimate for π is:$$\pi \approx \frac{2l \cdot N}{\text{Number of crossings} \cdot d}$$where $N$ is the number of throws.
Simulation
We simulate $N$ needle drops, randomly assigning center $x$ (uniform on $[0, d]$) and angle $\theta$ (uniform on $[0, \pi]$), counting crossings when $l \sin\theta > |x \mod d - d/2|$.


Tables

Method
N=100
N=1000
N=10000


Circle
3.12
3.136
3.142

Buffon’s Needle
3.20
3.152
3.145


 [Visuals](https://colab.research.google.com/drive/17PvYgn1M-0IpgUkv6WNnWXnjiqqveDy0?usp=sharing)   

Conclusion
Both methods estimate π effectively, with the circle method offering better consistency and Buffon’s Needle providing a geometric insight. Increasing $N$ improves accuracy, with computational cost scaling linearly.
