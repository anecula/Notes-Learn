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
```
sudo docker run --rm --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/rancher:/var/lib/rancher rancher/agent:v2.0-alpha4 http://127.0.0.1:8080/v3/scripts/1B9FFA7FA18AC81F22D7:1483142400000:Gy8u4EO2qzajnnJBtyaebNYOZ4
```

4. Adding a Container through the Rancher UI

- Testing
```sudo docker ps
CONTAINER ID        IMAGE                                                                                                        COMMAND                  CREATED             STATUS              PORTS                              NAMES
96e0768ea93c        rancher/kubernetes-dashboard-amd64@sha256:2c4421ed80358a0ee97b44357b6cd6dc09be6ccc27dfe9d50c9bfc39a760e5fe   "/dashboard --insecur"   10 minutes ago      Up 10 minutes                                          k8s_kubernetes-dashboard_kubernetes-dashboard-1319779976-lp16t_kube-system_3fefdbeb-a852-11e7-996a-0242ac110002_0
53eabb26d02d        rancher/pause-amd64@sha256:3b3a29e3c90ae7762bdf587d19302e62485b6bef46e114b741f7d75dba023bd3                  "/pause"                 11 minutes ago      Up 11 minutes                                          k8s_rancher-pause_kubernetes-support-kubectl-shell-1-c8e0713c_cattle-system_40e10b5c-a852-11e7-996a-0242ac110002_0
77cad53444e5        rancher/kubectld@sha256:990abc69fc07308649453bc362625e62ea888fb8412ad5fa9b1ec50bb8edc413                     "/rancher-entrypoint."   11 minutes ago      Up 11 minutes                                          k8s_kubernetes-support-kubectl-a2122996-bc67-4ce9-ac9e-657e0d1346ac_kubernetes-support-kubectl-shell-1-c8e0713c_cattle-system_40e10b5c-a852-11e7-996a-0242ac110002_0
18c8ed4cfd66        rancher/pause-amd64:3.0                                                                                      "/pause"                 12 minutes ago      Up 11 minutes                                          k8s_POD_kubernetes-dashboard-1319779976-lp16t_kube-system_3fefdbeb-a852-11e7-996a-0242ac110002_0
642ae1d79397        rancher/pause-amd64:3.0                                                                                      "/pause"                 12 minutes ago      Up 12 minutes                                          k8s_POD_kubernetes-support-kubectl-shell-1-c8e0713c_cattle-system_40e10b5c-a852-11e7-996a-0242ac110002_0
3066d568d549        rancher/net:v0.12.3                                                                                          "/rancher-entrypoint."   12 minutes ago      Up 12 minutes                                          r-core-services-ipsec-router-1-b358cfc3
4a4eb4c314ad        rancher/net:v0.12.3                                                                                          "/.r/r /rancher-entry"   12 minutes ago      Up 12 minutes                                          r-core-services-ipsec-1-79afc676
575ee28f806f        rancher/healthcheck:v0.3.3                                                                                   "/.r/r /rancher-entry"   12 minutes ago      Up 12 minutes                                          r-core-services-healthcheck-1-6988a75b
ee69dbf418e3        rancher/metadata:v1.0.7                                                                                      "/rancher-entrypoint."   13 minutes ago      Up 13 minutes                                          r-core-services-metadata-1-a181d749
64f4c3d8a19b        rancher/netes:v1.7.6-netes2-1                                                                                "/usr/bin/entry.sh ku"   13 minutes ago      Up 13 minutes                                          r-kubernetes-support-kubelet-1-3ed9d052
4f4e935aa8f2        rancher/netes:v1.7.6-netes2-1                                                                                "/usr/bin/entry.sh ku"   13 minutes ago      Up 13 minutes                                          r-kubernetes-support-controller-manager-1-917bc712
2b021638a64d        rancher/netes:v1.7.6-netes2-1                                                                                "/usr/bin/entry.sh ku"   13 minutes ago      Up 13 minutes                                          r-kubernetes-support-proxy-1-022cf3f3
74c49d401e44        rancher/netes:v1.7.6-netes2-1                                                                                "/usr/bin/entry.sh ku"   13 minutes ago      Up 13 minutes                                          r-kubernetes-support-scheduler-1-5e5b0c2d
567c7c01976f        rancher/dns:v0.16.4                                                                                          "/rancher-entrypoint."   14 minutes ago      Up 12 minutes                                          r-core-services-dns-1-269fe478
061e07daf86c        rancher/network-manager:v0.8.6                                                                               "/rancher-entrypoint."   15 minutes ago      Up 12 minutes                                          r-core-services-network-manager-1-303c33a0
02a77faff198        rancher/net:v0.12.3                                                                                          "/rancher-entrypoint."   15 minutes ago      Up 12 minutes                                          r-core-services-cni-driver-1-3c227e31
0f1a7b72006e        rancher/agent:v2.0-alpha4                                                                                    "/run.sh run"            16 minutes ago      Up 16 minutes                                          rancher-agent
29c68f3f364f        rancher/server:preview                                                                                       "/usr/bin/entry /usr/"   20 minutes ago      Up 20 minutes       3306/tcp, 0.0.0.0:8080->8080/tcp   gigantic_noether
```
