# Docker & AWS

Tuesday 24.11.2020

# Docker

> ### AWS
> We'll be using Amazon web services during this course, if you haven't already registered for a student account please to it now by following [these instructions](https://github.com/hgop/syllabus-2020/blob/master/AWS/README.md).

### What is Docker?

[Read this article before starting the assignment](https://opensource.com/resources/what-docker)

## Part 1

### Getting started with docker

- [ ] Install Docker

**Linux only**: After the installation you should add your user to the docker user group so that you do not have to use sudo to run commands.

When you are finished you should be able to run:\
`docker run hello-world`\
and get the following:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
...(snipped)...
```

### How do I know I'm done?

- [ ] Docker is installed.
- [ ] The docker command works without sudo privileges.

### FAQ

#### What Ubuntu do I have?

Run:\
`lsb_release -a`\
Should display (depending on your Ubuntu version):

```
Distributor ID:   Ubuntu
Description:      Ubuntu 20.04.3 LTS
Release:          20.04
Codename:         bionic
```

## Part 2

### Image vs Container

An instance of an image is called a container. You have an image, which is a set
of layers as you describe. If you start this image, you have a running container
of this image. You can have many running containers of the same image.

### Your new development environment

In the past, if you were to start writing a React app, your first order of
business was to install NodeJS and other dependencies onto your machine. But,
that creates a situation where the environment on your machine has to be just so
in order for your app to run as expected; ditto for the server that runs your
app.

With Docker, you can just grab a portable NodeJS runtime as an image, no
installation necessary. Then, your build can include the base NodeJS image right
alongside your app code, ensuring that your app, its dependencies, and the
runtime, all travel together.

These portable images are defined by something called a Dockerfile.

### Define a container with a Dockerfile

Dockerfile will define what goes on in the environment inside your container.
Access to resources like networking interfaces and disk drives is virtualized
inside this environment, which is isolated from the rest of your system, so you
have to map ports to the outside world, and be specific about what files you
want to “copy in” to that environment. However, after doing that, you can expect
that the build of your app defined in this Dockerfile will behave exactly the
same wherever it runs.

### The application
Let's create a React app and run it in a Docker container.

Enter the following commands to create the application:

`npm install -g create-react-app`

Make sure you don't do this in your hgop repository because we'll want this in another repo that we'll create later.
`create-react-app connect-four-client --template typescript`

`cd connect-four-client`

These commands will create a Typescript application in the folder `connect-four-client`

To make sure your new application is working as expected you can run it locally on your machine by:
- Installing the dependencies:\
`npm install`
- Starting the application:\
`npm start`
- The application should be running on `http://localhost:3000/`

### Dockerfile
Now we want to be able to run the application easily in multiple environments that might be very different from your local setup. We can use docker to build your application into a docker image which can be run on all kinds of environments.

Create a file called `Dockerfile` in the `connect-four-client` folder and copy-paste the following content into the file and replace the TODOs with the correct commands. A glossary of the commands can be found [here](https://github.com/hgop/syllabus-2020/blob/master/Glossary/docker.md).

```Dockerfile
# First we want to build on an image the includes the environment we need to build and run our code, we can use 'node:13.12.0-alpine':
TODO

# Now set the working directory to /app:
TODO

# Next we need to install all app dependencies,
# First copy both package.json and package-lock.json into the current working directory:
TODO
# Then run npm install
TODO

# Now we need the rest of our application code, copy your entire folder into the image working directory:
TODO

# Lastly we need to run the application when the container starts, set the command as npm start:
TODO
```

### Build the app

We are ready to build the app. Make sure you are still at the top level of your new directory which should contain:
```
connect-four-client
├── node_modules
│   ├── ...
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
│   └── ...
├── src
│   ├── App.css
│   ├── App.tsx
│   ├── index.css
│   ├── index.tsx
│   └── ...
├── package.json
├── tsconfig.json
├── Dockerfile
└── ...
```

Now run the build command. This creates a Docker image, which we’re going to tag using -t so it has a friendly name.

```
docker build -t connect-four-client .
```

Where is your built image? It’s in your machine’s local Docker image registry:

```
$ docker images

REPOSITORY            TAG                 IMAGE ID
connect-four-client   latest              326387cea398
...
```

### Run the app's image in a docker container

**Note**: Make sure to stop the local run of the application by ctrl-c before running the docker.

Run the app and map your machine’s port 3000 to the container’s published port 3000 using -p (If we don't map the ports there is no way for us to access the port because it's only available inside the docker container):

```
$ docker run -p 3000:3000 connect-four-client

> connect-four-client@0.1.0 start /app
> react-scripts start

ℹ ｢wds｣: Project is running at http://172.17.0.2/
ℹ ｢wds｣: webpack output is served from 
ℹ ｢wds｣: Content not from webpack is served from /app/public
ℹ ｢wds｣: 404s will fallback to /
Starting the development server...

Compiled successfully!

You can now view connect-four-client in the browser.

  Local:            http://localhost:3000
  On Your Network:  http://172.17.0.2:3000

Note that the development build is not optimized.
To create a production build, use npm run build.
```

Your shell will enter the docker container and won't allow you to ctrl+c
out of the process.

The app is a simple React App and can be visited by going to http://localhost:3000 in your preferred browser.

Now finally to exit the docker container, using another shell kill the
docker container by:

```
$ docker container list

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
931853651e89        connect-four-client "/bin/sh -c 'node ap…"   53 seconds ago      Up 51 seconds       0.0.0.0:3000->3000/tcp hopeful_golick

$ docker container stop 931853651e89
```

Now let’s run the app in the background, in detached mode (-d flag):

```
docker run -d -p 3000:3000 connect-four-client
```

You get the long container ID for your app and then are kicked back to your terminal. Your container is running in the background. You can also see the abbreviated container ID with docker container ls (and both work interchangeably when running commands):
```
$ docker container list

CONTAINER ID        IMAGE               COMMAND                   CREATED           PORTS
1fa4ab2cf395        connect-four-client "docker-entrypoint.s…"    28 seconds ago   0.0.0.0:3000->3000/tcp
```

You’ll see that that the app is running on http://localhost:3000/.

Now use docker container stop to end the process, using the CONTAINER ID, like
so:

```
docker container stop 1fa4ab2cf395
```

**Pro Tip** When running commands that require the `CONTAINER ID`, e.g.\
`docker container stop 1fa4ab2cf395`\
it's usually enough to write the first three characters or so, i.e.\
`docker container stop 1fa`

### Create a new Github repo from your existing project
1. Go into the directory containing the project.
- Type `git init` (Note: create-react-app has already done this for you)
- Type `git add .` to add all of the relevant files.
- Type `git commit -m "My initial commit`

2. Connect it to github
You’ve now got a local git repository. You can use git locally, like that, if you want. But if you want the thing to have a home on github, do the following.

- Go to github.
- Log in to your account.
- Click the new repository button in the top-right. You’ll have an option there to initialize the repository with a README file, but don’t.
- Click the “Create repository” button.

Now, follow the second set of instructions, “Push an existing repository…”

### Share your image

To demonstrate the portability of what we just created, let’s upload our built
image and run it somewhere else. After all, you’ll need to learn how to push to
registries when you want to deploy containers to production.

A registry is a collection of repositories, and a repository is a collection of
images—sort of like a GitHub repository, except the code is already built. An
account on a registry can create many repositories. The docker CLI uses Docker’s
public registry by default.

Note: We’ll be using Docker’s public registry here just because it’s free and
pre-configured, but there are many public ones to choose from, and you can even
set up your own private registry using Docker Trusted Registry.

### Log in with your Docker ID

If you don’t have a Docker account, sign up for one at
[cloud.docker.com](https://cloud.docker.com/). Make note of your username.

Log in to the Docker public registry on your local machine.

```
$ docker login
```

### Tag the image

The notation for associating a local image with a repository on a registry is
username/repository:tag. The tag is optional, but recommended, since it is the
mechanism that registries use to give Docker images a version. Give the
repository and tag meaningful names for the context, such as hgop:part2.

Now, put it all together to tag the image. Run docker tag image with your
username, repository, and tag names so that the image will upload to your
desired destination. The syntax of the command is:

```
docker tag image username/repository:tag
```

For example:

```
docker tag connect-four john/connect-four-client:day2
```

Run docker images to see your newly tagged image. (You can also use docker image
ls.)

```
$ docker images
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
connect-four-client      latest              d9e555c53008        3 minutes ago       195MB
john/connect-four-client day2                d9e555c53008        3 minutes ago       195MB
...
```

### Publish the image

Upload your tagged image to the repository:

```
docker push username/repository:tag
```

Once complete, the results of this upload are publicly available. If you log in
to Docker Hub, you will see the new image there, with its pull command.

Pull and run the image from the remote repository From now on, you can use
docker run and run your app on any machine with this command:

```
docker run -p 3000:3000 username/repository:tag
```

If the image isn’t available locally on the machine, Docker will pull it from
the repository. If you're working in a team try to pull images and run then from each other.

```
$ docker run -p 3000:3000 my-team-member/connect-four-client:day2
Unable to find image 'my-team-member/connect-four-client:day2' locally
part2: Pulling from my-team-member/connect-four-client
10a267c67f42: Already exists
f68a39a6a5e4: Already exists
9beaffc0cf19: Already exists
3c1fe835fb6b: Already exists
4c9f1fa8fcb8: Already exists
ee7d8f576a14: Already exists
fbccdcced46e: Already exists
Digest: sha256:0601c866aab2adcc6498200efd0f754037e909e5fd42069adeff72d1e2439068
Status: Downloaded newer image for my-team-member/connect-four-client:day2
 * Running on http://0.0.0.0:3000/ (Press CTRL+C to quit)
```

Note: If you don’t specify the :tag portion of these commands, the tag of
:latest will be assumed, both when you build and when you run images. Docker
will use the last version of the image that ran without a tag specified (not
necessarily the most recent image). No matter where docker run executes, it
pulls your image, along with Node and runs your code. It all travels together in
a neat little package, and the host machine doesn’t have to install anything but
Docker to run it.

### How do I know I'm done?

- [ ] I've created a Dockerfile
- [ ] I've created a docker image
- [ ] I ran an instance of my image (a docker container) and saw the results in
      a browser (curl)
- [ ] I've stored my docker image on Docker Cloud
- [ ] A team member has pulled my image and got it running
- [ ] I've committed all my code to a new GitHub repo

## Part 4 - Questions

Create an `answers.md` file that describes the following
(only 1-2 sentences each)

```
# Docker Exercise
TODO: What was this assignment about

## What is Docker?
TODO: short description

## What is the difference between:
* Virtual Machine
* Docker Container
* Docker Image
TODO: short comparison

## What is the purpose of a package.json file:
TODO: short description

## When creating a Dockerfile why is it better to only copy the dependency files first, build the dependencies and then copy the rest of the application code? (hint: docker layering):
TODO: short description

## Results
TODO: What was accomplished in this exercise
```

## Handin

This is how your repositories should look after todays assignment which you
will submit on Friday.

connect-four-client repository:
```bash
.
├── public
│   ├── ...
│   └── index.html
├── src
│   ├── ...
│   ├── index.tsx
│   └── App.tsx
├── .dockerignore
├── .gitignore
├── Dockerfile
├── tsconfig.json
├── package-lock.json
└── package.json
```

infrastructure repository:
```bash
.
├── assignments
│   ├── day01
│   │   └── answers.md
│   └── day02
│       └── answers.md
└── scripts
    └── setup_local_dev_environment.sh
```

They must be placed at these location to get full marks.
