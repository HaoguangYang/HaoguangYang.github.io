---
title: "Bezier curve interpolation"
date: 2022-12-24T12:00:00-05:00
excerpt_separator: "<!--more-->"
categories:
  - Robotics Notes
  - Gists
tags:
  - Math
---

Cubic Bezier curves are commonly used in robotics for piecewise path interpolation, owing to their good smoothness. By setting proper boundary conditions, cubic Bezier curve can interpolate a curve connecting discrete points with $C^2$ continuity (continuous curvature). Further, the boundary conditions of cubic Bezier curve interpolation converges with only three consecutive points, which minimizes the overhead for the overlapped ease-in and ease-out segments of cyclic paths.

<!--more-->

### Problem formulation

The parameterized equation of a $n$-th order Bezier curve is written as:

$$ B_n(t) = \sum_{k=0}^{n} \begin{pmatrix} n \\ k \end{pmatrix} (1 - t)^{n - k} t^k X_k, \tag{1}$$

where $X_k$ are control points of arbitrary dimensions. It's easy to show that $B_n(0)=X_0$ and $B_n(1)=X_n$, i.e. the curve passes only the first and last control points, while the other control points provide directional guidance to the curve. To use Bezier curve for interpolation, the provided sample points $P_i$ should only appear as $X_0$ and $X_n$ of each segment, while the remaining contron points are determined using boundary conditions.

For cubic Bezier curve (third order), the formulaiton expands to:

$$ B_3(X_0, X_1, X_2, X_3; t) = \begin{bmatrix} X_0 & X_1 & X_2 & X_3 \end{bmatrix} \cdot \begin{bmatrix} (1-t)^3 \\ 3 t (1-t)^2 \\ 3 t^2 (1-t) \\ t^3 \end{bmatrix}. \tag{2}$$

Let's take its first and second-order derivatives to prepare for smoothness boundary conditions:

$$ \dot{B}_3(t) = \begin{bmatrix} X_0 & X_1 & X_2 & X_3 \end{bmatrix} \cdot \begin{bmatrix} -3(1-t)^2 \\ 3(1-t)^2 - 6t(1-t) \\ 6t (1-t) - 3t^2 \\ 3t^2 \end{bmatrix}, \\
\ddot{B}_3(t) = \begin{bmatrix} X_0 & X_1 & X_2 & X_3 \end{bmatrix} \cdot \begin{bmatrix} 6(1-t) \\ -12(1-t) + 6t \\ 6(1-t) - 12t \\ 6t \end{bmatrix}. \tag{4}$$

Denote $\Gamma_i(A_i,B_i; t)=B_3(P_i, A_i, B_i, P_{i+1}; t)$ is the interpolated cubic Bezier curve given sample points $P_i, i = 0,1,\cdots,m$. Now let's apply the smoothness boundary conditions:

$$ \dot{\Gamma}_i(1) = \dot{\Gamma}_{i+1}(0), \\
\ddot{\Gamma}_i(1) = \ddot{\Gamma}_{i+1}(0). \tag{5}$$

Simplifying the boundary conditions (5) with (4) gets:

$$ B_i = 2P_{i+1} - A_{i+1}, \tag{6}$$

$$A_i + 2A_{i+1} = 2B_i + B_{i+1}. \tag{7}$$

Further combining (6),(7) yields:

$$ A_i + 4A_{i+1} + A_{i+2} = 4P_{i+1} + 2P_{i+2}, \ \ i=0, 1, \cdots, m-3. \tag{8}$$

Now consider the starting and end of the sample point series when $i=m-2, m-1$.

