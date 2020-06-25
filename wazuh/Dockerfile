# Wazuh Docker Copyright (C) 2020 Wazuh Inc. (License GPLv2)
FROM centos:7

ARG WAZUH_VERSION=3.13.0-1

ENV API_USER="foo" \
   API_PASS="bar"


# Set repositories.
RUN rpm --import https://packages.wazuh.com/key/GPG-KEY-WAZUH

COPY config/wazuh.repo /etc/yum.repos.d/wazuh.repo

RUN yum --enablerepo=updates clean metadata && \
  yum -y install openssl which && yum -y install wazuh-manager-${WAZUH_VERSION} -y && \
  curl --silent --location https://rpm.nodesource.com/setup_10.x | bash - && \
  yum -y install nodejs && yum -y install wazuh-api-${WAZUH_VERSION} && \
  sed -i "s/^enabled=1/enabled=0/" /etc/yum.repos.d/wazuh.repo && \
  yum clean all && rm -rf /var/cache/yum

ARG S6_VERSION="v2.0.0.1"
RUN curl --fail --silent -L https://github.com/just-containers/s6-overlay/releases/download/${S6_VERSION}/s6-overlay-amd64.tar.gz \
    -o /tmp/s6-overlay-amd64.tar.gz && \
    tar xzf /tmp/s6-overlay-amd64.tar.gz -C / --exclude="./bin" && \
    tar xzf /tmp/s6-overlay-amd64.tar.gz -C /usr ./bin && \
    rm  /tmp/s6-overlay-amd64.tar.gz

COPY config/etc/ /etc/

# Prepare permanent data
# Sync calls are due to https://github.com/docker/docker/issues/9547
COPY config/permanent_data.env config/permanent_data.sh /
RUN chmod 755 /permanent_data.sh && \
    sync && /permanent_data.sh && \
    sync && rm /permanent_data.sh

# Services ports
EXPOSE 55000/tcp 1514/udp 1515/tcp 514/udp 1516/tcp

ENTRYPOINT [ "/init" ]
