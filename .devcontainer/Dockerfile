FROM ubuntu:18.04
LABEL maintainer="wenhungi.huang.j5y@jp.denso.com"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    sudo \
    vim \
    wget \
    unzip \
    curl \
    libcap-dev \
    git 

# LF dependency 
RUN apt-get install -y --no-install-recommends  \
default-jdk
    
RUN git clone --recursive https://github.com/icyphy/lingua-franca.git 
ENV LF /lingua-franca
RUN mkdir /root/.gradle && echo  "systemProp.http.proxyHost=$(eval echo $https_proxy | cut -f 2 -d ":" | cut -f 3 -d "/")\n\
systemProp.http.proxyPort=$(eval echo $https_proxy | cut -f 3 -d ":")\n\
systemProp.https.proxyHost=$(eval echo $https_proxy | cut -f 2 -d ":" | cut -f 3 -d "/")\n\
systemProp.https.proxyPort=$(eval echo $https_proxy | cut -f 3 -d ":")"> /root/.gradle/gradle.properties
RUN /lingua-franca/bin/build-lfc

# install Micro-XRCE-DDS-Client
RUN git clone https://github.com/eProsima/Micro-XRCE-DDS-Client.git && cd Micro-XRCE-DDS-Client && mkdir build && cd build && cmake .. && make && make install


#Add new sudo user
ENV USERNAME lf
RUN useradd -m $USERNAME && \
        echo "$USERNAME:$USERNAME" | chpasswd && \
        usermod --shell /bin/bash $USERNAME && \
        usermod -aG sudo $USERNAME && \
        echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/$USERNAME && \
        chmod 0440 /etc/sudoers.d/$USERNAME && \
        # Replace 1001 with your user/group id
        usermod  --uid 1001 $USERNAME && \
        groupmod --gid 1001 $USERNAME
# Add lf search path
RUN echo "export PATH=$PATH:$LF/bin" >> /home/$USERNAME/.bashrc 
# Change user
USER ${USERNAME}
WORKDIR /home/${USERNAME}


 