#Copyright (c) Jupyter Development Team.
#Copyright for Raspi, step21
# Distributed under the terms of the Modified BSD License.

# Ubuntu 18.04 (bionic)
# https://hub.docker.com/_/ubuntu/?tab=tags&name=bionic
ARG BASE_CONTAINER=ubuntu:bionic-20200112
#@sha256:28b9597b09e4e4e5e7dd1ff3e470cdc76c4b8dfe6f37e8e0014be90c0e172630
FROM $BASE_CONTAINER
#28b9597b09e4e4e5e7dd1ff3e470cdc76c4b8dfe6f37e8e0014be90c0e172630

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"
ARG NB_USER="jovyan"
ARG NB_UID="1000"
ARG NB_GID="100"
ENV PYTHON_VERSION='3.6.6'

USER root

# Install all OS dependencies for notebook server that starts but lacks all
# features (e.g., download as all possible file formats)
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update \
 && apt-get install -yq --no-install-recommends \
    wget \
    bzip2 \
    ca-certificates \
    sudo \
    locales \
    fonts-liberation \
    run-one \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

# Configure environment
ENV CONDA_DIR=/opt/conda \
    SHELL=/bin/bash \
    NB_USER=$NB_USER \
    NB_UID=$NB_UID \
    NB_GID=$NB_GID \
    LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8 \
    &LANGUAGE=en_US.UTF-8
ENV PATH=$CONDA_DIR/bin:$PATH \
    HOME=/home/$NB_USER

# Copy a script that we will use to correct permissions after running certain commands
COPY fix-permissions /usr/local/bin/fix-permissions
RUN chmod a+rx /usr/local/bin/fix-permissions

# Enable prompt color in the skeleton .bashrc before creating the default NB_USER
RUN sed -i 's/^#force_color_prompt=yes/force_color_prompt=yes/' /etc/skel/.bashrc

# Create NB_USER wtih name jovyan user with UID=1000 and in the 'users' group
# and make sure these dirs are writable by the `users` group.
RUN echo "auth requisite pam_deny.so" >> /etc/pam.d/su && \
    sed -i.bak -e 's/^%admin/#%admin/' /etc/sudoers && \
    sed -i.bak -e 's/^%sudo/#%sudo/' /etc/sudoers && \
    useradd -m -s /bin/bash -N -u $NB_UID $NB_USER && \
    mkdir -p $CONDA_DIR && \
    chown $NB_USER:$NB_GID $CONDA_DIR && \
    chmod g+w /etc/passwd && \
    fix-permissions $HOME && \
    fix-permissions "$(dirname $CONDA_DIR)"

USER $NB_UID
WORKDIR $HOME
#ARG PYTHON_VERSION=default

# Setup work directory for backward-compatibility
RUN mkdir /home/$NB_USER/work && \
    fix-permissions /home/$NB_USER

# Install conda as jovyan and check the md5 sum provided on the download site
ENV MINICONDA_VERSION=4.7.12.1
ENV BERRYCONDA_MD5=1d8a60a9e201e3c30d927b5bf93b441d
ENV BERRYCONDA_VERSION=2.0.0

#RUN cd /tmp && \
#    wget --quiet https://repo.continuum.io/miniconda/Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh && \
#    echo "${MINICONDA_MD5} *Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh" | md5sum -c - && \
#    /bin/bash Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
#    rm Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh && \
#    echo "conda ${CONDA_VERSION}" >> $CONDA_DIR/conda-meta/pinned && \
#    conda config --system --prepend channels conda-forge && \
#    conda config --system --set auto_update_conda false && \
#    conda config --system --set show_channel_urls true && \
#    if [ ! $PYTHON_VERSION = 'default' ]; then conda install --yes python=$PYTHON_VERSION; fi && \
#    conda list python | grep '^python ' | tr -s ' ' | cut -d '.' -f 1,2 | sed 's/$/.*/' >> $CONDA_DIR/conda-meta/pinned && \
#    conda install --quiet --yes conda && \
#    conda install --quiet --yes pip && \
#    conda update --all --quiet --yes && \
#    conda clean --all -tipsy && \
#    rm -rf /home/$NB_USER/.cache/yarn && \
#    fix-permissions $CONDA_DIR && \
#    fix-permissions /home/$NB_USER

