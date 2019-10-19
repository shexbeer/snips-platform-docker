FROM ubuntu:18.04

RUN apt-get update \
    && apt-get install -y --force-yes apt-transport-https \
       ca-certificates \
       dirmngr 

ENV UNAME snips

RUN DEBIAN_FRONTEND=noninteractive apt-get install --yes alsa-utils \
	libasound2 \
	libasound2-plugins \
	pulseaudio \
	pulseaudio-utils \
	--no-install-recommends 
	
RUN bash -c  'echo "deb [trusted=yes] https://debian.snips.ai/stretch stable main" > /etc/apt/sources.list.d/snips.list' 

RUN apt-get update 
RUN apt-get install -y snips-platform-voice snips-watch snips-platform-demo 
RUN apt-get install -y snips-template snips-skill-server
RUN apt-get install -y snips-asr-google

RUN apt-get install -y --force-yes \
       jq \
       supervisor \
       mosquitto \
       mosquitto-clients \
       sox \
       unzip \
       --yes \
       --no-install-recommends \
    && mkdir -p /var/lib/snips

RUN apt-get -y install python3-pip
	
RUN apt-get -y autoclean \
    && apt-get -y autoremove \
    && rm -rf /var/lib/apt/lists/* 
	
# # SSH
# RUN apt-get update && apt-get install -y openssh-server sudo
#
# RUN groupadd sshgroup && useradd -ms /bin/bash -g sshgroup sshuser
# RUN echo 'sshuser:Pa$$word' | chpasswd
# RUN adduser sshuser sudo
#
# RUN echo "sshuser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

RUN usermod -aG audio,pulse,pulse-access _snips
RUN usermod -aG audio,pulse,pulse-access root

COPY default.pa /etc/pulse/default.pa
COPY client.conf /etc/pulse/client.conf
COPY daemon.conf /etc/pulse/daemon.conf
COPY snips.toml /etc/snips.toml

COPY assistant /usr/share/snips/assistant

COPY skills/action-Datum_und_Uhrzeit/ /var/lib/snips/skills/action-Datum_und_Uhrzeit/
RUN /var/lib/snips/skills/action-Datum_und_Uhrzeit/setup.sh

# COPY skills/Snips-Wecker/ /var/lib/snips/skills/Snips-Wecker/
# RUN /var/lib/snips/skills/Snips-Wecker/setup.sh

# ARG home=/home/sshuser
# RUN mkdir $home/.ssh
# COPY id_rsa.pub $home/.ssh/authorized_keys
# RUN chown sshuser:sshgroup $home/.ssh/authorized_keys && \
#     chmod 600 $home/.ssh/authorized_keys

COPY snips-entrypoint.sh /opt/snips/snips-entrypoint.sh
COPY VERSION /VERSION

ENTRYPOINT ["/opt/snips/snips-entrypoint.sh","--mqtt","192.168.179.11:1883"]