# Docker Commands

-   `docker version`
-   `docker info`
-   `ps ax | grep dockerd` To see dockerd details
-   `ls /var/lib/docker` docker objects and old docker logs
-   `ls /var/run/docker` current docker logs
-   `ls /sys/fs/cgroup` cgroup info
-   `ls /var/lib/docker/image/overlay2/layerdb/sha256/` shows list of layers
-   `/sys/fs/cgroup/cpu/docker/ or /sys/fs/cgroup/memory/docker/` where all memory info available
-   `docker pull ubuntu` Pulls latest tag image from hub.
-   `docker pull ubuntu:18.04` Pulls 18.04 tag from hub.
-   `docker images` List all the images
-   `docker search hello-world` Search hello-world image 
-   `docker inspect <image-id>` Image info first 3 digit of image id works
-   `docker inspect <container-id>` Inspect the container and image
-   `docker create fedora` Create a new container (pull+create)
-   `docker run hello-world` First it will search in local, pulls image from dockeer hub and create and runs container. pull + create + start + attach
-   `docker run -it --name testubuntu1 ubuntu` -it for Interactive terminal
-   `docker run -itd --name testubuntu2 ubuntu` it's necessary to include -it even you -d, When the ENTRYPOINT is bash or sh
-   `docker run -it ubuntu /bin/bash`
-   `docker stop <container-id>` Stop the container
-   `docker start <container-id>` Stop the container
-   `docker kill <container name>`
-   `docker kill $(docker ps -q)` kill all the running containers
-   `docker ps` Shows all the containers which are running
-   `docker ps -a` Shows all the containers stopped and running
-   `docker logs <container-id>` View the logs of the container
-   `docker logs -ft <container-id>` Tail the logs of the container
-   `docker history tomcat` Shows history of the image.
-   `docker top <container-id>` shows PID
-   `ps ax | grep containerd` to see containerd and ppid
-   `docker rm <container-id>` Remove a specific container
-   `docker rm $(docker ps -a -q)` Remove all containers -q is to get only container id's
-   `docker rmi <image-id>` remove image (note: no containers for this image should be running)
-   `docker rmi $(docker images -q)` remove all images
docker run -d ubuntu:14.04 will immediately stop, cause bash can't find any pseudo terminal to be allocated. You have to specify -it so that bash or sh can be allocated to a pseudo terminal.
-   `docker build -t my-httpd .` Building an image Syntax: docker build -t image-name location-of-dockerfile
-   `docker tag <image-id> tagname` to give another name to image.
-   `docker push`
-   `docker exec -it <container-id> /bin/bash`  exec for child and attach for parent. Logging into the container, Note: The container must be started before we can do this.
-   `docker attach imagename` Attach local standard input, output, and error streams to a running container
Docker attach attaches to the process that runs in the container with PID 1. Where as Docker exec is used run a new process inside the container. down the container because we are exiting from parent
-   `docker commit <container-id> cloudubuntu:v1` To convert container to image. docker run for image to container. Syntax: docker commit <container-id> <repository-name>/<image-name>:<tag>
-   `docker exec testUbuntu cat /etc/hosts`
-   `docker run -itd --name my-http-container-1 -p 5555:80 my-httpd:latest` Syntax: docker run -itd --name <container-name> -p <host-port>:<port in container> image-name:tag
-p : represents the host-port to container-port mapping, if you substitute it with -P you will get a random port allocated by docker
-   `docker save -o ubuntu.tar muniswamy/cloudutuntu:1.0` to save locally
-   `docker container prune` This will remove all stopped containers.
-   `docker image prune –a` This will remove all images if no container associated to them.
-   `docker stats cname` to see container occupied memory
-   `docker events --since 60` backup and recovery
-   `docker system df` Shows Images, Containers, Local Volumes, Build Cache list.
-   `docker volume ls` shows valume
-   `docker run -d --name monitornode --helth-cmd="curl 192.168.99.100:9001/listUsers" --helth-retries=10 --helth-timeout=2s  --helth-interval=1s newnginx` check memory utilization and health check
-   `docker login`
-   `docker tag cloudubuntu:v1 muniswamy/cloudubuntu:1.0` Syntax: docker tag <currentimage>:<tag> <repository-name>/<image-name>:<tag>
-   `docker push muniswamy/cloudutuntu:1.0` Syntax: docker push <repository-name>/<image-name>:<tag>
-   `docker pull muniswamy/cloudutuntu:1.0` Syntax: docker pull <repository-name>/<image-name>:<tag>
-   `docker ps -a --format "{{.ID}} {{.Image}}"`
-   `docker ps -a --format "{{.ID}} {{.Image}} {{.Command}}"` Create in specific Format as per the ask
-   `docker inspect -f "{{ .Created }}" mybuntu`
-   `docker inspect -f "{{.Created}} -- {{.HostConfig.CpuShares}} -- {{.State.Pid}} -- {{.NetworkSettings.IPAddress}}"  <<name of container>>` Get exact creation date of the Container
-   `docker inspect -f "{{.NetworkSettings.IPAddress}}" myoraclelinux`
-   `docker ps -q | xargs docker inspect -f "{{.NetworkSettings.IPAddress}}"`
-   `docker inspect -f '{{.NetworkSettings.IPAddress}} --> {{.Name}}' $(docker ps -q)`
-   `docker update --cpu-shares 256 --kernel-memory 200M nodesample`


# Docker Machine

-   `docker-machine ls` List of VM boxes running
-   `docker-machine status` Running
-   `docker-machine url` tcp://192.168.99.100:2376
-   `docker-machine ip` show machine ip


# Dockerfile:
-   FROM: to add base image.
-   COPY: add local files to image.
-   ADD: add local/cloud files to image. (files copied not permeations)
-   RUN: once image build we can run this.
-   ARG: confidential 
-   ENV:
-   CMD: after container created. (CMD is variable component)
-   ENTRYPOINT: is fixed component.


# Points
-   container which communicates with IP should be in same network.
-   container which communicates with Port should be in different network.
-   docker: is used inside docker machine
-   docker-machine: is used across docker machines.
-   kubectl: is used inside node.
-   kubeadm: is used cross nodes.
-   static port <30,000 and dynamic port is >30,000
