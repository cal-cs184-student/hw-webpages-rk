<script type="text/javascript" async 
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.0/es5/tex-mml-chtml.js">
</script>
<script type="text/javascript">
  window.MathJax = {
    tex: {
      inlineMath: [['$', '$'], ['\\(', '\\)']],
      displayMath: [['$$', '$$'], ['\\[', '\\]']]
    }
  };
</script>

# CS184/284A Spring 2025 Homework 4 Write-Up
## by Ramya Chitturi and Kerrine Tai

Link to webpage: <a href="https://cal-cs184-student.github.io/hw-webpages-rk/index.html">https://cal-cs184-student.github.io/hw-webpages-rk/hw4/index.html</a>

Link to GitHub repository: <a href="https://github.com/cal-cs184-student/sp25-hw3-r-k-4">https://github.com/cal-cs184-student/sp25-hw3-r-k-4</a>

<img src="clothsim.png" alt="Cloth draped over sphere" style="width:70%"/>

## Overview

In this homework, we explored real time simulation of cloth through different techniques. We defined the cloth as springs connected with point masses, and then simulated how this cloth interacted with other objects with collisions. Finally, we added different visual features to the cloth. This project was super cool, as in some ways, cloth is also just connected fibers interacting with other objects. Being able to see the collisions and outputted rendering immediate was exciting, especially because of how long we had to wait to see the results last project. Some of the images are bit small, but you can open them up in a new tab to see them more clearly!

## Part 1: Masses and springs

Screenshots of scene/pinned2.json! Cloth wireframe.

<img src="media/part1/pinned2_1.png" width="300px"/>
<img src="media/part1/pinned2_2.png" width="300px"/>
 
Wireframe with different constraints. 

| without any shearing constraints | with only shearing constraints | with all constraints |
| :----: | :----: | :----: |
| <img src="media/part1/no_shearing.png" width="500px"/> | <img src="media/part1/only_shearing.png" width="500px"/> | <img src="media/part1/all_constraints.png" width="500px"/> |

## Part 2: Simulation via numerical integration

| ks 100 | ks 10000 |
| :----: | :----: |
| <img src="media/part2/ks_100.png" width="500px"/> | <img src="media/part2/ks_10000.png" width="500px"/> |

Changing the ks, or spring constant, changes how stiff or flowy the fabric is. In the image on the left where ks = 100, while the cloth was moving, it was falling faster and was very bouncy. When ks = 10000, the cloth was very stiff and moved slower. This is because when the spring constant is higher, the cloth is less suseptible to changes such as gravity.

| density 1 | density 100 |
| :----: | :----: |
| <img src="media/part2/density_1.png" width="500px"/> | <img src="media/part2/density_100.png" width="500px"/> |

The density changes the cloth's weight in this simmulation. When the density = 1 (low density), the cloth falls gently, and it does not sag as much. On the other hand, when density = 100, the cloth drapes faster and sags down much further. The fall for the heavier density is also somewhat bouncy. 

| damping low | damping high |
| :----: | :----: |
| <img src="media/part2/damping_lowest.png" width="500px"/> | <img src="media/part2/damping_highest.png" width="500px"/> |

In this simulation, the damping term simulates loss of energy. When damping is low, little energy is lost, so the cloth is stays bouncy and has movement for longer. It reminded me of a bouncy ball. When the damping value is high, the cloth had little movement besides immediately going to the final resting position. 

Scene/pinned4.json in final resing state with default parameters

<img src="media/part2/pinned4_resting.png" width="500px"/>

## Part 3: Handling collisions with other objects

For collision with spheres, for every single point mass, we collided it with every single possible object. We did this by first checking to see whether the point mass is inside the sphere or not. If it is, we calculate the direction vector to "push" the point out and also how far out to push the point. We then change the point masses's position at every timestep towards the correct psition on the sphere surface, scaled by friction.

For collision with planes, the process is similar to spheres. However, instead of checking if the point mass is inside the plane, we check if the point has "crossed" the plane. We first get the point on plane and the current poisiton of the point mass, then check if the point mass has crossed the plane by using the dot product and checking the sign of the signed distance. If the point mass crossed the plane, we calculate where the point mass is projected to fall onto the plande with a correction vector (intersection point - last position of the point mass). Then we move the point above the plane (by SURFACE_OFFSET) and apply a collision correction. 

| ks = 500 | ks = 5000 | ks = 50000 |
| :----: | :----: | :----: |
| <img src="media/part3/sphere_ks500.png" width="500px"/> | <img src="media/part3/sphere_ks5000.png" width="500px"/> | <img src="media/part3/sphere_ks50000.png" width="500px"/> |

As described in part 1, changing the ks value changes the stiffness or flowiness of the cloth. With a low ks value of 500, the cloth is very flowy and is able to fall on the sphere and show the shape of the sphere more clearly. In ks = 50000, the sphere is stiff and is not able to wrap around the ball tightly. In ks = 5000 image, the spring constant is between the values in the other image, and the cloth is also reflected as such. The cloth is able to wrap around the ball better than when ks = 50000, but the shape is not as clear as when ks = 500. 

Cloth lying peacefully at rest
<img src="media/part3/plane_original.png" width="500px"/>