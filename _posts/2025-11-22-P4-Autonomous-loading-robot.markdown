---
layout: post  
title: "P4-Autonomous-Loading-Robot"  
date: 2025-11-22 11:00:00 +0200  
categories: service-robotics  
---

# **Autonomous Loading Robot in Warehouse Simulation**

This project presents the development of an **autonomous robot system** capable of navigating a warehouse environment, transporting shelves, and placing them in designated locations. The main goal is to combine path planning, precise motion control, and state-based task execution to achieve fully autonomous loading operations.

The system is designed to handle shelves of different sizes, avoid collisions, and interact with a simulated warehouse map to update the occupancy grid in real time.

## **General Strategy**

The architecture integrates several modules:

- **Occupancy Map Handling**: Processing warehouse maps, inflating obstacles, and adapting images for path planning.  
- **Path Planning**: Generating collision-free trajectories using the **OMPL Reeds-Shepp planner**, supporting forward and backward motion and minimum turning radius constraints.  
- **Control and Navigation**: Ackermann-style velocity and steering control to follow planned paths with precision.  
- **Shelf Manipulation**: Lifting and depositing shelves using virtual actuators, updating the occupancy map accordingly.  
- **State Management**: A finite state machine (FSM) coordinates planning, moving, elevating, dropping, and waiting stages.

## **Occupancy Map Processing**

The robot relies on a map image of the warehouse. Several transformations are applied:

1. **Map Inflation**: Obstacles are expanded by the robot radius to ensure safe navigation.  
2. **Numpy Adaptation**: The map is converted to numerical arrays for easy computation, where free space and obstacles are encoded with distinct intensity values.  
3. **Coordinate Transformations**: Functions convert between **world coordinates** and **map pixels**, enabling accurate collision checks and path visualization.

## **Path Planning with OMPL**

Collision-free paths are planned using **Reeds-Shepp curves**:

- **State Validity**: Each state is checked for collision by evaluating the robot’s footprint or the shelf’s footprint if carrying a load.  
- **Planner Setup**: The robot space is bounded to the warehouse dimensions. Start and goal positions include x, y, and yaw coordinates.  
- **Planner Execution**: `RRT*` searches for an optimal path, interpolated for smooth execution.  
- **Path Visualization**: Planned paths are drawn on the map using lines and points, providing real-time feedback of trajectories.

## **Control and Motion**

While moving along the path:

- The robot computes the distance and angular error to the next waypoint.  
- Linear and angular velocities are adjusted using proportional control to reduce positional and heading errors.  
- Special alignment maneuvers are executed when approaching shelves to ensure precise pickup.  
- Motion parameters are adapted depending on whether the robot is carrying a shelf or not.

## **Shelf Manipulation**

The robot performs two main operations with shelves:

1. **Elevate (Pickup)**:  
   When the robot reaches a shelf position, it lifts the shelf using `HAL.lift()`, temporarily removes it from the occupancy map, and marks it as carried.  

2. **Drop (Deposit)**:  
   Upon reaching the target position, the shelf is placed using `HAL.putdown()`, and the occupancy map is updated to reflect the shelf’s new location.

A waiting time ensures the elevator mechanism completes its movement before resuming planning.

## **Finite State Machine**

The robot’s behavior is coordinated by the following states:

1. **PLANNING**  
   Computes a new path to the current goal using the updated occupancy map.  

2. **MOVE**  
   Follows the planned trajectory while correcting speed and heading, including special alignment for pickup points.  

3. **ELEVATE**  
   Lifts the shelf from the floor and updates the map.  

4. **DROP_IT**  
   Deposits the shelf at the target location, updating the occupancy map.  

5. **ELAVATOR**  
   Waits for the lifting or dropping operation to finish before proceeding.  

6. **FINISHED**  
   Indicates completion of all assigned transport tasks.  

The FSM ensures robust sequencing of actions and allows the robot to autonomously cycle between multiple pickup and drop locations.

<div style="text-align: center;">
    <img src="/assets/images/warehouse_robot.png" alt="Autonomous loading robot path visualization" />
</div>

## **Visualization and Supervision**

The WebGUI interface shows:

- The current occupancy map with obstacles and shelves.  
- Planned paths with waypoints and traveled trajectories.  
- Updates in real time as shelves are moved, providing intuitive supervision of the robot’s operations.

## **Videos**

**Holonomic Robot**
<div style="display: flex; justify-content: center;">
  <iframe width="700" height="394" src="https://www.youtube.com/watch?v=THtjO0i5f7k" frameborder="0" allowfullscreen></iframe>
</div>

**Ackermann Robot**
<div style="display: flex; justify-content: center;">
  <iframe width="700" height="394" src="https://www.youtube.com/watch?v=gq3E_x_AsTk" frameborder="0" allowfullscreen></iframe>
</div>


## **Conclusions**

The autonomous loading robot demonstrates effective integration of path planning, motion control, and task sequencing in a warehouse scenario.  

Key achievements include:

- Collision-free navigation in dynamic warehouse maps.  
- Autonomous pickup and deposition of shelves with precise positioning.  
- Real-time visualization of paths and occupancy updates.  
- Modular FSM design, making it easy to extend or modify tasks.

This system forms a foundation for future autonomous logistics solutions, capable of handling multiple transport goals with minimal supervision.

