---
layout: post
title:  "P1-Autonomous-Vacuum-Cleaner-Grid"
date:   2025-10-12 11:00:00 +0200
categories: service-robotics
---

# **Autonomous Navigation for a Vacuum Robot**

In this blog, I present the implementation of a **Grid-Based Planning & Navigation Algorithm** for a vacuum robot. This system allows the robot to explore and clean an area efficiently, avoiding obstacles and optimizing surface coverage.

<div style="text-align: center;">
    <img src="/assets/images/cleaner_img.png" alt="Grid-based planning" />
</div>

## **Strategy**

The algorithm combines several techniques:

- **Obstacle Inflation 🚧**: Increases the perceived size of obstacles to ensure the robot does not collide.
- **Grid Creation**: The map is divided into cells classified by their state (free, occupied, obstacle, critical, or return).
- **Grid-Based Planning**: A movement plan is generated from the starting cell to cover all accessible areas.
- **Robot Control**: A proportional controller adjusts linear and angular velocity to follow the plan smoothly.
- **Initial Direction Selection**: Based on laser data, the robot decides its initial orientation toward the area with the most free space.

## **Map Preparation**

**1. Obstacle Inflation**  
The **map is inflated** to increase obstacle size considering the robot’s radius, ensuring it does not get too close to walls or furniture.

**2. NumPy Conversion**  
The map is converted into a **NumPy array** for easier processing, representing free and occupied cells with discrete values.

**3. Grid Creation**  
The grid divides the map into cells roughly the size of the robot’s diameter. Each cell is classified as:

- **FREE**: Accessible cell.
- **OBSTACLE**: Cell occupied by an obstacle.
- **CLOSED**: Partially accessible cell.
- **OCCUPIED**: Already visited cell.
- **CRITICAL**: Currently targeted cell.
- **RETURNED**: Return cell to follow unexplored paths.

Each cell stores its center in pixels and its state, facilitating planning and visualization.

<div style="text-align: center;">
    <img src="/assets/visual_grids.gif" alt="Grid visualization" />
</div>

## **Motion Planning**

**1. Selecting the Initial Cell**  
The robot’s current cell is identified using its world coordinates and a map-to-pixels transformation function.

**2. Priority and Exploration Order**  
The algorithm explores neighboring cells following a **Dynamic Priority Order** adjustable in real time. By default, it may follow West → North → East → South, but it can be modified depending on the environment or user preferences.

Each visited cell is marked as **OCCUPIED**. This dynamic priority is passed directly to the `planificate_grid` function, allowing the robot to change its exploration strategy based on specific needs or sensor data.

**3. Searching for the Nearest Free Cell**  
If the robot gets stuck, **BFS (Breadth-First Search)** is used to find the nearest free cell. This ensures the robot continues exploring the entire area without getting trapped.

**4. Plan Generation**  
The final plan consists of a list of cells the robot must visit, prioritizing full coverage while avoiding obstacles.

## **Robot Control**

The robot adjusts its **linear & angular velocity** using a proportional controller that considers:

- Distance to the target.
- Angular error between the robot’s orientation and the target cell direction.
- Alignment of upcoming cells to smooth or accelerate movement.
- Speed reduction when approaching the target cell.

Maximum speed is limited for safety, and adjustment factors are applied to keep the robot aligned with the planned trajectory.

## **Initial Direction Based on Laser**

Before starting, the laser data is analyzed to select the initial direction with the most free space. This avoids unnecessary movement toward blocked areas at the beginning of the route.

## **Robot States 🤖**

The algorithm defines three main states:

- **PLANIFICATE 🗺️**: Generates the exploration plan based on the grid.
- **TRAVEL_IT 🚶**: Robot follows the plan visiting cells.
- **FINISHED ✅**: All accessible cells have been explored.
- **TEST 🧪**: Allows visual verification of the grid and the robot’s current position without moving.

## **Visualization and Monitoring**

The grid and map are visualized in real time using WebGUI, with different colors according to each cell’s state:

- **Black**: Obstacle
- **Green**: Free
- **Indigo**: Occupied
- **Red**: Critical
- **Violet**: Return
- **Orange**: Default/Closed

This enables intuitive monitoring of the robot’s planning and progress.

<div style="text-align: center;">
    <img src="/assets/planify_bfs(1).gif" alt="Grid visualization" />
</div>

# **Videos**

- **Autonomous Cleaner 🤖**:
<div style="display: flex; justify-content: center;">
  <video width="700" controls>
    <source src="{{ '/assets/videos/Cleaner_aut_short.mp4' | relative_url }}" type="video/webm">
    Your browser does not support WebM format.
  </video>
</div>

## **Conclusion**

This **grid-based navigation system** allows a vacuum robot to:

- Explore complex areas autonomously.
- Avoid obstacles and minimize collisions.
- Efficiently cover the entire map surface.

Thanks to the combination of **Obstacle Inflation 🚧**, **Grid Planning 🟩**, **BFS for Alternative Routes 🔍**, and a **Proportional Controller ⚙️**, the robot achieves full and safe autonomous cleaning. This approach is scalable and can adapt to different environments by simply adjusting the grid size and the robot’s radius.
