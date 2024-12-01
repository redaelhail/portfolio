---
title: "Visualizing optimization algorithms"
date: 2023-05-01T07:21:52Z
draft: false
markup: html
tags: [Machine Llearning]
---


<h2>1. Gradient Descent</h2>

Gradient descent is an optimization algorithm that iteratively adjusts model parameters to minimize a function, typically a loss function. By moving in the direction of the steepest decrease, determined by the gradient, it helps find the optimal parameters that best fit the data.

<p><strong>Equation:</strong></p>
<p>θ = θ - α * ∇J(θ)</p>
<p><strong>Description:</strong></p>
<ul>
  <li><strong>θ:</strong> Parameters (weights) of the model being optimized.</li>
  <li><strong>α (alpha):</strong> Learning rate, determines the size of the steps taken during optimization.</li>
  <li><strong>∇J(θ):</strong> Gradient of the cost function with respect to the parameters.</li>
</ul>

<h2>2. Stochastic Gradient Descent (SGD)</h2>
Unlike traditional gradient descent, which uses the entire dataset, SGD updates the model using a single or small batch of data points per iteration. This makes it faster and helps avoid local minima, though it can lead to a noisier convergence path.
<p><strong>Equation:</strong></p>
<p>θ = θ - α * ∇J(θ, xi, yi)</p>
<p><strong>Description:</strong></p>
<ul>
  <li><strong>θ:</strong> Parameters (weights) of the model being optimized.</li>
  <li><strong>α (alpha):</strong> Learning rate, determines the size of the steps taken during optimization.</li>
  <li><strong>∇J(θ, xi, yi):</strong> Gradient of the cost function with respect to the parameters computed on a single training example (xi, yi).</li>
</ul>

<h2>3. Momentum</h2>
Momentum is an optimization technique used in machine learning to accelerate gradient descent by taking into account the past gradients when updating model parameters. Instead of just relying on the current gradient, momentum adds a fraction of the previous update to the current one. This helps in smoothing out the updates, allowing the algorithm to maintain direction and avoid oscillations, especially in regions with steep gradients. By building up velocity in directions of consistent gradients, momentum helps the optimization process converge faster and can help the model escape local minima.
<p><strong>Equation:</strong></p>
<p>v = β * v + (1 - β) * ∇J(θ)</p>
<p>θ = θ - α * v</p>
<p><strong>Description:</strong></p>
<ul>
  <li><strong>θ:</strong> Parameters (weights) of the model being optimized.</li>
  <li><strong>α (alpha):</strong> Learning rate, determines the size of the steps taken during optimization.</li>
  <li><strong>β:</strong> Momentum coefficient, controls the influence of the past gradients.</li>
  <li><strong>v:</strong> Momentum term, which accumulates the exponentially decaying moving average of past gradients.</li>
  <li><strong>∇J(θ):</strong> Gradient of the cost function with respect to the parameters.</li>
</ul>

<h2>4. RMSprop (Root Mean Square Propagation)</h2>
Root Mean Square Propagation is an adaptive learning rate optimization algorithm. It adjusts the learning rate for each parameter based on the average of the squared gradients from previous iterations. By keeping a moving average of the squared gradients, RMSProp helps mitigate the issue of vanishing or exploding gradients, ensuring more stable and efficient convergence.
<p><strong>Equation:</strong></p>
<p>v = β * v + (1 - β) * (∇J(θ))^2</p>
<p>θ = θ - α * ∇J(θ) / (sqrt(v) + ε)</p>
<p><strong>Description:</strong></p>
<ul>
  <li><strong>θ:</strong> Parameters (weights) of the model being optimized.</li>
  <li><strong>α (alpha):</strong> Learning rate, determines the size of the steps taken during optimization.</li>
  <li><strong>β:</strong> RMSprop decay rate, controls the exponential moving average of squared gradients.</li>
  <li><strong>v:</strong> RMSprop term, which accumulates the exponentially decaying moving average of squared gradients.</li>
  <li><strong>∇J(θ):</strong> Gradient of the cost function with respect to the parameters.</li>
  <li><strong>ε (epsilon):</strong> Small constant to prevent division by zero.</li>
</ul>


