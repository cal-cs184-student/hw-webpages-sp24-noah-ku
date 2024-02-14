---
layout: page
title: Homework 1
description: >-
    Course policies and information.
---

# Homework 1
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Task 1: Drawing Single-Color Triangles
To rasterize triangles, we want to sample one point at the center of each pixel and color it based on the given inputs to make our drawings.

For this task, we implemented the `rasterize_triangle()` function in `rasterizer.cpp` using a basic sampling method by taking the middle point of each pixel. At first, we
decided to loop through the entire width and height of the canvas to check if it's in bounds. We originally wanted to make our own function to check if a certain x y coordinate
was in a triangle, but we realized that we could call the `inside()` function that takes care of it for us. This seemed like a simple task. All we had to do was loop through the entire canvas,
sample the svg file at row i + 0.5, column j + 0.5, and fill that pixel with the sampled color.

However, we ran into some problems. The first problem was that there were some white lines generated during our first run. After some debugging, we realized that this was because of
how we made the for loop end at width - 1 and height - 1 when we didn't need to subtract one from both dimensions. It was an error in our thinking because we assumed the +0.5 would
go out of bounds, but the for loop already accounts for it if we use less than instead of less than or equals to. Additionally, we also had some images where only half sof each shape was rendered
correctly. This was because we forgot to account for the right hand rule, and all we had to do was add in another inside check in our if statement. Finally, we fixed the speed by making sure we loop in the right place.
We were originally looping through the entire screen, but we noticed that it's faster to loop around the bounding box of the triangle. We do this by taking the min of all x points, min of all y points,
max of all x points, and max of all y points to get our corresponding box dimensions. This sped up the process and made our images generate much faster than before.

<br>

*The Original Failure (No Right Hand Rule):*
![Task 1 Red Blue Fail](./assets/images/hw1/task1-redblue-fail.png)

*After Adding in the Right Hand Rule:*
![Task 1 Red Blue Good](./assets/images/hw1/task1-redblue-good.png)

*The Cube's Edges Using Basic Sampling:*
![Task 1 Cube](./assets/images/hw1/task1-cube.png)

*The test4.svg File Working:*
![Task 1 Triangles](./assets/images/hw1/task1-triangles.png)

## Task 2: Antialiasing by Supersampling

To supersample, we have to take more points inside each pixel and average them out. The basic sampling rate from Task 1 could be likened to a supersample rate of 1, but now we want to increase it based on
what the user wants. For example, if the supersample rate was 4, that means that we "divide" the pixel into 4, take the center of each of those divisions, and then average out the 4 colors. This
averaged color will be used to fill the entire pixel, giving it an antialiasing effect. Supersampling is important and useful because it can make jagged corners look more smooth and refined while also reducing
artifacts in our image.

For this task, we had to modify `rasterize_triangle()` and `fill_pixel()`. However, before changing those functions in order to calculate the colors, we needed to be able to store the points correctly.
First, we had to change the size of the sample_buffer depending on what supersampling rate we were using. We modified `set_framebuffer_target()` to correctly calculate the new size and adjust whenever needed.
Instead of just using `width * height`, we now want to use `width * height * sample_rate`. This indexing format meant that if the supersample rate was 4, each pixel would take up 4 indices. For example, pixel #1
would originally take the color at index 0. However, now pixel #1 would take the colors at index 0, 1, 2, and 3. The next pixel (#2) would take the colors 4, 5, 6, and 7.

After figuring out how to store the colors, we also needed a way to know which coordinates to sample and how we can access them later. We used the line `step_size = 1.0f / sqrt(get_sample_rate())` to calculate
our step size correctly, and we also figured out where to stop by using the line `max_sample_rate = sqrt(get_sample_rate())`. Our nested loops now contained 4 for loops, and we also modified `resolve_to_framebuffer`
to correctly retrieve the colors. To average the colors, we sum all colors up then divide each R, G, and B component by the sampling rate. For `fill_pixel()`, we also modified the parameters to take in a boolean called
`isPoint`. If `isPoint` is true, then we fill all indices related to that pixel based on the supersample rate to be that exact color. It will not be averaged with the surrounding colors if it's a point.

We ran into a few problems calculating the correct index, causing some weird behaviors like dotted patterns. Additionally, we would crash consistently because we did not resize our canvas properly. After some debugging, we were able to correctly sample the
colors and store in the `frame_buffer` while adjusting it when needed. This led to a working antialiasing effect for all our svg files.

<br>

*Our Notes:*
![Task 2 Notes](./assets/images/hw1/task2-notes.png)

*Clearer Flower, Dots Are Not Directly Supersampled*
![Task 2 Flower](./assets/images/hw1/task2-flower.png)

