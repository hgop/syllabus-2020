## Docker Basics
Borrowed from [Docker Explained](https://www.digitalocean.com/community/tutorials/docker-explained-using-dockerfiles-to-automate-building-of-images)

Docker containers are created by using [base] images. An image can be basic, with nothing but the operating-system fundamentals, or it can consist of a sophisticated pre-built application stack ready for launch.

When building your images with docker, each action taken (i.e. a command executed such as apt-get install) forms a new layer on top of the previous one. These base images then can be used to create new containers.

### Dockerfiles
Each Dockerfile is a script, composed of various commands (instructions) and arguments listed successively to automatically perform actions on a base image in order to create (or form) a new one. They are used for organizing things and greatly help with deployments by simplifying the process start-to-finish.
#### Dockerfile Syntax
Dockerfile syntax consists of two kind of main line blocks: comments and commands + arguments.
```
# Line blocks used for commenting
command argument argument ..
```
A Simple Example:
```
# Print "Hello docker!"
RUN echo "Hello docker!"
```

* ADD
The ADD command gets two arguments: a source and a destination. It basically copies the files from the source on the host into the container's own filesystem at the set destination.
```
# Usage: ADD [source directory or URL] [destination directory]
ADD /my_app_folder /my_app_folder
```

* CMD
The command CMD, similarly to RUN, can be used for executing a specific command. However, unlike RUN it is not executed during build, but when a container is instantiated using the image being built. Therefore, it should be considered as an initial, default command that gets executed (i.e. run) with the creation of containers based on the image.
```
# Usage 1: CMD application "argument", "argument", ..
CMD "echo" "Hello docker!"
```

* ENV
The ENV command is used to set the environment variables (one or more). These variables consist of “key = value” pairs which can be accessed within the container by scripts and applications alike. This functionality of docker offers an enormous amount of flexibility for running programs.
```
# Usage: ENV key value
ENV SERVER_WORKS 4
```

* EXPOSE
The EXPOSE command is used to associate a specified port to enable networking between the running process inside the container and the outside world (i.e. the host).
```
# Usage: EXPOSE [port]
EXPOSE 8080
```

* FROM
FROM directive is probably the most crucial amongst all others for Dockerfiles. It defines the base image to use to start the build process.
```
# Usage: FROM [image name]
FROM ubuntu
```

* MAINTAINER
One of the commands that can be set anywhere in the file - although it would be better if it was declared on top - is MAINTAINER. This non-executing command declares the author, hence setting the author field of the images. It should come nonetheless after FROM.
```
# Usage: MAINTAINER [name]
MAINTAINER authors_name
```

* RUN
The RUN command is the central executing directive for Dockerfiles. It takes a command as its argument and runs it to form the image. Unlike CMD, it actually is used to build the image (forming another layer on top of the previous one which is committed).
```
# Usage: RUN [command]
RUN install npm
```

* WORKDIR
The WORKDIR directive is used to set where the command defined with CMD is to be executed.
```
# Usage: WORKDIR /path
WORKDIR ~/
```

#### How to Use Dockerfiles
Using the Dockerfiles is as simple as having the docker daemon run one. The output after executing the script will be the ID of the new docker image.
```
# Build an image using the Dockerfile at current location
# Example: sudo docker build -t [name] .
sudo docker build -t ubuntu .
```
[Docker terminal commands](https://docs.docker.com/engine/reference/commandline/)

#### [Docker Compose](https://docs.docker.com/compose/overview/)
Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a Compose file to configure your application’s services. Then, using a single command, you create and start all the services from your configuration.

Using Compose is basically a three-step process.

1. Define your app’s environment with a Dockerfile so it can be reproduced anywhere.
2. Define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.
3. Lastly, run docker-compose up and Compose will start and run your entire app.

A docker-compose.yml looks like this:
```
version: '2'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

[Compose file reference](https://docs.docker.com/compose/compose-file/)
[Get started with Docker Compose](https://docs.docker.com/compose/gettingstarted/)
