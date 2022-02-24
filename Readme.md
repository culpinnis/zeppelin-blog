# Building Zeppelin and Spark with S3 support for Kubernetes
This repository includes the necessary files and instructions to build and
run Apache Zeppelin in Kubernetes. In addition we will also create a
Apache Spark distribution that supports S3 storage.
You can read the full story on this medium article.
 (Diagramm einfügen)

## Preparation

We will use the source code of Zeppelin to apply our modifications. So please get the version you want to use from the official GitHub page. In this example we will use 0.10.0.

```bash
wget https://github.com/apache/zeppelin/archive/refs/tags/v0.10.0.zip
unzip v0.10.0.zip
cd zeppelin-0.10.0
```

We already prepared a Apache Zeppelin source code with all Dockerfiles included at https://github.com/ThinkportRepo/zeppelin/tree/tp-release-v0.10.0.
You can clone this repo if you want, but please note that we will most likely not adapt changes to newer Apache Zeppelin versions.

```bash
git clone https://github.com/ThinkportRepo/zeppelin/
cd zeppelin
git checkout tp-release-v0.10.0
```

Please follow the build order as purposed here.

## Zeppelin Distribution

First, we will create the distribution image which is a complete build of Zeppelin and all available interpreters. Parts of this distribution will be used in the following image builds.

There are three options to get a Zeppelin Distribution as base image.

### Use an official build
You can use the images from https://hub.docker.com/r/apache/zeppelin/tags as
base image in the following Dockerfiles.
Please note that the tag must fit the version you have downloaded during
the preparation step.

### Build an image with a binary distribution
If you want to build your own image based on a release from the Apache Zeppelin
website, you can use [this Dockerfile](zeppelin-distribution-binary/). This makes sense when you want to exclude
certain interpreters or do other modifications to Zeppelin.

### Build Zeppelin from source
When you want to use the newest version (e.g. to use newer Spark releases) or a
certain release from the git, you have to build Zeppelin from source.
Please follow the instructions [here](zeppelin-distribution-source/).

## Zeppelin Server Image

The server image is used to start the spark server itself.
You can find the detailed instructions in [zeppelin-server](zeppelin-server/)

## Zeppelin Interpreter Image

The Zeppelin interpreter image will be used to interpret the content of your notebooks. That means R, Python or Spark will be run by executing this image.
All required files can be found at [zeppelin-interpreter](zeppelin-interpreter/)

## Spark Image

This image will be executed by the Zeppelin interpreter to run your Spark jobs. That means your Spark notebooks are interpreted by the Zeppelin interpreter container but executed in separated Spark containers (see image XXX).
You can find the Dockerfile to build Spark with S3 support [here](spark/).

# Kubernetes Deployment
