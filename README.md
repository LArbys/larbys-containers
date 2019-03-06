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

# Containers (and the heirarchy)

![Alt text](https://g.gravizo.com/source/custom_mark10?https%3A%2F%2Fraw.githubusercontent.com%2FLArbys%2Flarbys-containers%2Fmaster%2Fcontainer_graph.dot)

# Instructions

We use two packages: [docker](https://www.docker.com/why-docker) and [singularity](https://www.sylabs.io/singularity/).

Typically, we will use `docker` to build the containers and then convert the docker image into a `singularity` container.

In the end, it is not important what tool we use to build the containers (one could use just singularity), but ultimately we must end up with a singularity container to run on the Tufts cluster.  (The reason is that docker is not supported on the cluster due to security concerns with docker.)

You can run both docker and singularity from your personal machine. You can also use lab machines at:

* Tufts: meitner, rubin
* MIT: nudot

to build your containers.



