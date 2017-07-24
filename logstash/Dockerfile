FROM centos:7

# Install yum pkg deps
RUN yum update -y \
      && yum install -y \
        curl \
        java-1.8.0-openjdk-devel \
        unzip \
        which \
      && yum clean all

# Add jq
RUN export JQ_VER=1.5 \
    && export JQ_URL=https://github.com/stedolan/jq/releases/download/jq-${JQ_VER}/jq-linux64 \
    && curl -Ls --fail -o /bin/jq ${JQ_URL} \
    && chmod +x /bin/jq

# Add Logstash and set its configuration
ENV PATH=/usr/share/logstash/bin:$PATH
ENV LOGSTASH_HOME=/usr/share/logstash
RUN export LS_VER=5.5.0 \
    && export LS_PKG=logstash-${LS_VER}.tar.gz \
    && export  LS_URL=https://artifacts.elastic.co/downloads/logstash/${LS_PKG} \
    && export LS_SHA1=d380e8cdefb4c0d5291d0243b8ab5a9f20811826 \
    && curl -Ls --fail -o /tmp/${LS_PKG} ${LS_URL} \
    && echo "${LS_SHA1} /tmp/${LS_PKG}" | sha1sum -c \
    && tar zxf /tmp/${LS_PKG} -C /usr/share \
    && mv /usr/share/logstash-${LS_VER} /usr/share/logstash \
    && rm /tmp/${LS_PKG} \
    && export LS_PACK_URL=https://staging.elastic.co/5.5.0/downloads/logstash-plugins \
    && logstash-plugin install x-pack

# Add ContainerPilot and set its configuration
ENV CONTAINERPILOT=/etc/containerpilot.json5
RUN export CP_VER=3.3.0 \
    && export CP_PKG=containerpilot-${CP_VER}.tar.gz \
    && export CP_SHA1=62621712ef6ba755e24805f616096de13e2fd087 \
    && export CP_URL=https://github.com/joyent/containerpilot/releases/download/${CP_VER}/${CP_PKG} \
    && curl -Ls --fail -o /tmp/${CP_PKG} ${CP_URL} \
    && echo "${CP_SHA1} /tmp/${CP_PKG}" | sha1sum -c \
    && tar zxf /tmp/${CP_PKG} -C /usr/local/bin \
    && rm /tmp/${CP_PKG}

# Add Consul and set its configuration
RUN export CONSUL_VER=0.9.0 \
    && export CONSUL_PKG=consul_${CONSUL_VER}_linux_amd64.zip \
    && export CONSUL_URL=https://releases.hashicorp.com/consul/${CONSUL_VER}/${CONSUL_PKG} \
    && export CONSUL_SHA1=345efaf9b055c92caf6221a5d58d84e96acddf24 \
    && curl -Ls --fail -o /tmp/${CONSUL_PKG} ${CONSUL_URL} \
    && echo "${CONSUL_SHA1} /tmp/${CONSUL_PKG}" | sha1sum -c \
    && unzip /tmp/${CONSUL_PKG} -d /usr/local/bin \
    && rm /tmp/${CONSUL_PKG} \
    && mkdir /etc/consul \
    && mkdir /var/lib/consul \
    && mkdir /data \
    && mkdir /config

# Add our configuration files and scripts
#https://www.joyent.com/containerpilot/docs/configuration/configuration-file
COPY containerpilot.json5 /etc/containerpilot.json5
#https://www.elastic.co/guide/en/logstash/current/config-setting-files.html
COPY logstash.conf /usr/share/logstash/pipeline/logstash.conf
# https://www.elastic.co/guide/en/logstash/current/logstash-settings-file.html
COPY logstash.yml /usr/share/logstash/config/logstash.yml

COPY preStart.sh /usr/local/bin/preStart.sh

EXPOSE 514
EXPOSE 12201
EXPOSE 24224
