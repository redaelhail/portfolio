---
title: "Radar signal processing for target detection and tracking"
date: 2023-08-11T10:33:41Z
draft: false
markup: html
---

<p> Frequency Modulated Continuous Wave Radar (FMCW) is a sensor widely used in <a href="https://dev.ti.com/tirex/explore/node?node=A__AHbHS9Bx3XPwFpb927.8dw__radar_toolbox__1AslXXD__LATEST">different applications</a>. However, processing the FMCW might be challenging at first look. In this blogpost, I will try to go over all the steps of radar signal processing. Before that, some concepts will be explained to give an idea how FMCW radar data looks like.</p>

<h2 id="chirp">Chirp </h2>
A chirp is the signal emitted by the radar's antennas. It has a frequency that is linearly increasing with time.


<img src="images/chirp.png" alt="Frame composed of N chirps" width="400" height="200">

<ul>
<li>Start frequency (60Ghz) & End frequency (64 GHz) </li>
<li>Bandwidth: Difference between end and start frequency</li>
<li>Slope</li>
<li>Chirping time: Sweep time</li>
</ul>

<p>These parameters define the range resolution of the radar by the following formula:<code>d<sub>res</sub> = c / (2B)</code> where <code>c</code> is the speed of light and <code>B</code> is the bandwidth.</p>

<h2 id="Frame">Frame</h2>

<p>A frame consists of a sequence of chirps transmitted sequentially by an antenna. The key parameters defining a frame are:</p>

<ul>
  <li>Number of chirps</li>
  <li>Frame duration</li>
</ul>

<img src="images/frame.png" alt="Frame composed of N chirps" width="700" height="200">


<p>These parameters directly influence the Doppler resolution, which can be calculated using the following formula:<code>v = &lambda; / (2 &middot; T<sub>f</sub>)</code> where <code>T<sub>f</sub></code> is the frame time. Additionally, the maximum Doppler value is determined by:<code>v<sub>max</sub> = &lambda; / (4 &middot; T<sub>c</sub>)</code>. The relationship between frame time and chirp duration is given by <code>T<sub>f</sub> = N &middot; T<sub>c</sub></code>, where <code>N</code> is the number of chirps and <code>T<sub>c</sub></code> is the duration of a single chirp.</p>

It is important to choose the right values of the parameters defined above, because they directly define the 4 defining parameters of any application: Doppler and range resolution and maximum range and Doppler values.

<h2 id="Virtual antenna"> Antenna Array </h2>

<p>An antenna array in FMCW radar consists of multiple antenna elements arranged in a specific configuration, such as a linear or planar array. These elements work together to transmit and receive radar signals, enhancing the radar's ability to detect and locate objects. By using an array, the radar can achieve higher angular resolution, allowing to distinguish between targets that are close together in angle.</p>

<p>The image below shows an example of a planar antenna array. The left part of the image shows an architecture of a radar with 3 trasmit antenas and 4 receive antennas. The right part of the image shows the resulting virtual array. This virtual array allows for 2 linear arrays in both azimuth and elevation directions enhancing the radar angle resolution and signal to noise ratio (SNR).</p>
<img src="images/array.png" alt="Virtual antenna array" width="700" height="200">

<h2 id="Radar data"> Radar data</h2>


Let's visualize the radar data structure, because that is the most important step to start the signal processing and understand how the radar data is organized.

