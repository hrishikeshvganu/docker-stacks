FROM jupyter/minimal-notebook

MAINTAINER Jupyter Project <jupyter@googlegroups.com>

USER root



# R pre-requisites + xgboost
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    libxrender1 \
    fonts-dejavu \
    gfortran \
    git \
    gcc && apt-get clean

# Julia dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    julia \
    libnettle4 && apt-get clean


#xgboost Python3 is installed on ubuntu 14.04 now


RUN DEBIAN_FRONTEND=noninteractive apt-get update

# Install python packages
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y python3

# Install compile tools
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y python3-dev gfortran libatlas-base-dev libfreetype6-dev \
                    libxft-dev libpng-dev g++ make patch lib32ncurses5-dev

# Install pip
RUN apt-get install -y wget
RUN wget -O /tmp/get-pip.py https://bootstrap.pypa.io/get-pip.py
RUN python3 /tmp/get-pip.py

# Install Cython
RUN DEBIAN_FRONTEND=noninteractive pip install cython

# Install core packages
RUN DEBIAN_FRONTEND=noninteractive pip install numpy
RUN DEBIAN_FRONTEND=noninteractive pip install scipy
RUN DEBIAN_FRONTEND=noninteractive pip install matplotlib
RUN DEBIAN_FRONTEND=noninteractive pip install "ipython[all]"
RUN DEBIAN_FRONTEND=noninteractive pip install -U "pandas>0.14.0"
RUN DEBIAN_FRONTEND=noninteractive pip install -U scikit-learn

RUN DEBIAN_FRONTEND=noninteractive pip install seaborn

# Install pymc
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y git-core
RUN DEBIAN_FRONTEND=noninteractive pip install -U Theano patsy
RUN DEBIAN_FRONTEND=noninteractive pip install git+https://github.com/pymc-devs/pymc3

# Install xgboost
RUN cd /usr/local/src && git clone https://github.com/dmlc/xgboost.git  && cd xgboost && sh build.sh && cd python-package && python3 setup.py install


#Minimize image size
RUN (apt-get autoremove -y; \
     apt-get clean -y)

    
USER jovyan

# Install Python 3 packages + xgboost
RUN conda install --yes \
    'ipywidgets=4.0*' \
    'pandas=0.16*' \
    'matplotlib=1.4*' \
    'scipy=0.15*' \
    'seaborn=0.6*' \
    'scikit-learn=0.16*' \
    'scikit-image=0.11*' \
    'sympy=0.7*' \
    'cython=0.22*' \
    'patsy=0.3*' \
    'statsmodels=0.6*' \
    'cloudpickle=0.1*' \
    'dill=0.2*' \
    'numba=0.20*' \
    'bokeh=0.9*' \
    'setuptools=18*'\
    && conda clean -yt


# Install Python 2 packages
RUN conda create -p $CONDA_DIR/envs/python2 python=2.7 \
    'ipython=4.0*' \
    'ipywidgets=4.0*' \
    'pandas=0.16*' \
    'matplotlib=1.4*' \
    'scipy=0.15*' \
    'seaborn=0.6*' \
    'scikit-learn=0.16*' \
    'scikit-image=0.11*' \
    'sympy=0.7*' \
    'cython=0.22*' \
    'patsy=0.3*' \
    'statsmodels=0.6*' \
    'cloudpickle=0.1*' \
    'dill=0.2*' \
    'numba=0.20*' \
    'bokeh=0.9*' \
    pyzmq \
    && conda clean -yt

# R packages including IRKernel which gets installed globally.
RUN conda config --add channels r
RUN conda install --yes \
    'r-base=3.2*' \
    'r-irkernel=0.4*' \
    'r-plyr=1.8*' \
    'r-devtools=1.8*' \
    'r-dplyr=0.4*' \
    'r-ggplot2=1.0*' \
    'r-tidyr=0.2*' \
    'r-shiny=0.12*' \
    'r-rmarkdown=0.7*' \
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
RUN julia -e 'Pkg.add("IJulia")' && \
    mv /home/$NB_USER/.local/share/jupyter/kernels/* $CONDA_DIR/share/jupyter/kernels/ && \
    chmod -R go+rx $CONDA_DIR/share/jupyter && \
    rm -rf /home/$NB_USER/.local/share
RUN julia -e 'Pkg.add("Gadfly")' && julia -e 'Pkg.add("RDatasets")'

USER root



# Install Python 2 kernel spec globally to avoid permission problems when NB_UID
# switching at runtime.
RUN $CONDA_DIR/envs/python2/bin/python \
    $CONDA_DIR/envs/python2/bin/ipython \
    kernelspec install-self

