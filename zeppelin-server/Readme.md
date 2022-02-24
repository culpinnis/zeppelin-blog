# Zeppelin Server
The server image is used to start the spark server itself. The build process copies the required files from our distribution image into the server image that is based on Ubuntu 20.04. It installs OpenJDK-8 and sets the required environmental variables.
## Instructions
Copy this Dockerfile to scripts/docker/zeppelin-server in the Zeppelin source.

```bash
cd scripts/docker/zeppelin-server/
# insert/replace dockerfile
docker build -t yourrepo/zeppelin-server:0.10.0 .
docker push yourrepo/zeppelin-server:0.10.0
```
