# Kubernetes Deployment
This folder includes a sample yaml file to deploy Zeppelin Server in
Kubernetes.

There are some parts in the zeppelin-server.yaml that can/must be adjusted when using your own images. You have to edit the configMap zeppelin-server-conf-map:

*ZEPPELIN_K8S_SPARK_CONTAINER_IMAGE:* your Spark image
*ZEPPELIN_K8S_CONTAINER_IMAGE:* your Zeppelin interpreter image

Furthermore, in the deployment, you have to change the Container image to the name of your server image.
We also added PVC to have persistent storage for settings and notebooks.
If you are using our images, you can use this yaml directly. If you just want to play around without persistent storage, remove the volumeMounts for notebook and settings as well as the PVC entries.
