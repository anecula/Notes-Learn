
# Orchestrate Containers for Development with Docker Compose

Before dive into the docker-compose, lets start with a basic difference between `docker cli` and `docker-compose`

> The `docker cli` is used when managing individual containers on a docker engine. It is the client command line to access the docker daemon api.

> The `docker-compose cli` can be used to manage a multi-container application. It also moves many of the options you would enter on the docker run cli into the docker-compose.yml file for easier reuse. It works as a front end "script" on top of the same docker api used by docker, so `you can do everything docker-compose does with docker commands and a lot of shell scripting`. 

`Docker Compose`: 
```a tool for defining and running complex applications with Docker. With Compose, you define a multi-container application in a single file, then spin your application up in a single command which does everything that needs to be done to get it running.” (https://docs.docker.com/compose/).```
> All of that can be done by Docker Compose in the scope of a single host.

The main function of Docker Compose is the creation of microservice architecture, meaning the containers and the links between them. But the tool is capable of much more:
- Building images (if an appropriate Dockerfile is provided)
`docker-compose build`
- Scaling containers running a given service
`docker-compose scale SERVICE=3`
- Healing, i.e., re-running containers that have stopped
`docker-compose up --no-recreate`

All this functionality is available through the `docker-compose` utility, which has a very similar set of commands to what is offered by `docker`:
```
build    Build or rebuild services 
help     Get help on a command 
kill     Kill containers 
logs     View output from containers 
port     Print the public port for a port binding 
ps       List containers 
pull     Pulls service images 
rm       Remove stopped containers 
run      Run a one-off command 
scale    Set number of containers for a service 
start    Start services 
stop     Stop services 
restart  Restart services 
up       Create and start containers
```
They are not only similar, but they also behave like docker counterparts. The only difference is that they affect the entire multi-container architecture defined in the docker-compose.yml configuration file and not just a single container.
You’ll notice some docker commands are not present in docker-compose. Those are the ones that don’t make sense in the context of a completely multi-container setup. 
For instance:
Commands for image manipulation (e.g., `save, search, images, import, export, tag, history`)
User-interactive (e.g., attach, exec, `run -i, login, wait`)
A command that is worth your attention is the `docker-compose up` command. It is a shorthand form of `docker-compose build && docker-compose run`

### Docker Compose Workflow
There are three steps to using Docker Compose:
    Define each service in a Dockerfile.
    Define the services and their relation to each other in the docker-compose.yml file.
    Use docker-compose up to start the system.
    
I’ll show you the workflow in action using two real-life examples. First, I’ll demonstrate `basic syntax of docker-compose.yml and how to link containers`. 

Example 1: Basic Structure

> The syntax of the docker-compose.yml file closely reflects the underlying Docker operations. To demonstrate this, I’ll build a   container from Redis Commander sources and connect it to the Redis database.
  Implementation (source)

Let’s create a project with the following structure:
```
example1 
├── commander 
│ └── Dockerfile 
└── docker-compose.yml
```
Now let’s follow the workflow. Define the Dockerfile that builds Redis Commander (see source), and then create docker-compose.yml.
```
backend: 
  image: redis:3 
  restart: always

frontend: 
  build: commander 
  links: 
    - backend:redis  
  ports: 
    - 8081:8081 
  environment: 
    - VAR1=value 
  restart: always
  ```
Now execute:
`docker-compose up -d`
After that, point your browser to http://localhost:8081/. You should see the user interface of Redis Commander that’s connected to database.

```
Note:
The command docker-compose up -d has the same effect as the following sequence of commands:
docker build -t commander commander
docker run -d --name frontend -e VAR1=value -p 8081:8081
   --link backend:redis commander
   ```
Each service needs to point to an image or build directory; all other keywords (links, ports, environment, restart) correspond to docker options.
```
docker-compose up -d builds images if needed.
docker-compose ps shows running containers.
$ docker-compose ps
     Name               State               Ports
---------------------------------------------------
example1_backend_1       Up               6379/tcp   
example1_frontend_1 ...  Up               0.0.0.0:8081->8081/tcp
docker-compose stop && docker-compose rm -v stops and removes all containers
```
# Manage an application’s configuration data across development and testing environments
Example 2: Configuration Pack

Now let’s deploy an application to two different environments — development and testing — in such a way that it would use different configuration depending on the target environment.
Implementation (source)

One of our possible options is to wrap all environment-specific files into separate containers and inject them into the environment if needed.
Our project will have the following structure:
```
example2 
├── common 
│ └── docker-compose.yml 
├── development 
│ ├── content 
│ │ ├── Dockerfile 
│ │ └── index.html 
│ └── docker-compose.yml 
└── testing 
├── content 
│ ├── Dockerfile 
│ └── index.html 
└── docker-compose.yml
```
And again let’s follow the workflow.
First let’s create Dockerfiles ({development,testing}/content/Dockerfile) that wrap environment-specific content in containers. In this case, just to illustrate the mechanism, containers will contain only an index.html file with an environment-specific message (e.g., “You are in development!”).
```
FROM tianon/true

VOLUME ["/usr/share/nginx/html/"] 
ADD index.html /usr/share/nginx/html/
```
Note that because we don’t need an operating system, the images are built on top of the smallest fully functional image: tianon/true. The image is originally built FROM scratch and contains only /true binary.

Now let’s create docker-compose.yml files. common/docker-compose.yml contains shared services of application. In this case, it’s just one service: nginx server with the definition of port mappings:
```
web: 
  image: nginx 
  ports: 
    - 8082:80
    ```
The definition will be used through inheritance.
Each environment needs its own docker-compose.yml file (development/docker-compose.yml and testing/docker-compose.yml) that injects the correct configuration pack.
```
web: 
  extends: 
    file: ../common/docker-compose.yml 
    service: web 
  volumes_from: 
    - content

content: 
  build: content
```
Executing the following commands:
```
cd development 
docker-compose up -d
```
Point a browser to http://localhost:8082/. You should be able to see the “You are in development!” message. You can activate testing environment setup by executing:

```
docker-compose stop 
cd ../testing 
docker-compose up -d
```
When you reload the browser, you should see the “You are in testing!” message.
```
Note:
web service inherits from common/docker-compose.yml. Its original definition is extended by the volumes_from directive that maps volumes from content container. This is how the application gets access to environment-specific configuration.
After start, content container executes the true command and immediately exits, but its data remain exposed to the web container, which stays active.
```


Sources:
stackoverflow.com
docs.docker.com/compose/
codeship.com
