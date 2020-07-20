ARG JITSI_REPO=jitsi
FROM ${JITSI_REPO}/base

ADD https://dl.eff.org/certbot-auto /usr/local/bin/

COPY rootfs/ /

RUN \
	apt-dpkg-wrap apt-get update && \
	apt-dpkg-wrap apt-get install -y cron nginx-extras jitsi-meet-web npm nodejs && \
	apt-dpkg-wrap apt-get -d install -y jitsi-meet-web-config && \
    dpkg -x /var/cache/apt/archives/jitsi-meet-web-config*.deb /tmp/pkg && \
    mv /tmp/pkg/usr/share/jitsi-meet-web-config/config.js /defaults && \
	mv /usr/share/jitsi-meet/interface_config.js /defaults && \
	apt-cleanup && \
	rm -f /etc/nginx/conf.d/default.conf && \
	rm -rf /tmp/pkg /var/cache/apt

RUN \
	chmod a+x /usr/local/bin/certbot-auto && \
	certbot-auto --noninteractive --install-only

ENV BASE_DIR=/opt/entefy-server \
    SERVICE_NAME=conference \
    BRANCH=develop \
    CNW_BRANCH=develop

RUN mkdir -p /root/.ssh
COPY ./.ssh /root/.ssh/

RUN mkdir -p /opt/entefy-server && \
    git clone -b ${BRANCH} git@gitlab.entefy.com:alex/conference.git ${BASE_DIR}/${SERVICE_NAME}

WORKDIR ${BASE_DIR}/${SERVICE_NAME}

RUN node -v

RUN npm install
RUN make
RUN cp -a ${BASE_DIR}/${SERVICE_NAME}/* /usr/share/jitsi-meet/


EXPOSE 80 443

VOLUME ["/config", "/etc/letsencrypt", "/usr/share/jitsi-meet/transcripts"]