- For acyclic paths, [this blog](https://towardsdatascience.com/b%C3%A9zier-interpolation-8033e9a262c2) adopted **arbitrary boundary conditions**:

  $$\ddot{\Gamma}_0(0)=0, \ddot{\Gamma}_{m-1}(1)=0, \tag{9}$$

  which allows ease-in and ease-out of curvature. The solution of (6),(8),(9) yields:

  $$\begin{bmatrix}
  2 & 1 & 0 & 0 & \cdots & 0 \\
  1 & 4 & 1 & 0 & \cdots & 0 \\
  0 & 1 & 4 & 1 & \cdots & 0 \\
  \vdots & \ddots & \ddots & \ddots & \ddots & \vdots \\
  0 & \cdots & 1 & 4 & 1 & 0 \\
  0 & \cdots & 0 & 1 & 4 & 1 \\
  0 & \cdots & 0 & 0 & 2 & 7
  \end{bmatrix} \begin{bmatrix}
  A_0 \\ A1 \\ A2 \\ \vdots \\ A_{m-3} \\ A_{m-2} \\ A_{m-1}
  \end{bmatrix} = \begin{bmatrix}
  P_0 + 2P_1 \\ 4P_1 + 2P_2 \\ 4P_2 + 2P_3 \\ \vdots \\ 4P_{m-3} + 2P_{m-2} \\ 4P_{m-2}+2P_{m-1} \\ 8P_{m-1} + P_m
  \end{bmatrix}, \tag{10}$$

  $$B_i = 2P_{i+1}-A_{i+1}, i=0,1,\cdots,m-2, \\
  B_{m-1} = (A_{m-1}+P_m)/2. \tag{11}$$

  However, this is not the ideal case for cyclic paths, as it creates discontinuity in curvature when connecting from the last sample point to the first sample point.
  
- To avoid curvature discontinuity, for cyclic paths we can apply **cyclic boundary conditions**:

  $$P_m = P_0, \\
  \dot{\Gamma}_{m-1}(1) = \dot{\Gamma}_0(0), \ddot{\Gamma}_{m-1}(1) = \ddot{\Gamma}_0(0), \tag{12}$$

  Solving (6),(8),(12) yields:

  $$\begin{bmatrix}
  4 & 1 & 0 & 0 & \cdots & 1 \\
  1 & 4 & 1 & 0 & \cdots & 0 \\
  0 & 1 & 4 & 1 & \cdots & 0 \\
  \vdots & \ddots & \ddots & \ddots & \ddots & \vdots \\
  0 & \cdots & 1 & 4 & 1 & 0 \\
  0 & \cdots & 0 & 1 & 4 & 1 \\
  1 & \cdots & 0 & 0 & 1 & 4
  \end{bmatrix} \begin{bmatrix}
  A_0 \\ A1 \\ A2 \\ \vdots \\ A_{m-3} \\ A_{m-2} \\ A_{m-1}
  \end{bmatrix} = \begin{bmatrix}
  4P_0 + 2P_1 \\ 4P_1 + 2P_2 \\ 4P_2 + 2P_3 \\ \vdots \\ 4P_{m-3} + 2P_{m-2} \\ 4P_{m-2}+2P_{m-1} \\ 4P_{m-1} + P_m
  \end{bmatrix}, \tag{13}$$

  $$B_i = 2P_{i+1}-A_{i+1}, i=0,1,\cdots,m-2, \\
  B_{m-1} = 2P_m - A_0 = 2P_0 - A_0. \tag{14}$$

  and

- For acyclic paths, another viable approach is the **second-type boundary conditions**:

  $$\dot{\Gamma}_{0}(0) = 0, \dot{\Gamma}_{m-1}(1) = 0, \tag{15}$$

  which yield:

  $$\begin{bmatrix}
  1 & 0 & 0 & 0 & \cdots & 0 \\
  1 & 4 & 1 & 0 & \cdots & 0 \\
  0 & 1 & 4 & 1 & \cdots & 0 \\
  \vdots & \ddots & \ddots & \ddots & \ddots & \vdots \\
  0 & \cdots & 1 & 4 & 1 & 0 \\
  0 & \cdots & 0 & 1 & 4 & 1 \\
  0 & \cdots & 0 & 0 & 0 & 1
  \end{bmatrix} \begin{bmatrix}
  A_0 \\ A1 \\ A2 \\ \vdots \\ A_{m-3} \\ A_{m-2} \\ A_{m-1}
  \end{bmatrix} = \begin{bmatrix}
  P_0 \\ 4P_1 + 2P_2 \\ 4P_2 + 2P_3 \\ \vdots \\ 4P_{m-3} + 2P_{m-2} \\ 4P_{m-2}+2P_{m-1} \\ P_m
  \end{bmatrix}, \tag{16}$$

  $$B_i = 2P_{i+1}-A_{i+1}, i=0,1,\cdots,m-2, \\
  B_{m-1} = A_{m-2} + 4A_{m-1} - 4P_{m-1}. \tag{17}$$

### Numerical Solution

To solve the series of cubic Bezier curves, the general order is to first solve $A_i$, then use the $(P_i, A_i)$ pair to solve for $B_i$. These parameters fill in the missing control points between sample points.

The key to obtaining $A_i$ is the solution of the classical or perturbed tri-diagonal matrix. A fast method is to apply the [Thomas algorithm](https://www.cfd-online.com/Wiki/Tridiagonal_matrix_algorithm_-_TDMA_(Thomas_algorithm)). For cyclic boundary conditions, the tri-diagonal matrix is perturbed (upper right and lower left non-zero elements). To solve the perturbed variant, apply the Sherman-Morrison formula to transform the matrix into a classical tri-diagonal matrix first before applying the Thomas algorithm twice.

Pseudocode for the in-place Thomas algorithm is shown below:
```
FUNCTION ThomasAlgorithm(TriDiagonalMatrix M, Vector Y)
  for (n = 1; n < size(Y); n ++)
    M(n, n-1) = M(n, n-1) / M(n-1, n-1)
    M(n, n) = M(n, n) - M(n, n-1) * M(n-1, n)
    Y(n) = Y(n) - M(n, n-1) * Y(n-1)
  endfor
  Y(end) = Y(end) / M(end, end)
  for (n = size(Y) - 1; n > 0; n --)
    Y(n-1) = (Y(n-1) - M(n-1, n) * Y(n)) / M(n-1, n-1)
  endfor
  return Y
end
```

Pseudocode for the cyclic (perturbed) tri-diagonal matrix using Sherman-Morrison formula is shown below:
```
FUNCTION PerturbedTriDiagonalSolver(PerturbedMatrix M, Vector Y)
  gamma = -M(0,0)
  Vector U = [gamma, 0, ..., 0, M(end, 0)]
  Vector V = [1, 0, ..., 0, M(0, end) / gamma]
  M(0, 0) = M(0, 0) - gamma
  M(end, end) = M(end, end) - M(end, 0) * M(0, end) / gamma
  M(0, end) = 0
  M(end, 0) = 0
  Matrix M_copy = M
  Y = ThomasAlgorithm(M, Y)
  U = ThomasAlgorithm(M_copy, U)
  Y = Y - dot(V, Y) / (1 + dot(V, U)) * U
  return Y
end
```

A C++ library for this Bezier curve interpolation is included in our **reusable ROS2 path server** (to be released soon).
