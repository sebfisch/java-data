FROM ubuntu:22.04

RUN echo 'APT::Install-Suggests "0";' >> /etc/apt/apt.conf.d/00-docker
RUN echo 'APT::Install-Recommends "0";' >> /etc/apt/apt.conf.d/00-docker

RUN DEBIAN_FRONTEND=noninteractive apt-get update && \
  apt-get -y install git hugo sudo

ARG USER=me
ENV USER=$USER
ARG HOME=/home/$USER
ENV HOME=$HOME
ARG UID=1000
ENV UID=$UID
ARG GROUP=me
ENV GROUP=$GROUP
ARG GID=1000
ENV GID=$GID

RUN addgroup --gid $GID $GROUP && \
  adduser --disabled-password --gecos '' --uid $UID --gid $GID $USER && \
  echo "$USER ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USER
