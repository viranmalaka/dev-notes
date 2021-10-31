# DOCKER

eg: 
  - `docker container run --publish 80:80 nginx`
  - `docker contianer run --publish 80:80 --detach nginx`

## Learnings from Docker and Kubernetis: The Complete Guid (Sysco Udemy)
- why use docker: easy to setup / install new server/ application
- docker client is the application which communicates with the docker - daemon and does the task that we want.
- a docker container is a combination of 
  - the running process
  - kernel (part of)
  - piece of hardware allocated to it (Hard, RAM, CPU, Network)
- docker image is
  - snapshot of set of files
  - it has a startup command
- `docker run <container-image-name>`
  - check the local cache for the image
  - if not download
    - create a new container from the image
    - and execute the default command
- how to override the default command
  - `docker run <container-image-name> <override default command>`
    - eg:  `docker busybox echo hello`
---
- listdown all the running contains
  - `docker ps`

