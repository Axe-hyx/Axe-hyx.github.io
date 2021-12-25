---
layout: post
title: "reproduce paper [Jiang. 2017], deformed elastic material directions error"
author: "Axe-hyx"
categories: journal
tags: [paper,sample]
image: 
---

I am debuging my implementation of Curve in 3D [Jiang. 2017]. For the convenience of debuging, I ignore the compute step of `return mapping`.

**Issue** :  For a scene yarns parallel to Z axis drop to ground (no other collider, just ground), simulate will blow up soon.

The yarn's d matrix is:

$$
\left[
\begin{array}{ccc}
0 & -1 & 0\\
0 & 0 & -1\\
0.00195312 & 0 & 0
\end{array}
\right]
$$

**Observation** : After simulating for a while, the d matrix (deformed elastic material directions) is not orthogonal. And the particles simulation retults show some perturbation shouldn't exist.

**Temporal patch** : Skip update of $\mathbf d_{p,2}$, and $\mathbf d_{p,3}= \frac{\mathbf d_{p,1} \times \mathbf d_{p,2}}{\|\mathbf d_{p,1} \times \mathbf d_{p,2}\|}$. This make sure d matrix is orthogonal, and $\mathbf d_{p,3}$ is unit length.

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="./assets/img/coordinate.png" >
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">Coordinate system I use.</div>
</center>

# Simulation retults

$dt = 4e^{-5}$

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="./assets/img/dmatrixpass.png"
    width="45%" height="45%">
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="./assets/img/dmatrixerror.png"
    width="45%" height="45%">
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">Frame = 990. Left: With patch.</div>
    &ensp;
    &ensp;
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">Right: My wrong implementation.</div>
</center>

\\
Algorithm steps related to d matrix (deformed elastic material directions) are listed below.

# Initialize

$$
\begin{array}{l}
\quad\mathbf d_{p,1} = \text{mesh}(p, \beta) - \text{mesh}(p,0)\\
\quad\mathbf d_{p,2} = \mathbf d_{p,1} \times \begin{array}{c}[0 & 1 & 0]\end{array}\\
\quad\mathbf  d_{p,3} =\mathbf  d_{p,1} \times \mathbf  d_{p,2}
\end{array}
$$

# Update Particle State

$$
\nabla w_{ip}^n = \nabla N (\mathbf x_p^n-\mathbf x_i)\\
\textbf{for}\ \text{all particles}\ p\ \text{of type }(iii)\ \textbf{do}\\
\begin{array}{l}
\quad \nabla \mathbf v_p\leftarrow \sum_i\overline{\mathbf {v}}_i^{(n+1)}(\nabla w_{ip}^n)^T\\
\quad\textbf{for}\ \beta = 1\ \text{to}\ \gamma\ \textbf{do}\\
\quad\quad\hat{\mathbf d}_{p,\beta}^{E,n+1}\leftarrow \mathbf x_{\text{mesh}(p,\beta)}^{n+1} - \mathbf x_{\text{mesh}(p,0)}^{n+1}\\
\quad\textbf{for}\ \beta = \gamma + 1\ \text{to}\ 3\ \textbf{do}\\
\quad\quad\hat{\mathbf d}_{p,\beta}^{E,n+1}\leftarrow \mathbf (\mathbf I + \Delta t\nabla \mathbf v_p)\mathbf d_{p,\beta}^{E,n}\\
\end{array}
$$

# type iii force

$$
\mathbf f_i^{(iii)}(\hat x) = -\sum_{p\in \mathcal{I}^{(iii)}}\sum_{\beta = \zeta +1 }^3V_p^0\frac{\partial \psi}{\partial \mathbf F_\beta^E}\mathbf d_\beta^T \nabla w_{ip}^n
$$

-----------------
**TL,DR** other algorithms parts, appendix

# PROJECT

