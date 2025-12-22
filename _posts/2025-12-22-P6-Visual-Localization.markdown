---
layout: post
title: "P6-Visual-Localization"
date: 2025-12-22 11:00:00 +0200
categories: service-robotics
---

# **Visual Localization and Navigation Using AprilTags**

This project implements a **vision-based localization and navigation system** for a mobile robot using **AprilTags** as known references.  
The robot continuously detects visual markers, estimates its global pose from the camera, and autonomously navigates towards them while avoiding frontal obstacles.

The system combines camera geometry, pose estimation, reactive control, and landmark-based localization, **without relying on odometry or mapping**.

<div style="text-align: center;">
    <img src="/assets/images/PNP.png" alt="Visual Marker Example" />
</div>

## **General Strategy**

The robot follows a main loop that integrates:

- AprilTag detection using the onboard camera  
- Absolute pose estimation using known tag positions  
- Reactive obstacle avoidance using laser data  
- Navigation directed toward detected tags  
- Memory of visited landmarks  

If no valid visual references are available, the robot performs a controlled rotation to recover localization.



## **AprilTag Map**

Tag positions are defined **a priori** in a YAML file, each with:

- `(x, y, z)` position  
- `yaw` orientation  

These data are transformed into **4x4 homogeneous matrices**, allowing direct transformations between the tag frame, the camera, and the world, establishing a **global reference system**.



## **Camera Model and Tag Geometry**

The camera is modeled as a **pinhole projection**:

- Square focal length proportional to the image width  
- Principal point at the center  
- No lens distortion  

Each tag is defined as a square with known dimensions and its four local 3D points are stored, allowing pose estimation from a single image.



## **Visual Pose Estimation**

For each detected tag:

1. Extract the corners from the image  
2. Apply `solvePnP` to obtain the **camera→tag** transformation  
3. Invert it to get **tag→camera**  
4. Select the nearest tag to reduce noise  
5. Compute the chain of transformations to the world  

From this, we obtain:

- Global pose `(x, y)`  
- `yaw` orientation of the camera's forward axis  

This provides an **absolute pose estimate**, independent of prior state.



## **Reference Frame Alignment**

Fixed rotations are applied to align:

- Camera forward direction  
- Horizontal world plane  
- Extracted yaw angle  

This ensures a consistent geometric interpretation of position and orientation.



## **Reactive Obstacle Avoidance**

The frontal sector is continuously monitored with the laser:

- If a nearby obstacle is detected  
- And no tags are visible  

The robot stops linear motion and rotates in place. The rotation direction depends on the obstacle location, enabling safe exploration while searching for visual references.



## **Landmark Selection**

To avoid oscillations:

- Detected tags are filtered using a memory of visited ones  
- Unvisited tags are prioritized  
- When approaching a tag, it is marked as visited  
- After each visit, the rotation direction can be reversed to improve coverage  



## **Motion Control**

A proportional controller is used toward the selected tag:

- Linear velocity proportional to distance  
- Angular velocity proportional to orientation error  
- Commands are saturated to ensure smooth motion  

If no valid pose estimate exists, the robot adopts cautious movement or a recovery rotation.



## **Pose Propagation**

When no visual update is available, the estimated pose is propagated using differential kinematics.  
Angular velocity is scaled to compensate for actuator mismatches, improving temporal consistency.



## **Videos**

**Non-Inverter**
<div style="display: flex; justify-content: center;">
  <iframe width="700" height="394" src="https://www.youtube.com/embed/jmLAXsD6uHo" frameborder="0" allowfullscreen></iframe>
</div>

**Inverter**
<div style="display: flex; justify-content: center;">
  <iframe width="700" height="394" src="https://www.youtube.com/embed/8IMoRLsYa5M" frameborder="0" allowfullscreen></iframe>
</div>

## **Conclusion**

This project demonstrates a complete landmark-based visual localization system:

- Absolute pose estimation from tags  
- Robust recovery from reference loss  
- Reactive obstacle avoidance  
- Simple and effective navigation  
- Lightweight, modular, and suitable for indoor environments with visual markers