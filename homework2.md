---
layout: page
title: Homework 2
description: >-
  Course policies and information.
---

# Graphic Renderer
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}

---
## Overview
This is project focuses on curves and surfaces.

## Bezier Curves with 1D de Casteljau Subdivision

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


## Bezier Surfaces with Separable 1D de Casteljau

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

## Area-Weighted Vertex Normals

For this task, we are going to shade our models using Phong shading. This type of shading interpolates normal vectors across the triangles on a surface, and it creates a much smoother feel to the overall model. To do this,
we can use the half-edge data structure, weight its normal by the area, and normalize the sum of all normalized areas.

We had to modify `Vertex::normal()` to make this work. The function does not have any parameters passed in, but we can call `halfedge()` to get the corresponding half-edge to the vertex in the same class. We can use a do-while loop,
checking to see if the adjacent half-edges we moved to are not the same as our starting edge. We only stop once we've made a full loop and reach the same starting half-edge.

To approximate the unit normals, we have to use three Vector3Ds. They include the current half-edge's position, the next half-edge's position, and the position of the next half-edge after that. Since the edges we traverse are all triangles,
these are the only variables we need. After getting these positions by calling a combination of `next()`, `vertex()`, and `->position`, we can call `cross()` to get the cross product between the Vector3Ds `next - current` and `nextnext - current`.
Finally, we append to our starting zeroed out Vector3D called `rv` ("return vector"). Once we have everything added together, we can get the normalized sum by calling `unit()` on our `rv` variable. Returning this value gives us the smoothed out
surfaces.

<br>

*Left: Before Normalizing; Right: After Normalizing*
![Task 3 Teapot Comparison](./assets/images/hw2/task3teapotcomparison.png)

*Version Without Lines*
![Task 3 Teapot White](./assets/images/hw2/task3teapotwhite.png)

*Normalizing a Cow*
![Task 3 Cow](./assets/images/hw2/task3cow.png)

## Edge Flip

To implement edge flipping, we had to edit `HalfedgeMesh::flipEdge()`. This function acts as a local operation that flips the half-edge that is connected to two vertices into the other two adjacent vertices. In other words, given triangles with points
(a, b, c) and (c, b, d), we will flip the edge of the triangle to be perpendicular and make them (a, d, c) and (a, b, d).

We first noticed that we have an `EdgeIter` passed in, and we also need to return an `EdgeIter`. The first check is to see if it's a boundary. If it is, then we can automatically return the input and leave it un-flipped. Next, we made a long list of variables
we would use, such as `h0, h1, h0_next, h1_next...`, `v0, v1...`, and also `f0, f1...`. With these variables that we accessed from the input `e0`, we would be able to flip any edge correctly. The first operation is to change the neighbors that we had for the all the half-edges.
Each half-edge's new next would be its next's next. For example, `h0` becomes `h0_next_next`. `h0_next` becomes `h0` because `h0` is `h0_next`'s next_next. This process repeats for `h1` as well and all its neighboring half-edges. After we have these values set up, we can call
`setNeighbor()` on all the points. We pass in the vertices off by one (v of n becomes v of n-1) and also pass in the corresponding edges. This results in a new flipped half-edge, and we can return `e0`.

We encountered a few bugs when implementing this function. First, we did not realize that we had to set the edge or face for halfEdgeIter, so the faces would sometimes disappear and not flip correctly. We figured out how to fix this by further reading the documentation
and using `setNeighbor()` instead, giving us the option to ensure that all variables are initialized correctly before each function call. Our way to debug was to comment out various chunks of code, use `check_for()`, pinpoint the place where it began to error, and work from there. It was
also extremely helpful to draw out our thought process on an online notepad that both of us used.

<br>

*Flipping Center Half-Edges*
![Task 4 Flipped](./assets/images/hw2/task4flipped.png)

*Our Drawings for Task 4*
![Task 4 Notes](./assets/images/hw2/task4notes.png)

## Edge Split

For edge splitting, we had to change the function `HalfedgeMesh::splitEdge()`. This is similar to task 4 since we also have to perform operations on half-edges, but this task has a lot more moving parts and needs more variables to correctly split the edges. To split an edge, you have to look
at where the two triangles meet, then create another perpendicular split between those triangles to make 4 new triangles. The two split lines should meet at the middle point m.

