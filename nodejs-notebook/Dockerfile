FROM step21/jupyter-minimal-notebook

USER root
RUN wget -O - https://deb.nodesource.com/setup_12.x | bash
RUN apt-get install -y nodejs g++ make software-properties-common libzmq3-dev

RUN git clone https://github.com/notablemind/jupyter-nodejs.git $HOME/jupyter-nodejs
RUN chown -R $NB_USER $HOME/jupyter-nodejs
WORKDIR $HOME/jupyter-nodejs
#RUN touch /etc/ld.so.conf
#RUN echo "/opt/conda/lib" >> /etc/ld.so.conf

#RUN conda install -y jupyter_console
USER $NB_USER
RUN mkdir -p ~/.ipython/kernels/nodejs/
RUN npm install && node install.js
RUN npm run build
RUN npm run build-ext

#WORKDIR $HOME/jupyter-nodejs/node_modules/zmq/
#RUN npm run install

#CMD jupyter console --kernel nodejs
#jupyter console --kernel nodejs

#USER root
#WORKDIR $HOME/jupyter-nodejs
#RUN ldconfig

USER $NB_UID
