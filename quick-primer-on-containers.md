---
title: "(Bonus) Quick primer on Containers"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

   - What is a container and why would I want to publish one?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

  - Learn high-level overview of containers
  - Learn what applications fit better for containerization

::::::::::::::::::::::::::::::::::::::::::::::::



# Containers

[Containers][containers-google] are your packaged software application together with dependencies.
This sounds like a packaged library but these are different.
Containers serve more like executables and run very "isolated" from the system they are run on.
They bundle together such things like specific versions of programming language runtimes and libraries required to run.

Some of the typical benefits advertised for containers is:
* very portable
* lighweight (relative to things like virtual machines)
* application isolation on a system

They are very popular in any web-based software like websites, databases or software services (i.e. REST APIs)

And they can be used for documentation websites!

[static-page]: https://en.wikipedia.org/wiki/Static_web_page
[containers-google]: https://cloud.google.com/learn/what-are-containers

## Difference between "Container" and "Container Image"

So you might say _"Okay, we are going to make a container as our documentation software artifact, right?"_

Not exactly... we are going to make a _container image_.

The difference is that a container image is _built_, usually from another base container image like base OS images (i.e. Ubuntu and Windows Server for operating systems and NGINX for web servers)

Then _containers_ are created from _container images_, with runtime modifications appied for each individual container. So two containers could come from the same container image but would differ by the environmental variables that are set for each or different data put inside each container.

An analogy is there is one version of Windows 11 operating system.
It is installed on many Windows computers around the world.

Yet, each individual Windows computer far from identical since people modify their personal computers: different backgrouns, different files on them, different CPU resources for each computer.

So the artifact we want to produce is a _container image_.

Then, the User of the documentation container image can modify it at runtime, 
such as web server configurations.

# Docker

One of arguably the most popular container image tools is [Docker][docker].

Docker is widely used by developers to create software artifacts for web-based applications.
They are also super handy for development like in testing with multiple software services (i.e. run with a database, a software service, and a website locally on your machine).

We will go over very, very little about using Docker in this lesson.
There are [other Carpentries][carpentry-docker] which do go over using Docker for scientific computing for the interested reader.

::::::::::::::::::::::::::::::::::::: callout 

## What we will need
The very high-level Docker understanding we need is:
* A container image is created in Docker using a `Dockerfile`: a file that defines the container image build
* The command `docker build --file Dockerfile --tag <name of image> .` (or shorthand, `docker build -t <name of image> .`) will build the container image
* The command `docker run <name of image>` will spin up a container from the container image and run it

::::::::::::::::::::::::::::::::::::::::::::::::

We will simply be using Docker as a sort of "blackbox" tool to help perform our CI/CD operations in GitHub Actions for documentation artifact:
* Build the documentation in a container image
* Publish the container image ready to run our documentation server
* (_not covered here_) Deploy this container on a server, cloud-service, or container orchestration platform to serve our documentation to the Users.

# DockerHub

We discussed containers vs. container images.
And we stated that container images are the artifact we want to produce.
Yet, where to we put this artifact?

We had [PyPi](https://pypi.org/) for Python packages.

For Docker and container images, we mainly have [DockerHub](https://hub.docker.com/)

DockerHub is called a container image registry.
We can "push" our container images to DockerHub for others to be able to "pull" them down to re-use them.

DockerHub is not the only container image registry software.
It is probably the most popular.
But for now, you can consider it equivalent to PyPi but for containers instead of Python packages.

::::::::::::::::::::::::::::::::::::: callout 

## What we will need
The very high-level Docker understanding we need is:
* After building an image, we can put it in a container image registry via `docker push <name of image>`
* We can retrieve an image from a container image registry via `docker pull <name of image>`
* We can rename a contianer image via `docker tag <original tag> <new tag>`

::::::::::::::::::::::::::::::::::::::::::::::::

# Overall Docker Architecture

This graphic helps illustrate what we are doing with our `docker build`, `docker run`, `docker pull` and `docker push` commands we will be using:

![Docker arch](fig/docker-architecture.png){alt='Diagram of Docker Architecture'}

[image source](https://jerrelysan.blogspot.com/2021/06/enter-in-docker-container-what-is.html)


## Wrap up

Now we have an idea of what we what to do for documentation CI/CD
and a tool to use for performing these operations (Docker).

Next, we will take these together and attempt to publish a container image as a documentation artifact.

[docker]: https://docs.docker.com/get-docker/
[carpentry-docker]: https://hsf-training.github.io/hsf-training-docker/


::::::::::::::::::::::::::::::::::::: keypoints 

  - Containers are a very common packaging artifact for web-based applications 

::::::::::::::::::::::::::::::::::::::::::::::::