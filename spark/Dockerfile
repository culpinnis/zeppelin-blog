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

FROM yourrepo/zeppelin-interpreter:0.10.0

#We need to adjust the patches of hadoop below when changing ther verison
ENV SPARK_VERSION=3.2.0
ENV HADOOP_VERSION=3.2

WORKDIR /
USER 0

# support Kerberos certification
RUN export DEBIAN_FRONTEND=noninteractive && apt-get update && apt-get install -yq krb5-user libpam-krb5 && apt-get clean && \
    apt-get install -y curl unzip wget grep sed vim tzdata && apt-get clean && \
    apt-get install -y procps zip

#This is based on the zeppelin-interpreter image
ENV PATH /opt/conda/envs/python_3_with_R/bin:/opt/conda/bin:$PATH

RUN python3 -m pip install --upgrade pip \
    && python3 -m pip install psycopg2-binary \
    && python3 -m pip install chispa

# auto upload zeppelin interpreter lib
RUN rm -rf /zeppelin && rm -rf /spark
#RUN wget https://dlcdn.apache.org/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz
#The apache mirror site is very unstable. The link just disappeared from one day to another.
RUN wget https://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz && \
     tar zxvf spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz && \
     mv spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION} /opt/spark && \
     rm spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz

RUN cp /opt/spark/kubernetes/dockerfiles/spark/entrypoint.sh /opt/ && cp /opt/spark/kubernetes/dockerfiles/spark/decom.sh /opt/
RUN mkdir -p /opt/spark/work-dir
ENV SPARK_HOME /opt/spark
WORKDIR /opt/spark/work-dir
RUN chmod g+w /opt/spark/work-dir && chmod a+x /opt/decom.sh

# Download postgres jdbc driver
RUN curl https://jdbc.postgresql.org/download/postgresql-42.2.24.jar -o /opt/spark/jars/postgresql-42.2.24.jar

# Download delta file support
RUN curl https://repo1.maven.org/maven2/io/delta/delta-core_2.12/1.1.0/delta-core_2.12-1.1.0.jar -o /opt/spark/jars/delta-core_2.12-1.1.0.jar && \
    curl https://repo1.maven.org/maven2/org/antlr/antlr4-runtime/4.8/antlr4-runtime-4.8.jar -o /opt/spark/jars/antlr4-runtime-4.8.jar && \
    curl https://repo1.maven.org/maven2/org/codehaus/jackson/jackson-core-asl/1.9.13/jackson-core-asl-1.9.13.jar -o /opt/spark/jars/jackson-core-asl-1.9.13.jar


# remove old versions from aws. jars
RUN rm -f /opt/spark/jars/aws-java-sdk-bundle-1.11.*.jar && rm -f /opt/spark/jars/hadoop-aws-3.2.0.jar

# Download all jars for hadoop s3 integration
RUN curl https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/3.3.1/hadoop-aws-3.3.1.jar -o /opt/spark/jars/hadoop-aws-3.3.1.jar && \
    curl https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/1.11.901/aws-java-sdk-bundle-1.11.901.jar -o /opt/spark/jars/aws-java-sdk-bundle-1.11.901.jar && \
    curl https://repo1.maven.org/maven2/org/wildfly/openssl/wildfly-openssl/1.0.7.Final/wildfly-openssl-1.0.7.Final.jar -o /opt/spark/jars/wildfly-openssl-1.0.7.Final.jar


RUN find /opt -name log4j-1.*.jar -exec zip -d {} org/apache/log4j/net/JMSAppender.class org/apache/log4j/net/SocketServer.class \; 2>/dev/null

# Specify the User that the actual main process will run as
ARG spark_uid=185
USER ${spark_uid}

WORKDIR /opt/spark/work-dir
ENTRYPOINT [ "/opt/entrypoint.sh" ]