*Antialiasing Effects on the Cube*
![Task 2 Antialiased Cube](./assets/images/hw1/task2-antialiased-cube.png)

*Supersampling at Rates 1, 4, 9, and 16:*
![Task 2 Sample 1](./assets/images/hw1/task2-super-1.png)
![Task 2 Sample 4](./assets/images/hw1/task2-super-4.png)
![Task 2 Sample 9](./assets/images/hw1/task2-super-9.png)
![Task 2 Sample 16](./assets/images/hw1/task2-super-16.png)

## Task 3: Transforms

For Task 3, we had to modify the `transforms.cpp` file and change `translate()`, `scale()`, and `rotate()`. These three functions
were relatively simple to change. For translation, we start with a matrix filled with all 0's except for 1's filled diagonally from top left
to bottom right. Afterward, we put dx and dy in the right column to make translations work correctly. Next, we modified scale to be similar.
We also want to start with a matrix filled with 0's, but going diagonally from top left to bottom right, we would put dx, dy, and then finally a 1.
Lastly, for rotations, we had to put in a matrix filled with 0's. However, for the top left 4 elements of the matrix, we would put
cos(deg), -sin(deg), sin(deg), and cos(deg). Finally, we put a 1 in the bottom right corner of the matrix.

We originally ran into a bug where the sin and cos functions imported from the Math library would take in parameters as radians. To fix this,
we multiplied our input `deg` by `M_PI` (3.1415) and divided it by 180. Afterward, our robot was aligned correctly.

Our modified robot is rotated and transformed to look like it is jumping. We also modified the colors so that the robot looks more human and has
a blue shirt and navy blue jeans.

*The Default Red Robot:*
![Task 3 Robot](./assets/images/hw1/task3-robot.png)

*Our Modified Robot:*
![Task 3 My Robot](./assets/images/hw1/task3-my-robot.png)

## Task 4: Barycentric coordinates

PLACEHOLDER TEXT

<br>

*The Color Wheel:*
![Task 4 Color Wheel](./assets/images/hw1/task4-color-wheel.png)

## Task 5: "Pixel sampling" for texture mapping

key is clarity and succinctness
1. approach to the problem
2. implementation of each part
3. problems encountered and how it was solved


	- RasterizerImp::rasterize_textured_triangle
	approach: Explain pixel sampling in your own words and describe how you implemented
	it to perform texture mapping. Briefly discuss the two different pixel sampling methods, nearest and bilinear.
	Similar to previous tasks, we noticed that we have to After found coodrinates we noticed that we have to pass that value to
	sample_nearest and sample_bilinear

	implementation: We get smallest/largest x and y out of all x and y and see if those x and y are in the triangle using inside function and
	if it is we get barycentric coordinates and get uv 2D vector. Because we want the level to be 0, we will just pass in uv 2D vector
	and 0 in sample_nearest and sample_bilinear fucntion depends on psm.

	Issues: I encountered when to use sample_nearst and sample_bilinear but turns out we just needed to see whether if psm is P_LINEAR or P_NEAREST.

	- Texture::sample_nearest
	approach: I noticed that there are two parameter and one of the hint was using get_texel function. our initial approach was
	first to get tx and ty and using get_texel, get the color.

	implementation: using uv, we get x and y by multiplying uv.x*(width - 1) and uv.y*(height - 1). Because uv coordinates
	are normalized, we need to subtract one from width and height when multiplying it.

	Issues: None

	- Texture::sample_bilinear
	dinates for x,y,z and apply on u and v variables. Als and since we knew
	what
Check out the svg files in the svg/texmap/ directory. Use the pixel inspector
to find a good example of where bilinear sampling clearly defeats nearest sampling.
Show and compare four png screenshots using nearest sampling at 1 sample per pixel,
nearest sampling at 16 samples per pixel, bilinear sampling at 1 sample per pixel,
and bilinear sampling at 16 samples per pixel.


Comment on the relative differences. Discuss when there will be a large
difference between the two methods and why.

		Task 5:

*Campanile, Sample Rate 1, Nearest Neighbor*
![Task 5](./assets/images/hw1/task5-1-near.png)

*Campanile, Sample Rate 16, Nearest Neighbor*
![Task 5](./assets/images/hw1/task5-16-near.png)

*Campanile, Sample Rate 1, Bilinear*
![Task 5](./assets/images/hw1/task5-1-bilinear.png)

*Campanile, Sample Rate 16, Bilinear*
![Task 5](./assets/images/hw1/task5-16-bilinear.png)

## Task 6: "Level sampling" with mipmaps for texture mapping

PLACEHOLDER


