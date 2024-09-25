---
layout: post
title:  "Vacuum Cleaner"
date:   2024-09-25 18:49:50 +0200
categories: jekyll update
---

# Vacuum Cleaner Algorithm

My vacuum cleaner algorithm is designed to ensure thorough cleaning of the floor. To achieve this, my algorithm prioritizes area coverage over the speed of exploring multiple rooms.

## Cleaning Strategy

- **Oval Cleaning Patterns**: 
  Utilizes oval patterns that effectively clean elongated spaces, such as rectangles.

- **Directional Bumper**: 
  The bumper alters the direction of rotation based on its impact orientation, allowing small "dodge" maneuvers—retreating, turning, and advancing while rotating. This enables the robot to cover large areas without leaving uncleaned gaps.

- **Randomly size ovals**: 
  Selects a random oval size when it has previously collided with a side. If it completes the oval without colliding, it returns to performing ovals of a predetermined size.

## Limitations

While this approach ensures thorough cleaning, it comes with some trade-offs:

1. **Time Consumption**: 
   The robot sometimes revisits areas it has already cleaned, leading to wasted time.

2. **Battery Capacity**: 
(rehacer)
3. **Speed Restrictions**: 
(rehacer)

## Performance Summary

The average performance of my algorithm allows it to clean a fairly extensive surface area in about 10 minutes without leaving traces behind. Additionally, it incorporates a detection counter that only tracks transitions between states related to the bumper, along with a random modifier for the oval's amplitude, which helps prevent the robot from getting stuck.

## Attempts

- **First try with random ovals**:
    ![Texto alternativo](/assets/images/Vacuum_cleaner_randomly.png)

- **Perfecting parameters**:
    ![Texto alternativo](/assets/images/Bastante_bueno.png)

- **Good cleans**:
    ![Texto alternativo](/assets/images/simplemente_increible.png)

- **Perfect clean**:
    ![Texto alternativo](/assets/images/Vacuum_cleaner_10min.png)

## Conclusion

In summary, while there are some limitations to consider, the primary goal of my robot remains thorough cleaning.

Check out my practice on [GitHub][github-p1].

[github-p1]: https://github.com/urjc-docencia-robotica-movil/p1-vacuum-cleaner-24-25-avalen2022

