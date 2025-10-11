---
layout: post
title:  "P2-Follow-line"
date:   2024-09-30 9:22:00 +0200
categories: mobile-robotic
---
## Detect and Follow the Red Line

This algorithm is designed to detect a red line in an image, calculate its slope, and adjust the linear and angular velocities of an autonomous car. The camera captures the image, and the HSV filter isolates the red pixels.

<div style="text-align: center;">
    <img src="/assets/images/slope.png" alt="Texto alternativo" />
</div>

# Following Strategy

- **Red Color Detection:**
  An HSV filter is applied to detect all possible shades of red, covering the full range of hue and saturation values, corresponding to the line in the image.

- **Slope Calculation:**
  Based on the start and end points of the red line, obtained from the midpoint of the first red pixel and the last red pixel on a row, the slope is calculated. This slope is then used to adjust the car’s velocities, correcting its direction based on the detected slope of the line.

- **Dynamically Adjusted Velocities:**
  The linear velocity is also adjusted according to the car’s distance from the center of the red line, ensuring it stays on track during sharp turns or abrupt maneuvers.

# Limitations

Like other line-following algorithms, this one has some limitations:

1. **Sudden Velocity Changes:**
   If the line slope changes abruptly, the car may oscillate until the angular velocity stabilizes.

2. **Performance on Long, Gentle Curves:**
   On long, gentle curves, the linear velocity tends to increase, but the angular velocity struggles to control it in a stable way. As a result, at some point, the car starts to oscillate, leading to instability in following the line accurately.

# Algorithm Performance

In controlled conditions, the algorithm keeps the car on the line, adjusting angular velocity based on the slope and applying a limited linear velocity. The response to sharp curves is excellent, although long, gentle curves require more slope adjustment

# Attempts

- **P try**:
    ![Texto alternativo](/assets/images/P.png)

- **PD on all velocities**:
    ![Texto alternativo](/assets/images/Time_pd_all.png)
<div style="text-align: center;">
<iframe width="560" height="315" src="https://www.youtube.com/embed/Zg2ssoEZrcg" frameborder="0" allowfullscreen></iframe>
</div>

# Conclusion

In summary, this algorithm efficiently follows red lines, adjusting both angular and linear velocities dynamically. While it excels in sharp curves, it faces more challenges in long, gentle curves where the linear velocity tends to increase, causing the angular control to become unstable and leading to oscillations.

