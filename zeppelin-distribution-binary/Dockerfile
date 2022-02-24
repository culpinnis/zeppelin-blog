FROM ubuntu:20.04
# Metainformation
LABEL org.opencontainers.image.authors="Apache Software Foundation <dev@zeppelin.apache.org>"
LABEL org.opencontainers.image.vendor="Thinkport GmbH <kontakt@thinkport.digital>"
LABEL org.opencontainers.image.source https://github.com/ThinkportRepo/zeppelin
# Zeppelin version
ARG version=0.10.0

# This part is taken from the official dockerfile
RUN set -ex && \
    apt-get -y update && \
    # Install language and other base packages
    DEBIAN_FRONTEND=noninteractive apt-get install -y language-pack-en openjdk-8-jre-headless tini wget && \
    # Cleanup
    rm -rf /var/lib/apt/lists/* && \
    apt-get autoclean && \
    apt-get clean

# Download Zeppelin, check sha512 and extract to /opt/zeppelin
RUN wget https://downloads.apache.org/zeppelin/zeppelin-${version}/zeppelin-${version}-bin-all.tgz && \
    wget https://downloads.apache.org/zeppelin/zeppelin-${version}/zeppelin-${version}-bin-all.tgz.sha512 && \
    sha512sum -c zeppelin-${version}-bin-all.tgz.sha512 && \
    mkdir -p /opt/zeppelin && \
    tar -xf zeppelin-${version}-bin-all.tgz --strip-components=1 --directory /opt/zeppelin

#Maybe not necessary, because we do not want to run Zeppelin with this image.
ENV LANG=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8 \
    JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 \
    VERSION="${version}" \
    HOME="/opt/zeppelin" \
    ZEPPELIN_HOME="/opt/zeppelin" \
    ZEPPELIN_ADDR="0.0.0.0" \
    ZEPPELIN_WAR_TEMPDIR="/tmp/webapps"

# Add runtime folders
RUN mkdir -p "${ZEPPELIN_HOME}/logs" "${ZEPPELIN_HOME}/run" "${ZEPPELIN_HOME}/notebook" "${ZEPPELIN_HOME}/local-repo" && \
     # Allow process to edit /etc/passwd, to create a user entry for zeppelin
    chgrp root /etc/passwd && chmod ug+rw /etc/passwd && \
    # Give access to some specific folders
    chmod -R 775 "${ZEPPELIN_HOME}/logs" "${ZEPPELIN_HOME}/run" "${ZEPPELIN_HOME}/conf" "${ZEPPELIN_HOME}/notebook" "${ZEPPELIN_HOME}/local-repo"

# Log4j fix
RUN find /opt -name log4j-1.*.jar -exec zip -d {} org/apache/log4j/net/JMSAppender.class org/apache/log4j/net/SocketServer.class \; 2>/dev/null

# Set user and expose
# Also maybe not necessary because we do not want to run Zeppelin with this image
USER 1000
EXPOSE 8080
ENTRYPOINT [ "/usr/bin/tini", "--" ]
WORKDIR ${ZEPPELIN_HOME}
CMD ["bin/zeppelin.sh"]
