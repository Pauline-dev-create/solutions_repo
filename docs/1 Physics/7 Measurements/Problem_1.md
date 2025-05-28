# Problem 1

## Problem 1: Measuring Earth's Gravitational Acceleration with a Pendulum

### Motivation:
The acceleration $ g $ due to gravity is a fundamental constant that influences a wide range of physical phenomena. Measuring $ g $ accurately is crucial for understanding gravitational interactions, designing structures, and conducting experiments in various fields. One classic method for determining $ g $ is through the oscillations of a simple pendulum, where the period of oscillation depends on the local gravitational field.

### Task:
Measure the acceleration $ g $ due to gravity using a pendulum and in detail analyze the uncertainties in the measurements.

This exercise emphasizes rigorous measurement practices, uncertainty analysis, and their role in experimental physics.

### Procedure:

#### 1. Materials:
- A string (1 or 1.5 meters long).
- A small weight (e.g., bag of coins, bag of sugar, key chain) mounted on the string.
- Stopwatch (or smartphone timer).
- Ruler or measuring tape.


##  Calculations

### 1. Mean and Standard Deviation

- Calculate the **mean time** $$ \overline{T_{10}} $$ and **standard deviation** $$ \sigma_{T_{10}} $$

$$
\overline{T_{10}} = \frac{1}{n} \sum_{i=1}^{n} T_{10}^{(i)} \qquad
\sigma_{T_{10}} = \sqrt{\frac{1}{n-1} \sum_{i=1}^{n} \left( T_{10}^{(i)} - \overline{T_{10}} \right)^2}
$$

- Compute the **uncertainty in the mean**:

$$
\Delta T_{10} = \frac{\sigma_{T_{10}}}{\sqrt{n}}
$$

---

### 2. Period of One Oscillation

$$
T = \frac{\overline{T_{10}}}{10} \qquad
\Delta T = \frac{\Delta T_{10}}{10}
$$

---

### 3. Gravitational Acceleration

$$
g = \frac{4\pi^2 L}{T^2}
$$

---

### 4. Propagation of Uncertainty

$$
\frac{\Delta g}{g} = \sqrt{ \left( \frac{\Delta L}{L} \right)^2 + \left( 2 \cdot \frac{\Delta T}{T} \right)^2 }
$$

$$
\Delta g = g \cdot \sqrt{ \left( \frac{\Delta L}{L} \right)^2 + \left( 2 \cdot \frac{\Delta T}{T} \right)^2 }
$$

---

##  Final Results

- $$ L = 1.00 \pm 0.01 \ \text{m} $$
- $$ \overline{T_{10}} = 9.90 \pm 0.10 \ \text{s} $$
- $$ T = 0.990 \pm 0.010 \ \text{s} $$
- $$ g = 9.98 \pm 0.25 \ \text{m/s}^2 $$

---

## 🗣️ Analysis

1. **Comparison** with the standard value of gravity:

$$
g_{\text{standard}} = 9.81 \ \text{m/s}^2
$$

2. **Sources of Uncertainty**:
   - Human reaction time in starting/stopping the timer
   - Measurement error in the length of the pendulum
   - Small-angle approximation

3. **Experimental Limitations**:
   - Air resistance and friction at the pivot point
   - Limited resolution of stopwatch and ruler

---

- Tabulated raw data 10 trials of $$ T_{10} $$
- Calculated:
  - Mean $$ T_{10} $$
  - Standard deviation $$ \sigma_{T_{10}} $$
  - Period $$ T $$
  - Gravitational acceleration $$ g $$
  - Uncertainty $$ \Delta g $$

![alt text](image.png)