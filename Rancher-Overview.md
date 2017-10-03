# Rancher Overview

> Rancher is an open source platform that provides a complete set of capabilities for managing containers in production, on any infrastructure.   

```
1. Setting up Rancher
2. Setting up Access Control
3. Creating Environments 
4. Attaching private registries 
5. Adding resources/hosts
6. UI, API, and CLI access
7. Launching containers
8. Creating stacks and services
9. Rancher Compose
10. Load Balancers and Service Discovery
11. Networking options
```

# 1. Setting up Rancher

Steps: 
1. Preparing a Linux Host:
  - Install a supported version of Docker on the host. To install Docker on the server, follow the instructions from Docker
      (https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/)
  - Install rancher (https://releases.rancher.com/cli/v0.6.1/rancher-linux-amd64-v0.6.1.tar.gz):
  ```
  1. untar rancher archive
  2. cd in rancher folder
  3. sudo mv rancher  /usr/bin/
  4. sudo docker logs -f  <conteiner ID>
```
2. Launching Rancher Server and Accessing the Rancher UI
  - Run this Docker command on your host:

  `$ sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server:preview`
  - This process might take several minutes to complete.
  - Test it:
  `http://<SERVER_IP>:8080`
  
3. Adding a Host through the Rancher UI


Importing an Existing Kubernetes Cluster
Adding a Container through the Rancher UI
