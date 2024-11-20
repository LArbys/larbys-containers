Repository to hold various Docker and singularity container building scripts


## What are containers?

Containers according to Amazon:

>Containers provide a standard way to package your application's code, configurations, and dependencies into a single object.
>Containers share an operating system installed on the server and run as resource-isolated processes, ensuring quick,
>reliable, and consistent deployments, regardless of environment.

As far as our group is concerned, we use containers to be able to run the same piece of code on
the various compute platforms we have access to.
This is primary the Tufts cluster, which requires us to put our code into `Singularity` containers.

## What's the repo for?

We hold instructions on how to build particularly useful containers for our work.
In addition to packing up the code, containers can be built on top of another allow us to build, for example,
a container holding the common dependencies of our different software packages.

This allows one to build a container for a specific analysis without having to repackage the whole stack of code again.

## Are you going to make me build all of these myself?

No! We keep copies of the containers on dockerhub and singularity hub:

* Dockerhub [ubdl/larbys](https://hub.docker.com/r/larbys/ubdl)
* [Singularity](https://www.singularity-hub.org/collections/2494)

# Containers (and the heirarchy)

Current building chain as of 2024/11/20.

In reverse order:

  * `minkowski/singularity_minkowskiengine_u20.04.cu111.torch1.9.0`
  * `minkowski/Dockerfile_minkowski_u20.04_cu111_torch1.9.0`: [larbys/ubdl:u20.04_cu11.1_torch1.9_root6.24.02_minkowski_xgboost](https://hub.docker.com/layers/larbys/ubdl/u20.04_cu11.1_torch1.9_root6.24.02_minkowski_xgboost/images/sha256-5d55750df04b558e04c73301a4c25d8044f50fcc9a7dc290b84b42c96b3f2f8b?context=repo)
  * `root/Dockerfile_ubuntu20.04_root6.24.02_py3_fromscn`: [larbys/ubdl:depsonly_py3_u20.04_cu11.1_pytorch1.9_root6.24.02](https://hub.docker.com/layers/larbys/ubdl/depsonly_py3_u20.04_cu11.1_pytorch1.9_root6.24.02/images/sha256-470b0eb0c9a7fc73c878afced9680ee5eb7fb82bda66ea05474b2acb39a19107?context=repo)
  * `pytorch/Dockerfile_pytorch1.9_ubuntu20.04_py3`: [larbys/pytorch:v1.9_cu11.1_u20.04](https://hub.docker.com/layers/larbys/pytorch/v1.9_cu11.1_u20.04/images/sha256-f50c17ae703222365513391717881f617b28752e3cec571fca860b9e76734376?context=repo)
  * Base container: [nvidia/cuda:11.1.1-devel-ubuntu20.04](https://hub.docker.com/layers/nvidia/cuda/11.1.1-devel-ubuntu20.04/images/sha256-299cd42d4c364516bd120fa8097b0397cac6c962e3761ef822656e5c5a25ea40?context=explore)


## Built containers on Tufts

On the Tufts Cluster you can find the containers at:

    /cluster/tufts/wongjiradlab/larbys/larbys-containers

# Instructions

We use two packages: [docker](https://www.docker.com/why-docker) and [singularity](https://www.sylabs.io/singularity/).

Typically, we will use `docker` to build the containers and then convert the docker image into a `singularity` container.

In the end, it is not important what tool we use to build the containers (one could use just singularity), but ultimately we must end up with a singularity container to run on the Tufts cluster.  (The reason is that docker is not supported on the cluster due to security concerns with docker.)

You can run both docker and singularity from your personal machine. You can also use lab machines at:

* Tufts: meitner, rubin
* MIT: nudot, trex

to build your containers.


## What do I need to do to build a container?

(still under construction)

In general, you just need to know the instructions you'd type to install the software in question. 
You put those instructions into a recipe file and tell docker or singularity to build the container.

As an example, we will use the anticipated most-likely case, which is to make a container with a new version of analysis code (`ubdl`).

In the folder `ubdl`, there is the docker recipe file to build this container.
It probably looks something like the following (assuming it hasn't changed too much since the time this README was written):

```
FROM larbys/sparseconvnet:ubuntu16.04_latest

MAINTAINER taritree.wongjirad@tufts.edu

# UBDL
RUN apt-get update -y && apt install -y rsync && apt-get autoremove -y && apt-get clean -y
RUN pip install pyyaml typing figcan zmq
RUN cd /usr/local && git clone --recursive https://github.com/larbys/ubdl && \
     cd ubdl && chmod +x setenv.sh && chmod +x buildall.sh && chmod +x configure.sh
RUN cd /usr/local/ubdl/larcv && cp misc/FindCUDA.cmake /usr/local/share/cmake-3.13/Modules/
RUN cd /usr/local/ubdl && bash -c "source /usr/local/root/build/bin/thisroot.sh && source setenv.sh && source configure.sh && source  buildall.sh"
```

The first line tells docker to build off of an existing image.
This happens to be the `larbys/sparseconvnet` image,
which contains the software stack up to the Sparse Convolutional Network library.
The SparseConvNet library is the last dependency for the `ubdl` code.
So all that's left to finish the container is to build `ubdl` into the container.

The docker file is just the list of instructions to install `ubdl`.

To build it, run

    docker build -t larbys/ubdl:dev . -f Dockerfile_ubuntu16.04

The `-t` flag is to the set the "name" or "tag" of the image.
`.` tells Docker where to find the docker recipe file.
And '-f' is what recipe file to use (in '.').

With the image with ubdl built, the next step if one wants to create a container to run
at Tufts, is to create a singularity container.
Like the docker build file above,
we list the commands we would run to configure the computer for `ubdl`.

As an example, in the `ubdl` folder,
you'll see a file called `Singularity.ubdl`,
which contains the instructions to build the `ubdl` repository.
It'll look something that the following:

```
bootstrap: docker
From: larbys/ubdl:latest

%post
  mkdir -p /cluster/home
  mkdir -p /cluster/kappa
  mkdir -p /cluster/shared
  mkdir -p /opt/shared

%environment

```

## Alternative, build `ubdl` outside the container

Here, we of course start with the container we built with docker above, `larbys/ubdl:latest`.
You can see all we do is create four folders.
These folders server to provide a mount point for our container to the network storage area.
When making singularity containers for the Tufts cluster,
please include these commands.

Note that the instructinos here were about installing `ubdl` into the container.
However, an alternative is to clone the `ubdl` code into some folder and then compile that source
using the libraries found in the container.
We provide the `ubdl-dependencies` container for this.

Instructions on how to do that can be found [here](https://github.com/LArbys/ubdl/wiki/Build-development-copy-of-UBDL-with-container)
as part of the `ubdl` wiki.