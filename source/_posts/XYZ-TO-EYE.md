---
layout: post
title:  "OpenGL Screen Space to View Space"
date:   2015-09-15 10:22:44
categories: tech
tags: opengl
---
*What I thought about the process of recovering the positions in view space from screen space was wrong*

I'm working on my raytracer recently. In order to use the same camera system I use in rasterizing, it is desirable to transform rays in screen space to world space. [This wiki](https://www.opengl.org/wiki/Compute_eye_space_from_window_space) on opengl.org states the way to recover position from screen space. 

However, after reading the wiki, I was a little bit confused about the process and tried to figure it out myself from projection matrix. 

```
+-----------+ Projection +--------+ Divided
| Camera    | Matrix     |Clip    | by w   
| Space     +------------+Space   +-------+
+-----------+            +--------+       |
                                          |
+----------+ Viewport        +------+     |
| Screen   | Transformation  | NDC  |     |
| Space    +-----------------+      +-----+
+----------+                 +------+      

```

The figure above illustrates the OpenGL transformation pipeline. Obviously, in order to recover camera space position, we need to first convert screen space to NDC (Normalized Device Space with $x,y,z \in [-1, 1]$), then multiply by $w$ in clip space. Finally, we can get the camera space by multiplying inverse of projection matrix. 



Camera Space to NDC
---

Let's first take a look at how we transform from `camera space` to `NDC`
{% math %}
\begin{pmatrix}
x_{clip}\\
y_{clip}\\
z_{clip}\\
w_{clip}
\end{pmatrix} =
\begin{pmatrix}
\frac{2n}{r-l} & 0 & \frac{r+l}{r-l} & 0 \\ 
0 & \frac{2n}{t-b} & \frac{t+b}{t-b} & 0 \\ 
0 & 0 & \frac{-(f+n)}{f-n} & \frac{-2fn}{f-n} \\ 
0 & 0 &  -1 & 0
\end{pmatrix}
\begin{pmatrix}
x_{view}\\
y_{view}\\
z_{view}\\
w_{view}
\end{pmatrix}
{% endmath %}
Thus,
{% math %}
\left\{\begin{matrix}
x_{clip}=\frac{2n}{r-l}x_{view} + \frac{r+l}{r-l}z_{view}\\
y_{clip}=\frac{2n}{t-b}y_{view} + \frac{t+b}{t-b}z_{view}\\
z_{clip}=\frac{-(f+n)}{f-n}z_{view} + \frac{-2fn}{f-n}w_{view}\\
w_{clip}=-z_{view}
\end{matrix}\right.
{% endmath %}

Then divide each element by $w_{view}$, we can get the NDC:

{% math %}
\left\{\begin{matrix}
x_{NDC} = x_{clip}/w_{clip}\\
y_{NDC} = y_{clip}/w_{clip}\\
z_{NDC} = \frac{f+n}{f-n}+\frac{2fn}{f-n}\cdot\frac{w_{view}}{z_{view}}
\end{matrix}\right.
{% endmath %}

where {% math %}w_{view}=1.0{% endmath %} and {% math %}w_{clip} = -z_{view}{% endmath %}. 

Screen Space to NDC
---

Generally speaking, in screen space {% math %}x_s, y_s, z_s \in [0, 1]{% endmath %}. In order to transform it to NDC, we can simply apply 
{% math %}
(x_{NDC}, y_{NDC}, z_{NDC}) = 2\cdot(x_s, y_s, z_s)-(1, 1, 1)
{% endmath %}

NDC to Camera Space
---

Knowing {% math %}(x_{NDC}, y_{NDC}, z_{NDC}){% endmath %}, we can get {% math %}z_{view}{% endmath %} by plugin {% math %}z_{NDC}{% endmath %} into 
{% math %}
z_{NDC} = \frac{f+n}{f-n}+\frac{2fn}{f-n}\cdot\frac{w_{view}}{z_{view}}
{% endmath %}


{% math %}
z_{view} = -w_{clip}= \frac{2fn}{f-n}/(z_{NDC}+(-\frac{f+n}{f-n}))
{% endmath %}

Note that $2fn/(f-n)$ and $(f+n)/(f-n)$ are elements in projection matrix. 
By mutiply each element in NDC, we can get `clip coordinate`.

```C
clipPos.w = camMats.projMat[3][2] / ( ndcPos.z + camMats.projMat[2][2]);

clipPos.xyz = ndcPos*clipPos.w;
```
Camera coordinate can be computed simply by multiplying clip coordinate with inverse of projection matrx.
```C
cameraPos = inverse(camMats.projMat) * clipPos;
```

This [snippet](https://gist.github.com/v3c70r/21312f645a6ab6746cc1) is the code I used to get camera coordinate from screen coordinate.


