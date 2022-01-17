Run Django and PostgreSQL in Docker
====================

With Explaination

How to run?
----------

`docker-compose up` then visit http://localhost:8000 in your browser.

Here's a tutorial: [django-docker-and-postgresql-tutorial](https://learndjango.com/tutorials/django-docker-and-postgresql-tutorial)

What does docker do?
-------------------

1. Download image `python:3` locally.
2. Download image `postgresql:10.11` locally.
3. create a container from image `postgresql:10.11`, call it container A
4. create a container from the `python:3` image, and install some dependencies, copy some files, set some environment variables,  Call it container B (check file **Dockfile**)
5. Build a new image called `django_app_docker_demo_web` from container B
6. run the container A, when A is ready, run a container from image `django_app_docker_demo_web` (You may call it new container B, check file **docker-compose.yaml** -- map some volumes folder mapping: new folder created inside `./data/db` to store data files and etc.) 

Build a new Docker image
-----------------
Everytime after you change python codes, build it and add a new tag.

#### Userful tutorials about building docker images

[Build multi-arch images with Buildx](https://docs.docker.com/desktop/multi-arch/)

[在 M1 Mac 上构建 x86 Docker 镜像](https://printempw.github.io/build-x86-docker-images-on-an-m1-macs/)

In brief

check buildx bootstrap
```docker buildx inspect --bootstrap```
output 

```Name:   default
Driver: docker

Nodes:
Name:      default
Endpoint:  default
Status:    running
Platforms: linux/arm64, linux/amd64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/arm/v7, linux/arm/v6
```

I'm using M1 MacBook Pro, by default it only builds ARM64 docker images.

So I create a new builder for M1 mac. I want to build image for AMD64 and ARM64.

`docker buildx create --use --name m1_builder`

Build and push it to docker hub.

`docker buildx build --platform linux/amd64,linux/arm64 --push -t xros/django_app_docker_demo_web:0.1 .`

Check its architecture

`docker buildx imagetools inspect xros/django_app_docker_demo_web:0.1`

It will show 
```
Name:      docker.io/xros/django_app_docker_demo_web:0.1
MediaType: application/vnd.docker.distribution.manifest.list.v2+json
Digest:    sha256:b04da36cfafc626f281178598041378eccf5e914e0cfd93f40330bdd2b4e9551
           
Manifests: 
  Name:      docker.io/xros/django_app_docker_demo_web:0.1@sha256:afec423f932db9a7f7d6614bd65fddc1da31a5c4de7e8a975f48d5c32ff4aea3
  MediaType: application/vnd.docker.distribution.manifest.v2+json
  Platform:  linux/amd64
             
  Name:      docker.io/xros/django_app_docker_demo_web:0.1@sha256:899cb52ccc886751ad8f2d41de4b9c060867a8751a7fe9e3f428a466c7af9706
  MediaType: application/vnd.docker.distribution.manifest.v2+json
  Platform:  linux/arm64
```

Check it from DockerHub: https://hub.docker.com/r/xros/django_app_docker_demo_web , then write a new `docker-compose.yaml` to run directly from the new Docker Image.


Other tutorials Run Django app with Postgresql in Docker
-----------------

This is basically the same as the tutorial from Docker's offical tutorial from here: https://docs.docker.com/samples/django/

But there's little difference. I have to downgrade postgresql to version 10.11 because from v10.11+, postgresql forces users to use password. More info can be found at: https://discuss.circleci.com/t/postgresql-image-password-not-specified-issue/34555

It also provide solutions to use password in postgresql docker, such as 

- Option 1 - Implement a password

- Option 2 - Disable the password requirement

- Option 3 - Downgrade postgresql version. (not recommended)


