# Zeppelin Distribution Image built from git source code
The Dockerfile presented here will download a defined commit of Zeppelin from GitHub. You can change the Zeppelin version with the version build-arg. 
## Instructions
You have to set the version and commit build-args. The version must include -SNAPSHOT as suffix. 

```bash
docker build --build-arg version=0.11.0-SNAPSHOT --build-arg commit=aa32f62 -t yourrepo/zeppelin-distribution:0.11.0-SNAPSHOT .
```
