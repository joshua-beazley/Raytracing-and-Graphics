
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
    <img src="Splatting/imgs/gsplat_random_learn_scale_luminance/init=random-blobs=5000-batch=0.1-LR_scale=0.001-LR_lum=0.0001-epoch=1000.0.png" width="32%" /> 
    <img src="Splatting/imgs/gsplat_random_learn_scale_luminance/init=random-blobs=5000-batch=0.1-LR_scale=0.001-LR_lum=0.0001-epoch=27000.0.png" width="32%" /> 
  <br>
  <br>
  <em><b>Figure 5:</b> Gsplats struggled to spread color to their borders and to mix between one another. Left shows initial random initialization; right shows learning after 27,000 epochs  </em>
</p>


<p align="center">
    <img src="Splatting/imgs/gsplat_random_learn_scale_luminance/init=random-blobs=5000-batch=0.1-LR_scale=0.001-LR_lum=0.0001-epoch=11000.0.png" width="42%" />
    <img src="Splatting/imgs/gsplat_random_learn_scale_luminance/init=random-blobs=5000-batch=0.1-LR_scale=0.001-LR_lum=0.0001-epoch=12000.0.png" width="42%" />
  <br>
  <br>
  <em><b>Figure 6:</b> After 12,000 epochs of training, many pixels in the center-top became unstable and collapsed. Some strange artefact of overfitting. </em>
</p>


## Anti-Aliasing

Anti-aliasing (AA) is a fundamental technique used to mitigate "jaggies"—the stair-stepped aliasing artifacts that occur when representing high-resolution geometric edges on a pixel grid. By sampling multiple rays per pixel and averaging the resulting color values, AA creates a smoother transition between objects. However, uniform supersampling is computationally expensive and oftentimes signifigant computational resources are spent on pixels that contribute little to the final image quality. 

To optimize this process, I developed an algorithm designed to apply AA selectively to the most problematic regions of a scene. By implementing a simple edge-detecting heuristic, I was able to detect edges in parallel with raycasting the scene. Anti-aliasing supersampling is applied at subpixel positions within the detected edges afterwards. A final weighted average delivers the final image. 


<p align="center">
    <img src="Rays/results/wizard_hat/no_anti_aliasing.png" width="32%" /> 
    <img src="Rays/results/wizard_hat/edge_detect_AA-uniform-N=5-THICK=1.0-shadows=true.png" width="32%" />
  <br>
  <br>
  <em><b>Figure 1:</b> Anti-aliasing improves the clarity at the expense of increased computation time. </em>
</p>

<p align="center">
    <img src="Rays/results/Edge_Detection/edge_detection_REFLECTION_&_SHADOWS.png" width="40%" /> 
  <br>
  <br>
  <em><b>Figure 2:</b> Edges can be found in reflections and shadows too! </em>
</p>

A second optimization to the Raytracer is the Bounding Volume Hierarchy (BVH). The BVH is an algorithm which splits the scene into a hierarchy of rectangular bounding boxes. Now a ray only needs to check for intersection with shapes within its bounding box. This optimization is especially important in scenes with large meshes with thousands of triangles. 

<p align="center">
    <img src="Rays/results/shadow_wizard_money_gang/edge_detect_AA-uniform-N=5-THICK=1.0-shadows=true.png" width="40%" /> 
  <br>
  <br>
  <em><b>Figure 3:</b> The Bounding Volume Heirarchy enables complex scenes like this to be rendered in fractions of a second. </em>
</p>
