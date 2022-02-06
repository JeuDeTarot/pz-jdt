FROM ubuntu:20.04

LABEL maintainer='jeudetarot <https://steamcommunity.com/id/jeudetarot_1>' \
      version="1.0" \
      original='LinuxGSM <me@danielgibbs.co.uk>'

ENV DEBIAN_FRONTEND noninteractive
ENV TERM=xterm
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install UTF-8 unicode
RUN echo "**** Install UTF-8 ****" \
    && apt-get update \
    && apt-get install -y locales apt-utils debconf-utils
RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

## Upgrade Ubuntu
RUN echo "**** apt upgrade ****" \
    && apt-get update; \
    apt-get upgrade -y

RUN echo "**** Install Base Requirements ****" \
    && apt-get update \
    && apt-get-repository multiverse \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
    # Core packages
    binutils \
    bsdmainutils \
    ca-certificates \
    locales \
    # File management tools and more
    bc \
    bash \
    file \
    nano \
    sudo \
    tmux \
    # Languages and processors
    jq \
    # Network
    curl \
    hostname \
    netcat \
    iproute2 \
    iputils-ping \
    wget \
    # Compressing and archive management
    bzip2 \
    cpio \
    gzip \
    tar \
    unzip \
    xz-utils \
    # Libs
    lib32gcc1 \
    lib32stdc++6 \
    libsdl2 \
    #
    cron \
    tini \
    #vim \

RUN echo "**** Install SteamCMD ****" \
    && echo steam steam/question select "I AGREE" | debconf-set-selections \
    && echo steam steam/license note '' | debconf-set-selections \
    && dpkg --add-architecture i386 \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends libsdl2-2.0-0:i386 steamcmd \
    && ln -s /usr/games/steamcmd /usr/bin/steamcmd

# Install NodeJS
RUN echo "**** Install NodeJS ****" \
    && curl -sL https://deb.nodesource.com/setup_16.x | bash - \
    && apt-get update && apt-get install -y nodejs

# Install GameDig https://docs.linuxgsm.com/requirements/gamedig
RUN echo "**** Install GameDig ****" \
    && npm install -g gamedig

# Install Cleanup
RUN echo "**** Cleanup ****"  \
    && apt-get -y autoremove \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/* \
    && rm -rf /var/tmp/*

##Need use xterm for LinuxGSM##
ENV DEBIAN_FRONTEND noninteractive

ARG USRA admin
ARG USRA_UID=1000
ARG USRA_GID '$USRA_UID'

ARG USRS steam
ARG USRS_PUID=1003
ARG USRS_PGID=1002


## Add admin user
RUN echo "**** Add admin user ****" \
# Create the user
    && groupadd --gid $USRA_GID $USRA \
    && useradd --uid $USRA_UID --gid $USRA_GID -m $USRA \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && echo $USRA ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USRA \
    && chmod 0440 /etc/sudoers.d/$USRA \
    && chown $USRA:$USRA /home/$USRA

# Create /opt/admin
RUN echo "**** Create /opt/admin ****" \
    && mkdir -p /opt/$USRA \
    && chown $USRA:$USRA /opt/$USRA

## Download main.sh
RUN echo "**** Download main.sh ****" \
    && set -ex \
    && wget -O main.sh https://github.com/JeuDeTarot/pz-jdt/blob/master/pzds/scripts/main.sh \
    && chmod +x /main.sh

WORKDIR /home/$USRA
ENV PATH=$PATH:/home/$USRA
USER $USRA

COPY entrypoint.sh /home/$USRA/entrypoint.sh

ENTRYPOINT [ "/usr/bin/tini", "--" ]
CMD [ "bash","./entrypoint.sh" ]
