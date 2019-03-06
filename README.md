Repository to hold various Docker and singularity container building scripts


## What are containers?

Containers according to Amazon:

>Containers provide a standard way to package your application's code, configurations, and dependencies into a single object.
>Containers share an operating system installed on the server and run as resource-isolated processes, ensuring quick,
>reliable, and consistent deployments, regardless of environment.

As far as our group is concerned, we use containers to be able to run the same piece of code on
the various compute platforms we ~can get our hands on~ have access to. This is primary the Tufts cluster, which requires
us to put our code into `Singularity` containers.

## What's the repo for?

We hold instructions on how to build particularly useful containers for our work.
In addition to packing up the code, containers can be built on top of another allow us to build, for example,
a container holding the common dependencies of our different software packages.

This allows one to build a container for a specific analysis without having to repackage the whole stack of code again.

## Are you going to make me build all of these myself?

No! We keep copies of the containers on our [dockerhub](dockerhub) and [singularity](https://www.singularity-hub.org/collections/2494) hub pages.

# Containers (and the heirarchy)

![Alt text](https://g.gravizo.com/source/custom_mark10?https%3A%2F%2Fraw.githubusercontent.com%2FLArbys%2Flarbys-containers%2Fmaster%2Fcontainer_graph.dot)

| Container | Descripton |
|:---------:|:-----------|
| ubuntu    | official ubuntu containers on [dockerhub](https://hub.docker.com/_/ubuntu) |
| ROOT      | build of CERN's [ROOT](https://github.com/root-project/root) data-analysis library |
| OpenCV    | open source [library](https://github.com/opencv/opencv) of computer vision algorithms |
| PyTorch   | deep learning [library](https://pytorch.org/) |
| SubConvNet | includes submanifold convolution library for pytorch |
| dllee_unified   | current-gen analysis code for MicroBooNE DL low-energy excess analysis |
| ubdl      | repository with next-gen LArbys tools for MicroBooNE DL-working group analysis |

## Specific Versions

Here we list official stack versions to be used for production and analysis studies

| Stack Name | ubuntu     | ROOT    | OpenCV | PyTorch   | SubConvNet | dllee_unified | ubdl  |
|:----------:| -----------|---------|--------| --------- | ---------- | ------------- | ----- |
|  mcc8jan19 |  16.04 LTS | 6.14/06 | 3.2    | 1.0.1post | tagXXXXXX  | tagXXXXXXXX   |  n/a  |
|  mcc9jan19 |  18.04 LTS | 6.16/00 | 3.2    | 1.0.1post | tagXXXXXX  |    n/a        |  tagxxxx |

# Instructions

We use two packages: [docker](https://www.docker.com/why-docker) and [singularity](https://www.sylabs.io/singularity/).

Typically, we will use `docker` to build the containers and then convert the docker image into a `singularity` container.

In the end, it is not important what tool we use to build the containers (one could use just singularity), but ultimately we must end up with a singularity container to run on the Tufts cluster.  (The reason is that docker is not supported on the cluster due to security concerns with docker.)

You can run both docker and singularity from your personal machine. You can also use lab machines at:

* Tufts: meitner, rubin
* MIT: nudot

to build your containers.