<h2>5. Adam (Adaptive Moment Estimation)</h2>
Adam combines the benefits of two other methods: momentum and RMSprop. It computes adaptive learning rates for each parameter by maintaining exponentially decaying averages of past gradients (momentum) and their squared values (RMSprop). This approach allows Adam to adjust the learning rate dynamically, leading to faster convergence and better handling of noisy or sparse gradients.
<p><strong>Equation:</strong></p>
<p>m = β1 * m + (1 - β1) * ∇J(θ)<br>
v = β2 * v + (1 - β2) * (∇J(θ))^2<br>
θ = θ - α * m / (sqrt(v) + ε)</p>
<p><strong>Description:</strong></p>
<ul>
  <li><strong>θ:</strong> Parameters (weights) of the model being optimized.</li>
  <li><strong>α (alpha):</strong> Learning rate, determines the size of the steps taken during optimization.</li>
  <li><strong>β1, β2:</strong> Exponential decay rates for the moment estimates.</li>
  <li><strong>m:</strong> First moment (mean) estimate.</li>
  <li><strong>v:</strong> Second moment (uncentered variance) estimate.</li>
  <li><strong>∇J(θ):</strong> Gradient of the cost function with respect to the parameters.</li>
  <li><strong>ε (epsilon):</strong> Small constant to prevent division by zero.</li>
</ul>


<div id="visualization-container"></div>

<style>
.sgd {
    stroke: black;
}

.momentum {
    stroke: blue;
}

.rmsprop {
    stroke: red;
}

.adam {
    stroke: green;
}

.SGD {
    fill: black;
}

.Momentum {
    fill: blue;
}

.RMSProp {
    fill: red;
}

.Adam {
    fill: green;
}

circle:hover {
  fill-opacity: .3;
}
</style>
<body>
<script src="https://d3js.org/d3.v4.min.js"></script>
<script src="https://d3js.org/d3-contour.v1.min.js"></script>
<script src="https://d3js.org/d3-scale-chromatic.v1.min.js"></script>
<script>

var width = 700,
    height = 500,
    nx = parseInt(width / 5), // grid sizes
    ny = parseInt(height / 5),
    h = 1e-7, // step used when approximating gradients
    drawing_time = 30; // max time to run optimization

var svg = d3.select("#visualization-container")
            .append("svg")
            .attr("width", width)
            .attr("height", height);

// Parameters describing where function is defined
var domain_x = [-2, 2],
    domain_y = [-2, 2],
    domain_f = [-2, 8],
    contour_step = 0.5; // Step size of contour plot

var scale_x = d3.scaleLinear()
                .domain([0, width])
                .range(domain_x);

var scale_y = d3.scaleLinear()
                .domain([0, height])
                .range(domain_y);

var thresholds = d3.range(domain_f[0], domain_f[1], contour_step);

var color_scale = d3.scaleLinear()
    .domain(d3.extent(thresholds))
    .interpolate(function() { return d3.interpolateYlGnBu; });

var function_g = svg.append("g").on("mousedown", mousedown),
    gradient_path_g = svg.append("g"),
    menu_g = svg.append("g");

/*
 * Set up the function and gradients
 */

/* Value of f at (x, y) */
function f(x, y) {
    return -2 * Math.exp(-((x - 1) * (x - 1) + y * y) / .2) + -3 * Math.exp(-((x + 1) * (x + 1) + y * y) / .2) + x * x + y * y;
}

/* Returns gradient of f at (x, y) */
function grad_f(x,y) {
    var grad_x = (f(x + h, y) - f(x, y)) / h
        grad_y = (f(x, y + h) - f(x, y)) / h
    return [grad_x, grad_y];
}


/* Returns values of f(x,y) at each point on grid as 1 dim array. */
function get_f_values(nx, ny) {
    var grid = new Array(nx * ny);
    for (i = 0; i < nx; i++) {
        for (j = 0; j < ny; j++) {
            var x = scale_x( parseFloat(i) / nx * width ),
                y = scale_y( parseFloat(j) / ny * height );
            // Set value at ordering expected by d3.contour
            grid[i + j * nx] = f(x, y);
        }
    }
    return grid;
}

/*
 * Set up the contour plot
 */

var contours = d3.contours()
    .size([nx, ny])
    .thresholds(thresholds);

var f_values = get_f_values(nx, ny);

