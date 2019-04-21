## Introduction

In this Blog, I **Omar Garrido Martin**, student of the Master in Computer Vision from the university URJC, Madrid, Spain, will describe all the steps Ive gone through to reconstruct a 3d scene from a stereo pair of cameras in the [JdeRobot Academy Simulator](https://unibotics.org/main_page)

This is part of an exercise for the subject Robotic Vision. Here the setup of the simulator can be seen:

<img src="./resources/environment.PNG" alt="Foto entorno" />

The goal of the exercise is to reconstruct the scene seen by the pair of cameras in 3d with the best possible accuracy. Also as a second criteria the speed of reconstruction is taking into account.

## 1. Introduction to the problem and the environment

There are two cameras, one left and one right on the same plane. This cameras form a caconical stereo configuration. This means that both cameras are in the same plane with the same orientation and has some advantages over a normal stereo configuration that will be discussed later. Even the stereo configuration is canonical this exercise will be solved using the methods that you will generally used in a stereo configuration 3d reconstruction, no advantage of the canonical configuration will be used. So the techniques used could be used for any stereo pair.

The two cameras are calibrated, we have both the intrinsec and the extrinsics parameters:

<img src="./resources/instrinsic.PNG" alt="Intrinsic" />
<img src="./resources/extrinsic.PNG" alt="Extrinsic" />

With the intrinsic matrix and the extrinsic matrix the Projection matrix can be created (Mint x Mext).

This projection matrix allows to pass from a 3d point in the world coordinates to a 2d point in the image. But not in the inverse way 2d -> 3d. This is due to the linear system having more variables than equation. (Passing from 2D point to a 3D point, three variables but only two equations). To solve this problem and be able to reconstruct on 3D we need two cameras to add more equations to be able to solve the system.

By looking at the extrinsic parameters for each camera, it can be seen that the world coordinates is in the middle of the two cameras, one is 110 mm to the left and the other 110 mm to the right.

But since the simulator environment counts with some methods to project and reproject, the camera intrinsic and extrinsic parameters from the camera are not needed, since this methods will take care of all the calculus.

The methods are:

- **self.camLeftP.graficToOptical(point2d)** 
- **self.camLeftP.backproject(point2d)**
- **self.camLeftP.opticalToGrafic(point2d)** 
- **self.camLeftP.project(point3d)** 

There will be discussed later when needed on this document.

## 2. Reconstruction

### 2.1 Points to reconstruct

For simplicity and speed, instead of trying to reconstruct the entire scene, first Ill try to reconstruct only a certain part of the scene like the edges. Using canny algorithm Ill reconstruct only those points retrieve by the canny algorithm:

<img src="./resources/canny.PNG" alt="Canny" />

Using canny, all the points retrieved are edges, in edges there is texture, but if I try to reconstruct the entire scene pixel by pixel, some points may lack texture information (homogeneous areas), making it hard to find their correspondences in the other image, so to start lets use canny.

After this Ill loop over the entire image and check in the pixels within the canny algorithm that were positive (edges).

### 2.2 Reprojection
