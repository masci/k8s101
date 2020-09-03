# Intro to Kubernetes

Welcome to this introductory course to Kubernetes!

![Photo by Joseph Barrientos on Unsplash](img/joseph-barrientos-eUMEWE-7Ewg-unsplash.jpg)

## Audience

If you never operated a cluster, not even as a user, this course is for you. If you had previous
exposure to the matter through webinars, blog posts, demos, this course might still be not too
boring but if you know the difference between a Pod and a container, a Deployment and a Service,
or you know what an Ingress does, well you might skip it.

## Prerequisites

Despite being a 101 course, a solid understanding of Docker fundamentals (containers, images,
registry, etc.) is required.

## Setup

Before starting, please [install Homebrew](https://brew.sh/) and follow these instructions in
order to setup your laptop. Some system-wide binaries will be installed with `brew` while we
are going to use [asdf-vm](https://asdf-vm.com/) for some specific programs like `kubectl` in
order to have a better control over installes versions:

* Install system-wide required binaries by running `brew bundle`
* Install the following [asdf-vm](https://asdf-vm.com/) plugins by running:
    * `asdf plugin-add kubectl`
    * `asdf plugin-add helm`
* Finally, install versioned dependencies with `asdf install`
* Git clone [https://github.com/masci/k8s101/](https://github.com/masci/k8s101/) and perform the exercises from the root of the repo

## kubectl create course

You can now move to [the first batch of exercises](010-first-contact.md) to start the course.
