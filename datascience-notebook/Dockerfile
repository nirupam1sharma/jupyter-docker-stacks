# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
FROM giffordlab/minimal-notebook

MAINTAINER Matt Edwards <matted@mit.edu>

USER root

RUN apt-get update -qq && apt-get install -qqy software-properties-common && \
    add-apt-repository ppa:marutter/rrutter && \
    add-apt-repository ppa:staticfloat/juliareleases && \
    add-apt-repository ppa:staticfloat/julia-deps && \
    add-apt-repository ppa:octave/stable

# R pre-requisites
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    fonts-dejavu \
    gfortran \
    gcc && apt-get clean

# Julia dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    julia \
    libnettle4 && apt-get clean

# Assorted other dependencies
RUN apt-get update -qq && apt-get install -qqy \
    octave \
    octave-signal \
    scilab && apt-get clean

USER jovyan

# Install Python 3 packages
RUN conda install --yes \
    'ipywidgets=4.0*' \
    'pandas=0.17*' \
    'matplotlib=1.4*' \
    'scipy=0.16*' \
    'seaborn=0.6*' \
    'scikit-learn=0.16*' \
    'scikit-image=0.11*' \
    'sympy=0.7*' \
    'cython=0.22*' \
    'patsy=0.4*' \
    'statsmodels=0.6*' \
    'cloudpickle=0.1*' \
    'dill=0.2*' \
    'numba=0.22*' \
    'bokeh=0.10*' \
    && conda clean -yt

# Install Python 2 packages
RUN conda create -p $CONDA_DIR/envs/python2 python=2.7 \
    'ipython=4.0*' \
    'ipywidgets=4.0*' \
    'pandas=0.17*' \
    'matplotlib=1.4*' \
    'scipy=0.16*' \
    'seaborn=0.6*' \
    'scikit-learn=0.16*' \
    'scikit-image=0.11*' \
    'sympy=0.7*' \
    'cython=0.22*' \
    'patsy=0.4*' \
    'statsmodels=0.6*' \
    'cloudpickle=0.1*' \
    'dill=0.2*' \
    'numba=0.22*' \
    'bokeh=0.10*' \
    pyzmq \
    && conda clean -yt

# R packages including IRKernel which gets installed globally.
RUN conda config --add channels r
RUN conda install --yes \
    'rpy2=2.7*' \
    'r-base=3.2*' \
    'r-irkernel=0.5*' \
    'r-plyr=1.8*' \
    'r-devtools=1.9*' \
    'r-dplyr=0.4*' \
    'r-ggplot2=1.0*' \
    'r-tidyr=0.3*' \
    'r-shiny=0.12*' \
    'r-rmarkdown=0.8*' \
    'r-forecast=5.8*' \
    'r-stringr=0.6*' \
    'r-rsqlite=1.0*' \
    'r-reshape2=1.4*' \
    'r-nycflights13=0.1*' \
    'r-caret=6.0*' \
    'r-rcurl=1.95*' \
    'r-randomforest=4.6*' && conda clean -yt

# Install IJulia packages as jovyan and then move the kernelspec out
# to the system share location. Avoids problems with runtime UID change not 
# taking effect properly on the .local folder in the jovyan home dir.
## RUN julia -e 'Pkg.add("IJulia")' && \
##     mv /home/$NB_USER/.local/share/jupyter/kernels/* $CONDA_DIR/share/jupyter/kernels/ && \
##     chmod -R go+rx $CONDA_DIR/share/jupyter && \
##     rm -rf /home/$NB_USER/.local/share
## RUN julia -e 'Pkg.add("Gadfly")' && julia -e 'Pkg.add("RDatasets")'

RUN pip install octave_kernel
RUN pip install bash_kernel
RUN pip install scilab_kernel

RUN python -m octave_kernel.install
RUN python -m bash_kernel.install
RUN python -m scilab_kernel.install

USER root

# Install Python 2 kernel spec globally to avoid permission problems when NB_UID
# switching at runtime.
RUN $CONDA_DIR/envs/python2/bin/python \
    $CONDA_DIR/envs/python2/bin/ipython \
    kernelspec install-self

USER jovyan
