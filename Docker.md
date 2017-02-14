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

[<< Back](README.md)
