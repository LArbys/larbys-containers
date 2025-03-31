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

| -------------- | -------------- | ---------------------- |
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