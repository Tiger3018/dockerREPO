# derived from http://github.com/osrf/docker_images/blob/master/gazebo/gazebo7/gzweb7/Dockerfile

FROM ubuntu:focal
# originally from MAINTAINER Steven Peters scpeters+buildfarm@osrfoundation.org
MAINTAINER Giovanni De Gasperis giovanni@giodegas.it
MAINTAINER Tiger3018<anonymous>

# if you are not ubuntu, used with caution!!
COPY sources.list /etc/apt
RUN apt update && apt install curl gnupg lsb-release -y \
    && rm -rf /var/lib/apt/lists/*
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null

# preesed tzdata, update package index, upgrade packages and install needed software
## From https://stackoverflow.com/posts/47909037/timeline
ENV DEBIAN_FRONTEND noninteractive
ENV DEBCONF_NONINTERACTIVE_SEEN true
RUN truncate -s0 /tmp/preseed.cfg; \
    echo "tzdata tzdata/Areas select Europe" >> /tmp/preseed.cfg; \
    echo "tzdata tzdata/Zones/Europe select Berlin" >> /tmp/preseed.cfg; \
    debconf-set-selections /tmp/preseed.cfg && \
    rm -f /etc/timezone /etc/localtime && \
    apt update && \
    apt install -y tzdata \
    && rm -rf /var/lib/apt/lists/*

RUN apt update && apt install -q -y \
    gazebo11 \
    libgazebo11-dev \
    && rm -rf /var/lib/apt/lists/*


# install packages
RUN apt update && apt upgrade -q -y && apt-get install -q -y \
    build-essential \
    cmake \
    imagemagick \
    libboost-all-dev \
    libgts-dev \
    libjansson-dev \
    libtinyxml-dev \
    mercurial \
    nodejs \
#   nodejs-legacy \
    npm \
    pkg-config \
    psmisc\
    && rm -rf /var/lib/apt/lists/*

# clone gzweb
RUN apt update && apt install -q -y \
    git xvfb \
 && rm -rf /var/lib/apt/lists/*
RUN git clone https://github.com/osrf/gzweb ~/gzweb

# build gzweb
RUN rm -rf /tmp && mkdir -m 777 -p /tmp && mkdir -p /root/gzweb/http/client/assets
RUN /bin/bash -c "cd ~/gzweb \
    && source /usr/share/gazebo/setup.sh \
    && xvfb-run ./deploy.sh -m -c -t"

# setup environment
EXPOSE 8080
EXPOSE 7681

# make a missing folder
# CMD mkdir /root/gzweb/http/client/assets
# download models & assets
# RUN rm /tmp -r 
# RUN cd /root/gzweb && npm run deploy -c -t

# run gzserver and gzweb
# CMD ./root/gzweb/start_gzweb.sh && gzserver
CMD cd /root/gzweb && npm start& gzserver --verbose
