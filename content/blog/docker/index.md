---
title: "Docker for code developement and deployement on a RaspberryPi"
date: 2023-09-07T07:26:41Z
draft: false
tags: [Docker]
---

## Intro

Lately, I had to develop a demo working on a raspberrypi and send it to some project partners. However, it was very difficult in the beginning, since whenever I change something and push it to GitHub, they were not aware of that, and also faced problems with dependecies sometimes. So I decided to solve this problem and use a Docker container.

The first step was to check if Docker runs on RaspberryPi, which is the case. Then, I used the same Docker container and use on my laptop with a bind mount to develop code on the computer. It's much more easier. Finally when the demo is working on the laptop, I push it to GitHub, and a GitHub action is automatically making a new Docker image and build it in Docker Hub. This image can be then pulled by the rasperrypi and garenteed to work.

This was a high level description. Let's get into the steps I followed. 

## 1. Install Docker runtime

To install Docker runtime on a RaspberryPi, it's straightforward by following this [steps](https://docs.docker.com/engine/install/raspberry-pi-os/).

## 2. Write a **DockerFile**:

This Dockerfile sets up a Python environment, installs dependencies from requirements_pi.txt, and specifies the command to run the application. I intentinally used `python:3.11-slim-bookworm` for its low memory size since I am deploying on an edge device.


    # Python runtime as the base image
    FROM python:3.11-slim-bookworm

    # Set the working directory in the container
    WORKDIR ./demonstrator

    # Copy the requirements file into the container
    COPY requirements_pi.txt .

    # Install any needed packages specified in requirements.txt
    RUN pip install --upgrade pip
    RUN pip install --no-cache-dir -r requirements_pi.txt

    # Command to run your application
    CMD ["python", "main.py"]

## 3. Set up **compose.yml** file

This Docker Compose file sets up a service named `python_env` to build an image using the Dockerfile in the current directory. It mounts the current directory to `/demonstrator` in the container, runs the command `python main.py`, and ensures the service restarts automatically.


    version: '3.8'

    services:
    python_env:
        build:
        context: .
        dockerfile: Dockerfile

        working_dir: /demonstrator
        volumes:
        - .:/demonstrator
        privileged: true
        command: ["python", "main.py"]
        restart: always


Once the Dockerfile and compose file  are ready, it is possible to edit code on the host machine and run it in an Docker container since we are using a bind mount. we can now do code developement and test on the laptop before deploying on the RaspberryPi.

In other words, starting a container with bind mounts allows to use a container to run tools that we don't want to install on our host, and yet still work with our host's files.

## 4. Run

Finally, launching the demo is simply done with a single command: `docker compose up`
