---
title: OpenCV 3D Reconstruction
date: 2016-03-03 22:01:26
tags: opencv
---
A recent 3D reconstruction job requires finding the rays shot from pixels of different cameras. i.e. map a pixel $(u, v)$ to a point in world space $(x, y, z)$. The cameras are calibrated with OpenCV and the parameters are stored in files. This post discussed my experiment with these parameters.
## Decomposition of Transformation
According to the definition given by [OpenCV site](http://docs.opencv.org/2.4/modules/calib3d/doc/camera_calibration_and_3d_reconstruction.html), the intrinsic and extrinsic parameters are given in this form:
{% math %}
\mathbf{m'=A[R|t]M'}
{% endmath %}
where $m'(u,v,1)$ is the projected pixel; $A$ is the camera matrix; $[R|t](x,y,z)=R\times(x,y,z)+t$ is the transformation matrix given by extrinsic parameters. $M'(x,y,z)$ is the point in world space. 
Obviously, in order to map a ray from image to world space, we need to do another way around. i.e. compute $M'$ by giving $m'$:
{% math %}
M'=[R|t]^{-1}\underbrace{A^{-1}m'}_{\text{ray in model space}}
{% endmath %}
First, let's deal with the ray in model space. 
Plugin camera matrix (intrinsice paramteres)
{% math %}
\mathbf{A}=\begin{bmatrix}
f_x & 0 & c_x \\ 
0 & f_y & c_y\\ 
0 & 0 & 1
\end{bmatrix},
{% endmath %}
then we get
{% math %}
M'=[R|t]^{-1} (\frac{u-c_x}{f_x}, \frac{v-c_y}{f_y}, 1)^T.
{% endmath %}
Also for extrinsic parameters, since 
{% math %}
[R|t][x,y,z]^T=R\times[x,y,z]^T+t,
{% endmath %}
then
{% math %}
[R|t]^{-1}[x,y,z]^T=R^{-1}([x,y,z]^T-\mathbf{t})
{% endmath %}
While $\mathbf{R}$ is an orthogonal matrix, $\mathbf{R}^{-1}=\mathbf{R}^T$. We can write the transformation matrix in the homogeneous 4 by 4 matrix in the following form:
{% math %}
\mathbf{M_T}=\begin{bmatrix}
    \mathbf{R}^T & \begin{matrix} -t_x \\ -t_y \\ -t_z\end{matrix} \\
        \begin{matrix} 0 & 0 & 0 \end{matrix} & 1
        \end{bmatrix}
{% endmath %}
## Generating Ray
We define the ray by its starting point and direction in homogenous coordinate in world space. {% math %}\mathbf{r}=\mathbf{o}+t*\mathbf{d}{% endmath %}

```
struct Ray
{
    glm::vec4 origin;
    glm::vec4 dir;
};
```

Note that the dir is normalized.
Thus starting point is the camera position in the world space:
{% math %}
origin=\mathbf{M_T}\times[0,0,0,1],
{% endmath %}
and direction is calculated by a given pixel $m'(u,v)$:
{% math %}
dir=\mathbf{M_T} (\frac{u-c_x}{f_x}, \frac{v-c_y}{f_y}, 1,0)^T
{% endmath %}
## Reconstruction
Now assume that we have found two correspondent points in two camera images, and have got two rays $\mathbf{r_1}=\mathbf{p_0}+t\mathbf{u}, \mathbf{r_2}=\mathbf{p_1}+t\mathbf{v}$. Since there might be some errors, the two rays are not necessarily collide in space. Therefore, we find a segments $\mathbf{l}$ that perpendicular to both rays, and the treat the midpoint as the reconstructed point. Assume the two ending points of segment $\mathbf{l}$ are $\mathbf{p}=\mathbf{p_0}+t_0\mathbf{u}, \mathbf{q}=\mathbf{p_1}+t_1\mathbf{v}$. We can have $\mathbf{l=p-q}$. Since $\mathbf{l}$ is perpendicular to both lines, we can have the following equations:
{% math %}
\left\{\begin{matrix}
\mathbf{l\cdot r_1}=0 \\ 
\mathbf{l\cdot r_2}=0
\end{matrix}\right.  
{% endmath %}
Two equations, two unknown numbers $(t_0, t_1)$. Problem solved.








