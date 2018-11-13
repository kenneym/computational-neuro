FROM nvcr.io/nvidia/tensorflow:18.10-py3

ENV PATH /opt/conda/bin:$PATH

RUN apt-get update && apt-get install -y wget

RUN MINICONDA="Miniconda3-latest-Linux-x86_64.sh" && \
    wget --quiet https://repo.continuum.io/miniconda/$MINICONDA && \
    bash $MINICONDA -b -p /miniconda && \
    rm -f $MINICONDA

ENV PATH /miniconda/bin:$PATH

RUN conda install jupyter biopython numpy
