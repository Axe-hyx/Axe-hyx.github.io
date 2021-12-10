---
layout: post
title: "reproduce paper [Jiang. 2017], deformed elastic material directions error"
author: "Axe-hyx"
categories: journal
tags: [paper,sample]
image: 
---

I am debuging my implementation of Curve in 3D [Jiang. 2017]. For the convenience of debuging, I ignore the compute step of `return mapping`.

**Issue** :  For a scene yarns parallel to X axis drop to ground (no other collider, just ground), simulate retults look fine. But, when I rotate yarns 90 degrees around the Y axis, simulate will blow up soon.

**Observation** : After simulating for a while, the d matrix (deformed elastic material directions) is not orthogonal. And the particles simulation retults show some perturbation shouldn't exist.

<img src="./assets/img/coordinate.png" div align=center/>
<center>Coodinate I use.</center>

Denote yarn parallel to X axis as scene I, yarns parallel to Z axis as Scene II.
# Simulation retults

$dt = 4e^{-5}$
<img src="./assets/img/xgroundinit.png" div align=center/>
<center>Scene I, after initialization, frame = 1.</center>
<img src="./assets/img/xgroundfinish.png" div align=center/>
<center>Scene I, droped to ground (before hitting ground), frame = 8600.</center>

Scene I, results look fine.

<img src="./assets/img/zgroundinit.png" div align=center/>
<center>Scene II, after initialization, frame = 1.</center>
<img src="./assets/img/zgroundfinish.png" div align=center/>
<center>Scene II, next moment will blow up, frame = 198.</center>
Scene II, d matrix will no be orthogonal after some time, finally will blow up.

\\
Algorithm steps related to d matrix (deformed elastic material directions) are listed below.

# Initalize
$$
\begin{array}{l}
\quad\mathbf d_{p,0} = \text{mesh}(p, \beta) - \text{mesh}(p,0)\\
\quad\mathbf d_{p,1} = \mathbf d_{p,0} \times \begin{array}{c}[0 & 0 & 1]\end{array}\\
\quad\mathbf  d_{p,2} =\mathbf  d_{p,0} \times \mathbf  d_{p,1}
\end{array}
$$

# Update Particle State
$$
\nabla w_{ip}^n = w_{ip}(\mathbf D_p^n)^{-1}(x_i-x_p^n)\\
\textbf{for}\ \text{all particles}\ p\ \text{of type }(iii)\ \textbf{do}\\
\begin{array}{l}
\quad \nabla \mathbf v_p\leftarrow \sum_i\overline{\mathbf {v}}_i^{(n+1)}(\nabla w_{ip}^n)^T\\
\quad\textbf{for}\ \beta = 1\ \text{to}\ \gamma\ \textbf{do}\\
\quad\quad\hat{\mathbf d}_{p,\beta}^{E,n+1}\leftarrow \mathbf x_{\text{mesh}(p,\beta)}^{n+1} - \mathbf x_{\text{mesh}(p,0)}^{n+1}\\
\quad\textbf{for}\ \beta = \gamma + 1\ \text{to}\ 3\ \textbf{do}\\
\quad\quad\hat{\mathbf d}_{p,\beta}^{E,n+1}\leftarrow \mathbf (\mathbf I + \Delta t\nabla \mathbf v_p)\mathbf d_{p,\beta}^{E,n}\\
\end{array}
$$

# compute force
$$
\mathbf f_i^{(iii)}(\hat x) = -\sum_{p\in \mathcal{I}^{(iii)}}\sum_{\beta = \zeta +1 }^3V_p^0\frac{\partial \psi}{\partial \mathbf F_\beta^E}\mathbf d_\beta^T \nabla w_{ip}^n
$$




