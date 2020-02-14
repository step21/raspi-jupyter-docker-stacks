# Copyright (c) Jupyter Development Team, step21
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=step21/rpi-base-notebook
FROM $BASE_CONTAINER

LABEL maintainer="step21 <step21@devtal.de>"

USER root

# Install all OS dependencies for fully functional notebook server
RUN apt-get update
RUN apt-get install -yq --no-install-recommends \
     build-essential \
     emacs \
     git \
     inkscape \
     jed \
     libsm6 \
     libxext-dev \
     libxrender1 \
     lmodern \
     netcat \
     pandoc \
     python-dev \
     texlive-fonts-extra \
     texlive-fonts-recommended \
     texlive-generic-recommended \
     texlive-latex-base \
     texlive-latex-extra \
     texlive-xetex \
     tzdata \
     unzip \
     nano
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Switch back to jovyan to avoid accidental container runs as root
USER $NB_UID
