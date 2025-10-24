---
layout: post  
title: "P2-Rescue-drone"  
date: 2025-10-23 11:00:00 +0200  
categories: service-robotics  
---

# **Autonomous Search and Rescue Drone**

This project presents the implementation of an **autonomous search and rescue system** for a drone, designed to explore a defined area, locate people through computer vision, and automatically return to its base upon mission completion or under critical conditions.  

The main objective is to ensure systematic and efficient exploration of the environment by combining geometric planning, flight control, and visual detection through image processing.

<div style="text-align: center;">
    <img src="/assets/images/drone_sweep.png" alt="Rescue Drone Pattern" />
</div>

## **General Strategy**

The system integrates several coordinated modules:

- **Path Planning**: Generation of a square sweep trajectory over the search area.  
- **Autonomous Flight Control**: Waypoint tracking with position and orientation control.  
- **Computer Vision**: Human face detection using a cascade classifier.  
- **State Management**: Automatic transition between takeoff, exploration, return, and landing phases.  
- **Temporal Supervision**: Automatic return to the base point when the battery is depleted.

## **Sweep Planning**

The search area is structured as a square defined by an initial coordinate, centered at the origin, and a lateral size.  
The drone generates a set of evenly spaced waypoints covering the entire surface, alternating the exploration direction between rows to optimize the path and avoid unnecessary displacements.  

The flight pattern ensures complete coverage with a constant altitude and an orientation angle calculated toward each new target. Each point is considered reached when the distance to the drone falls below a defined tolerance threshold.

## **Geographical Conversion**

The coordinates of key points, such as the base ship position and the rescue zone, are defined in **degrees, minutes, and seconds (DMS)** format and converted into **UTM** distances, which are then transformed into Cartesian coordinates.  
This conversion allows precise calculation of distances and directions using spherical trigonometry, taking the Earth’s radius as the reference.

## **System States**

The drone operates sequentially according to a defined set of states:

1. **TAKEOFF**  
   The drone takes off to a predefined operating altitude. Depending on the recharge status or if an interruption has been triggered, it proceeds to planning or resumes the search.

2. **PLANIFICATE**  
   The position of the rescue area is calculated from geographic coordinates, and the square exploration pattern is generated.

3. **SEARCH_IT**  
   The drone follows the planned trajectory. During flight, it analyzes images from its downward-facing camera to identify potential people using a face detection model.  
   Image rotations and HSV color-space filtering are applied to reduce false positives and improve robustness against lighting variations.

4. **RETURN**  
   Once the search is complete or the battery signal is triggered, the drone automatically returns to the base point, adjusting its orientation and flight path accordingly.

5. **LAND**  
   The drone initiates a controlled landing procedure. After verifying its position and maintaining stability for a minimum duration, the final landing is executed.

6. **DONE**  
   The mission concludes when the number of detected people reaches the predefined target or when the flight cycle is completed.  

<div style="text-align: center;">
    <img src="/assets/images/drone_states.png" alt="Drone States Diagram" />
</div>

## **Person Detection**

Human identification is performed using a Haar Cascade classifier trained for face detection.  
During the search, the drone analyzes multiple image orientations to increase detection probability from different angles.  
Each detection is validated against previously registered positions to avoid duplicates in nearby areas.  

Detected positions are stored in an internal list associating the drone’s spatial coordinates with the locations where faces were identified.

## **Interruption Management and Automatic Return**

The system includes a safety timer that limits mission duration to simulate battery depletion.  
If the set time is exceeded or a critical event is detected, an interruption is triggered to force a transition to the return state.  
This mechanism ensures the drone preserves enough battery to complete a safe landing.

## **Visualization and Supervision**

During execution, the downward and front cameras are displayed in real time through the WebGUI interface, allowing monitoring of both navigation and human detection.  
This visual system facilitates algorithm validation, drone behavior verification, and spatial coverage analysis.

<div style="text-align: center;">
    <img src="/assets/images/drone_cam_feed.png" alt="Drone camera feed" />
</div>

## **Video**

<div style="display: flex; justify-content: center;">
  <video width="700" controls>
    <source src="{{ '/assets/videos/Drone_rescue.mp4' | relative_url }}" type="video/webm">
    Your browser does not support WebM format.
  </video>
</div>

## **Conclusions**

The search and rescue drone combines autonomous planning, flight control, computer vision, and real-time supervision to conduct exploration missions in bounded environments.  
Its state-based architecture enhances system extensibility, allowing new conditions or sensors to be integrated without altering the main structure.  

The integration of strategies such as sweep planning, automatic return, and multicriteria face detection forms a solid foundation for future applications in autonomous rescue and surveillance missions.
