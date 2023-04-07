---
title: "Bezier curve interpolation"
date: 2022-12-24T12:00:00-05:00
excerpt_separator: "<!--more-->"
categories:
  - Robotics Infrastructure
  - Gists
tags:
  - Bezier Curve
  - Interpolation
---

Cubic Bezier curves are commonly used in robotics for piecewise path interpolation, owing to their good smoothness. By setting proper boundary conditions, cubic Bezier curve can interpolate a curve connecting discrete points with $C^2$ continuity (continuous curvature). Further, the boundary conditions of cubic Bezier curve interpolation converges with only three consecutive points, which minimizes the overhead for the overlapped ease-in and ease-out segments of cyclic paths.

<!--more-->

The parameterized equation of a $n$-th order Bezier curve is written as:

$$ B_n(t) = \sum_{k=0}^{n} \begin{pmatrix} n \\ k \end{pmatrix} (1 - t)^{n - k} t^k X_k, $$

where $X_k$ are control points of arbitrary dimensions. It's easy to show that $B_n(0)=X_0$ and $B_n(1)=X_n$, i.e. the curve passes only the first and last control points, while the other control points provide directional guidance to the curve. To use Bezier curve for interpolation, the provided sample points $P_i$ should only appear as $X_0$ and $X_n$ of each segment, while the remaining contron points are determined using boundary conditions.

For cubic Bezier curve (third order), the formulaiton expands to:

$$ B_3(X_0, X_1, X_2, X_3; t) = \begin{bmatrix} X_0 & X_1 & X_2 & X_3 \end{bmatrix} \cdot \begin{bmatrix} (1-t)^3 \\ 3 t (1-t)^2 \\ 3 t^2 (1-t) \\ t^3 \end{bmatrix}.$$

Let's take its first and second-order derivatives to prepare for smoothness boundary conditions:

$$ \dot{B}_3(t) = \begin{bmatrix} X_0 & X_1 & X_2 & X_3 \end{bmatrix} \cdot \begin{bmatrix} -3(1-t)^2 \\ 3(1-t)^2 - 6t(1-t) \\ 6t (1-t) - 3t^2 \\ 3t^2 \end{bmatrix}, \\
\ddot{B}_3(t) = \begin{bmatrix} X_0 & X_1 & X_2 & X_3 \end{bmatrix} \cdot \begin{bmatrix} 6(1-t) \\ -12(1-t) + 6t \\ 6(1-t) - 12t \\ 6t \end{bmatrix}. $$

Denote $\Gamma_i(A_i,B_i; t)=B_3(P_i, A_i, B_i, P_{i+1}; t)$ is the interpolated cubic Bezier curve given sample points $P_i$. Now let's apply the smoothness boundary conditions:

$$ \dot{\Gamma}_i(1) = \dot{\Gamma}_{i+1}(0), \\
\ddot{\Gamma}_i(1) = \ddot{\Gamma}_{i+1}(0). $$

Simplifying the boundary conditions one gets:

$$ B_i = 2P_{i+1} - A_{i+1}, \\
A_i + 2A_{i+1} = 2B_i + B_{i+1}. $$

Further combining the two equations yields:

$$ A_i + 4A_{i+1} + A_{i+2} = 4P_{i+1} + 2P_{i+2}, \ \ i=0, 1, \cdots, m-3.$$

Now consider the starting and end of the sample point series ( $i=m-2, m-1$ ). For acyclic paths, [this blog](https://towardsdatascience.com/b%C3%A9zier-interpolation-8033e9a262c2) adopted an

**arbitrary boundary conditions**: $\ddot{\Gamma}_0(0)=0, \ddot{\Gamma}_{m-1}(1)=0,$

which allows ease-in and ease-out of curvature. This is not the ideal case for cyclic paths, as it creates discontinuity. As recommended in the comments, for cyclic paths we can apply

**cyclic boundary conditions**: $\dot{\Gamma}_{m-1}(1) = \dot{\Gamma}_0(0), \ddot{\Gamma}_{m-1}(1) = \ddot{\Gamma}_0(0),$

and for acyclic paths apply

**symmetric boundary conditions**: $\dot{\Gamma}_{m-1}(1) = \dot{\Gamma}_{m-1}(0), \dot{\Gamma}_{0}(1) = \dot{\Gamma}_0(0).$

