---
layout: post
title: "P3-Autonomous-Parking"
date: 2025-11-07 11:00:00 +0200
categories: service-robotics
---

# **Autonomous Parking Controller**

This project describes the implementation of a complete autonomous parking system applied to ground mobile robots. The main goal is to enable the robot to move through the environment, detect a free parking spot on its right side, align with it, and perform a full automatic parking maneuver with no human intervention.

The overall architecture combines laser–based perception, continuous reactive control over orientation, and a state machine responsible for selecting the correct behavior in each stage of the maneuver.

## **General Strategy**

The system continuously reads data from the front, right, and rear laser sensors.  
From these scans it performs:

- Lateral correction and heading stabilization  
- Detection of available free space on the right side  
- Classification of lateral blocking context  
- State transitions based on geometric patterns

Control is executed by publishing linear and angular velocities through HAL.

## **Sensor Processing**

Laser readings are filtered, infinite values are removed, and only valid useful ranges close to the robot are considered.  
This allows conversion of raw scan data into relevant information such as:

- Real lateral distance to obstacles  
- Frontal/rear proximity  
- Angular minimum patterns for geometric reasoning

## **Lateral Stabilization**

While searching for a spot, the robot keeps a stable lateral distance to the side.  
This is done by combining front, back and center-right differences to maintain a parallel trajectory relative to the street.

## **Environment Classification**

Once the system detects a potential parking space on the **right** side of the robot, it analyzes the lateral context to determine which variant of parking maneuver should be executed.

The classification determines if on the right side there are vehicles:

- In front of the parking gap  
- Behind the parking gap  
- On both sides of the parking gap

This allows the controller to choose the correct parking sequence variant, adapting steering direction and geometry to the scenario.

## **Gap Detection with Trigonometry**

To verify if the parking gap is truly usable, the system checks whether a rectangle equivalent to the parking dimensions (`PARKING_SIZE`) could physically fit inside that space.

Using the lateral distance measured by the right laser, the controller calculates the angular region that represents the potential volume of the parking slot. Through trigonometric relations, the diagonal of that rectangle is obtained, representing the maximum spatial ray that should remain unobstructed. Si todas las lecturas dentro de ese rango angular son mayores que esta diagonal, significa que el robot puede entrar sin colisión.

Una vez que esta condición se cumple, el hueco se valida y se pasa a la fase de alineación.


<div style="text-align: center;">
    <img src="/assets/images/gap_trig.png" alt="Texto alternativo" />
</div>


## **State Machine**

The full maneuver is structured as:

1. **SEARCHING**  
   Drives forward while maintaining lateral regulation until a valid spot is found.

2. **MOVE_CARS**  
   Waits stable before starting alignment to allow external vehicle motion or scenario stabilization.

3. **ALIGN**  
   Initial alignment before starting the backup turn.

4. **F_PARKING**  
   First steering movement into the parking gap.

5. **S_PARKING**  
   Inverse steering correction finishing the “S” shape.

6. **S_ALIGN**  
   Final alignment phase inside the parking spot.

7. **FINISHED**  
   Parking completed.

All transitions depend exclusively on laser perception, no mapping, global navigation, or localization is required.

<div style="text-align: center;">
    <img src="/assets/images/Auto_park.png" alt="Texto alternativo" />
</div>

## **Test**

The system also supports evaluation without depending on real scenario detection.  
Through the internal variable `test_env_` you can force manually which lateral scenario the robot believes it is in.

This allows testing each branch of the maneuver logic without needing to physically move cars inside the simulator:

- `[1,0]` simulates a vehicle in front of the parking right side  
- `[0,1]` simulates a vehicle behind  
- `[1,1]` simulates vehicles both in front and behind the spot

This significantly accelerates functional testing and allows tuning before executing tests with realistic or more complex worlds.

## **Video**

**Both cars**
<div style="display: flex; justify-content: center;">
  <video width="700" controls>
    <source src="{{ '/assets/videos/auto_park.mp4' | relative_url }}" type="video/webm">
    Your browser does not support WebM format.
  </video>
</div>

**Front car**
<div style="display: flex; justify-content: center;">
  <video width="700" controls>
    <source src="{{ '/assets/videos/auto_park_1front.mp4' | relative_url }}" type="video/webm">
    Your browser does not support WebM format.
  </video>
</div>

**Back car**
<div style="display: flex; justify-content: center;">
  <video width="700" controls>
    <source src="{{ '/assets/videos/auto_park_1back.mp4' | relative_url }}" type="video/webm">
    Your browser does not support WebM format.
  </video>
</div>

## **Conclusion**

This autonomous parking controller provides a practical and self–contained solution for parking in mobile robots.  
Its reactive perception-driven architecture and state machine design enable robust behavior without complex planning systems.

Its lightweight structure is ideal for AGV applications, experimental urban robotics, and industrial autonomous navigation research.

