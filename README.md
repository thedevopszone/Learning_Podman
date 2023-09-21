# Learning Podman


## Starting Container

```
podman run --name=alpinebash -it docker.io/alpine sh

apk update
apk add bash
exit

# Show what was edited in the container
podman diff alpinebash
```

## Creating Image From Edited Container

```
podman ps -a
podman commit alpinebash softxpert/alpinebash

podman run --name=newalpine -it softxpert/alpinebash bash
```

## Show Layers

```
podman history localhost/softxpert/alpinebash

```

## Build Image

Default Filenames:
- Dockerfile
- Containerfile

```
podman build -t myimagename:v1 .

podman build -t myimagename:v1 -f filename

# To not use the cached layers
podman build -t myimagename:v1 --no-cache .
```

## Dockerfile Commands

```
FROM
RUN
LABEL
CMD
EXPOSE
ENV
COPY
ADD
VOLUME
USER
WORKDIR
ENTRYPOINT
```

vi Dockerfile
```
# This is an comment
FROM docker.io/centos:8
RUN yum update -y
RUN yum clean all -y  # Clean cache to make image smaller
RUN yum install httpd php -y
RUN yum clean all -y  # Clean cache to make image smaller
```

Build
```
podman build . -t myhttpd

podman history myhttpd
```

Better
```
# This is an comment
FROM docker.io/centos:8
RUN yum update && yum install httpd php -y && yum clean all -y

```


















