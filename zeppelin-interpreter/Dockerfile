# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM zeppelin-distribution:latest AS zeppelin-distribution

FROM ubuntu:20.04

LABEL maintainer="Apache Software Foundation <dev@zeppelin.apache.org>"

# Metainformation
LABEL org.opencontainers.image.authors="Apache Software Foundation <dev@zeppelin.apache.org>"
LABEL org.opencontainers.image.vendor="Thinkport GmbH <kontakt@thinkport.digital>"
LABEL org.opencontainers.image.source https://github.com/ThinkportRepo/zeppelin
# Zeppelin version
ARG version=0.10.0

ENV VERSION="${version}" \
    ZEPPELIN_HOME="/opt/zeppelin"

RUN set -ex && \
    apt-get -y update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y openjdk-8-jre-headless wget tini && \
    # Cleanup
    rm -rf /var/lib/apt/lists/* && \
    apt-get autoclean && \
    apt-get clean

COPY --from=zeppelin-distribution /opt/zeppelin/bin ${ZEPPELIN_HOME}/bin
COPY log4j.properties ${ZEPPELIN_HOME}/conf/
COPY log4j_yarn_cluster.properties ${ZEPPELIN_HOME}/conf/
# Decide:
## 1) Build a huge image with all interpreters (default)
COPY --from=zeppelin-distribution /opt/zeppelin/interpreter ${ZEPPELIN_HOME}/interpreter
## 2) Build an image with only some interpreters
#### Copy interpreter-shaded JAR, needed for all interpreters
### COPY --from=zeppelin-distribution /opt/zeppelin/interpreter/zeppelin-interpreter-shaded-${VERSION}.jar ${ZEPPELIN_HOME}/interpreter/zeppelin-interpreter-shaded-${VERSION}.jar
#### Copy specific interpreters,  replace "${interpreter_name}" with your interpreter. Of course you can repeat the line with different interpreter
### COPY --from=zeppelin-distribution /opt/zeppelin/interpreter/${interpreter_name}  ${ZEPPELIN_HOME}/interpreter/${interpreter_name}
# Decide: Install conda to manage python and R packages. Maybe adjust the packages in pip_packages.txt or env_python_3_with_R.yaml
# Some python packages are not available via conda, so we are using pip
COPY pip_packages.txt /pip_packages.txt


# Install conda to manage python and R packages
ARG miniconda_version="py37_4.9.2"
# Hashes via https://docs.conda.io/en/latest/miniconda_hashes.html
ARG miniconda_sha256="79510c6e7bd9e012856e25dcb21b3e093aa4ac8113d9aa7e82a86987eabe1c31"
# Install python and R packages via conda
COPY env_python_3_with_R.yml /env_python_3_with_R.yml

RUN set -ex && \
    wget -nv https://repo.anaconda.com/miniconda/Miniconda3-${miniconda_version}-Linux-x86_64.sh -O miniconda.sh && \
    echo "${miniconda_sha256} miniconda.sh" > anaconda.sha256 && \
    sha256sum --strict -c anaconda.sha256 && \
    bash miniconda.sh -b -p /opt/conda && \
    export PATH=/opt/conda/bin:$PATH && \
    conda config --set always_yes yes --set changeps1 no && \
    conda info -a && \
    conda install mamba -c conda-forge && \
    mamba env update -f /env_python_3_with_R.yml --prune && \
    # Cleanup
    rm -v miniconda.sh anaconda.sha256  && \
    # Cleanup based on https://github.com/ContinuumIO/docker-images/commit/cac3352bf21a26fa0b97925b578fb24a0fe8c383
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    mamba clean -ay
    # Allow to modify conda packages. This allows malicious code to be injected into other interpreter sessions, therefore it is disabled by default
    # chmod -R ug+rwX /opt/conda
ENV PATH /opt/conda/envs/python_3_with_R/bin:/opt/conda/bin:$PATH
RUN set -ex pip install -q -r /pip_packages.txt

RUN mkdir -p "${ZEPPELIN_HOME}/logs" "${ZEPPELIN_HOME}/run" "${ZEPPELIN_HOME}/local-repo" && \
     # Allow process to edit /etc/passwd, to create a user entry for zeppelin
    chgrp root /etc/passwd && chmod ug+rw /etc/passwd && \
    # Give access to some specific folders
    chmod -R 775 "${ZEPPELIN_HOME}/logs" "${ZEPPELIN_HOME}/run" "${ZEPPELIN_HOME}/local-repo"

USER 1000
ENTRYPOINT [ "/usr/bin/tini", "--" ]
WORKDIR ${ZEPPELIN_HOME}
