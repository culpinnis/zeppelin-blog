# Zeppelin Server
The server image is used to start the spark server itself. The build process copies the required files from our distribution image into the server image that is based on Ubuntu 20.04. It installs OpenJDK-8 and sets the required environmental variables.
## Instructions
You should set the version to the version of Zeppelin used in the Zeppelin distribution image.
You have to set the REPO build-arg to the name of your repository (or Dockerhub account).

```bash
export version=0.10.0
docker build --build-arg version=$version --build-arg REPO=yourrepo -t yourrepo/zeppelin-server:${version} .
docker push yourrepo/zeppelin-server:${version}
```
