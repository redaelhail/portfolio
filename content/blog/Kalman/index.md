---
title: "Kalman filtering: Linear, Extended, Unscented"
date: 2024-09-12T07:21:52Z
draft: false
markup: html
tags: [Machine Llearning]
cover:
  image: "EKF_example.jpg"
  alt: "<alt text>"
  caption: "<text>"
  relative: false # To use relative path for cover image, used in hugo Page-bundles
---
<p> Kalman filter is a powerful algorithm used for estimating the state of a dynamic system from noisy measurements. It was developed by Rudolf Kalman in 1960 and has found applications in various fields including navigation, robotics, and signal processing.</p>
The Kalman filter works by combining two sources of information:
<l>
    <li>Predictions based on a mathematical model of the system </li>
    <li>Measurements from sensors </li>

</l>


The key features of a Kalman filter include:

<ul>
<li>Recursive nature: It doesn't need to store all past data, making it computationally efficient</li>
<li>Optimal estimation: Under certain conditions, it provides the best possible estimate (Can be mathematically proven)</li>
<li>Handling uncertainty: It accounts for both measurement and process noise </li>
</ul>
Kalman filters are particularly useful in situations where the system state cannot be directly observed, but indirect and potentially imperfect measurements are available. For example, measuring the temperature of the brain from inside is not possible since we would need invasive tools. However, it can be estimated from the measured temperature of the skin using Kalman filtering.
 
In this article, I will show different types of Kalman filters with examples and Matlab code. For access to all the code examples discussed here, please visit the associated GitHub <a href="https://github.com/redaelhail/Kalman-filter-variations
">repository</a>
<h2>Linear Kalman Filter</h2>

<h3>Stock price tracking</h3>

<p>In the world of stock trading, cutting through market noise is crucial. This MATLAB script demonstrates how Kalman filtering can achieve this more effectively than traditional moving averages.</p> 

<center><img src="kalman1d.jpg" alt="" width="600" height="400"></center>

<p>The code simulates 100 stock price points with added volatility, then applies a Kalman filter to smooth out the data. By plotting both the raw prices and the filtered estimates, potential trends that might be hidden in the noise are revealed. The script also calculates the filter's error, providing a measure of confidence in its predictions. This approach can offer traders a more sophisticated tool for analyzing price movements, potentially leading to better-informed trading decisions.</p>

<script src="https://gist.github.com/redaelhail/421952f6a9bd6ff015ba3697495d13ff.js"></script>

<h3>3D position tracking</h3>

<p>This Kalman filter implementation is designed to track a rocket's position and velocity in three-dimensional space. It uses a state vector that includes both position and velocity components for each dimension (x, y, z). </p>

<p>The filter operates recursively, alternating between prediction and update steps. In the prediction step, it uses the state transition matrix A to estimate the rocket's new state based on its previous state and a simple motion model. The measurement matrix H then maps this state estimate to the expected measurement space. The state transition matrix A assumes that the rocket has a constant velocity. </p>

<p>The filter accounts for uncertainties in both the process model (via the process noise covariance matrix Q) and the measurements (via the measurement noise covariance matrix R). These uncertainties are crucial for calculating the Kalman gain K, which determines how much the filter should trust the prediction versus the new measurement and gives a tradeoff between the preiction and measurment. The error covariance matrix P represents the filter's confidence in its state estimate and is updated with each iteration.</p>

<center><img src="kalman2d.jpg" alt="" width="600" height="400"></center>

<script src="https://gist.github.com/redaelhail/929cda89accd8cdd4a8860c5167c6e37.js"></script>

<h2>Extended Kalman Filter (EKF)</h2>

<p>Extended Kalman Filters are an adaptation of the standard Kalman filter for nonlinear systems. They work by linearizing the nonlinear functions around the current estimate using partial derivatives. This linearization allows the filter to apply the standard Kalman filter equations to nonlinear problems. While more versatile than standard Kalman filters, EKFs can struggle with highly nonlinear systems. </p>

<center><img src="EKF_example.jpg" alt="" width="600" height="400"></center>

<p>The following code is an implementation of an amazing example given in <a href="https://stanford.edu/class/ee363/lectures/ekf.pdf"> Stanford University course </a> about EKFs. This problem describes a vehicle tracking scenario using a state-space model. The vehicle's position and velocity evolve according to a linear dynamic system with random noise. Measurements are taken as noisy distance readings from the vehicle to nine fixed points in space. The challenge is to estimate the vehicle's true position and velocity given these noisy, nonlinear measurements.</p>

<script src="https://gist.github.com/redaelhail/5cca336ddf7b63bdd3f67bccc6a5ea9f.js"></script>