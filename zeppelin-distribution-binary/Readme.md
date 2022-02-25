# Zeppelin Distribution Image built from binary release
The Dockerfile presented here will download Zeppelin release v0.10.0 as binary distribution for x86_64 architecture.
You can change the Zeppelin version with the version build-arg. Check the [Zeppelin download page](https://zeppelin.apache.org/download.html) for available versions.
If you want to use the newest snapshot, you should use the Dockerfile in [build from source](../zeppelin-distribution-source/).

## Instructions
```bash
docker build --build-arg version=0.10.0 -t yourrepo/zeppelin-distribution:0.10.0 .
```
