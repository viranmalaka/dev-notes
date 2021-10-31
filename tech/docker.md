# **DOCKER**

**\=======**

```powershell
docker container run --publish 80:80 nginx

```

**docker container run --publish 80:80 --detach nginx**

**docker  
**

# **Docker and Kubernetes: The Complete Guide**

-   **why use docker: easy to setup / install new server/ application**
    
-   **docker client is the application which communicates with the docker daemon and does the task that we want.**
    

**a docker container is a combination of **

-   **the running process**
    
-   **kernel (part of)**
    
-   **piece of hardware allocated to it (Hard, RAM, CPU, Network)**
    

**docker image is**

-   **snapshot of set of files**
    
-   **it has a startup command**
    

**docker run <container-image-name>**

-   **check the local cache for the image**
    
-   **if not download**
    
-   **create a new container from the image**
    
-   **and execute the default command**
    

**docker run <container-image-name> <override default command>**

-   **docker busybox echo hello**
    

**docker ps**

-   **list all running images**
    

**docker create / docker start**