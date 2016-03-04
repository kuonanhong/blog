---
title: Derivation of Navier-Stokes Equation
tags: [SPH, physics]
---
The Navier-Stokes equation governs fluid hydrodynamics. It describes the acceleration of a control volume by the compound force of external forces, pressure force and viscosity, generally given as:
{% math %}
\frac{d\mathbf{u}}{dt}=\underbrace{\mathbf{f}}_{external}-\underbrace{\frac{1}{\rho}\nabla p}_{pressure} + \underbrace{\nu\nabla^2 \mathbf{u}}_{viscosity}.
{% endmath %}

Understanding the derivation of this equation is a great help to understand the SPH process. However, the derivation [posted on Wikipedia](https://en.wikipedia.org/wiki/Derivation_of_the_Navier–Stokes_equations) is a little bit too advanced for people without fluid dynamics background like me. Therefore, in this post, I will explicitly explain all of the necessary steps and backgrounds while describing the derivation process.

This post is orginized in the following structure:

* Introduction of two specifications of fluid: Eulerian and Lagrangian.
* Derivation of continuum equation using the conservation of mass.
* Strain and stress tensors.
* Derivation of Navier-Stokes equation using the conservation of momentum.

## Eulerian vs Lagrangian

Eulerian and Lagrangian specifications are two specification used to describe the movement of fluid. In Eulerian specification, the movement of fluid is described as the amount of fluid flow in and out of a fixed space. However, in Lagrangian specification, the movement of fluid is consisted by the movement of particles. Thus, we have two mathematical descriptions of fluid:

{% math %}
\mathbf{r} = \mathbf{r}(a,b,c,t)
\label{eq:lag}
{% endmath %}

{% math %}
\mathbf{u}=[x(t), y(t), z(t), t].
\label{eq:eul}
{% endmath %}
In eq {% math %}\ref{eq:lag}{% endmath %}
## Full Derivative
In Eulerian specification of fluid, we can describe the velocity of a point in the fluid as:
{% math %}
\mathbf{u}=[x(t), y(t), z(t), t].
{% endmath %}
Note that in this equation, not only the speed is the function of the time, but also the position $x,y,z$. Thus, the acceleration of velocity can be represented as:
{% math %}
\frac{d\mathbf{u}}{dt}=\frac{\partial \mathbf{u}}{\partial t}+(\mathbf{u}\cdot\nabla)\mathbf{u}
{% endmath %}
The first partial derivative on the right describes the change of velocity at same position over time and the second describes the change of velocity over positions. 

## Continuum Equation
Continuum equation is derived from the conservation of mass. That is to say, the total amount of mass flows into the control volume is equal to its change of the density. Considering a control volume like this. The  total amount of mass flows into the equal to the mass flows into minus  the mass flow out of the control volume:

Here we discuss the conservation of mass on the $x$ direction. in this case. 

