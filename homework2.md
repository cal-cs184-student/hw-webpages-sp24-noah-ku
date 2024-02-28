---
layout: page
title: Homework 2
description: >-
  Course policies and information.
---

# Homework 2
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}

---
## Overview
This is our second homework which focuses on curves and surfaces. We apply the techniques we learned from lecture and use them to edit our 3D models like the teapot and modified cube.

## Task 1: Bezier Curves with 1D de Casteljau Subdivision

The de Casteljau Subdivision Algorithm is a way to smooth out jagged connected lines and create Bézier curves. We can use the example of three control points in space, with two lines connecting
them all together. When we look at the first line, we can use a parameter value t to pick our starting point. We put a new point t units away from the first control point and also add a new point
t units away from the second control point. Afterwards, we connect those two newly added points together to get one step closer to the final curve. This action gets recursively called to create a final
Bézier curve that is very smooth.

To implement this, we had to modify the `BezierCurve::evaluateStep()` function to create our Bézier curves in the program. First, we used the function `(1 - t) * (p of i) + t * (p of i+1)` to calculate the specific lerp
or new step in the function. To integrate this into our function, all we had to do was use a for loop that goes from 0 to the length of the control points - 1 (to not get an out-of-bounds error with the i+1), initialize
an array of Vector2Ds, and use the equation given to us to append to that array. Since this function was relatively simple to implement, we didn't run into too many errors when compiling our code.

<br>

*CS 184 Lecture Slide that Demonstrates the de Casteljau Algorithm.*
![Task 1 Caseljau Slide](./assets/images/hw2/casteljau.jpeg)

*Default curve1.bzc with 4 control points (white), toggled steps (blue), and a fully evaluated curve (green)*
![Task 1 Default Curve1](./assets/images/hw2/task1default.png)

*New bzc with 6 points*
![Task 1 New Curve](./assets/images/hw2/task1step1.png)

*New bzc with 6 points, showing 1 step*
![Task 1 New Curve 1](./assets/images/hw2/task1step2.png)

*New bzc with 6 points, showing 2 steps*
![Task 1 New Curve 2](./assets/images/hw2/task1step3.png)

*New bzc with 6 points, showing 3 steps*
![Task 1 New Curve 3](./assets/images/hw2/task1step4.png)

*New bzc with 6 points, showing 4 steps*
![Task 1 New Curve 4](./assets/images/hw2/task1step5.png)

*New bzc with 6 points, showing 4 steps with final Bézier curve*
![Task 1 New Curve 5](./assets/images/hw2/task1step6.png)

*Modified the control point positions*
![Task 1 New Curve 6](./assets/images/hw2/task1step7.png)

*Modified the control point positions, sliding the t value*
![Task 1 New Curve 7](./assets/images/hw2/task1step8.png)


## Task 2: Bezier Surfaces with Separable 1D de Casteljau

For this task, we have to apply the same techniques from the previous task to make the de Casteljau algorithm work for surfaces. To this, we have to modify three functions in our code:
`BezierPatch::evaluateStep()`, `BezierPatch::evaluate1D()`, and `BezierPatch::evaluate()` The `evaluate()` function will ultimately be using the other two to create Bézier surfaces instead
of just curves like the lines from earlier. We will be using a technique called separable 1D for this task. Previously, we were evaluating basic Bézier curves, but with the help of these three functions, we
will be able to fully evaluate Bézier patches and use them to render 3D models like the teapot. Given any u and v parameters, we will be able to create a "moving curve" with the Separable 1D technique.

The `evaluateStep()` function is very similar to the way we implemented step evaluation in the first task. First, we create an array that will store all the points, append the newly found result
to that array, and then return the new whole set of points. The only difference between the `BezierPatch::evaluateStep()` here and `BezierCurve::evaluateStep()` is that the Bézier Patch function
stores all points in a Vector3D format. This `evaluateStep()` function will be called later on.

We also implemented `evaluate1D()`. This function is responsible for fully evaluating Casteljau's algorithm instead of calculating just one step. To do this, we can make a new deep copy of the `points` parameter,
evaluate the step with our set of points by calling our earlier function `evaluateStep()`, reassign our array/vector, and keep calling `evaluateStep()` until our array reaches a length of 1. The array will eventually
reach a length of 1 because the for loop in `evaluateStep()` only goes from 0 to the points size - 1. We originally found an infinite loop bug when we did not add the - 1 to the end of our for loop, but it started working
again after we added it in. Once we reach the end, we can return the first value of the array, which would be the final Vector3D point we reach after all calculations.

Finally, we had to implement `evaluate()`. This final function is in charge of using the controlPoints variable, which is an N x N 2D array filled with control points that we will use to create 3D models. First,
we had to create an array called `u_points` that would store all calculations of `evaluate1D()` when passing in the row `controlPoints[i]` and `u` value in our for loop. After completing the calculations for `u_points`, we
return the result of `evaluate1D(u_points, v)`. This way, we will be able to calculate the Bézier patch at any given parameter (u, v).

<br>

*CS 184 Lecture Slide that Demonstrates the Separable 1D Algorithm.*
![Task 2 Separable 1D Slide](./assets/images/hw2/task2slide1D.jpeg)

*Our Rendered Teapot*
![Task 2 Teapot](./assets/images/hw2/task2teapot.png)

*Our Rendered Wavy Cube*
![Task 2 Cube](./assets/images/hw2/task2cube.png)

## Task 3: Area-Weighted Vertex Normals

For this task, we are going to shade our models using Phong shading. This type of shading interpolates normal vectors across the triangles on a surface, and it creates a much smoother feel to the overall model. To do this,
we can use the half-edge data structure, weight its normal by the area, and normalize the sum of all normalized areas.

We had to modify `Vertex::normal()` to make this work. The function does not have any parameters passed in, but we can call `halfedge()` to get the corresponding half-edge to the vertex in the same class. We can use a do-while loop,
checking to see if the adjacent half-edges are not the same as our starting edge. We only stop once we've made a full loop.

To approximate the unit normals, we have to use three Vector3Ds. They include the current half-edge's position, the next half-edge's position, and the position of the next half-edge after that. Since the edges we traverse are all triangles,
these are the only variables we need. After getting these positions by calling a combination of `next()`, `vertex()`, and `->position`, we can call `cross()` to get the cross product between the Vector3Ds `next - current` and `nextnext - current`.
Finally, we append to our starting zeroed out Vector3D called `rv` ("return vector"). Once we have everything added together, we can get the normalized sum by calling `unit()` on our `rv` variable. Returning this value gives us the smoothed out
surfaces.

<br>

*Left: Before Normalizing; Right: After Normalizing*
![Task 3 Teapot Comparison](./assets/images/hw2/task3teapotcomparison.png)

## Task 4: Edge Flip

PLACEHOLDER

## Task 5: Edge Split

PLACEHOLDER

## Task 6: Loop Subdivision for Mesh Upsampling

PLACEHOLDER
