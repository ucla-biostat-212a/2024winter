# Sample usage: 
#
# docker build . -t huazhou/ucla_econ_425t_2023w
# docker-compose up
# docker image push huazhou/ucla_econ_425t_2023w
#
# https://github.com/jupyter/docker-stacks
# ubuntu: 22.04 (jammy), R: 4.2.2, Python: 3.10

ARG OWNER=jupyter
ARG BASE_CONTAINER=$OWNER/datascience-notebook
FROM $BASE_CONTAINER 

LABEL maintainer="Hua Zhou <huazhou@ucla.edu>"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

# Quarto pre-requisites
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    pandoc \
    pandoc-citeproc \    
    gdebi-core \
    && rm -rf /var/lib/apt/lists/*

# Quarto installation
# Default values can be overridden at build time
# (ARGS are in lower case to distinguish them from ENV)
# Check https://quarto.org/docs/get-started/
# ARG QUARTO_VERSION="0.9.522"
# RUN curl -o quarto-linux-amd64.deb -L https://github.com/quarto-dev/quarto-cli/releases/download/v${QUARTO_VERSION}/quarto-${QUARTO_VERSION}-linux-amd64.deb
RUN curl -LO https://quarto.org/download/latest/quarto-linux-amd64.deb
RUN gdebi --non-interactive quarto-linux-amd64.deb
RUN rm quarto-linux-amd64.deb

USER ${NB_UID}

# R packages including IRKernel which gets installed globally.
# r-e1071: dependency of the caret R package
RUN mamba install --quiet --yes \
    'r-gam' \
    'r-GGally' \
    'r-ggformula' \
    'r-gtsummary' \
    'r-ISLR2' \
    'r-JuliaCall' \
    'r-leaps' \
    'r-mgcViz' \
    'r-quantmod' \
    'r-reticulate' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Install Python 3 packages
RUN mamba install --quiet --yes \
    'watermark' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

USER ${NB_UID}

WORKDIR "${HOME}"