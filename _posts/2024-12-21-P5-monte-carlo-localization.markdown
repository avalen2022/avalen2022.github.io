---
layout: post
title:  "P5-Monte-Carlo-Localization"
date:   2024-12-21 9:30:00 +0200
categories: robotica-movil
---

# **Monte Carlo Localization (MCL)**

Monte Carlo Localization (MCL) is a probabilistic algorithm used for estimating the position and orientation of a mobile robot within a known map. It employs a **particle filter** approach, which models the robot’s location using a set of particles distributed across the map. Each particle represents a hypothesis about the robot’s state, and these hypotheses are iteratively refined as the robot moves and receives sensor data.

#### 1. Particle Initialization  
The algorithm begins by generating particles randomly distributed within the map. These particles represent possible initial positions and orientations of the robot. Gaussian noise is applied to each particle to simulate uncertainty, ensuring the robot's position and orientation are likely represented by one of the particles.

![Animación de ejemplo](/assets/random-start-filter.gif)

#### 2. Motion Update (Propagation)  
As the robot moves, the particles are updated based on the motion commands. Random noise is added to account for slippage, wheel errors, and other sources of inaccuracy. This step ensures that the particle distribution reflects the robot’s movement with realistic deviations.

![Animación de ejemplo](/assets/propagation-particles.gif)

#### 3. Sensor Update  
For each particle, simulated sensor readings are compared to actual sensor measurements. This comparison helps determine how likely each particle is to represent the robot’s true position. Particles that closely match the real sensor data are assigned higher weights.

#### 4. Resampling  
Particles are resampled based on their weights, giving preference to the most likely hypotheses. Low-probability particles are discarded, while high-probability particles are duplicated. This step effectively concentrates the particles around the areas where the robot is most likely located.

![Animación de ejemplo](/assets/resampling.gif)

#### 5. Visualization  
Particles are displayed visually, providing a real-time graphical representation of the robot’s position estimate. This visualization aids in debugging and helps validate the algorithm’s performance.

# **Why Use MCL?**

MCL is particularly useful because it handles uncertainty effectively and adapts dynamically to the robot’s movements and sensor readings. It is suitable for both static and dynamic environments, making it a versatile approach for mobile robot localization.

# **Challenges and Limitations**

Despite its strengths, MCL also has some limitations:  

1. **Dependence on an Accurate Map:**  
   The algorithm assumes the map is precise. Errors in the map can lead to localization failures.  

2. **Particle Count Trade-off:**  
   Using too few particles may produce poor estimates, while too many particles can increase computational costs.  

3. **Sensor Noise Sensitivity:**  
   Noisy sensor data can degrade performance if not properly modeled.  

# **Parameter Tuning**

To optimize performance, several parameters were fine-tuned:  

- **Number of Particles:** 1,000 particles were used to balance accuracy and computational efficiency.  
- **Initial Uncertainty:** Gaussian noise was applied to both position and orientation to simulate realistic uncertainty at startup.  
- **Motion Noise:** Small random variations were introduced during propagation to account for robot dynamics.  
- **Sensor Noise:** Probabilistic models were used to handle noisy sensor data and improve robustness.  
- **Roulette Wheel Noise:**  
  To ensure diversity during the **resampling phase**, Gaussian noise was added to the particles selected by the **roulette wheel algorithm**. This prevents particle depletion and avoids excessive clustering, particularly in environments with ambiguous sensor data or multiple hypotheses.  

# **Videos**

- **MCL**:
<div style="text-align: center;">
<iframe width="560" height="315" src="https://www.youtube.com/embed/PSR3bDRM7eE" frameborder="0" allowfullscreen></iframe>
</div>

# **Conclusion**

The Monte Carlo Localization algorithm implemented in this project successfully estimates the robot’s position using a particle filter approach. Through careful parameter tuning and noise modeling, the algorithm achieves reliable performance even in the presence of sensor and motion uncertainties. Future improvements could include adaptive particle counts and integration with SLAM techniques for unknown environments.

