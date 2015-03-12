---
author: gvivekcbe
comments: true
date: 2015-03-01 22:40:33+00:00
layout: post
title: Project 1 - Raytracer
---
Planning to do one project each week. Lets see how it goes.

## Raycaster:

A raycaster is a technique to show 3D surfaces on a 2d display.
Imagine a camera pointed to a block of walls. 
The 3d view of camera is projected on a 2d screen. So a closer block of wall appears taller than the one behind. In otherwords, the length of the camera ray determines the height of the block.

##Math:

To convert degree to radians:
radians = degree * (pi / 180)



##Components:


###map.js:

Displays the mini-map. It has a grid array where 1 represents wall.The draw method shows the mini-map.

	height on screen / distance from screen = wall height / length of ray
	tan(fov/2) = (width/2)/distance from screen

### camera.js:

Controls the camera on the mini-map. project method has the logic of projecting the map

## project method pseudocode:

* Iterate through each pixel of the canvas along x direction(width) and call castRay.
* castRay shoots a ray at an angle. The starting angle would be currentAngle - fov / 2. castray returns length of ray
* calculate height on screen by the math formula
* After each ray is cast, the angle of the ray has to be incremented by fov/canvas width.
* calculate y position which would be beginning of the slice.
* draw a rectangle starting at x and y, with width 1 and height = height on screen.
* walls on back has to be shadowed.

## castRay pseudoCode:

casts on single ray at a  given angle. The ray is drawn until it hits the wall. Returns the length of the ray till it hits a wall otherwise maxdistance. 

* First initialize the ray position to x and y of camera
* Now the ray has to be drawn to maxDistance pixels. For each pixel, increment x and y. x has to be incremented by the horizontal component which is given by the cos(angle) and y has to be incremented by the vertical component which is given by sin(angle)
* check if new x and y is hit by a wall.

## Fisheye distortion:

The border of the square rectangle walls look curved. This is because the ray length that falls on the edges of the wall is longer than the length of the ray that falls on the center. So the ray length is adjusted by a simple trignometric formula. 

	distance = distance * cos(angle)

Also added move methods to move around the map

Source: [Ray caster](https://github.com/excray/ray-caster.git)
