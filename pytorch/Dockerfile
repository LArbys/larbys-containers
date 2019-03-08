FROM larbys/opencv:3.4.0_ubuntu18.04

MAINTAINER taritree.wongjirad@tufts.edu

# PyTorch
RUN apt-get update -y && apt install -y python-pip && \
    pip install https://download.pytorch.org/whl/cu100/torch-1.0.1.post2-cp27-cp27mu-linux_x86_64.whl && \
    pip install torchvision