FROM larbys/pytorch:1.0.1post2_ubuntu18.04

MAINTAINER taritree.wongjirad@tufts.edu

# PyTorch
RUN apt update -y && apt install -y libsparsehash-dev && \
    apt autoremove -y && \
    cd /tmp && git clone https://github.com/nutufts/SparseConvNet && \
    cd SparseConvNet && chmod +x build.sh && \
    ./build.sh