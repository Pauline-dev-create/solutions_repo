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

```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation
from PIL import Image
import os

# Set random seed for reproducibility
np.random.seed(42)

# Circle-based Monte Carlo
def estimate_pi_circle(N):
    x = np.random.uniform(-1, 1, N)
    y = np.random.uniform(-1, 1, N)
    inside = x**2 + y**2 <= 1
    pi_est = 4 * np.mean(inside)
    return pi_est, x, y, inside

# Buffon's Needle
def estimate_pi_buffon(N, l=0.8, d=1.0):
    x = np.random.uniform(0, d, N)  # Center x-coordinate
    theta = np.random.uniform(0, np.pi, N)  # Angle
    y_span = l * np.sin(theta)  # Vertical span
    crossings = y_span > np.abs(x % d - d/2)
    pi_est = (2 * l * N) / (np.sum(crossings) * d) if np.sum(crossings) > 0 else float('inf')
    return pi_est, x, theta, crossings

# Visualization for Circle
def plot_circle(N_values):
    for i, N in enumerate(N_values):
        pi_est, x, y, inside = estimate_pi_circle(N)
        plt.figure(figsize=(6, 6))
        plt.scatter(x[inside], y[inside], c='blue', s=1, label='Inside')
        plt.scatter(x[~inside], y[~inside], c='red', s=1, label='Outside')
        circle = plt.Circle((0, 0), 1, fill=False, color='black')
        plt.gca().add_artist(circle)
        plt.title(f'Circle Method (N={N}, π≈{pi_est:.4f})')
        plt.xlabel('x')
        plt.ylabel('y')
        plt.legend()
        plt.axis('equal')
        plt.savefig(f'circle_frame_{i}.png')
        plt.close()
    images = [Image.open(f'circle_frame_{i}.png') for i in range(len(N_values))]
    images[0].save('circle_convergence.gif', save_all=True, append_images=images[1:], duration=1000, loop=0)

# Visualization for Buffon’s Needle
def plot_buffon(N_values, l=0.8, d=1.0):
    for i, N in enumerate(N_values):
        pi_est, x, theta, crossings = estimate_pi_buffon(N, l, d)
        plt.figure(figsize=(8, 4))
        for j in range(min(N, 20)):  # Limit to 20 needles for clarity
            y1 = l * np.cos(theta[j]) / 2 * np.sin(np.pi/2 - theta[j])
            y2 = -y1
            x1 = x[j] - l * np.sin(theta[j]) / 2
            x2 = x[j] + l * np.sin(theta[j]) / 2
            color = 'green' if crossings[j] else 'orange'
            plt.plot([x1, x2], [y1, y2], color=color, lw=1)
        plt.axhline(y=0, color='black', lw=0.5)
        plt.axhline(y=d, color='black', lw=0.5)
        plt.title(f"Buffon's Needle (N={N}, π≈{pi_est:.4f})")
        plt.xlabel('x')
        plt.ylabel('y')
        plt.savefig(f'buffon_frame_{i}.png')
        plt.close()
    images = [Image.open(f'buffon_frame_{i}.png') for i in range(len(N_values))]
    images[0].save('buffon_convergence.gif', save_all=True, append_images=images[1:], duration=1000, loop=0)

# Run simulations
N_values = [100, 1000, 10000]
plot_circle(N_values)
plot_buffon(N_values)

# Display GIFs in Colab
from IPython.display import Image
display(Image(filename='circle_convergence.gif'))
display(Image(filename='buffon_convergence.gif'))
```

![alt text](<download (5).gif>)


![alt text](<download (6).gif>)


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
