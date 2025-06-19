# LANTERN FermiGrid production Container

This repository contains recipes for building the lantern container used for production of MicroBooNE data and MC on FermiGrid.

Current tagged version is `v2_me_06_03`.

## Get the container

To get a copy of this version of the container do:

```
docker pull larbys/lantern:v2_me_06_03_ubdl
```

To then build a singularity container as a single image file based on this docker image:

```
singularity build lantern_v2_me_06_03.sif docker://larbys/lantern:v2_me_06_03_ubdl
```

## Run the container

To run on FermiGrid, you must use the 'sandbox' version of the container which builds the container as a directory:

```
singularity build --sandbox lantern_v2_me_06_03_prod docker://larbys/lantern:v2_me_06_03_ubdl
```

To run an interactive shell in the container do:

```
singularity shell lantern_v2_me_06_03.sif
```

or for the expanded directory folder
```
singularity shell lantern_v2_me_06_03_prod
```

On fermilab:

```
apptainer shell -B /cvmfs -B /exp/uboone -B /pnfs/uboone -B /run/user -s /bin/bash --env UPS_OVERRIDE="-H Linux64bit+3.10-2.17" lantern_v2_me_06_03_prod
```

Note: will have copy of this container on `cvmfs` in the future, in which case can use

```
apptainer shell -B /cvmfs -B /exp/uboone -B /pnfs/uboone -B /run/user -s /bin/bash --env UPS_OVERRIDE="-H Linux64bit+3.10-2.17" /cvmfs/uboone.opensciencegrid.org/containers/lantern_v2_me_06_03_prod
```


## Docker Recipe files and how to build

This was used to produce the CCnue and CCnumu selections currently described in MicroBoone Public Note [MICROBOONE-NOTE-1123-PUB](https://microboone.fnal.gov/wp-content/uploads/2024/06/MICROBOONE-NOTE-1123-PUB.pdf).

The software stack is as follows:

| Receipe File   | docker hub tag | description            |
| -------------- | -------------- | ---------------------- |
| Dockerfile_pytorch1.9_ubuntu20.04_py3_ubdldeps | `larbys/lantern:v2_me_06_03_ubdl_deps` | Dependencies installed on top of ubuntu20.04. Includes: pytorch 1.9.0+cpu, MinkowskiEngine, CERN root v6.28.12-gcc9.4, OpenCV 3.4.13 |
| Dockerfile_pytorch1.9_ubuntu20.04_py3_ubdl | `larbys/lantern:v2_me_06_03_ubdl` | Installation of `larbys/ubdl` along with SSNet, LArPID needed for Lantern Reco workflow |

The build occurs in three steps.

Link to [lantern google drive folder](https://drive.google.com/drive/folders/1_RF-0bhC9gppsxIe0AQ-GHjDrXvzNwne?usp=sharing).

1) download weight and script files from Google Drive. Make sure they are in this same folder as the receipe files and this readme.
    * [link to lantern weights](https://drive.google.com/file/d/1n5D_FtFH-GiOcuaakncTFHzbl8zSAXpT/view?usp=sharing)
    * [link to LArPID default weights](https://drive.google.com/file/d/1kOPP9MH09Pm0Iw7erYmZjy_LhbPScL-Z/view?usp=sharing)
    * [link to LArPID alternative weights](https://drive.google.com/file/d/1eq2kxJ__JSUhjbqDtn-1P2nMqyZEheBW/view?usp=sharing)

2) build the dependency container. Note: change the image name and tag to suit your needs.
    ```
    docker build -t ubdl_deps:v2_me_06_03 . -f Dockerfile_pytorch1.9_ubuntu20.04_py3_ubdldeps
    ```

3) build ubdl and install the various networks for the LANTERN reco. Note: you need to go into `Dockerfile_pytorch1.9_ubuntu20.04_py3_ubdl` and change the base container to match
   the one name and tag you used in Step 2.
    ```
    docker build -t ubdl:v2_me_06_03 . -f Dockerfile_pytorch1.9_ubuntu20.04_py3_ubdl
    ```
4) build a singularity image as described above, using your image name and tag

## Container Version Control in uboone MCC10 Releases

From the [MCC10 uboone wiki](https://cdcvs.fnal.gov/redmine/projects/uboone-physics-analysis/wiki/MCC10_Release_Page):

```
The LANTERN container version used in an MCC10 release is given by the "lantern_container" dependency of uboonecode.
The scripts in MCC10 workflows will launch the LANTERN reconstruction using the container at /cvmfs/uboone.opensciencegrid.org/containers/lantern_<container_version>

For every new container release, a tag is made for the larbys-containers repo described above under "container documentation" with the exact same name that was used for the container version in uboonecode.
One can therefore determine the version of all LANTERN repositories and dependencies used in a given MCC10 release by:

1. Find the lantern container version from the "lantern_container" dependency of uboonecode
2. Clone or go to https://github.com/LArbys/larbys-containers, select the tag that matches the LANTERN container version from the release, and inspect the LANTERN repo tags and software versions specified in the Docker files described above under "container documentation"
```

To maintain proper version control, the following procedure should therefore be used when updating the LANTERN container for a uboonecode release:

1) Make your changes to whichever specific LANTERN repo (e.g. ubdl) needs to be updated, and make a new github tag for that repo.
2) In the production branch of this larbys-containers repo, update Dockerfile_pytorch1.9_ubuntu20.04_py3_ubdl to pull in the new tag for the modified repo made in step 1
3) Tag the production branch of this larbys-containers repo. The name of this tag will define the "LANTERN container version"
4) Rebuild the LANTERN container with the new docker file, and install it on cvmfs at `/cvmfs/uboone.opensciencegrid.org/containers/lantern_<container_version>`, where the name of the tag created in step 3 is used for "container_version"
5) Update the lantern_container dependency of uboonecode for a new MCC10 release to the name of the tag created in step 3
6) Document the updated github tags for the new container in the "container releases" section of the "MCC 10 LANTERN Container" documentation in the [MCC10 release wiki](https://cdcvs.fnal.gov/redmine/projects/uboone-physics-analysis/wiki/MCC10_Release_Page)


