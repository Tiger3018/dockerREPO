# derived from http://github.com/osrf/docker_images/blob/master/gazebo/gazebo7/gzweb7/Dockerfile

FROM gazebo:libgazebo11
# originally from MAINTAINER Steven Peters scpeters+buildfarm@osrfoundation.org
MAINTAINER Giovanni De Gasperis giovanni@giodegas.it
MAINTAINER Tiger3018<anonymous>

# if you are not ubuntu, used with caution!!
RUN export && cat /etc/apt/sources.list
COPY sources.list /etc/apt
COPY 99proxy /etc/apt/apt.conf.d
#RUN apt update && apt-cache show gazebo9 && apt install -q -y \
#    gazebo9 \
#    libgazebo9-dev \
#    && rm -rf /var/lib/apt/lists/*
#RUN apt update && apt upgrade -q -y && apt install curl gpg lsb-release -q -y
#RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg
#RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu focal main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null

# install gazebo packages
RUN apt clean && apt update && apt upgrade -q -y && apt install libgazebo11-dev -q -y

# install packages
RUN apt update && apt install -q -y \
#   build-essential \
    cmake \
    imagemagick \
    libboost-all-dev \
    libgts-dev \
    libjansson-dev \
    libtinyxml-dev \
    && rm -rf /var/lib/apt/lists/*
RUN apt update && apt install -q -y \
    mercurial \
    nodejs \
#   nodejs-legacy \
    npm \
    pkg-config \
    psmisc\
    && rm -rf /var/lib/apt/lists/*

#clone gzweb from new source - github
RUN apt update && apt install git -q -y
RUN git clone https://github.com/osrf/gzweb ~/gzweb

# fix libprotobuf in focal. fix thumbnail.
COPY sources.list.focal /etc/apt/sources.list
RUN apt update && apt upgrade -q -y \
 && apt install protobuf-compiler=3.6.1.3-2ubuntu5 -q -y --allow-downgrades \
 && apt install libgazebo11-dev python xvfb -q -y \
 && rm -rf /var/lib/apt/lists/*
# RUN protoc --version

# build gzweb
## see https://github.com/osrf/docker_images/issues/43
RUN rm -rf /tmp && mkdir -m 777 -p /tmp && mkdir -p /root/gzweb/http/client/assets
RUN /bin/bash -c "cd ~/gzweb \
#   && source /usr/share/gazebo/setup.sh \
#   && hg up default \
    && xvfb-run -s '-screen 0 1280x1024x24' ./deploy.sh -m -c -t"

# setup environment
EXPOSE 8080
EXPOSE 7681
ENV GAZEBO_MODEL_PATH /root/gzweb/http/client/assets

# run gzserver and gzweb
CMD /usr/bin/bash -c "cd /root/gzweb \
 && source /usr/share/gazebo/setup.sh \
 && npm start& gzserver --verbose "

