## Introduction
For understanding how docker works it is important to understand the very basics of how virtual machines and why docker outperformes a virtual machines by some key architectural perks. 

Head over to the docker documentation for understanding how docker is different from a virtual machine. After reading the documetation here is the conclusion I drew from it, In case a typical virtual machine there is what is called a hypervisor that runs over the host operating system in order to allocate resources to each of the guest operating systems which are essentially virtual machines. This is a bad design when compared to docker because this design is resource intensive and the resulting image and application state is an entanglement of the OS settings. What docker will do instead is use the same host operating system's kernel and the only information that needs to be in the docker container as they are called is the package dependencies and the executable for the application. 

Docker also provides great support for continuous integration and continuus delivery. Each docker container is a light weight process and this is essential as this is the key to making docker efficient and fast.

## Building an App the Docker way
The bottom of the hierarchy in building an app is the container, above this level is the service which defines how containers are supposed to behave in production. Finally at the top is the stack which defines interactions to all other services. 

### Defining a new development environment
So for instance in case of a development environment in python, we were required to install a virtual environment and the environemnt would be like this folder in `/home/<username>/virtualenvs/<environment_name>`
