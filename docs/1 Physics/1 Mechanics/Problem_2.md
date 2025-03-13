# Problem 2
# Investigating the Dynamics of a Forced Damped Pendulum

## **1. Motivation**
The forced damped pendulum is a classic example of a nonlinear system exhibiting a rich spectrum of dynamic behaviors. By introducing damping and external periodic forcing, the system transitions from simple harmonic motion to more complex phenomena, such as resonance, quasiperiodicity, and chaos. Understanding these dynamics is essential in fields ranging from mechanical engineering to climate modeling and electrical circuits.

The introduction of an external periodic force adds new parameters, including amplitude and frequency, which significantly affect the pendulum's motion. By varying these parameters, we can observe different behaviors, including synchronized oscillations, chaotic motion, and resonance effects. These dynamics not only illustrate fundamental physical principles but also provide practical insights into engineering applications such as energy harvesting, vibration isolation, and resonance mitigation.

---

## **2. Theoretical Foundation**

### **2.1 Governing Differential Equation**
The motion of a forced damped pendulum is governed by Newton’s Second Law applied to rotational motion:

$$I \alpha = \sum \tau$$

For a simple pendulum of mass $m$ and length $L$, the moment of inertia about the pivot is:


$$I = mL^2$$


The forces acting on the pendulum bob include:

- **Gravitational force**: Produces a restoring torque:

$$\tau_{\text{gravity}} = -mgL \sin \theta$$

- **Damping force**: Opposes motion due to air resistance:

$$\tau_{\text{damping}} = -b L \frac{d\theta}{dt}$$

- **External forcing**: A periodic force applied to the system:

$$\tau_{\text{forcing}} = F_0 L \cos(\omega t)$$


Applying Newton’s Second Law:

$$mL^2 \frac{d^2\theta}{dt^2} = -mgL \sin\theta - b L \frac{d\theta}{dt} + F_0 L \cos(\omega t)$$

Dividing by $mL^2$, we obtain the standard form:

$$
\frac{d^2\theta}{dt^2} + \gamma \frac{d\theta}{dt} + \omega_0^2 \sin\theta = A \cos(\omega t)
$$

where:

- **Natural frequency**: 

$$\omega_0 = \sqrt{\frac{g}{L}}$$


- **Damping coefficient**: 

$$\gamma = \frac{b}{mL}$$

- **Forcing term**: 

$$A = \frac{F_0}{mL}$$


---

### 1.2 Approximate Solutions for Small-Angle Oscillations

For small angles $\theta \ll 1$, we use the small-angle approximation:

$$\sin\theta \approx \theta$$

Substituting this into the equation simplifies it to:

$$\frac{d^2\theta}{dt^2} + b \frac{d\theta}{dt} + \frac{g}{L} \theta = A \cos(\omega t)$$

This is a linear inhomogeneous second-order differential equation that can be solved using standard techniques.

#### 1.2.1 General Solution

The general solution consists of:

1. Homogeneous solution $ A = 0 $, solving:

$$\frac{d^2\theta}{dt^2} + b \frac{d\theta}{dt} + \frac{g}{L} \theta = 0$$

The characteristic equation is:

$$r^2 + br + \frac{g}{L} = 0$$

Solving for $r$, the solution depends on the damping coefficient $b$:

- Underdamped case $b^2 < 4g/L$:

$$\theta_h(t) = e^{-bt/2} (C_1 \cos(\omega{\prime} t) + C_2 \sin(\omega{\prime} t))$$

where $$\omega{\prime} = \sqrt{\frac{g}{L} - \frac{b^2}{4}}$$ is the damped frequency.

- Overdamped and critically damped cases are handled similarly but result in non-oscillatory motion.

2. Particular solution for the forced term:

$$\theta_p(t) = A_0 \cos(\omega t - \delta)$$

where:

$$A_0 = \frac{A}{\sqrt{\left(\frac{g}{L} - \omega^2\right)^2 + b^2 \omega^2}}$$

and

$$\tan\delta = \frac{b\omega}{\frac{g}{L} - \omega^2}$$

Thus, the full solution is:

