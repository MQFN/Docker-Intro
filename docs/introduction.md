## Introduction
For understanding how docker works it is important to understand the very basics of how virtual machines and why docker outperformes a virtual machines by some key architectural perks. 

Head over to the docker documentation for understanding how docker is different from a virtual machine. After reading the documetation here is the conclusion I drew from it, In case a typical virtual machine there is what is called a hypervisor that runs over the host operating system in order to allocate resources to each of the guest operating systems which are essentially virtual machines. This is a bad design when compared to docker because this design is resource intensive and the resulting image and application state is an entanglement of the OS settings. What docker will do instead is use the same host operating system's kernel and the only information that needs to be in the docker container as they are called is the package dependencies and the executable for the application. 

Docker also provides great support for continuous integration and continuus delivery. Each docker container is a light weight process and this is essential as this is the key to making docker efficient and fast.

## Building an App the Docker way
The bottom of the hierarchy in building an app is the container, above this level is the service which defines how containers are supposed to behave in production. Finally at the top is the stack which defines interactions to all other services. 

### Defining a new development environment
So for instance in case of a development environment in python, we were required to install a virtual environment and the environment would be like this folder in `/home/<username>/virtualenvs/<environment_name>`. This now is an issue because we would have this requirements file and pip would have to installed in the production machines or qa machines and we would separately have to run `pip install requirements.txt` in order to install the requirements into the virtual environment set up previously in the production machine. This is all too much work. So here comes docker to ease this process. With docker, we can just grab a portable python runtime as an image, no installation necessary. Then our build can include the base python image right alongside the app thus ensuring the app, its dependencies and the runtime all travel along with the app itself. These portable images are defined in what is called the `Dockerfile`.

* Dockerfile: `Dockerfile` pretty much define what goes on inside the environment of our container. Access to resources like networking interfaces and disk drives is virtualized inside this environment, which is isolated from the rest of the system, so we will need to map ports to the outside world and be specific about what files to copy into the environment. So in the end all we have to do is to build this dockerfile and run the application, building the docker file first creates an image and once that image is created we can easily go around running the application with that image.

### Registry and Repository
Registry is where we store our images. We can run our own registry or we can use docker's public registry called docker hub. Inside a registry the images are stored in repositories. Inside each repository there can be multiple images. So for instance if we have a registry containing 3 repositories, one for ubuntu, one for redhat and one for nginx then inside each repository there can be multiple images. For instance inside the ubuntu repository there can be multiple ubuntu images. Each one of these images could represent a different ubuntu version. Think of dockerhub as the app store for docker. 

### Docker Orchestration tools
Orchestration is the automated management of computer systems basically. 
* The Docker machine provisions Docker hosts and installs Docker Engine in them. 
* Docker Swarm: Clusters many engines and schedules containers. So when we run a container, it decides which host in the cluster the container should run on.
* Docker Compose: Allows us to define multi container applications. It creates and manages docker containers. 

### Benefits
Greater separation of responsibility and concerns. For instance a developer can simply worry about developing the application in the container and a sys-admin can worry about building the application. Previously there may be missing dependencies that the sys-admin needed to worry about. However with docker everything that the app needs is actually inside the container itself and there is no chance of missing dependencies anywhere. 

### Docker Images
We first know that images are stored in registries and the public registry is DockerHub. Once we login to docker hub 'hub.docker.com', Once logged in we see our profile and in our profile we can see all of our repos. We can also browse for repositories from the community. When we try spinning up a container using an image, docker will first try to find that image in our local machine. If nothing is found then only docker will go over to the internet to download the image. Images are specified using a repository and a tag. The same image can actually have multiple tags. If no tags are specified for a repo, docker will use the default tag which is `latest`. To find out what tags are available for a repo, you will need to check that out in the registry. 

- Docker run command: A docker instance can be spun straight away using the following command. `docker run <reponame>:<tag> <command> <args>`. Recall that an image is always specified by a repository and a tag. So the pair of repo name and args actually recognize a specific image. command is simply a command to be run in the container.

- Running a container with a terminal: This is useful and is done in the following way. `docker run -i -t <reponame>:<tag> /bin/bash`. The -i flag simply makes sure that an input is configured with the container and the -t option attaches a sudo terminal witht the container. 

### Container Process
Our containers for instance in the very first case where we were simply running one command and nothing more, don't run for very long. The container would only run as long as the process we specify inside the docker run command is running. 

### Container ID
When we wanna reference a container, we want to use either the container ID or the container name. The short id of a container we can get simply by typing `container ps`. This will give us the short id of the container. Finding our containers is rather easy using a command like `docker ps`. `docker ps` lists out all containers that are currently running.

### Running Containers in Background
Most of the time it has been seen that we would actually want to run the container in the detached mode or running the containers in background. This is done using the `-d` option. After doing that we can use `docker logs <long_id>` to find out the logs generated by the container running in background.

### Port Mapping in Docker
Port mapping between the ports the container has exposed and the ports on our system needs to be done using the `-P` flag. Now the web browser will allow us to access our instance on the container. In the docker run command if we do not specify a command it is going to use the default command of the image or configured in the image. The -P flag will actually port map based on the settings of the image. 

### Building Images by Committing the changes we make in a container
A docker image is essentially made up of file systems layered over each other. Each layer is itself also an image. The image that is below is refered to as the parent image and the image that is at the very bottom of the stack is called the base image or the base layer. All these file systems are mounted as read only. When we lauch a container from an image docker adds a writable layer in other words a read-write file system on top of all the layers. The process that we tell the container to run will run on thi read-write layer. Any changes that we make occur on this layer as well. Now if we want to make any changes on the read only layer then how do we that. Well if we want to make a change to a file in the read only layer that file is first copied onto the writable layer and then changes are made there. The same file is also present in the read only layer however it is hidden. 

#### Docker commit command
This command saves the changes we make in a container as a new image. When we run the command we specify which container we are committing and we need to specify the image repo and tag. The default tag is used in case we do not specify a tag. The exact syntax of this command would be `docker commit <id_of_image> <repo_name>:<tag>`. Now the repo name convention should be username followed by a slash and the name of the application that the container is supposed to run. 
