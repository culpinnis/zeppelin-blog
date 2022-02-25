# Spark Distribution
This image will be executed by the Zeppelin interpreter to run your Spark jobs. That means your Spark notebooks are interpreted by the Zeppelin interpreter container but executed in separated Spark containers (see [this image](img/kubernetes.png)). The Kubernetes implementation of Zeppelin is doing some magic by running an init container when starting the Zeppelin interpreter container for Spark. This init container copies the Spark distribution of the image we are going to build in this section into the Zeppelin interpreter pod (into the path /spark). It is recommended and sometimes even required that the interpreter container and the job container have the same versions of all involved libraries and applications. E.g. if you use pyspark the Python version must be the same. Thus we decided to use the interpreter image as base for the spark image because it includes all dependencies like Python or R already.

Our Dockerfile will install Kerberos certification and the java libraries required for S3 storage support in addition. We are using Spark version 3.1.2 with Hadoop version 3.2. Spark version 3.2.0 is not supported in Zeppelin version 0.10.0.

## Instructions
You have to configure the right build-args for the Zeppelin, Spark, Hadoop and Amazon SDK versions.
The example is taken from the [build_from_binary.sh](../build_from_binary.sh) script.

```bash
# Your repo name
export repo=yourrepo
# Zeppelin version
export version=0.10.0
# Spark version compatible to the Zeppelin version
export spark_version=3.1.3
# Hadoop major version
export hadoop_version=3.2
# Hadoop minor version
export hadoop_minor_version=0
# AWS SDK version compatible to the Hadoop version
export aws_sdk_version=1.11.271
docker build --build-arg version=$version --build-arg SPARK_VERSION=$spark_version \
 --build-arg HADOOP_VERSION=$hadoop_version --build-arg HADOOP_VERSION_MINOR=$hadoop_minor_version \
 --build-arg AWS_SDK_VERSION=$aws_sdk_version --build-arg REPO=$repo \
 -t ${repo}/spark:${version} .

```

## Excursion: How to get the correct Hadoop libraries for S3 support in Spark

By default, the libraries for using the S3 filesystem are not included in the Spark distribution. The Hadoop version mentioned in the Spark download is just given for the minor version, but not the bugfix version level (spark-3.1.2-bin-hadoop3.2.tgz). This could lead to incompatibilities when e.g. the version used in the Spark distribution is 3.2.0, but you try to add the storage library in version 3.2.3 (over even 3.3).

* Extract the Spark archive
* Do a file listing at jars/
* Note the version of the Hadoop libraries
* Go to Maven and search for [Hadoop-aws](https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-aws)
* Select the required version
* Take a look at the compile dependencies (aws-java-sdk-bundle)
* Note the version of the aws-java-sdk-bundle
* Set the build-args when using our Dockerfiles
