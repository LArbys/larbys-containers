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

![Alt text](https://g.gravizo.com/source/custom_mark10?https%3A%2F%2Fraw.githubusercontent.com%2FTLmaK0%2Fgravizo%2Fmaster%2FREADME.md)

<details>
<summary></summary>
custom_mark10
  digraph G {
      size ="4,4";
          UBUNTU (https://hub.docker.com/_/ubuntu) [shape=box];
	      main -> parse [weight=8];
	          parse -> execute;
		      main -> init [style=dotted];
		          main -> cleanup;
			      execute -> { make_string; printf};
			          init -> make_string;
				      edge [color=red];
				          main -> printf [style=bold,label="100 times"];
					      make_string [label="make a string"];
					          node [shape=box,style=filled,color=".7 .3 1.0"];
						      execute -> compare;
						        }
							custom_mark10
							</details>