function_g.selectAll("path")
          .data(contours(f_values))
          .enter().append("path")
          .attr("d", d3.geoPath(d3.geoIdentity().scale(width / nx)))
          .attr("fill", function(d) { return color_scale(d.value); })
          .attr("stroke", "none");

/*
 * Set up buttons
 */
var draw_bool = {"SGD" : true, "Momentum" : true, "RMSProp" : true, "Adam" : true};

var buttons = ["SGD", "Momentum", "RMSProp", "Adam"];

menu_g.append("rect")
      .attr("x", 0)
      .attr("y", height - 40)
      .attr("width", width)
      .attr("height", 40)
      .attr("fill", "white")
      .attr("opacity", 0.2);

menu_g.selectAll("circle")
      .data(buttons)
      .enter()
      .append("circle")
      .attr("cx", function(d,i) { return width/4 * (i + 0.25);} )
      .attr("cy", height - 20)
      .attr("r", 10)
      .attr("stroke-width", 0.5)
      .attr("stroke", "black")
      .attr("class", function(d) { console.log(d); return d;})
      .attr("fill-opacity", 0.5)
      .attr("stroke-opacity", 1)
      .on("mousedown", button_press);

menu_g.selectAll("text")
      .data(buttons)
      .enter()
      .append("text")
      .attr("x", function(d,i) { return width/4 * (i + 0.25) + 18;} )
      .attr("y", height - 14)
      .text(function(d) { return d; })
      .attr("text-anchor", "start")
      .attr("font-family", "Helvetica Neue")
      .attr("font-size", 15)
      .attr("font-weight", 200)
      .attr("fill", "white")
      .attr("fill-opacity", 0.8);

function button_press() {
    var type = d3.select(this).attr("class")
    if (draw_bool[type]) {
        d3.select(this).attr("fill-opacity", 0);
        draw_bool[type] = false;
    } else {
        d3.select(this).attr("fill-opacity", 0.5)
        draw_bool[type] = true;
    }
}

/*
 * Set up optimization/gradient descent functions.
 * SGD, Momentum, RMSProp, Adam.
 */

function get_sgd_path(x0, y0, learning_rate, num_steps) {
    var sgd_history = [{"x": scale_x.invert(x0), "y": scale_y.invert(y0)}];
    var x1, y1, gradient;
    for (i = 0; i < num_steps; i++) {
        gradient = grad_f(x0, y0);
        x1 = x0 - learning_rate * gradient[0]
        y1 = y0 - learning_rate * gradient[1]
        sgd_history.push({"x" : scale_x.invert(x1), "y" : scale_y.invert(y1)})
        x0 = x1
        y0 = y1
    }
    return sgd_history;
}

function get_momentum_path(x0, y0, learning_rate, num_steps, momentum) {
    var v_x = 0,
        v_y = 0;
    var momentum_history = [{"x": scale_x.invert(x0), "y": scale_y.invert(y0)}];
    var x1, y1, gradient;
    for (i=0; i < num_steps; i++) {
        gradient = grad_f(x0, y0)
        v_x = momentum * v_x - learning_rate * gradient[0]
        v_y = momentum * v_y - learning_rate * gradient[1]
        x1 = x0 + v_x
        y1 = y0 + v_y
        momentum_history.push({"x" : scale_x.invert(x1), "y" : scale_y.invert(y1)})
        x0 = x1
        y0 = y1
    }
    return momentum_history
}

function get_rmsprop_path(x0, y0, learning_rate, num_steps, decay_rate, eps) {
    var cache_x = 0,
        cache_y = 0;
    var rmsprop_history = [{"x": scale_x.invert(x0), "y": scale_y.invert(y0)}];
    var x1, y1, gradient;
    for (i = 0; i < num_steps; i++) {
        gradient = grad_f(x0, y0)
        cache_x = decay_rate * cache_x + (1 - decay_rate) * gradient[0] * gradient[0]
        cache_y = decay_rate * cache_y + (1 - decay_rate) * gradient[1] * gradient[1]
        x1 = x0 - learning_rate * gradient[0] / (Math.sqrt(cache_x) + eps)
        y1 = y0 - learning_rate * gradient[1] / (Math.sqrt(cache_y) + eps)
        rmsprop_history.push({"x" : scale_x.invert(x1), "y" : scale_y.invert(y1)})
        x0 = x1
        y0 = y1
    }
    return rmsprop_history;
}