First, we had to calculate where m is. We can get this by adding together the positions of vertices v0 and v1, then dividing the sum by 2. This gives us a new Vector3D which can be used to initialize `VertexIter m`. Next, we had to set the neighbors. This required a lot of planning and debugging, but our
thought process is mostly outlined in the diagram below. Afterwards, we had to call `setNeighbor()` on `h0, h1, h2, h3`, and add the respective vertices and new neighbors. Finally, we change the faces and each vertex's corresponding half-edge and return the resulting `m`.

We ran into a lot of segmentation faults when coding for this task. We originally thought it had something to do with our faulty `setNeighbor()` calls, but we realized it was related to not initializing properly. We caught this bug by also commenting out most code and using the CLion debugger to stop at the line
where it crashes. Later on, we also had a bug where the triangles would split in the correct orientation, but one of the faces would appear on top of the other. We fixed this by changing the face's vertices and other variables to make it start working.

<br>

*Splitting Half-Edges*
![Task 5 Split](./assets/images/hw2/task5split.png)

*Splitting and Flipping Half-Edges*
![Task 5 Split](./assets/images/hw2/task5splitflipcombo.png)

*Our Drawings for Task 5*
![Task 5 Split](./assets/images/hw2/task5notes.png)

## Loop Subdivision for Mesh Upsampling

We implemented `MeshResampler::upsample`, which involves looping using `HalfedgeIter` and calculating vertex and edge points. I basically followed the instructions in the skeleton code and specs. For calculating all vertices in the input mesh, I first have a for loop that iterates through all the meshes, and for each iteration, I did a calculation based on the number of degrees of the vertex, stored the result, and set `isNew` to false. Then, I calculated a new vertex using each edge's vertices' positions and set that as the new position of the edge and set `isNew` to false. For splitting edges, I put all the edges into a vector, iterated through the vector, and split if the edges were original, and the vertices that were created using `splitEdge` were set as new vertices. For `flipEdge`, we want to flip if and only if the edge is new and only one of the vertices in the edge is new. At the end, I set the position to the new position for all vertices that are `isNew` false. For debugging tricks, I took note of each vertex's address to try to identify which one is which. Also, actually looking at the 3D model and understanding why shapes are in certain positions really helped as well.
I was struggling with which one to set `isNew` to false and it resulted in a very uneven-looking shape, but fortunately, I was able to find which one to set to true by identifying which was created.

<br>

*Edge of Cube*
![Task 6 Before Subdivison Cube](./assets/images/hw2/task6beforesubcube.png)

*Edge of Cube After 1 Subdivison*
![Task 6 After Subdivision Cube](./assets/images/hw2/task6aftersubcube.png)

*Edge of Cube After 2 Subdivisons*
![Task 6 After Subdivision Cube](./assets/images/hw2/task6subdiv2.png)

*Edge of Cube After 3 Subdivisons*
![Task 6 After Subdivision Cube](./assets/images/hw2/task6subdiv3.png)

*Edge of Cube After 4 Subdivisons*
![Task 6 After Subdivision Cube](./assets/images/hw2/task6subdiv4.png)

For this picture, I noticed that some of the edges and corners are getting smoother and less sharp. This can be improved by pre-splitting some edges, increasing the mesh's resolution for sharp parts which makes smoothing preserve more of the original shape.


<br>

*Cube Before Pre-Processing With Splits*
![Task 6 before pre-process subdivisoned Cube](./assets/images/hw2/task6beforepreprocesssubcube.png)

*Cube After Pre-Processing With Splits*
![Task 6 before pre-process subdivisoned Cube](./assets/images/hw2/task6afterpreprocesssubcube.png)

It is possible to pre-process the cube by splitting edges before pressing `L`. The reason why it was asymmetrical is that each mesh of the cube was uneven and if it is uneven, the subdivision will create more meshes based on an uneven mesh resulting in an asymmetrical shape. By making each edge look like `X`, it made each mesh uniform, thus maintaining symmetry after subdivision.

<br>

*Bean Upsample 0*
![Task 6 upsample 0 bean](./assets/images/hw2/task6bean1.png)
*Bean Upsample 1*
![Task 6 upsample 1 bean](./assets/images/hw2/task6bean2.png)
*Bean Upsample 2*
![Task 6 upsample 2 bean](./assets/images/hw2/task6bean3.png)

<br>

*Beast Upsample 0*
<br>
![Task 6 upsample 0 beast](./assets/images/hw2/task6beast1.png)

<br>

*Beast Upsample 1*
<br>
![Task 6 upsample 1 beast](./assets/images/hw2/task6beast2.png)

<br>

*Beast Upsample 2*
<br>
![Task 6 upsample 2 beast](./assets/images/hw2/task6beast3.png)

