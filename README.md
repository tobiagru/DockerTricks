# DockerTricks
Selection of good to knows for Docker

## Dockerfile Best Practice
This is a collection of best practices for writing Dockerfiles. 

The goal would be that dockerfiles used for (&between) development and production should be as similar as possible. Thus try to write the Dockerfile as if it would be used for production as early as possible. The goal in development is still to iterate fast but production ready dockerfiles should not stop you from iterating fast.


### Using Small Base Image
Use the smallest possible base image to run your code. This is typically a version of debian-slim or Alpine linux. Alpine linux uses a different packaging manager (apk) but it is worth getting to know the commands. Packages might be called differently in alpine linux but you can find them here https://pkgs.alpinelinux.org/packages


### Multistage Builds for build stage and runtime
Compilation and installation of packages produces many artifacts thus compile, build & install all your files and dependencies in a first Docker. The first container can include any tool that you require to build, compile and install (i.e npm or pip). Then copy all of your dependencies and compiled sources to a new container that is only contains runtime environments for your code (i.e node or python). This as well means that the runtime container should, not include any debugging tools and similar, only the runtime.


### Install only required packages and clean up after install
Stop the package manager from installing recommended packages that come with a package you want to install and only install the packages required to run the desired package.

apt-get: -- no-install-recommends

apk: --no-cache

As well as clean up the installation files for the packages after installation

apt-get: && rm -rf /var/lib/apt/lists/*

apk: && rm -rf /var/cache/apk/*

### Combine layers

Every layer (every line of code inside your Dockerfile) adds size to your docker image. So combine commands, i.e if you have multiple pip install stages combine them with && to a single layer.

Another method to combine layers is to use docker build with --squash, this will squash together layers. However this will reduce the capability of docker to use cache for layers. Thus only use it directly before pushing a docker to the container registry.


### Use a .dockerignore
Use a .dockerignore file to ignore directories that are not supposed to go into your container. This might be IDE specific files (.vs, .vscode, .idea) as well as build directories if someone built the directory without a docker.


### Don’t use the root user
Don’t use the root user inside you container to execute your code, but a user with priviliges that are just enough to run the code. Node.js for example comes with a built in user node that is setup to run node.js code.


### Don’t include secrets and credentials in your code & docker
Don’t hard code credentials such as admin accounts and secretes such as keys to generate tokens. Configuration Variables such as protocols, IPs, and non secret configurations can be set through environment variables. Secrets and Credentials should be read from files outside of the container at runtime (use i.e Kubernetes secrets or Azure Key Vault to manage them).


### Use tini to execute your code
Tini (https://github.com/krallin/tini - You don't have permissions to view 
Try another account
) is a tool to manage subprocesses and can be used with any language. It takes care that SIG commands are properly forwarded to subprocesses with your docker.


### Linter your File for even more improvements
https://www.fromlatest.io/#/

## BASE Dockerfiles

### Python
--> https://github.com/tobiagru/DockerTricks/blob/master/Dockerfile.Python.Base
### Node.js
--> https://github.com/tobiagru/DockerTricks/blob/master/Dockerfile.NodeJS.Base
