# Copyright (c) Jupyter Development Team, step21
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=step21/jupyter-minimal-notebook
FROM $BASE_CONTAINER

LABEL maintainer="step21 <step21@devtal.de>"

USER root

COPY ./solc /usr/bin

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Switch back to jovyan to avoid accidental container runs as root
USER $NB_UID

