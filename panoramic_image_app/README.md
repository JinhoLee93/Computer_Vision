# Panoramic Image App

**Project created by [Professor Shree K. Nayar](http://www.cs.columbia.edu/~nayar/)**

Written by Jinho Lee (jl5027@columbia.edu)

Think of this app as the iPhone panoramic camera (but in lower quality and not live. Good job, Apple!)!

The app combines three images in order to make one panoramic image. This project is so cool in three major aspects:
- As long as there are enough number of shared features that can be detected by [SIFT detector](https://en.wikipedia.org/wiki/Scale-invariant_feature_transform), you can make your own panoramic photo with this app!
- Should angles in which you take the pictures matter? No! This app is smart enough to match all the images in the same angle using [Affine Transformation](https://en.wikipedia.org/wiki/Affine_transformation).
- But what happens if there's inconsistent lighting in each picture? Will that affect the productivity of this app? No! The app will blend the images so that it produces one complete panoramic image!

Before we jump into making a panoramic image, there are a few prerequisite steps to prepare for.
- To detect the same features shared in the pictures in different angle with SIFT detector, we should come up with a way to compute [homography](https://en.wikipedia.org/wiki/Homography) between two pictures, one as the center image and the other for either the left or the right image. 
  - [computeHomography.m](https://github.com/JinhoLee93/Computer_Vision/blob/main/panoramic_image_app/computeHomography.m) calculates homography between the two.
    - Compute homography with simple matrix A. And A'* A becomes an eigenvector problem.
    - Return the computed homography
  - Let's see how well the homography computed works in a real problem using [applyHomography.m](https://github.com/JinhoLee93/Computer_Vision/blob/main/panoramic_image_app/applyHomography.m)!
    - Apply the homography to the destination image, in here, the differently angled image.
    - Since the homography is homogenous, let's turn that back to the image coordinate by dividing each entry with the last imaginary entry.
    - Let's test the homography with these two images! 
    - <img src="https://user-images.githubusercontent.com/60580427/117561631-612f2600-b0d3-11eb-8428-615f36a9b2e7.png" width="20%" height="20%"> <img src="https://user-images.githubusercontent.com/60580427/117561637-69876100-b0d3-11eb-9df1-162d3eba8ba9.png" width="30%" height="30%">
    - And the result is
    - <img src="https://user-images.githubusercontent.com/60580427/117561683-e4e91280-b0d3-11eb-938d-10a6f6175825.png" width="50%" height="50%">
    - I think it works really well!
  
  - Okay. Now we know homograpy works really well in terms of matching features in different angles. However, only with applying homography, you can't match images in a certain angle. You need to [backward (reverse) warp](https://en.wikipedia.org/wiki/Image_warping) the left and right images to fit the center image.
    - All you need to do is change each pixel according to the computed homography above to warp images.
    - Don't forget to check the boundaries of the warped image so that the result gets all messed up!
    - Let's test it with some images below.
    - I want to backward warp this image <img src="https://user-images.githubusercontent.com/60580427/117561631-612f2600-b0d3-11eb-8428-615f36a9b2e7.png" width="20%" height="20%"> into the blank canvas in <img src="https://user-images.githubusercontent.com/60580427/117562029-cf291c80-b0d6-11eb-97d5-c6fbc040f482.png" width="30%" height="30%">
    - The result looks like
    - <img src="https://user-images.githubusercontent.com/60580427/117562057-ff70bb00-b0d6-11eb-926c-42ab493aa8b1.png" width="30%" height="30%">
    - You're barely able to see the canvas because they match so well!
   
  - The app is almost fully prepared to be complete! Now we are running SIFT detector to find as many shared features as possible. 
    - [genSIFTMatches](https://github.com/JinhoLee93/Computer_Vision/blob/main/panoramic_image_app/genSIFTMatches.m) was provided by generous [Professor Shree K. Nayar](http://www.cs.columbia.edu/~nayar/)
    - The result after SIFT detector is:
    - <img src="https://user-images.githubusercontent.com/60580427/117562247-68a4fe00-b0d8-11eb-9cec-ff66b767c99f.png" width="80%" height="80%">
    - Oh no! Do you see the problem? It's detecting way too many features and, due to that, it detects some useless features outside the overlapped part as well, which is a common problem with SIFT. We need [RANSAC](https://en.wikipedia.org/wiki/Random_sample_consensus) to pick the best matching features between pictures!
    - Get Euclidean distance between the original destination points and those created by the homography and find the distance in which most features fall in. 
    - Will my RANSAC work properly? Let's see!
    - <img src="https://user-images.githubusercontent.com/60580427/117562530-98550580-b0da-11eb-8711-6bd62e1a881a.png" width="80%" height="80%">
    - Yes, it does! 
  
  - Even though the pictures share some features, they are, in fact, different images which might vary in brightness. If the pictures aren't blended well when combined, the result will be quite dissapointing. Thus, the app should be able to blend some differences that exist in each picture. 
    - [blendImagePair.m](https://github.com/JinhoLee93/Computer_Vision/blob/main/panoramic_image_app/blendImagePair.m) blends pictures so that the results has the least incongruity that might be caused by putting two pictures with different brightness.
    - Weighted blend was used. 
    - Let's blend some pictures:
    - <img src="https://user-images.githubusercontent.com/60580427/117567769-a31e9300-b0f8-11eb-898e-abb21c86af6c.png" width="50%" height="50%">
    - <img src="https://user-images.githubusercontent.com/60580427/117567771-a6198380-b0f8-11eb-8eb1-a5b00caa5a7d.png" width="50%" height="50%">
    - The result is:
    - <img src="https://user-images.githubusercontent.com/60580427/117567808-d9f4a900-b0f8-11eb-8438-3aa81d0d54fd.png" width="50%" height="50%">
    - Quite impressive!

Ok. So far, we've covered homography, backward warping, SIFT, RANSAC and blending. It's time to put everything together to complete the app. 
- If put together, everything becomes [stitchImg.m](https://github.com/JinhoLee93/Computer_Vision/blob/main/panoramic_image_app/stitchImg.m)
- Since a panoramic image has different size than the original pictures, you must first change edges according to the direction of the pictures. For example, if we're combining the left and the center pictures the edges of the result must be expanded from x = 0 to -.
- Repeat the same process as we did above.
- The pictures I'm making panoramic are:
- <img src="https://user-images.githubusercontent.com/60580427/117568380-df52f300-b0fa-11eb-97bc-2c8286cdd78e.png" width="50%" height="50%"> left
- <img src="https://user-images.githubusercontent.com/60580427/117568375-dbbf6c00-b0fa-11eb-8cfd-b52d3f2b0602.png" width="50%" height="50%"> center
- <img src="https://user-images.githubusercontent.com/60580427/117568387-e37f1080-b0fa-11eb-94f5-f68ce8f74666.png" width="50%" height="50%"> right
- The moment of truth!
- <img src="https://user-images.githubusercontent.com/60580427/117568427-1c1eea00-b0fb-11eb-8587-8b5a69ca5b2b.png" width="80%" height="80%">
- This is what I call successful!
