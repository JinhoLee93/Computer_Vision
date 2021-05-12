# Photometric Stereo

Written by Jinho Lee (jl5027@columbia.edu)

[Photometric Stereo](https://en.wikipedia.org/wiki/Photometric_stereo) is a very essential computer vision technique for the computer to recognize the shapes of objects.

The information we need to gather to conduct photometric stereo is 
- An object with a [Lambertian surface](https://en.wikipedia.org/wiki/Lambertian_reflectance) since lights reflected on Lambertian surfaces have the same brightness all over the surfaces and they only differ in magnitude based on their directions. Illustrated below is the typical behavior of lights on Lambertian surfaces. <img width="1089" alt="Screen Shot 2021-05-12 at 5 40 38 PM" src="https://user-images.githubusercontent.com/60580427/117945352-34844400-b349-11eb-8de6-e645a3682309.png"> (Provided by [Professor Nayar](http://www.cs.columbia.edu/~nayar/))

- Pictures of the object with lights from different directions shining on it
- Properties of the object
- Correct light directions
- Surface normals

Let's say we want to recognize this sphere below.
- <img src="https://user-images.githubusercontent.com/60580427/117942486-1537e780-b346-11eb-9a55-763a06b76d3e.png" width="20%" height="20%">

First, we need to translate the 3D information of the sphere into the computer's 2D world. Then, how do we adjust any 3D information into 2D?
- To begin with, the computer needs to be able to detect the sphere. 
- I'm using [im2bw function](https://www.mathworks.com/help/images/ref/im2bw.html) from MATLAB to render it easier to get the object properties in [findSphere.m](https://github.com/JinhoLee93/Computer_Vision/blob/main/photometric_stereo/findSphere.m).

Now we have all the information we need from the sphere. The computer is able to detect it! The next step should be gathering information from light directions. 
- As we can see from the picture of the sphere above, the brightness 