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

# CS184/284A Spring 2025 Homework 3 Write-Up
## by Ramya Chitturi and Kerrine Tai

Link to webpage: <a href="https://cal-cs184-student.github.io/hw-webpages-rk/index.html">https://cal-cs184-student.github.io/hw-webpages-rk/hw3/index.html</a>

Link to GitHub repository: <a href="https://github.com/cal-cs184-student/sp25-hw3-r-k-3">https://github.com/cal-cs184-student/sp25-hw3-r-k-3</a>

<img src="cornell.png" alt="Cornell Boxes with Bunnies" style="width:70%"/>

## Overview

In this homework, we explored rendering through ray tracing, bounding boxes, and illuminating scenes through different techniques. We first had to wrap our heads around converting from world space to camera space and vice versa to successfully implement ray object intersection, then accelerated the process through bounding boxes. Next, we learned how to render images with illumination, and finally accelerated that process through adaptive sampling. For each part, visualizing in our head was crutial, so we often referred back to lecture slides and recordings. We encountered many problems, mostly related so objects showing up but being completely black. We solved it by debugging the intersection and illumination functions. Some of the images are bit small, but you can open them up in a new tab to see them more clearly!

## Part 1: Ray Generation and Scene Intersection

For ray generation, we had to take a ray that originated from a camera and trace the ray until it intersected  the image plane at a specific point. In `generate_ray`, we first calculated the sensor's bottom left corner and top right corner given the equations in the spec. We then determined the exact point on the sensor plane the ray passes through and used this to create a direction vector. Then we converted this direction vecotr into the world space with the `c2w` to create the ray. For every single pixel, we use `ns_aa` and `gridSampler`to sample some rays per pixel. We generate the ray using `generate_ray` function from above, then got the radiance with `est_radiance_global_illumination`. 

The primitive intersection part was where we checked if a ray intersected any object in the scene.  We implemented ray-triangle and ray-sphere intersection. For the ray-triangle intersection, we used Moller Trumbore algorithm, which barycentric coordinates (like previous homeworks!). We first calculated the edge vectors for the triangle and determinates we needed for the barycentric coordinates.We calculatd the barycentric coordinates and where along the ray the intersection happens. Of course, we need to check if the intersection is even valid, and we do this by checking for valid barycentric coordinate range and that the point is within the valid part of the ray. 

Here are some images with normal shading:

<img src="media/part1/banana.png" width="300px"/>
<img src="media/part1/CBspheres.png" width="300px"/>
<img src="media/part1/coil.png" width="300px"/>


## Part 2: Bounding Volume Hierarchy

For our BVH construction algorithm, since it is recursive, we made a base case where we stop when the number of primitives in the current range is less than or equal to the `max_leaf_size`. This is where we create the leaf node that stores the primitives. Otherwise, we compute the bounding box. WE then loop through the primitives to create 2 `BBox`, one for the ray intersections and one for the centroids. The splitting axis was choosen with a helper function we wrote `largestDimention`. For our heuristic, we decided to use the average centroid position along the axis that we chose. We thought it was intiutive to just use the average when splitting things in two. Once we split the primitives, we recursivly called the function on the left and right subtree. 

Here are some images that we could only render with BVH acceleration: 

<img src="media/part2/lucy.png" width="300px"/>
<img src="media/part2/maxplanck.png" width="300px"/>

BVH significantly sped up the time it took to render images, and it showed the most in the more complex geometries. For example, it took maxplanck 282.98 seconds to render without BVH, and 1.974s with. It took lucy 588.12s to render without BVH, and 1.915s with. Even for moderately complicated geometries, the differences showed. It took 21.289 seconds to render the cow without BVH, and 0.436 seconds with. Conceptually, if we do not use BVH, rendering is linear to the number of primities, but since we divide the primitives by around half each recursive level in BVH, rendering is logarithmic. 

## Part 3: Direct Illumination

For the hemiphere sampling, we sample at num_sample points uniformly over the hemisphere. For each sample, we use `hemisphereSampler` to get a random sampled direction, then use it to create a shadow ray and see if the ray intersects something with `bbh->intersect`. It if does hit an object, we are able to get the emission with the bsdf. We then use the values we collected per sample to solve for the reflection equation given in lecture. Finally, we averae all the rays we sampled to get the illumination. 

Direct lighting by imporance sampling lights was a little more difficult because this was not an uniform sampling. We looped through every light scource in the scene. If the light source is a delta light, we saved time by sampling it only once because all samples from a point light will be the same. Otherwise, we sample the light num_samples time. Each time, this was similar to the process in hemisphere sampling where we get the create the shadow ray, see if it intersects, and solve for the reflection equation. WE then average over all the samples for the specific light, then add together the radiance for all the lights in the scene. 

Here are some images with both implementations of the direct lighting function. 

| Hemisphere sampling | Importance sampling |
| :----: | :----: |
| <img src="media/part3/CBbunny_hemisphere_64_32.png" width="500px"/> | <img src="media/part3/CBbunny_importance_64_32_1.png" width="500px"/> |
| <img src="media/part3/sphere_hemi_64_32.png" width="500px"/> | <img src="media/part3/sphere_importance_64_32.png" width="500px"/> |

Scenes with -l 1, 4, 16, 64 in order. 

<img src="media/part3/spheres_importance_s1_l1.png" width="500px"/>
<img src="media/part3/spheres_importance_s1_l4.png" width="500px"/>
<img src="media/part3/spheres_importance_s1_l16.png" width="500px"/>
<img src="media/part3/spheres_importance_s1_l64.png" width="500px"/>

The main difference between uniform hemisphere sampling and lighting sampling reflected in the images is from the graniness of the images. For hemisphere sampling, the renderd images are more grainy and noisy. With hemisphere sampling, althought it samples many parts of the image, many samples are useless. However, with imporance sampling, the image is a lot more smooth. This is because imporance sampling samples actual light sources which makes the samples more useful to determing the actual lighting of the image. 