$$\theta(t) = e^{-bt/2} (C_1 \cos(\omega{\prime} t) + C_2 \sin(\omega{\prime} t)) + A_0 \cos(\omega t - \delta)$$

where the first term decays over time, leaving only the steady-state oscillation.

---




### 1.3 Resonance Conditions and Energy Implications

Resonance occurs when the driving frequency $\omega$ approaches the system’s natural frequency:

$$\omega \approx \sqrt{\frac{g}{L}}$$

At resonance, the amplitude $A_0$ is maximized:

$$A_{\text{max}} \approx \frac{A}{b\omega}$$

which shows that:
- For small damping $b \to 0$, the amplitude can grow excessively, leading to large oscillations.
- For strong damping, resonance is suppressed, and energy is dissipated before large oscillations develop.

The total energy of the system:

$$E = \frac{1}{2} m L^2 \dot{\theta}^2 + \frac{1}{2} mgL \theta^2$$

is maximized at resonance, which can lead to destructive consequences in mechanical systems (e.g., bridges, buildings). Damping helps dissipate this energy and stabilize the system.

---



## 2.2  Python Visualizations
```python
import numpy as np
import matplotlib.pyplot as plt

# Parameters
g = 9.81          # gravitational acceleration (m/s^2)
L = 1.0           # pendulum length (m)
b = 0.1           # damping coefficient (s^-1)
A = 0.5           # forcing amplitude
omega = np.sqrt(g / L)  # driving frequency (resonance condition, rad/s)
theta0 = 0.1      # initial angle (radians)
theta_dot0 = 0.0  # initial angular velocity (rad/s)

# Time settings
t_max = 20.0      # total simulation time (s)
dt = 0.01         # time step (s)
t = np.arange(0, t_max, dt)  # time array

# Define the system of ODEs
def pendulum_derivatives(t, state, b, g, L, A, omega):
    theta, theta_dot = state
    dtheta_dt = theta_dot
    dtheta_dot_dt = -b * theta_dot - (g / L) * theta + A * np.cos(omega * t)
    return np.array([dtheta_dt, dtheta_dot_dt])

# RK4 solver
def rk4_step(t, state, dt, b, g, L, A, omega):
    k1 = pendulum_derivatives(t, state, b, g, L, A, omega)
    k2 = pendulum_derivatives(t + dt/2, state + dt * k1/2, b, g, L, A, omega)
    k3 = pendulum_derivatives(t + dt/2, state + dt * k2/2, b, g, L, A, omega)
    k4 = pendulum_derivatives(t + dt, state + dt * k3, b, g, L, A, omega)
    return state + (dt / 6) * (k1 + 2*k2 + 2*k3 + k4)

# Initialize arrays
state = np.array([theta0, theta_dot0])  # [theta, theta_dot]
theta = np.zeros(len(t))
theta_dot = np.zeros(len(t))
theta[0], theta_dot[0] = state

# Simulate the motion
for i in range(1, len(t)):
    state = rk4_step(t[i-1], state, dt, b, g, L, A, omega)
    theta[i], theta_dot[i] = state

# Calculate total energy
m = 1.0  # mass (kg), assumed for simplicity
energy = 0.5 * m * L**2 * theta_dot**2 + 0.5 * m * g * L * theta**2

# Plotting
plt.figure(figsize=(12, 8))

# Angle vs Time
plt.subplot(2, 1, 1)
plt.plot(t, theta, label=r'$\theta(t)$')
plt.xlabel('Time (s)')
plt.ylabel('Angle (rad)')
plt.title('Forced Damped Pendulum Motion')
plt.grid(True)
plt.legend()

# Energy vs Time
plt.subplot(2, 1, 2)
plt.plot(t, energy, label='Total Energy', color='orange')
plt.xlabel('Time (s)')
plt.ylabel('Energy (J)')
plt.title('Total Energy Over Time')
plt.grid(True)
plt.legend()

plt.tight_layout()
plt.show()

# Optional: Phase space plot
plt.figure(figsize=(6, 6))
plt.plot(theta, theta_dot, label='Phase Trajectory')
plt.xlabel(r'$\theta$ (rad)')
plt.ylabel(r'$\dot{\theta}$ (rad/s)')
plt.title('Phase Space: Angle vs Angular Velocity')
plt.grid(True)
plt.legend()
plt.show()
```
![alt text](image-8.png)
![alt text](image-9.png)

