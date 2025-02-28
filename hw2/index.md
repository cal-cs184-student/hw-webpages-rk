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

# CS184/284A Spring 2025 Homework 2 Write-Up
## by Ramya Chitturi and Kerrine Tai

Link to webpage: <a href="https://cal-cs184-student.github.io/hw-webpages-rk/index.html">https://cal-cs184-student.github.io/hw-webpages-rk/hw1/index.html</a>

Link to GitHub repository: <a href="https://github.com/cal-cs184-student/sp25-hw2-r-k-2">https://github.com/cal-cs184-student/sp25-hw2-r-k-2</a>

## Overview

<img src="teapot.png" width="400px" style="display: block; margin: 0 auto;"/>

In this project, we explored several geometric modeling techniques using the half-edge data structure. We used the de Casteljau algorithm to build Bezier curves and surfaces, manipulated triangle meshes represented by the half-edge data structure, and implemented loop subdivision through splitting and flipping edges.

## Section I: Bezier Curves and Surfaces

### Part 1: Bezier curves with 1D de Casteljau subdivision

The goal was to create Bezier curves in 1D using the de Casteljau algorithm. Using the control points, we used the fraction `t` to linearly interpolate the points. Thus, the `n` input points will become `n-1` output points to produce one level of subdivision in the algorithm. We keep recursively running this algorithm by sampling regular intervals of `t` until we only have 1 point left as our base case. We can find some point $p'_i$ by doing $p'_i = (1-t)p_i + p_{i+1}$.

We used the following code to create our own Bezier curve with 6 control points and visualize the algorithm.
```
6
0.200 0.300
0.300 0.700
0.500 0.580
0.600 0.400
0.700 0.700
1.000 0.900
```

<img src="media/part1/Screenshot 2025-02-27 at 10.06.14 PM.png" width="300px"/>
<img src="media/part1/Screenshot 2025-02-27 at 10.06.18 PM.png" width="300px"/>
<img src="media/part1/Screenshot 2025-02-27 at 10.06.22 PM.png" width="300px"/>
<img src="media/part1/Screenshot 2025-02-27 at 10.06.25 PM.png" width="300px"/>
<img src="media/part1/Screenshot 2025-02-27 at 10.06.28 PM.png" width="300px"/>
<img src="media/part1/Screenshot 2025-02-27 at 10.06.34 PM.png" width="300px"/>
<img src="media/part1/Screenshot 2025-02-27 at 10.06.38 PM.png" width="500px"/>

We moved the second point from the right and changed `t` to get the following result.

<img src="media/part1/Screenshot 2025-02-27 at 10.10.00 PM.png" width="500px"/>


### Part 2: Bezier surfaces with separable 1D de Casteljau

We can extend the de Casteljau algorithm to Bezier surfaces as well. First, we need to evaluate the final points for all of the rows (one of the axes) by calling `evaluate1D` on each row. The `t` value for this axis is `u`. Then, we evaluate those points to get the final points for the column (the other axis). The `t` value for this axis is `v`. We call `evaluate1D` on the vector created by the calls to each row. Then, we call `evaluateStep` until there is only 1 remaining point. 

Here is our result of rendering teapot.bez with this step.

<img src="media/part2/Screenshot 2025-02-22 at 6.20.42 PM.png" width="500px"/>

## Section II: Triangle Meshes and Half-Edge Data Structure

### Part 3: Area-weighted vertex normals

We calculate the area-weighted normals of all of the neighboring triangles to the vertex. We then average this out and normalized the result. We iterated through all of the neighboring triangles using a loop. 

#### TODO: add more detail

| teapot.dae without Phong shading | teapot.dae with Phong shading |
| :----: | :----: |
| <img src="media/part3/Screenshot 2025-02-22 at 7.13.39 PM.png" width="500px"/> | <img src="media/part3/Screenshot 2025-02-22 at 7.05.06 PM.png" width="500px"/> |

### Part 4: Edge flip

We used the following resource from CMU provided in the half-edge primer heavily when implementing edge flip: http://15462.courses.cs.cmu.edu/fall2015content/misc/HalfedgeEdgeOpImplementationGuide.pdf. We particularly used the after flip image to define all of our halfedges, vertices, etc. in all of our calls to `setNeighbor` to ensure everything was correct in relation to each other.

We were first confused about how to implement this part without creating new elements. Using the diagram in resource above helped a lot in visualizing what each of halfedges needed to be reassigned for their next, twin, vertex, edge, and face values. It also helped provide a starting point for the different sections in our code for Parts 4 and 5.

Here is the original teapot image, following by the teapot image after flipping some edges. There are some cases where one triangle is a lot darker than the other after flipping, but these are degenerate cases we don't need to worry about (as per the spec).

<img src="media/part3/Screenshot 2025-02-22 at 7.13.39 PM.png" width="500px"/>
<img src="media/part4/Screenshot 2025-02-25 at 5.00.16 PM.png" width="500px"/>

### Part 5: Edge split

### Part 6: Loop subdivision for mesh upsampling