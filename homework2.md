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
an array of Vector2Ds, and use the equation given to us to append to that array.

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

PLACEHOLDER

## Task 3: Area-Weighted Vertex Normals

PLACEHOLDER

## Task 4: Edge Flip

PLACEHOLDER

## Task 5: Edge Split

PLACEHOLDER

## Task 6: Loop Subdivision for Mesh Upsampling

PLACEHOLDER
