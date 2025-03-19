# Problem 2
# Investigating the Dynamics of a Forced Damped Pendulum

## **1. Theoretical Foundation**
The equation of motion for a **forced damped pendulum** is:

$
\frac{d^2\theta}{dt^2} + b \frac{d\theta}{dt} + \frac{g}{L} \sin\theta = A \cos(\omega t)
$

where:
- \( \theta \) is the angular displacement,
- \( b \) is the damping coefficient,
- \( g \) is gravitational acceleration,
- \( L \) is the length of the pendulum,
- \( A \) is the amplitude of the external force,
- \( \omega \) is the driving frequency.

### **1.1 Small-Angle Approximation**
For small angles ($ \theta \approx 0 $), we can approximate $ \sin\theta \approx \theta $, reducing the equation to:

$
\frac{d^2\theta}{dt^2} + b \frac{d\theta}{dt} + \frac{g}{L} \theta = A \cos(\omega t)
$

which is a **driven damped harmonic oscillator**, allowing for analytical solutions in simple cases.

### **1.2 Resonance Condition**
Resonance occurs when the external forcing frequency \( \omega \) matches the system's **natural frequency**:

$
\omega_0 = \sqrt{\frac{g}{L}}
$

At resonance, the pendulum exhibits maximum oscillations, which can lead to instability if damping is low.

---

## **2. Analysis of Dynamics**
### **2.1 Effects of Parameters**
1. **Damping coefficient $ b $**: Higher damping suppresses oscillations, preventing chaotic motion.
2. **Driving amplitude $ A $**: Larger values can lead to more complex behavior.
3. **Driving frequency $ \omega $**: Controls resonance effects and synchronization.

### **2.2 Transition to Chaos**
- At low damping, the system can exhibit **period-doubling bifurcations**, leading to chaos.
- Phase portraits and **Poincaré sections** help visualize this transition.

---

## **3. Practical Applications**
- **Mechanical Systems**: Bridges, suspension cables, and earthquake-resistant structures.
- **Electrical Circuits**: Analogous to driven **RLC circuits**.
- **Biomechanics**: Human balance and walking dynamics.

---

## **4. Implementation: Python Simulation**
We use **Runge-Kutta numerical methods** to integrate the equation of motion and plot various behaviors.

### **4.1 Python Code for Visual Studio Code**
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.integrate import solve_ivp

# Parameters
g = 9.81  # Gravity (m/s^2)
L = 1.0   # Pendulum length (m)
b = 0.2   # Damping coefficient
A = 1.2   # Driving amplitude
omega = 2.0  # Driving frequency

# Differential equation
def forced_damped_pendulum(t, y):
    theta, omega_t = y
    dtheta_dt = omega_t
    domega_dt = - (g / L) * np.sin(theta) - b * omega_t + A * np.cos(omega * t)
    return [dtheta_dt, domega_dt]

# Initial conditions
t_span = (0, 50)  # Time range
t_eval = np.linspace(*t_span, 1000)  # Time points
y0 = [0.5, 0]  # Initial angle and angular velocity

# Solve using Runge-Kutta
sol = solve_ivp(forced_damped_pendulum, t_span, y0, t_eval=t_eval, method='RK45')

# Extract solutions
t = sol.t
theta = sol.y[0]

# Plot results
plt.figure(figsize=(8, 5))
plt.plot(t, theta, label='Angular Displacement (theta)')
plt.xlabel('Time (s)')
plt.ylabel('Theta (radians)')
plt.title('Forced Damped Pendulum Motion')
plt.legend()
plt.grid(True)
plt.savefig("forced_damped_pendulum.png", dpi=300)
plt.show()
```



