

## Anti-Aliasing

The technique of anti-aliasing (AA) removes jagged edges from an image by sampling multiple rays per pixel, then averaging the colors together. Anti-aliasing creates a smooth transition between objects in a scene. However, AA is incredibly expensive computationally. I sought to apply AA to a subset of pixels which I believed to be "jagged". This technique utilizes an algorithm to detect edges between objects in a scene. This edge detection algorithm works for shadows, reflections, and refractions. You may view images at ```Rays/results/Edge_Detection```

<p align="center">
    <img src="Rays/results/wizard_hat/no_anti_aliasing.png" width="48%" /> 
    <img src="Rays/results/wizard_hat/edge_detect_AA-uniform-N=5-THICK=1.0-shadows=true.png" width="48%" />
  <br>
  <br>
  <em><b>Figure 1:</b> Anti-aliasing improves the clarity at the expense of increased computation time. </em>
</p>

## Optimizing Anti-Aliasing through Selective Edge Detection

By integrating edge detection with anti-aliasing (AA), I developed a more targeted approach to image refinement. I noticed that many images suffered from "blurring" and aliasing artifacts along their boundaries, which compromised the overall visual quality. To solve this, I used a heuristic to isolate the specific pixels where these high-frequency spatial discontinuities occur.

Rather than applying computationally heavy AA algorithms to the entire frame, I restricted their use to these identified subsets. This approach allowed me to balance visual fidelity with computational efficiency—allocating system resources only where they were truly needed. The result was a significant reduction in edge artifacts while preserving the integrity and performance of the overall system.

<p align="center">
    <img src="Rays/results/Edge_Detection/edge_detection_REFLECTION_&_SHADOWS.png" width="80%" /> 
  <br>
  <br>
  <em><b>Figure 2:</b> The edge detection algorithms works in linear time. </em>
</p>

A second optimization to the Raytracer is the Bounding Volume Hierarchy (BVH). The BVH is an algorithm which splits the scene into a hierarchy of rectangular bounding boxes. Now a ray only needs to check for intersection with shapes within its bounding box. This optimization is especially important in scenes with large meshes with thousands of triangles. 

<p align="center">
    <img src="Rays/results/shadow_wizard_money_gang/edge_detect_AA-uniform-N=5-THICK=1.0-shadows=true.png" width="80%" /> 
  <br>
  <br>
  <em><b>Figure 3:</b> The Bounding Volume Heirarchy enables complex scenes like this to be rendered in fractions of a second. </em>
</p>

## Creating Images from Splats of Color

I used splats of color to recreate an image. I began by sampling color from a source image at random positions. Those random positions make the centroids of my splat. The splat is drawn as an ellipse (Gaussian normal) with scaling parameters in the x and y direction. I use Stochastic Gradient Descent to optimize my scaling and transparency parameters with respect to a loss function. My loss function is simply the difference between the ground truth image and the forward pass of my model. As a future expansion, I could create a multispectoral loss equation which optimizes towards the correct wavelength of light. My neural network is nothing fancy, I trained for 7,000 epochs before I observed diminishing returns. I used sensible learning rates and performed no hyperparameter tuning. 

My best Gaussian Splatting results still contain many areas with no color (no Gaussian was drawn close enough to that pixel). To fix this issue, I decided to interpolate pixels based on their nearest centroid, instead of drawing a Gaussian. This is actually a well studied problem titled "Voronoi Tessellation". I was able to find a github repo by Arthur Santaza which impelements Fortune's algorithm, an O(n log n) approach to interpolate pixels from their nearest centroid. Interfacing this repo with my code, I observed massive performance increases. However, I did not utilize Stochastic Gradient Descent to optimize with respect to a loss function because I do not know how to take the derivative of "find the closest centroid". Additionally, performance was significantly slower for Nearest Neighbor interpolation than Gaussian splats, which was dissapointing. 

## Voronoi Tesselation

<p align="center">
    <img src="Splatting/imgs/neighborhood_splats_powers_of_2/random-sampling_256-splats.png" width="32%" /> 
    <img src="Splatting/imgs/neighborhood_splats_powers_of_2/random-sampling_32768-splats.png" width="32%" />
    <img src="Splatting/imgs/neighborhood_splats_powers_of_2/random-sampling_1048576-splats.png" width="32%" />
  <br>
  <br>
  <em><b>Figure 4:</b> Image clarity is improved with increased Voronoi tiles. (2^8, 2^15, 2^20) </em>
</p>

## Gaussian Splatting

<p align="center">
    <img src="Splatting/imgs/neighborhood_splats_powers_of_2/random-sampling_256-splats.png" width="32%" /> 
    <img src="Splatting/imgs/neighborhood_splats_powers_of_2/random-sampling_32768-splats.png" width="32%" />
    <img src="Splatting/imgs/neighborhood_splats_powers_of_2/random-sampling_1048576-splats.png" width="32%" />
  <br>
  <br>
  <em><b>Figure 5:</b> Gsplats struggled to spread color to their borders, hence the idea to color my image from a Voronoi tesselation. </em>
</p>


<p align="center">
    <img src="Splatting/imgs/gsplat_random_learn_scale_luminance/init=random-blobs=5000-batch=0.1-LR_scale=0.001-LR_lum=0.0001-epoch=11000.0.png" width="42%" />
    <img src="Splatting/imgs/gsplat_random_learn_scale_luminance/init=random-blobs=5000-batch=0.1-LR_scale=0.001-LR_lum=0.0001-epoch=12000.0.png" width="42%" />
  <br>
  <br>
  <em><b>Figure 6:</b> After 12,000 epochs of training, many pixels in the center-top became unstable and collapsed. Some strange artefact of overfitting. </em>
</p>

