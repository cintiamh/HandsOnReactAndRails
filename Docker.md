[<< Back](README.md)

# Docker

Install Docker: https://docs.docker.com/docker-for-mac/

List the docker processes currently running:

```
$ docker ps
```

To stop a process:

```
$ docker stop webserver
```

Where `webserver` is the process name. To start it again, just run:

```
$ docker start webserver
```

A stopped container will not show up with `docker ps`, for that, you need to run:

```
$ docker ps -a
```

To stop and remove the running container with a single command:

```
$ docker rm -f webserver
```

This will remove the container, but not the `nginx` image. You can list local images with:

```
$ docker images
```

To remove an image you no longer need, use:

```
$ docker rmi
```

followed by an image ID or image name.

## Images and Containers

An Image is a filesystem and parameters to use at runtime.
It doesn't have a state and never changes.

A container is a running instance of an image.

When you run:

```
$ docker run hello-world
```

The Docker Engine:

* checked to see if you had the `hello-world` software image.
* downloaded the image from the Docker Hub.
* loaded the image into the container and "ran" it.

Docker Engine lets people create and share software through Docker images.

## Find and run the whalesay image

Docker Hub: https://hub.docker.com

Search for `whalesay` and select docker/whalesay image.

Each image repository contains information about an image.

### Run the whalesay image

```
$ docker run docker/whalesay cowsay boo
```

If you type `docker images`, you see the `docker/whalesay` image listed.

You can re-run the image and play with it.

```
$ docker run docker/whalesay cowsay boo-boo
```

## Build your own image

### Write a Dockerfile

Dockerfile reference: https://docs.docker.com/engine/reference/builder/

A Dockerfile is a recipe which describes the files, environment, and commands that make up an image.

Dockerfile
```
FROM docker/whalesay:latest
RUN apt-get -y update && apt-get install -y fortunes
CMD /usr/games/fortune -a | cowsay
```

* The `FROM` keyword tells Docker which image your image is based on.
* The `RUN` will use `apt-get` from Ubuntu to install the `fortunes` program into the image.
* The `CMD` statement, tells the image the final command to run after its environment is set up.

### Build an image from your Dockerfile

In the same folder you have your Dockerfile:

```
$ docker build -t docker-whale .
```

The `-t` parameter gives your image a tag, so you can run it more easily later.
Don't forget the `.`, it tells the `docker build` to look in the current directory for a file called Dockerfile.

### Run your new docker-whale

```
$ docker run docker-whale
```

## Compose and Rails

https://docs.docker.com/compose/rails/

Create the following files:

Dockerfile
```
FROM ruby:2.3.3
RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs
RUN mkdir /myapp
WORKDIR /myapp
ADD Gemfile /myapp/Gemfile
ADD Gemfile.lock /myapp/Gemfile.lock
RUN bundle install
ADD . /myapp
```

Gemfile (this will be replaced)
```
source 'https://rubygems.org'
gem 'rails', '5.0.0.1'
```

Gemfile.lock (empty file)

docker-compose.yml
```
version: '2'
services:
  db:
    image: postgres
  web:
    build: .
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
    depends_on:
      - db
```

Build the project:

```
$ docker-compose run web rails new . --force --database=postgresql --skip-bundle
```

If you edit `Gemfile`, you will need to build the image again.

```
$ docker-compose build
```

### Connect the database

Instead of the localhost database, you need to point to the postgres image.

config/database.yml
```
development: &default
  adapter: postgresql
  encoding: unicode
  database: myapp_development
  pool: 5
  username: postgres
  password:
  host: db

test:
  <<: *default
  database: myapp_test
```

You now can boot the app with

```
$ docker-compose up
```

Finally, you need to create a database. In another terminal, run:

```
$ docker-compose run web rails db:create
```

If you stop the example application and attempt to restart it, you might get the following error: web_1 | A server is already running. Check /myapp/tmp/pids/server.pid. One way to resolve this is to delete the file tmp/pids/server.pid, and then re-start the application with docker-compose up.

[<< Back](README.md)
