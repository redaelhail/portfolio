---
title: Radar human activity recognition with machine learning
date: 2024-05-01T07:06:09Z
draft: false
markup: html
---

This article shows a demonstration of indoor human activity recognition using FMCW radar and machine learning. The schematic below shows the end to end system from the sensor node to the cloud. I was 

<img src="demo.png" alt="Demo schematic">


This demo runs a convolutional neural network that was trained on a previously collected dataset in the same environment with different people.

<img src="cnn_architecture.png" alt="Model architecture">

<iframe width="1192" height="671" src="https://www.youtube.com/embed/-NEJ6uBrJiI" title="Demonstration movie" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="1192" height="671" src="https://www.youtube.com/embed/AY_CD8Vj0Ik" title="Parallel localisation &amp; event recognition with an FMCW Radar" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="980" height="551" src="https://www.youtube.com/embed/Vi7wcmVHEV8" title="Radar GUI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For more technical information, check the evaluation report of <a href="https://www.nextperception.eu"> NextPerception project</a>
