FROM centos:7

# Install yum pkg deps
RUN yum update -y \
      && yum install -y \
        curl \
        fontconfig \
        freetype \
        nc \
        unzip \
      && yum clean all

# Add jq
RUN export JQ_VER=1.5 \
    && export JQ_URL=https://github.com/stedolan/jq/releases/download/jq-${JQ_VER}/jq-linux64 \
    && curl -Ls --fail -o /bin/jq ${JQ_URL} \
    && chmod +x /bin/jq

# Add Kibana and set its configuration
ENV PATH=/usr/share/kibana/bin:$PATH
ENV KIBANA_HOME=/usr/share/kibana
RUN export KB_VER=5.5.0 \
    && export KB_PKG=kibana-${KB_VER}-linux-x86_64.tar.gz \
    && export KB_URL=https://artifacts.elastic.co/downloads/kibana/${KB_PKG} \
    && export KB_SHA1=935e925713cb84eb1879a59ac68708fccf3361d4 \
    && curl -Ls --fail -o /tmp/${KB_PKG} ${KB_URL} \
    && echo "${KB_SHA1} /tmp/${KB_PKG}" | sha1sum -c \
    && tar zxf /tmp/${KB_PKG} -C /usr/share \
    && mv /usr/share/kibana-${KB_VER}-linux-x86_64 /usr/share/kibana \
    && rm /tmp/${KB_PKG} \
    && export KB_PACK_URL=http://staging.elastic.co/${KB_VER}/downloads/kibana-plugins/x-pack/x-pack-${KB_VER}.zip \
    && ${KIBANA_HOME}/bin/kibana-plugin install x-pack \
    && groupadd -g 1000 kibana \
    && adduser -u 1000 -g 1000 -d ${KIBANA_HOME} kibana

# Add ContainerPilot and set its configuration
ENV CONTAINERPILOT=/etc/containerpilot.json5
RUN export CP_VER=3.3.0 \
    && export CP_PKG=containerpilot-${CP_VER}.tar.gz \
    && export CP_SHA1=62621712ef6ba755e24805f616096de13e2fd087 \
    && export CP_URL=https://github.com/joyent/containerpilot/releases/download/${CP_VER}/${CP_PKG} \
    && curl -Ls --fail -o /tmp/${CP_PKG} ${CP_URL} \
    && echo "${CP_SHA1} /tmp/${CP_PKG}" | sha1sum -c \
    && tar zxf /tmp/${CP_PKG} -C /usr/local/bin \
    && rm /tmp/${CP_PKG} \
    && mkdir -p /var/run/containerpilot \
    && chown kibana:kibana /var/run/containerpilot

# Add Consul and set its configuration
RUN export CONSUL_VER=0.9.0 \
    && export CONSUL_PKG=consul_${CONSUL_VER}_linux_amd64.zip \
    && export CONSUL_URL=https://releases.hashicorp.com/consul/${CONSUL_VER}/${CONSUL_PKG} \
    && export CONSUL_SHA1=345efaf9b055c92caf6221a5d58d84e96acddf24 \
    && curl -Ls --fail -o /tmp/${CONSUL_PKG} ${CONSUL_URL} \
    && echo "${CONSUL_SHA1} /tmp/${CONSUL_PKG}" | sha1sum -c \
    && unzip /tmp/${CONSUL_PKG} -d /usr/local/bin \
    && rm /tmp/${CONSUL_PKG} \
    && mkdir /data \
    && chown kibana:kibana /data \
    && mkdir /config \
    && chown kibana:kibana /config

# Copy Other Files into the Image
COPY manage.sh /usr/local/bin/manage.sh
COPY kibana.yml ${KIBANA_HOME}/config
COPY containerpilot.json5 /etc/containerpilot.json5

EXPOSE 5601
# Set the Kibana user
USER kibana
