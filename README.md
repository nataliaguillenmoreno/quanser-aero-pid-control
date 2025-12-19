# Qualitative PID Control of a Quanser AERO Pitch Axis

## Project Motivation
Modern aerospace systems—from drones to spacecraft—rely heavily on feedback control to maintain stability, reject disturbances, and track commanded motions. While control theory is often taught abstractly, real-world systems introduce nonlinearities, noise, actuator limits, and sensor imperfections that fundamentally shape controller performance.

The motivation behind this project was to bridge theoretical PID control concepts with real hardware behavior by tuning a controller on a physical aerospace testbed. Rather than designing a controller purely in simulation, this experiment emphasized hands-on controller tuning, sensitivity analysis, and understanding how each PID gain affects a real dynamic system.

## What Is the Quanser AERO?
The Quanser AERO is an educational aerospace laboratory platform designed to model the dynamics of rotorcraft and vertical-lift vehicles. It consists of a rigid beam mounted on a low-friction pivot, with two electric propellers mounted at each end. 

The system is instrumented with:
<br />

* **Encoders** for angular position
* **An inertial measurement unit (IMU)** for rate sensing
* **DC motors** driven by voltage commands from Simulink

<p align="center">
  <img src="https://i.imgur.com/BKo5Qo2.jpeg" height="50%" width="50%" alt="Quanser AERO Hardware"/>
  <br />
  <i>Figure 1: The Quanser AERO aerospace laboratory testbed.</i>
</p>

Because of its unstable and lightly damped dynamics, the Quanser AERO is well-suited for feedback control experiments, particularly for studying the effects of proportional, derivative, and integral control on system response.

## Control Objective
The goal was to control the pitch angle using a PID controller to meet the following performance requirements:
<br />

* **Peak overshoot:** within $\pm 0.35$ radians
* **Peak time:** less than $1.1$ seconds
* **Steady-state error:** within $\pm 3\%$ of a commanded $0.3$ radian square wave

## Control Architecture and Modeling Approach
The control system was implemented in MATLAB/Simulink using a standard PID form:

$$C(s) = K_p + \frac{K_i}{s} + K_d s$$

To reduce noise amplification associated with derivative control, a first-order low-pass filter was applied to the derivative term:

$$F(s) = \frac{20s}{s + 20}$$

## Experimental Tuning Strategy
The controller was built up incrementally to isolate the effect of each individual term.

### 1. Proportional Control ($K_p$)
Increasing $K_p$ reduced rise time and improved tracking but introduced oscillations and eventual instability. This demonstrated that proportional control alone cannot guarantee stability in lightly damped systems.

<p align="center">
  <img src="https://i.imgur.com/qW1wNA0.png" height="60%" width="60%" alt="Step response varying Kp"/>
  <br />
  <i>Figure 2: Step response behavior with varying Proportional Gain ($K_p$).</i>
</p>

### 2. Derivative Control ($K_d$)
Derivative control was added to improve damping. By responding to the rate of change, the derivative term counteracted overshoot. As $K_d$ increased, oscillations decreased and settling time improved.

<p align="center">
  <img src="https://i.imgur.com/shQZZ2Z.png" height="60%" width="60%" alt="Step response varying Kd"/>
  <br />
  <i>Figure 3: Step response behavior with varying Derivative Gain ($K_d$).</i>
</p>

<p align="center">
  <img src="https://i.imgur.com/0bvaHNq.png" height="60%" width="60%" alt="Square-wave response varying Kd"/>
  <br />
  <i>Figure 4: Square-wave response behavior with varying Derivative Gain ($K_d$).</i>
</p>

### 3. Integral Control ($K_i$)
Finally, integral control was introduced to eliminate steady-state error. While increasing $K_i$ improved accuracy, large values caused increased overshoot, highlighting the stability/accuracy trade-off.

<p align="center">
  <img src="https://i.imgur.com/62kdNIy.png" height="60%" width="60%" alt="Square-wave response varying Ki"/>
  <br />
  <i>Figure 5: Square-wave response behavior with varying Integral Gain ($K_i$).</i>
</p>

## Final PID Performance
Through iterative tuning, the final gains were selected as: **$K_p = 11.55$**, **$K_d = 3.47$**, and **$K_i = 3$**.

**Performance Results:**
* **Peak time:** $\approx 1.01$ s
* **Steady-state error:** $\approx 0.0085$ rad
* **Overshoot:** within $\pm 0.35$ rad

## Engineering Takeaways
<br />

* **Interdependence:** PID gains are strongly coupled and must be tuned as a system.
* **Noise Sensitivity:** Derivative control improves stability but requires filtering to manage sensor noise.
* **Stability vs. Accuracy:** Integral control is essential for zero steady-state error but can destabilize the system if over-saturated.
* **Hardware Reality:** Real-world dynamics (friction, motor limits) often require more conservative tuning than idealized models suggest.
