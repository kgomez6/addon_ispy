FROM homeassistant/amd64-base-debian:latest

ARG TZ=Europe/Amsterdam

RUN  echo 'deb http://http.us.debian.org/debian testing main non-free contrib' >> /etc/apt/sources.list &&\
    apt update &&\
    apt install -y unzip alsa-utils libgdiplus ffmpeg wget

VOLUME ["/data"]

ADD https://ispyfiles.azureedge.net/downloads/Agent_Linux64_4_4_6_0.zip /agent/
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ARG BASHIO_VERSION="v0.15.0"
RUN wget -c $(wget -qO- "https://www.ispyconnect.com/api/Agent/DownloadLocation4?platform=Linux64&fromVersion=0" | tr -d '"') -O agent.zip; \
    unzip agent.zip -d /agent && \
    \
    && curl -J -L -o /tmp/bashio.tar.gz \
        "https://github.com/hassio-addons/bashio/archive/${BASHIO_VERSION}.tar.gz" \
    && mkdir /tmp/bashio \
    && tar zxvf \
        /tmp/bashio.tar.gz \
        --strip 1 -C /tmp/bashio \
    \
    && mv /tmp/bashio/lib /usr/lib/bashio \
    && ln -s /usr/lib/bashio/bashio /usr/bin/bashio \
    rm agent.zip

COPY run /agent/

RUN chmod +x /agent/Agent && \
  chmod +x /agent/agent-register.sh && \
  chmod +x /agent/agent-reset.sh && \
  chmod +x /agent/agent-reset-local-login.sh && \
  chmod +x /agent/run

# Define default environment variables
ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
# Fix a memory leak on encoded recording
ENV MALLOC_TRIM_THRESHOLD_=100000

# Main UI port
EXPOSE 8090

# STUN server port
EXPOSE 3478/udp

# TURN server UDP port range
EXPOSE 50000-50010/udp


CMD ["/agent/run"]

LABEL \
  io.hass.version="VERSION" \
  io.hass.type="addon" \
  io.hass.arch="amd64"
