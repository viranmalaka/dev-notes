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
- `docker create / docker start return` 
  - docker create returns id of the new container
  - `-a` return the container output
  - set the default command 
- `docker system prune`
  - delete all the docker containers (clean the space)
- `docker log <id>`
  - get the log from the container
- `docker stop <id>`
  - use `SIGTERM`, the container can do some cleanup things and stop the process properly. If the process does not respond to that signal, the docer will kill the container after waiting 10s.
- `dokcer kill <id>`
  - use `SIGKILL`, the container have to shut id down immediately
- `docker exec -it <id> <cmd>`
  - execute another command on the running container instead of default cmd.
  - `-i` enabled the STDN and STDOUT channel,
  - `-t` enabled the properly formatted input output from the terminal.
> every process running in Linux env has 3 main communcation channels. they are `STDIN`, `STDOUT` and `STDERR`
- `docker exec -it <id> sh`
  - shell access to the container
- `docker run -it <image-name>`

---
## Create Dokcer Images
- `Dockerfile`
  - `FROM` - set the base image
  - `RUN` - execute some command when creating the image
  - `CMD` - command to run when the container is starting.

- Docker image creation background
  - for each command step, docker creates a brand new container with the previous output and execute the given command, after that command is over, it saves that temp container file system as snapshot. 
  - the next command will be used the filesystem that has output by it’s previous step.
  - Docker users cache technique to speedup the image building.
    - docker will only execute the command that are affected by the changed. the first steps are taken from the cache.
- tagging and image name
  - `-t` to tag the building image
  - convention: `<your docker id>/<project>:<version>`
  - Note: image tag is the version of the image.
- Generate an image from a container (don’t use this in general)
  - startup a docker image `docker run alpine -it sh`
  - find the id of above container by `docker ps`
  - commit it
    - docker commit -c `CMD [“redis-server”] <docker id>`
- Port Forwarding
  - `-p 80:80`

- Docker Compose
  - use to start a set of docker iamges.
  - does the same thing as `docker-cli`, but as a file, we can reduce the repetition.
```yaml
version: '3'
services:
  redis-server:
    image: 'redis'
  node-app:
    build: .
    ports:
      - "4000:5000"
```
  - the docker-compose creates a default network with the above two containers
  - one container can access the other by using its container name (`redis-server`, `node-app`) as the hostname
  - so inside the nodejs code, we can access Redis by `redis-server` as the host.
  - `docker-compose up` -> for start the app
  - `docker-compose up --build` -> rebuild the images
  - `docker-compose down` -> stop all the service which started by that docker file
  - automatic restart
    - `Status Code = 0`: we exited and everything is OK
    - `Status Code = 1, 2, 3, etc..`: We exited because of something went wrong!
  - docker restart policies
    - `no` - (default) and not start again
    - `always`
    - `on-failure` - restart for an error (non 0 exit status codes)
    - `unless-stopped`
- Bookmarking
  - `docker run -p 3000:3000 -it -v /app/node_modules -v $(pwd):/app <con-id>`
    - if `-v` parameter has `:` it says to map those two paths
    - if `-v` parameter doesn't have `:` it says to keep it as it is and don't do any mapping. 
    - in this case, map all the present working dir files to `:/app` folder but ignore the `node_modules` folder. so docker will use the `node_modules` folder inside the container

- Multi-step build process
  - build the react app
  - copy the build folder and open up an nginx server
```docker
FROM renovate/yarn as builder
USER root
WORKDIR '/app'

COPY package.json .
COPY yarn.lock .
RUN yarn

COPY . .
RUN yarn run build

FROM nginx
COPY --from=builder /app/build /usr/share/nginx/html
```

---
## Multiple Container Dev Environment
- setup react client, node expressjs server, worker node for doing heavy calculation
- dbs are redis and postgress
- expose all the service from one nginx port with easy dev setup

```
# client Dockerfile.dev. server and worker will similar to that except paths
FROM node:alpine
WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "start"]

# nginx/Dockerfile.dev
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

```nginx
# nginx/default.conf file

upstream client {
  server client:3000;
}

upstream api {
  server api:5000;
}

server {
  listen 80;

  location / {
    proxy_pass http://client;
  }

  location /sockjs-node {
    proxy_pass http://client;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
  }

  location /api {
    rewrite /api/(.*) /$1 break;
    proxy_pass http://api;
  }
}
```

```yaml
# this is docker-compose.yaml file in the root dir
version: "3"
services:
  postgres:
    image: "postgres:10"
    environment:
      POSTGRES_PASSWORD: "example"  # default password is must to start the server
  redis:
    image: "redis:latest"           # just setup the redis instance
  api:
    build:
      dockerfile: Dockerfile.dev    
      context: ./server             # api service is based on the Dockerfile which is in the /server dir
    volumes:
      - /app/node_modules           # ignore mapping node_modules from the volumes
      - ./server:/app               # use the current directory from the container to take the latest update when coding
    environment:                    # setting the envs
      REDIS_HOST: "redis"
      REDIS_PORT: 6379
      PGUSER: "postgres"
      PGHOST: "postgres"
      PGDATABASE: "postgres"
      PGPASSWORD: "example"
      PGPOST: 5432
  client:
    stdin_open: true                # react client will need this to keep the server up
    build:
      dockerfile: Dockerfile.dev
      context: ./client
    volumes:
      - /app/node_modules
      - ./client:/app
  worker:
    stdin_open: true
    build:
      dockerfile: Dockerfile.dev
      context: ./worker
    volumes:
      - /app/node_modules
      - ./worker:/app
    environment:
      REDIS_HOST: "redis"
      REDIS_PORT: 6379
  nginx:
    restart: always                 # set restart policies to keep the nginx server up always
    build:
      dockerfile: Dockerfile.dev    
      context: ./nginx
    ports:                          # this port will be open to access the full application
      - "3000:80"
    depends_on:
      - api
      - client

```