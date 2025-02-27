---
title: "Docker for code developement and deployement on a RaspberryPi"
date: 2024-10-07T07:26:41Z
draft: false
tags: [Docker]
cover:
  image: "Pictu.png"
  alt: "<alt text>"
  caption: "<text>"
  relative: false # To use relative path for cover image, used in hugo Page-bundles
---


![alt text](./Pictu.png)

## Intro

Lately, I had to develop a demo working on a raspberrypi and send it to other project partners. However, we faced many difficulties in the beginning, since whenever I change something and push it to GitHub, they were not aware of that, and also faced problems with dependecies sometimes to make the demo work on their devices. So I decided to solve this problem and use a Docker container. I knew that with Docker you can run any application on any environemnt. But I was not aware it could also work on a Raspebrry Pi.

Moreover, Docker bind mounts allow for code developement. This is what I used to develop code on laptop first. Then when the demo is working, it is pushed to GitHub. A GitHub action workflow is automatically activated on every push action. This github worflow builds a  Docker image and publish it in Docker Hub. This image can be then pulled by the rasperrypi and garenteed to work.

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

## 5. CI pipeline

GitHub Actions is a CI/CD tool that we'll use to automate our pipeline. I first create a new file .github/workflows/ci.yml in the project repository with the following contents

    name: ci

    on:
      push:
        branches:
          - main

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
          -
            name: Checkout
            uses: actions/checkout@v4
          -
            name: Login to Docker Hub
            uses: docker/login-action@v3
            with:
              username: ${{ secrets.DOCKER_USERNAME }}
              password: ${{ secrets.DOCKERHUB_TOKEN }}
          -
            name: Set up Docker Buildx
            uses: docker/setup-buildx-action@v3
          -
            name: Build and push
            uses: docker/build-push-action@v5
            with:
              context: .
              push: true
              tags: ${{ secrets.DOCKER_USERNAME }}/${{ github.event.repository.name }}:latest

This workflow will run on every push to the main branch. It first checks the latest code version from the repository, then it logs in to Docker hub. Afterwards, it builds and pushs the Docker image to container registry DockerHub.