function get_adam_path(x0, y0, learning_rate, num_steps, beta_1, beta_2, eps) {
    var m_x = 0,
        m_y = 0,
        v_x = 0,
        v_y = 0;
    var adam_history = [{"x": scale_x.invert(x0), "y": scale_y.invert(y0)}];
    var x1, y1, gradient;
    for (i = 0; i < num_steps; i++) {
        gradient = grad_f(x0, y0)
        m_x = beta_1 * m_x + (1 - beta_1) * gradient[0]
        m_y = beta_1 * m_y + (1 - beta_1) * gradient[1]
        v_x = beta_2 * v_x + (1 - beta_2) * gradient[0] * gradient[0]
        v_y = beta_2 * v_y + (1 - beta_2) * gradient[1] * gradient[1]
        x1 = x0 - learning_rate * m_x / (Math.sqrt(v_x) + eps)
        y1 = y0 - learning_rate * m_y / (Math.sqrt(v_y) + eps)
        adam_history.push({"x" : scale_x.invert(x1), "y" : scale_y.invert(y1)})
        x0 = x1
        y0 = y1
    }
    return adam_history;
}


/*
 * Functions necessary for path visualizations
 */

var line_function = d3.line()
                      .x(function(d) { return d.x; })
                      .y(function(d) { return d.y; });

function draw_path(path_data, type) {
    var gradient_path = gradient_path_g.selectAll(type)
                        .data(path_data)
                        .enter()
                        .append("path")
                        .attr("d", line_function(path_data.slice(0,1)))
                        .attr("class", type)
                        .attr("stroke-width", 3)
                        .attr("fill", "none")
                        .attr("stroke-opacity", 0.5)
                        .transition()
                        .duration(drawing_time)
                        .delay(function(d,i) { return drawing_time * i; })
                        .attr("d", function(d,i) { return line_function(path_data.slice(0,i+1));})
                        .remove();

    gradient_path_g.append("path")
                   .attr("d", line_function(path_data))
                   .attr("class", type)
                   .attr("stroke-width", 3)
                   .attr("fill", "none")
                   .attr("stroke-opacity", 0.5)
                   .attr("stroke-opacity", 0)
                   .transition()
                   .duration(path_data.length * drawing_time)
                   .attr("stroke-opacity", 0.5);
}

/*
 * Start minimization from click on contour map
 */

function mousedown() {
    /* Get initial point */
    var point = d3.mouse(this);
    /* Minimize and draw paths */
    minimize(scale_x(point[0]), scale_y(point[1]));
}

function minimize(x0,y0) {
    gradient_path_g.selectAll("path").remove();

    if (draw_bool.SGD) {
        var sgd_data = get_sgd_path(x0, y0, 2e-2, 500);
        draw_path(sgd_data, "sgd");
    }
    if (draw_bool.Momentum) {
        var momentum_data = get_momentum_path(x0, y0, 1e-2, 200, 0.8);
        draw_path(momentum_data, "momentum");
    }
    if (draw_bool.RMSProp) {
        var rmsprop_data = get_rmsprop_path(x0, y0, 1e-2, 300, 0.99, 1e-6);
        draw_path(rmsprop_data, "rmsprop");
    }
    if (draw_bool.Adam) {
        var adam_data = get_adam_path(x0, y0, 1e-2, 100, 0.7, 0.999, 1e-6);
        draw_path(adam_data, "adam");
    }
}

</script>

<div>
    <p>Click anywhere on the function heatmap to start a minimization. You can toggle the different algorithms (SGD, Momentum, RMSProp, Adam) by clicking on the circles in the lower bar.</p>
    <p>The global minimum is on the left. A local minimum is found on the right. Interestingly, different initializations make some algorithms converge to the local minimum while others converge to the global minimum.</p>
    <p><em>Note:</em> The learning rate is 1e-2 for Adam, SGD with Momentum and RMSProp, while it is 2e-2 for SGD (to make it converge faster)</p>
</div>

<div>
So this demo sows how quickly Adam optmizer converges with respect to other optimizers due to intoduced momenteum term in the opitimization problem. The source code of this visualization is from this <a href ="https://github.com/EmilienDupont/optimization-visualization">GitHub repo of Emilien Dupont.</a>
</div>