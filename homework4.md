---
layout: page
title: Homework 4
description: >-
  Course policies and information.
---

# Homework 4
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
   {:toc}

---

## Part 1: Masses and Springs

In our `buildGrid` function, we have two nested for-loops that build the point masses. We iterate over `num_height_points` * `num_width_points`, setting the position of each point based on its indices. For instance, in the case of a `HORIZONTAL` orientation, we set the x and z positions, and y is set to 1. Otherwise, we set x, y, and make z a random value within a specific range. We also check if the point is pinned; if it is, we initialize it by setting `is_pinned` to true.

Afterward, we connect the points with springs based on their types: Structural, Shearing, and Bending. Structural springs connect points to their left and above, creating the cloth's basic structure. Shearing springs connect diagonally to oppose shearing forces, and Bending springs are used for enhancing the stiffness of the points by skipping one point in either the horizontal or vertical direction to simulate bending resistance.

*Images of springs*

![pinned2.json](./assets/images/hw4/part1.png)

![Structural and bending springs](./assets/images/hw4/part1str/bend.png)

![Shearing springs](./assets/images/hw4/part1shear.png)

## Part 2: Simulation via Numerical Integration

In the `simulate` function, we implement a simulation step that updates the position of the cloth's point masses based on external acceleration (such as gravity). The first thing we did was calculate the mass and time step based on width, height, density from `ClothParameters`, and `delta_t`. Then we compute the total external forces. Then we compute the spring forces exerted based on current length, `ks`, and rest length. Then, using Verlet integration, we need to update the position if a point is not pinned. This allows the point to move based on damping as well. Lastly, we apply a constraint on spring length change to prevent it from stretching or compressing too quickly.

### Spring Constant, ks
Here we are going to test for different `ks` values to see how does it make the cloth look different. As we increase the spring constant `ks`, we can observe that the cloth becomes more stiff, and with lower `ks` we can see that it is more stretched out.

*From ks = 50000 to ks = 50*

![ks 50000](./assets/images/hw4/part2ks50000.png)

![ks 5000](./assets/images/hw4/part2ks5000.png)

![ks 500](./assets/images/hw4/part2ks500.png)

![ks 50](./assets/images/hw4/part2ks50.png)

### Density
Unlike spring constant, density is an attribute for point masses. As we increase the density, the effect is opposite of ks. As we increase, we see that the cloth is more stretched, and with lower density, we see a stiff cloth. And this is because of the force exerted on the point proportional to its mass.

*From density = 1500 to density = 1.5*

![density 1500](./assets/images/hw4/density1500.png)

![density 150](./assets/images/hw4/part2density150.png)

![density 15](./assets/images/hw4/part2density15.png)

![density 1.5](./assets/images/hw4/part2density1point5.png)

### Damping
Damping is an interesting factor. It affects how quickly the cloth comes to rest after being played with. With lower damping, we observe that the cloth will not stop moving for a long period of time, whereas with higher damping, we noticed that the cloth comes to rest much more quickly.

*From damping = 1 to density = 0*

![damping 1](./assets/images/hw4/part2daming10.png)

![damping .5](./assets/images/hw4/part2daming5.png)

![damping .2](./assets/images/hw4/part2daming2.png)

![damping 0](./assets/images/hw4/part2daming0.png)

### Default state of pinned 2 and pinned 4

![default pinned 2](./assets/images/hw4/part2default2.png)
![default pinned 4](./assets/images/hw4/part2default1.png)

## Part 3: Handling Collisions with Other Objects

To make the cloth interact with another object, we needed to exploit the vector definition of cloth that touches a sphere and plane. In both `Plane::collide` and `Sphere::collide` functions, we are calculating based on the current vector and the point that is touching either plane or sphere. Then we calculate using `normal` and find the tangent vector and apply the new vector.

Here are images of different `ks` values for cloth falling on a sphere. And we can observe that with higher `ks` the stiffer and more resistant to deformation, and with lower `ks` we can observe that the points of cloth are closer to the sphere and look more relaxing. This is happening because with higher `ks` it is holding the points together, makes less bendy, and lower `ks` that is less force in holding together.

*Images of ks falling on sphere ks = 50000 to ks = 500*

![ks 50000](./assets/images/hw4/part3ks50000.png)

![ks 5000](./assets/images/hw4/part3ks5000.png)

![ks 500](./assets/images/hw4/part3ks500.png)

## Part 4: Handling Self-Collisions

In order to perform self-collisions, we first need to build a spatial map which is done in `build_spatial_map`. Here we iterate through `point_masses` and push back the point to the corresponding hash value. and by hash value we also implement a function called `hash_position` that allows us to have a unique value for each position. Then we implemented a function called `self_collide` where we get the point and see if that point is in the `map` and if it is we change its position appropriately.

*Images of self-colliding cloth falling on plane*

![Cloth](./assets/images/hw4/part41.png)

![Cloth](./assets/images/hw4/part42.png)

![Cloth](./assets/images/hw4/part43.png)

![Cloth](./assets/images/hw4/part44.png)

![Cloth](./assets/images/hw4/part45.png)

*Images with different densities 150 to 1.5*

Density affects how the cloth moves under gravity. With higher density, cloth has more mass which makes it fall faster and fewer spaces when folded. On the other hand, with lower density, it is lighter which means it should have more fluttery behavior and more spaces when folded.

![density 150](./assets/images/hw4/part4150.png)

![density 15](./assets/images/hw4/part415.png)

![density 1.5](./assets/images/hw4/part41point5.png)

Spring constant (ks) determines the stiffness of the cloth. With higher `ks` the cloth should be more resistant to bending and folding which makes less folds. With lower `ks` it allows more flexibility and complexity in how the cloth falls and settles.

*Images with different ks 50000 to 500*

![ks 50000](./assets/images/hw4/part4ks50000.png)

![ks 5000](./assets/images/hw4/part4ks5000.png)

![ks 500](./assets/images/hw4/part4ks500.png)
