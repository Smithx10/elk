FROM centos:7

# Install yum pkg deps
RUN yum update -y \
      && yum install -y \
        curl \
        iproute \
        java-1.8.0-openjdk-headless \
        unzip \
        which \
      && yum clean all

ENV JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk

# Add Cerebros 
RUN export CEREB_VER=0.6.6 \
		&& export CEREB_PKG=cerebro-${CEREB_VER}.zip \
		&& export CEREB_SHA1=5abc928c1f2fbdc0a2d3179c084e9d1525759b8b \
		&& export CEREB_URL=https://github.com/lmenezes/cerebro/releases/download/v${CEREB_VER}/${CEREB_PKG} \
		&& curl -Ls --fail -o /tmp/${CEREB_PKG} ${CEREB_URL} \
		&& echo "${CEREB_SHA1} /tmp/${CEREB_PKG}" | sha1sum -c \
		&& unzip /tmp/${CEREB_PKG} -d /tmp \
		&& mv /tmp/cerebro-${CEREB_VER} /usr/share/cerebro \ 
		&& rm -rf /tmp/${CEREB_PKG}  

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
    && mkdir -p /var/run/containerpilot 

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
    && mkdir /config

EXPOSE 9000
