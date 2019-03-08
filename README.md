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
| ubuntu    | [nvidia's containers](9.2-cudnn7-devel, 9.2-cudnn7-devel-ubuntu16.04) which include cuda and cuDNN libraries |
| ROOT      | build of CERN's [ROOT](https://github.com/root-project/root) data-analysis library |
| OpenCV    | open source [library](https://github.com/opencv/opencv) of computer vision algorithms |
| PyTorch   | deep learning [library](https://pytorch.org/) |
| SparseConvNet | includes submanifold convolution library for pytorch |
| dllee_unified   | current-gen analysis code for MicroBooNE DL low-energy excess analysis |
| ubdl      | repository with next-gen LArbys tools for MicroBooNE DL-working group analysis |

## Specific Versions

Here we list official stack versions to be used for production and analysis studies

| Stack Name | ubuntu     | ROOT    | OpenCV | PyTorch   | SubConvNet | dllee_unified | ubdl  |
|:----------:| -----------|---------|--------| --------- | ---------- | ------------- | ----- |
|  mcc8jan19 |  16.04 LTS+CUDA 9.0+cuDNN 7 | 6.14/06 | 3.2    | 1.0.1post | tagXXXXXX  | tagXXXXXXXX   |  n/a  |
|  mcc9jan19 |  18.04 LTS+CUDA 10.0+cuDNN 7 | 6.16/00 | 3.4    | 1.0.1post | tagXXXXXX  |    n/a        |  tagxxxx |

## Built containers on Tufts

On the Tufts Cluster you can find the containers at:

    /cluster/tufts/wongjiradlab/larbys/larbys-containers

# Instructions

We use two packages: [docker](https://www.docker.com/why-docker) and [singularity](https://www.sylabs.io/singularity/).

Typically, we will use `docker` to build the containers and then convert the docker image into a `singularity` container.

In the end, it is not important what tool we use to build the containers (one could use just singularity), but ultimately we must end up with a singularity container to run on the Tufts cluster.  (The reason is that docker is not supported on the cluster due to security concerns with docker.)

You can run both docker and singularity from your personal machine. You can also use lab machines at:

* Tufts: meitner, rubin
* MIT: nudot

to build your containers.


## What do I need to build a container?

(still under construction)

In general, you just need to know the instructions you'd type to install the software in question. 
You put those instructions into a recipe file and tell docker or singularity to build the container.

As an example, we will use the anticipated most-likely case, which is to make a container with a new version of analysis code (`dllee_unified`).

In the `dllee_unified` folder, you'll see a file called `dllee_unifiee.singularity` which contains the instructions to run to build the dllee_unified repository. It'll look something that the following:

```
bootstrap: docker
From: larbys/pytorch:6.12.04_cuda8.0

%post
  export USER=your-name-here
  cp /tmp/$USER/krb5.conf /etc/
  cp -r /tmp/$USER/grid-security /etc/$USER/
  cp /tmp/$USER/vomses /etc/
  apt-get update -y
  apt-get install -y voms-clients krb5-user jacksum emacs vim  
  mkdir -p /cluster/home
  mkdir -p /cluster/kappa
  mkdir -p /cluster/shared
  mkdir -p /opt/shared

%environment

```




