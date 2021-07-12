Introduction
The image stitching experiment is divided into three parts
1. Harris corner detection
2. Use the NCC algorithm to perform brute force matching to connect the strongly related
corner points of the two pictures
3. The used RANSAC removes the mismatched points and calculates the homograph matric
(correspondence between the left and right picture positions) through these
corresponding points to complete the picture stitching.
I will describe in detail the knowledge I have learned, and the problems encountered at different stages.
Harris corner detection
The core idea of Harris corner detection is in a small window. If the window is moved in all directions and the gray level of the area inside the window changes greatly, it is considered that a corner point.
First, I use the Sobel operator to calculate the gradient of the image in the x and y directions, which we call Ix and Iy.
Using Gaussian function as the window function to filter (Ix)2 , (Iy)2 and IxIy. Then we can have the matrix M.
Calculate the eigenvalue of the local eigen result matrix C = det (M) − K (trace(M)) where K = 0.04.
Finally, the value C of the calculated response function is suppressed by non-maximum value, and some points that are not corner points are filtered out. Meanwhile, the top 1000 points of the set threshold value should be satisfied.
In this experiment, Gaussian weighting function is used as the window function. By adjusting the size of the window, I found that Harris corner detection did not have scale invariance, and the position of corners detected would be affected after the window was enlarged. The image below shows a Gaussian window function with a size of 7*7 on the left and a window function with a size of 3*3 on the right. It can be seen from the image that the detected corner points are slightly different.
  
 Feature matching
After finding the corner points in the left and right pictures, we created a feature descriptor vector of 15*15 size with the left corner as the center. Calculate the NCC value of each corner on the left and all corner points on the right using brute force matching. The value of NCC actually describes the correlation between two points, and the closer the value is to 1, the stronger the correlation is. However, some feature points are too generalized to be accurately matched to the corresponding location. So, I keep the first and second NCC scores of each match. If the two scores are too similar, we discard them. The following figure shows the feature matching achieved by the above method. It can be seen from the image that there are still some points that are mismatched
Image Stitching
The inaccurate matching of feature points may lead to inaccurate calculation of the position relationship between the two images, resulting in a lot of noise. Therefore, it is very necessary to use RANSAC for pretreatment. I used RANSAC method to randomly select four pairs of matching points to calculate the homography matrix. The matrix is used to obtain the Inlier feature matching pairs. After 1000 iterations the optimal feature matching points are shown in the figure below.
                  
Based on these feature matching points obtained by RANSAC method, we re-use DLT to calculate the homography matrix to improve the accuracy. Homography matrix describes the position relationship between the left and right images, through which we can finally complete the stitched images as shown in the figure below
 Conclusion
Through the experience of the development Image stitching, I successfully realized the
whole process and learned the characteristics of corner points, Sobel operator and the
code implementation method of Gaussian blur. Meanwhile, I have also mastered
normalized cross correlation algorithm to realize corner matching. The use of RANSAC
has a certain anti-noise, can improve the matching accuracy. Finally, homography matrix
is used to realize image stitching.
Compared among those three sections, how to use Harris Corner Detection which is the
part that I think challenges me the most. The implementation of the Gaussian function
and the entire process of Harries Corner Detection are quite complex. But at the end, I
have successfully achieved the goals
