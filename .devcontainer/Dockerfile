FROM alpine

ARG USER=me
ENV USER=$USER
ARG HOME=/home/$USER
ENV HOME=$HOME
ARG UID=501
ENV UID=$UID
ARG GROUP=me
ENV GROUP=$GROUP
ARG GID=501
ENV GID=$GID

RUN sed -i -e 's/v[[:digit:]]\..*\//edge\//g' /etc/apk/repositories && \
apk --no-cache add curl git hugo openssh sudo tzdata && \
apk upgrade musl && \
addgroup --gid $GID $GROUP && \
adduser --disabled-password --gecos '' --uid $UID --ingroup $GROUP $USER && \
echo "$USER ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USER