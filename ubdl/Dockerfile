FROM larbys/pytorch:1.0.1post2_ubuntu18.04

MAINTAINER taritree.wongjirad@tufts.edu

# UBDL
RUN cd /usr/local && git clone --recursive https://github.com/larbys/ubdl && \
     cd ubdl && chmod +x setenv.sh && chmod +x buildall.sh && chmod +x configure.sh
RUN cd /usr/local/ubdl/larcv && cp misc/FindCUDA.cmake /usr/share/cmake-3.10/Modules/
RUN cd /usr/local/ubdl && bash -c "source /usr/local/root/build/bin/thisroot.sh && source setenv.sh && source configure.sh && source  buildall.sh"
RUN apt-get update -y && apt install -y rsync && apt-get autoremove -y && apt-get clean -y
RUN pip install pyyaml typing figcan zmq
