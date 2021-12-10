---
layout: post
title: "reproduce paper [Jiang. 2017], project error"
author: "Axe-hyx"
categories: journal
tags: [paper,sample]
image: cards.jpg
---

With the help of [Nghia Truong](https://ttnghia.github.io), I fixed this bug, which is caused by the wrong project step.

# Correct update step

TO BE UPDATED

I am debuging my implementation of Curve in 3D [Jiang. 2017]. For the convenience of debuging, I ignore the compute step of `return mapping` and `the type iii force`.

I do not quiet unsderstand the following steps, there my be some error in other part of algorithm. 

1. get the Kirchhoff stress $$\tau$$ from an input $F = \hat R$, using Drucker-Prager model.

2. the computation of type ii force. According to my understanding, the Kronecker delta $\delta$ term in the parentheses compute the force direction; for 3D curve, which $\gamma = 1$, the left part is the product of $\frac{\partial \psi}{\partial \mathbf F}$ and first row of $\mathbf D^{-1}$.

# PROJECT

$$
\mathbf F_p^E = \mathbf U_p \mathbf \Sigma_p\mathbf V_p^T \quad \text{Let}\ \epsilon_p = \ln \Sigma_p\\
\hat \epsilon = \epsilon_p - \frac{\text{tr}(\epsilon_p)}{d}\mathbf I \quad \delta \gamma_p = ||\hat \epsilon_p||_F + \frac{d\lambda + 2\mu}{2\mu}\text{tr}(\epsilon_p)\alpha_p\\
\begin{cases}
\mathbf\Sigma_p = \mathbf\Sigma_p & \delta\gamma_p \le 0\\
\mathbf\Sigma_p = \mathbf I & ||\hat\epsilon_p||_F=0 \ \text{or} \text{tr}(\mathbf\epsilon_p) > 0\\
\mathbf\Sigma_p = e^{\mathbf H_p} & \text{otherwise}, \mathbf H_p = \mathbf \epsilon_p - \delta\gamma_p\frac{\hat \epsilon_p}{||\hat\epsilon_p||_F}\
\end{cases}
$$

# Force increment

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
\frac{\partial \psi}{\partial \mathbf d^E} =\mathbf Q \mathbf A \mathbf R^{-T}\\
\frac{\partial \psi}{\partial \mathbf F} = \frac{\partial \psi}{\partial \mathbf d^E}  \mathbf D^T\\
\mathbf{f}_{q \zeta}^{(i i)}(\hat{\mathbf{x}})=-\sum_{p \in \mathcal{I}^{(i i i)}} \sum_{\epsilon} V_{p}^{0} \frac{\partial \psi}{\partial F_{\zeta \epsilon}} \sum_{\beta=1}^{\gamma}\left(\delta_{q, \operatorname{mesh}(p, \beta)}-\delta_{q, \operatorname{mesh}(p, 0)}\right) D_{p, \beta \epsilon}^{-1}\\
\mathbf f_q^{(ii)} = -\sum_{p \in \mathcal{I}^{(i i i)}} V_p^0 \frac{\partial \psi}{\partial \mathbf F} D^{-1}_{\text{row}(0)} (\delta_{q,\operatorname{mesh}(p,1)} - \delta_{q,\operatorname{mesh}(p,0)})
$$
