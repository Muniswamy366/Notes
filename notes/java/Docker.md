# Docker Commands

-   `docker version`
-   `docker info`
-   `ps ax | grep dockerd` To see dockerd details
-   `ls /var/lib/docker` docker objects and old docker logs
-   `ls /var/run/docker` current docker logs
-   `ls /sys/fs/cgroup` cgroup info
-   `ls /var/lib/docker/image/overlay2/layerdb/sha256/` shows list of layers
-   `docker pull ubuntu` Pulls latest tag image from hub.
-   `docker pull ubuntu:18.04` Pulls 18.04 tag from hub.
-   `docker images` List all the images
-   `docker inspect <image-id>` Image info first 3 digit of image id works
-   `docker inspect <container-id>` Inspect the container and image
-   `docker create fedora` Create a new container (pull+create)
-   `docker run hello-world` First it will search in local, pulls image from dockeer hub and create and runs container. pull + create + start + attach
-   `docker run -it --name testubuntu1 ubuntu` -it for Interactive terminal
-   `docker run -itd --name testubuntu2 ubuntu` it's necessary to include -it even you -d, When the ENTRYPOINT is bash or sh
-   `docker stop <container-id>` Stop the container
-   `docker start <container-id>` Stop the container
-   `docker kill <container name>`
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
-   `docker commit <container-id> cloudubuntu:v1` To convert container to image. docker run for image to container.
-   `docker exec testUbuntu cat /etc/hosts`

Running a container from the image
Syntax: docker run -itd --name <container-name> -p <host-port>:<port in container> image-name:tag
note in the above syntax:
-d : represents (detached mode), note that if you dont run this in detached mode, the life of the container will be the life of the terminal in which you are executing it.
-p : represents the host-port to container-port mapping, if you substitute it with -P you will get a random port allocated by docker
--name : represents the name of the container 

docker run -itd --name my-http-container-1 -p 5555:80 my-httpd:latest
docker run -itd --name my-http-container-2 -p 5556:80 my-httpd:latest
docker run -itd --name my-http-container-with-random-port -P my-httpd:latest

#note if you run this it will NOT run in detached mode.
docker run -it --name my-http-container-not-detached-mode -p 5557:80 my-httpd:latest



Step 6: Logging into Dockerhub account on the terminal
-------------------------------------------------------------------
docker login 
docker tag <currentimage>:<tag> <repository-name>/<image-name>:<tag>
docker push <repository-name>/<image-name>:<tag>
docker commit <container-id> <repository-name>/<image-name>:<tag>

docker pull <repository-name>/<image-name>:<tag>



1. Pull MySql image from Docker Hub (https://hub.docker.com/_/mysql/)
`docker pull mysql:5.7`

2. Create MySql container from docker image.
`docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test -e MYSQL_USER=muni -e MYSQL_PASSWORD=root -d mysql:5.7`

3. In the Spring Boot Application, use the same container name of the mysql instance in the application.properties
`spring.datasource.url = jdbc:mysql://mysql-container:3306/test`

4. Using the Dockerfile create the Docker image.
From the directory of Dockerfile - `docker build . -t spring-boot-jpa-hibernate-docker`

5. Run the Docker image (spring-boot-jpa-hibernate-docker) created in #4.
`docker run -p 8086:8086 --name spring-boot-jpa-hibernate-docker --link mysql-container:mysql -d spring-boot-jpa-hibernate-docker`

# Pulling Image from Docker hub

1. Follow above steps 1 to 3.

2. Pull spring-boot-jpa-hibernate-docker project image from Docker Hub (https://hub.docker.com/r/muniswamy/springbootrep/)
`docker pull muniswamy/springbootrep`

3. Run the Docker image (spring-boot-jpa-hibernate-docker) created in #4.
`docker run -p 8086:8086 --name spring-boot-jpa-hibernate-docker --link mysql-container:mysql -d muniswamy/springbootrep`

# Sample Request

{
"id":1,
"name":"muni",
"salary":200
}

## Useful Docker commands
- `docker images`
- `docker container ls`
- `docker logs <container_name>`
- `docker container rm <container_name`
- `docker image rm <image_name`
