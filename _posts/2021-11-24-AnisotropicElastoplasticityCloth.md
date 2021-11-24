---
layout: post
title: "reproduce paper [Jiang. 2017]"
author: "Axe-hyx"
categories: journal
tags: [paper,sample]
image: cards.jpg
---

I am debuging my implementation of Curve in 3D [Jiang. 2017]. For the convenience of debuging, I ignore the compute step of `return mapping` and `the type iii force`.

I do not quiet unsderstand the following steps, there my be some error in other part of algorithm. 

1. get the Kirchhoff stress $\tau$ from an input $F = \hat R$, using Drucker-Prager model.

2. the computation of type ii force. According to my understanding, the Kronecker delta $\delta$ term in the parentheses compute the force direction; for 3D curve, which $\gamma = 1$, the left part is the product of $\frac{\partial \psi}{\partial \mathbf F}$ and first row of $\mathbf D^{-1}$.