$$
\text{Let}\ C = \text{cohesion}\\
\epsilon_i = \ln \sigma_i - C \\ 
\text{tr} = \sum \epsilon _i + \ln J_p \\
\hat \epsilon = \epsilon - \frac{\text{tr}}{\text {d}}\mathbf I \\
\begin{cases}
\text{case II = expansion: project to tip}\\
\Sigma = \exp(C) & \text{tr} > =0 \quad\\
\ln J_p = \beta  \sum \epsilon_i + \ln J_p \\
\ln J_p = 0 & \delta\gamma = ||\hat \epsilon||_F+ \frac{d \lambda + 2 \mu}{2\mu} \text{tr}\alpha \quad\\
\text {case I = static friction: inside yield surface, no plasticity}\\
\mathbf H = \epsilon + C \  \mathbf I  & \delta \gamma <=0\\
\text{case III = dynamic friction: do projection}\\
\mathbf H = \epsilon - \frac{\delta \gamma}{||\hat \epsilon||_F}\hat \epsilon + C \mathbf I & \text {otherwise}\\
\Sigma = \exp(H)
\end{cases}
$$

# compute force

$$
\mathbf F_{p}^{E,n} =\mathbf Q \mathbf R\\
f(\mathbf R_1) = \frac{k}{2}(r_{11} - 1)^2\rightarrow f^{\prime} = k (r11-1)\\
g(\mathbf R_2) = \frac{\gamma}{2}(r_{12}^2+r_{13}^2)\rightarrow g^{\prime} = \gamma\\
\mathbf r^T = \left[\begin{array}{c}r_{12} & r_{13}\end{array}\right]\\
\mathbf{\hat R} = \left[\begin{array}{cc}_{r22} & r_{23}\\
0 & r_{33}\end{array}\right]\\
\text{Let}\ \mathbf F = \mathbf{\hat R},\quad \text{do}\ \mathbf F = U\Sigma V^T \quad \text{return mapping of dry sand}\\ 
\mathbf\Sigma \leftarrow \text{PROJECT}(\mathbf \Sigma, \alpha	)\\
\frac{\partial \psi}{\partial{\hat R}}(\mathbf{\hat R}) = \frac{\partial \psi}{\partial \mathbf F}(\mathbf F) = \mathbf U ( 2\mu \mathbf \Sigma^{-1}\ln\mathbf \Sigma + \lambda\text{tr}(\ln\mathbf\Sigma)\mathbf\Sigma^{-1})\mathbf V^T\\
\tau = \mathbf P \mathbf F^T\\
p\mathbf I =\frac{\text{tr}(\tau)}{2} \mathbf I  \\
\mathbf{A}=\left[\begin{array}{cc}\\
f^{\prime} r_{11}+g^{\prime} \mathbf{r}^{T} \mathbf{r} & g^{\prime} \mathbf{r}^{T} \hat{\mathbf{R}}^{T} \\
g^{\prime} \mathbf{r}^{T} \hat{\mathbf{R}}^{T} & \hat{\mathbf{P}} \hat{\mathbf{R}}^{T}
\end{array}\right] \stackrel{\text{replace with}}{\rightarrow}\left[\begin{array}{cc}\\
f^{\prime} r_{11}+g^{\prime} \mathbf{r}^{T} \mathbf{r} & g^{\prime} \mathbf{r}^{T} \hat{\mathbf{R}}^{T} \\
g^{\prime} \mathbf{r}^{T} \hat{\mathbf{R}}^{T} & p\mathbf I
\end{array}\right] \\
\frac{\partial \psi}{\partial \mathbf F} =\mathbf Q \mathbf A \mathbf R^{-T}\\
\mathbf{f}_{q \zeta}^{(i i)}(\hat{\mathbf{x}})=-\sum_{p \in \mathcal{I}^{(i i i)}} \sum_{\epsilon} V_{p}^{0} \frac{\partial \psi}{\partial F_{\zeta \epsilon}} \sum_{\beta=1}^{\gamma}\left(\delta_{q, \operatorname{mesh}(p, \beta)}-\delta_{q, \operatorname{mesh}(p, 0)}\right) D_{p, \beta \epsilon}^{-1}\\
\mathbf f_q^{(ii)} = -\sum_{p \in \mathcal{I}^{(i i i)}} V_p^0 \frac{\partial \psi}{\partial \mathbf F} D^{-1}_{\text{row}(0)} (\delta_{q,\operatorname{mesh}(p,1)} - \delta_{q,\operatorname{mesh}(p,0)})
$$