#Install BerryConda
RUN cd /tmp && \
    mkdir -p $CONDA_DIR && \
  wget https://github.com/jjhelmus/berryconda/releases/download/v${BERRYCONDA_VERSION}/Berryconda3-${BERRYCONDA_VERSION}-Linux-armv7l.sh && \
    echo "${BERRYCONDA_MD5} *Berryconda3-${BERRYCONDA_VERSION}-Linux-armv7l.sh" | md5sum -c - && \
    /bin/bash Berryconda3-${BERRYCONDA_VERSION}-Linux-armv7l.sh -f -b -p $CONDA_DIR && \
    rm Berryconda3-${BERRYCONDA_VERSION}-Linux-armv7l.sh && \
    echo "conda ${BERRYCONDA_VERSION}" >> $CONDA_DIR/conda-meta/pinned && \
    conda config --system --prepend channels conda-forge && \
    conda config --system --set auto_update_conda false && \
    conda config --system --set show_channel_urls true && \
    conda config --system --add channels rpi && \ 
    if [ ! $PYTHON_VERSION = 'default' ]; then conda install --yes python=$PYTHON_VERSION; fi && \
    conda list python | grep '^python ' | tr -s ' ' | cut -d '.' -f 1,2 | sed 's/$/.*/' >> $CONDA_DIR/conda-meta/pinned && \
#   conda install --quiet --yes conda && \
    conda install --yes python=$PYTHON_VERSION --channel rpi && \
    conda install --quiet --yes pip && \
    conda clean --all -y -tipsy && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    fix-permissions $conda_dir && \
    fix-permissions /home/$NB_USER


# Install Tini
#RUN conda install --quiet --yes 'tini=0.18.0' && \
#    conda list tini | grep tini | tr -s ' ' | cut -d ' ' -f 1,2 >> $CONDA_DIR/conda-meta/pinned && \
#    conda clean --all -f -y && \
#    fix-permissions $CONDA_DIR && \
#    fix-permissions /home/$NB_USER
USER root
# Install Tini from binary. Tini operates as a process subreaper for jupyter. This prevents kernel crashes.
ENV TINI_VERSION 0.18.0
RUN cd /tmp && wget --quiet https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini-armhf && \
  echo "01b54b934d5f5deb32aa4eb4b0f71d0e76324f4f0237cc262d59376bf2bdc269 *tini-armhf" | sha256sum -c - && \
  mv tini-armhf /usr/local/bin/tini && \
  chmod +x /usr/local/bin/tini
USER $NB_UID
# Install Jupyter Notebook, Lab, and Hub
# Generate a notebook server config
# Cleanup temporary files
# Correct permissions
# Do all this in a single RUN command to avoid duplicating all of the
# files across image layers when the permissions change
# somehow setting notebook version etc doesn't work with berryconda...
RUN conda install --quiet --yes -c rpi \
    'notebook' \
    'jupyterlab' && \
    conda clean --all -tipsy && \
#    npm cache clean --force && \
    jupyter notebook --generate-config && \
    rm -rf $CONDA_DIR/share/jupyter/lab/staging && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

EXPOSE 8888

# Configure container startup
ENTRYPOINT ["tini", "-g", "--"]
CMD ["start-notebook.sh"]

# Copy local files as late as possible to avoid cache busting
COPY start.sh /usr/local/bin/
COPY start-notebook.sh /usr/local/bin/
COPY start-singleuser.sh /usr/local/bin/
COPY jupyter_notebook_config.py /etc/jupyter/

# Fix permissions on /etc/jupyter as root
USER root
RUN fix-permissions /etc/jupyter/

# Switch back to jovyan to avoid accidental container runs as root
USER $NB_UID

