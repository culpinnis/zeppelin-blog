# Zeppelin Interpreter
The Zeppelin interpreter image will be used to interpret the content of your notebooks. That means R, Python or Spark will be run by executing this image. For this reason, we will include all binaries and applications that are required to run your code. If you know that you will not use one of the runtime engines or their packages, you can exclude them in the Dockerfile and the env_python3_with_R.yaml file or the pip_packages.txt.

Our modification to the original Dockerfile is the usage of mamba instead of conda because when including all interpreters conda will calculate the environment forever without any solution. Mamba is already implemented in the scripts/docker/zeppelin/bin/Dockerfile by the Zeppelin devs and we just took it from there.

## Instructions
Copy the content of this folder to scripts/docker/zeppelin-interpreter in the Zeppelin source.

```bash
cd ..
cd zeppelin-interpreter
# insert/replace dockerfile
# insert env_python3_with_R.yaml
# insert pip_packages.txt
docker build -t yourrepo/zeppelin-interpreter:0.10.0 .
docker push yourrepo/zeppelin-interpreter:0.10.0
```