## Explanation of the Code
### Parameters

The following physical constants and initial conditions are defined with realistic values:

- $g$: Gravitational acceleration (m/s²).
- $L$: Pendulum length (m).
- $b$: Damping coefficient (s⁻¹).
- $A$: Forcing amplitude.
- $\omega$: Driving frequency (rad/s), set to $\omega = \sqrt{\frac{g}{L}}$ to match the natural frequency for resonance.
- Initial conditions:
  - $\theta_0$: Initial angle (radians), starting the pendulum slightly displaced.
  - $\dot{\theta}_0$: Initial angular velocity (rad/s).

### ODE Definition

The `pendulum_derivatives` function computes the derivatives of the system:
- $$\frac{d\theta}{dt}$$: Angular velocity.
- $$\frac{d\dot{\theta}}{dt}$$: Angular acceleration, based on the equation of motion.

### RK4 Solver

The `rk4_step` function implements the 4th-order Runge-Kutta method to advance the solution one time step:
- Numerically integrates the ODEs using four stages (k1, k2, k3, k4) to update $\theta$ and $\dot{\theta}$.

### Simulation

The simulation process:
- A loop iterates over time, updating:
  - $\theta$: Angular displacement.
  - $\dot{\theta}$: Angular velocity.
- Energy is calculated using:
  - $E = \frac{1}{2} m L^2 \dot{\theta}^2 + \frac{1}{2} m g L \theta^2$ (small-angle potential energy, in joules).

### Visualization

The results are visualized with:
- Two plots:
  1. Angle ($ \theta $) vs. time.
  2. Energy ($ E $) vs. time.
- A phase space plot:
  - $ \theta $ vs. $ \dot{\theta} $, showing the system’s trajectory in phase space.

----

![alt text](image-1.png)
![alt text](image-2.png)
![alt text](image-3.png)
![alt text](image-4.png)
---

## **3. Analysis of Dynamics**
### **3.1 Influence of Parameters**
- **Damping coefficient $(\gamma)$**:
  - High damping suppresses oscillations.
  - Low damping allows oscillations to persist longer.
- **Driving force amplitude $( A )$**:
  - Larger $ A $ can lead to chaotic motion.
- **Driving frequency $( \omega )$**:
  - At $\omega \approx \omega_0$, resonance occurs.
  - For high $\omega$, behavior becomes irregular.

### **3.2 Transition to Chaos**
As forcing increases, the system moves from periodic motion to quasiperiodic and eventually chaotic behavior. This is analyzed using:
- **Phase portraits** (visualizing velocity vs. displacement)
- **Poincaré sections** (snapshots at regular time intervals)
- **Bifurcation diagrams** (tracking equilibrium solutions as parameters vary)

---

## **4. Practical Applications**
- **Energy Harvesting**: Piezoelectric pendulums convert oscillations into electrical energy.
- **Suspension Bridges**: Understanding resonance prevents structural failures.
- **Electrical Circuits**: Analogous to forced RLC circuits.
- **Biomechanics**: Gait patterns modeled using pendulum dynamics.

---

## **5. Computational Implementation**
### **5.1 Numerical Simulation**
The forced damped pendulum equation is solved using numerical integration (Runge-Kutta method). The code generates:
- **Motion plots** for varying parameters.
- **Phase portraits** to visualize periodic and chaotic behavior.
- **Poincaré sections** for transition analysis.
- **Bifurcation diagrams** to study nonlinear effects.

### **5.2 Model Limitations & Extensions**
#### **Limitations**:
- Small-angle approximations break down at large amplitudes.
- Linear damping does not always accurately model real-world resistance.
- External forcing is not always purely periodic.

#### **Extensions**:
- Nonlinear damping (e.g., quadratic drag) can be incorporated.
- Stochastic forcing to simulate real-world perturbations.
- Coupled oscillators for multi-degree-of-freedom systems.

---


