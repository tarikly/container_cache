# docker cache layering scenario

Requirements:

docker
buildah


## host conf

`/etc/containers/registries.conf`
```
unqualified-search-registries = ["docker.io"]

[[registry]]
  prefix = "docker.io"
  location = "${DOCKER_REGISTRY_SERVER_ADDRESS}:5000"
  insecure = true
```

`$ docker run -it --privileged -v "C:\Users\tarikly\registries.conf":/etc/containers/registries.conf --rm debian /bin/bash`


## docker registry conf 


`/etc/docker/registry/config.yml`
```
version: 0.1
log:
  fields:
    service: registry
storage:
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
# auth:
#   htpasswd:
#     realm: basic-realm
#     path: /etc/registry
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
proxy:
  remoteurl: https://registry-1.docker.io
```


`$ docker run -i --rm --name mirror -v "C:\Users\tarikly\config.yml":/etc/docker/registry/config.yml -e MIRROR_SOURCE=https:/registry-1.docker.io -e MIRROR_SOURCE_INDEX=https://index.docker.io -p 5000:5000 registry:2.7`


### Resources:

https://docs.docker.com/docker-hub/mirror/

https://github.com/containers/buildah/blob/main/tests/registries.conf

https://blog.alexellis.io/how-to-configure-multiple-docker-registry-mirrors/

https://docs.docker.com/docker-hub/mirror/#:~:text=Run%20a%20Registry%20as%20a,described%20in%20the%20following%20subsection.

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/building_running_and_managing_containers/working-with-container-registries_building-running-and-managing-containers

https://gdevillele.github.io/registry/configuration/
