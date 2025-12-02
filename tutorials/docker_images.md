# Docker Images

## Docker registry

Where do the images we've run in the previous module come from?

The images used in Docker, including the ones you've run in the previous module, typically come from **Docker registries**.
A Docker registry is a centralized repository for storing and distributing container images.
It serves as a hub where you can find pre-built container images created by others or upload and share your own images.

By default, Docker pulls images from the [DockerHub registry](https://hub.docker.com/), which is a public registry provided by Docker. 
Docker Hub hosts a vast collection of official and community-contributed images for a wide range of applications and operating systems.
It is a popular source for finding and sharing container images.

However, Docker also allows you to work with **private registries**. Organizations often set up private registries to store proprietary or sensitive container images.
Private registries offer control over image distribution, access control, and integration with other deployment pipelines or processes.

## Image manipulations 

You can manage docker images is a similar way you've managed containers. 
List available images with `docker images`, pulling images from a registry using `docker pull`, pushing images to a registry with `docker push`, removing images using `docker image rm`, and inspecting image details with `docker inspect`, among others.

## Build images 

It's time to learn how to build your own images.
In the below example, we will build the [NetflixFrontendLotus](https://github.com/alonitac/NetflixFrontendLotus) app. 

A **Dockerfile** is a text document in which you tell docker how to containerize an application.

Assume you start with a clean version of Linux and want to run a Node.js application.
The natural approach might involve manual installation steps such as using `apt-get` to update the system, installing Node.js and npm, installing app dependencies using `npm install`, setting up the application environment, and configuring runtime settings.

A Dockerfile provides a declarative and automated way to define all these steps as instructions for docker to build. 
The final result after building an image according to a Dockerfile is a self-contained and portable artifact that encapsulates the application code, its dependencies, and the runtime environment. 
This image can be run as a container on any system that has Docker installed, ensuring consistent behavior and eliminating the need for manual setup and configuration.

Here's the Dockerfile you'll use as the starting point in order to pack our app into an image:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

CMD ["node", "app.js"]
```

In your NetflixFrontendLotus repo, create a file called `Dockerfile` (note the capital `D`) and copy the above content. We will explain each line soon.
For now, let's build an image out of it.

Open up a terminal where the current working directory root dir of the repo. Then build the image using the `docker build` command:

```bash
docker build -t netflix-frontend:0.0.1 .
```

The working directory from which the image is built is called the **Build context**.
In the above case, the `.` at the end of the command specifying to docker that the "current working directory", is the build context. 
The docker daemon looks by default for a file called `Dockerfile` and builds the image according to the instructions specified in the Dockerfile. 

Run a container based on your built image, the image name is `netflix-frontend:0.0.1`.
The app listens to port `8080`, publish this port to the host machine, so you can visit the app from your web browser. 


## The Dockerfile

A Docker build consists of a series of ordered build instructions.

### Base image - the `FROM` instruction

All Docker images start with a **base image**, and as changes are made and new content is added to the image, new layers are added on top of the base image.
The [`FROM` instruction](https://docs.docker.com/engine/reference/builder/#from) uses the official [`node:18-alpine`](https://hub.docker.com/_/node) image as the base image upon which our image will be built.
This base image is a docker image itself (based on the [`alpine`](https://hub.docker.com/_/alpine) image), which provides a lightweight and minimalistic version of the Node.js runtime environment, allowing developers to build Node.js applications without the need to install Node.js in the image from scratch.

So, our image is based on a Node.js base image, which in turn based on an Alpine Linux base image. 
What is the Debian image based on?

Based on [Hindu mythology](https://en.wikiquote.org/wiki/Turtles_all_the_way_down), the Debian image rest on the back of an elephant which rested in turn on the back of a turtle,
what did the turtle rest on? Another turtle. And that turtle? 'Ah, Sahib, after that it is turtles all the way down.'.

Back to the Docker world... all images are based on the [`scratch`](https://hub.docker.com/_/scratch) "image". 
The `FROM scratch` is a special instruction used in a Dockerfile to indicate that you are starting the image from scratch, without using any existing base image.
When `FROM scratch` is specified, it means you are creating the image from the bare minimum, without including any pre-built operating system or runtime environment.

### Working dir - the `WORKDIR` instruction

The [`WORKDIR` instruction](https://docs.docker.com/engine/reference/builder/#workdir) in a Dockerfile is used to set the working directory for any subsequent instructions within the Dockerfile. 
It allows you to define the directory where commands such as `RUN`, `CMD`, `COPY`, and `ADD` will be executed.

In our example, we set the working directory inside the container to `/app`.

By setting the working directory, you avoid having to specify the full path for subsequent instructions, making the Dockerfile more readable and maintainable.

### Copying files from the build machine into the image - the `COPY` instruction

The `COPY <src> <dst>` instruction copies new files or directories from the `<src>` path in the host machine, and adds them to the filesystem of the image at the path `<dst>`.

In our example, we copy the file under the `.` path, which is the current working directory **relative to the build context**, into the image's filesystem working directory, which is `/app` according to what specified in `WORKDIR`.

### The `.dockerignore` 

The `.dockerignore` file is used to specify which files and directories should be excluded from the Docker build context when building an image.
It works similarly to `.gitignore`, where you can list patterns to match files and directories that should be ignored.

### Execute commands in the image - the `RUN` instruction

The [`RUN` instruction](https://docs.docker.com/engine/reference/builder/#run) in a Dockerfile is used to execute commands during the image build process. 
It allows you to run any valid command or script inside the image that is being created.

Naturally, after you've copied the `package.json` and `package-lock.json` files, you need to install the Node.js dependencies specified in the `package.json` file. 
The `RUN npm install` instruction does so. 

Note that `npm` is already available for you as part of the Node.js base image. 

### Define the command to run a container - the `CMD` instruction

The [`CMD` instruction](https://docs.docker.com/engine/reference/builder/#cmd) in a Dockerfile is used to specify the default command to run when a container based on the image is started. 
It defines the primary executable or process that should be executed within the container.

In our example, we specify the command to run when the container starts, which is `node app.js`.

## Image size is matter 

When it comes to Docker images as part of [microservices architecture](https://microservices.io/), big is bad!
Images should be as small as possible. Big images are hard to work with by means of the time it takes to pull an image and run it as a container. 

Another critical reason is potential vulnerabilities. 
Containers are designed to run just one application or service, so they only include the necessary code and dependencies for that specific purpose. 
The bigger your images are, a larger your attack surface, because your image contains more binaries and packages that can potentially have security vulnerabilities. 

The [official Alpine Linux](https://hub.docker.com/_/alpine/tags) Docker image is about 3.25MB in size and is an extreme example of how small Docker images can be.
[Read here for more information about other flavors of linux docker images](https://medium.com/swlh/alpine-slim-stretch-buster-jessie-bullseye-bookworm-what-are-the-differences-in-docker-62171ed4531d). 

Built your images only with what you need to run the application. 


## Semantic version for image tags 

While the image creator can decide the way she tags her images, many organizations (including the above `node` image you've pulled) follow specific tagging method called [**semantic version**](https://semver.org/), or **SemVer**. 

Take a look on the `node` image version: `18.19.0`. Each component has a specific meaning, given a version number `MAJOR.MINOR.PATCH`:

1. Increment the `MAJOR` version when you make **incompatible** API changes.
2. Increment the `MINOR` version when you **add functionality** in a backward compatible manner.
3. Increment the `PATCH` version when you make backward compatible **bug fixes**.

#### 🧐 Test yourself

Assume you have an app exposing an API for your clients. For each of the following changes, decide the increment:

Given version `1.14.0`:

1. What is the next version after a variable name refactor?
2. What is the next version after adding a new endpoint to the api?
3.  What is the next version after changing the api endpoint from `/uploading` to `/upload`.



# Exercises

### :pencil2: Push the `netflix-frontend` image to DockerHub

[Create a free account](https://docs.docker.com/docker-id/) in DockerHub.

Push the built `netflix-frontend:0.0.1` image to your registry.

### :pencil2: Build the 2048 game

The 2048 game source code is available in this git repo: https://github.com/gabrielecirulli/2048

The game is a completely static web app, consisting of HTML, CSS, and JavaScript files.
There is no backend code, all logic is implemented in JavaScript and runs in the browser.

Hence, you'll use Nginx to serve the game.
Nginx is a popular web server that can serve static files.

The client requests the game from the Nginx server, which in turn processes the request and serves the static files to the client's browser.

Use the `nginx:1.23.3-alpine-slim` base image to pack this game in a docker image.

The 2048 repo files have to be located inside the image under `/usr/share/nginx/html`.
This directory is the default directory from which nginx serves static files.

Run a container from the newly built image, and make sure you can play the game:

```bash
docker run -p 8080:80 my-2048-game
```


### :pencil2: Build your own microservice image 

Similar to what we've done for the netflix-frontend, build a docker image for your own microservice.


### :pencil2: Roberta app

In this exercise we'll build an app called Roberta (can be found in our course repo under `roberta_v2`).
This is a simple flask webserver that serves a [sentiment analysis](https://en.wikipedia.org/wiki/Sentiment_analysis) AI model based on [roberta-base](https://huggingface.co/roberta-base).
The model was trained on a data from Reddit to analyze the sentiment of an English sentence.

Build a Docker image for the application found in `roberta_v2`.
**Inside the `Dockerfile` you have to clone** the following repo into the workdir: https://huggingface.co/SamLowe/roberta-base-go_emotions.
Since this repo contains large files (~1GB), you have to make sure that `git-lfs` is installed (https://git-lfs.com) before cloning.

Try to keep your image as small as possible, and to utilize build caching as much as possible.

Run a container from your built image, while publishing port `8081`, and test it by:

```shell
# Sentence: Intel is happy and excited to launch the new generation of processors
# Expect positive labels: optimism, excitement
curl localhost:8081/analyze?text=Intel%20is%20happy%20and%20excited%20to%20launch%20the%20new%20generation%20of%20processors

# Sentence: This is terrible movie
# Expect negative labels: disappointment, disgust, fear
curl localhost:8081/analyze?text=This%20is%20terrible%20movie

# Sentence: This is a neutral statement
# Expect neutral labels: neutral
curl localhost:8081/analyze?text=This%20is%20a%20neutral%20statement
```
