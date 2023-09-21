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
RUN   # When image is build
LABEL
CMD   # When container is running
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

vi Dockerfile
```
# This is an comment
FROM docker.io/centos:8
RUN yum update && yum install httpd php -y && yum clean all -y
LABEL author Thomas Mundt
LABEL desription This ia Apache PHP Image based on CentOS 8
```

```
podman build . -t myhttpd
podman inspect myhttpd
```

```
podman run -it --rm myhttpd bash
ls /sbin/httpd
/sbin/httpd -DFOREGROUND
exit
```

vi Dockerfile
```
# This is an comment
FROM docker.io/centos:8
RUN yum update && yum install httpd php -y && yum clean all -y
LABEL author Thomas Mundt
LABEL desription This ia Apache PHP Image based on CentOS 8
# CMD /sbin/httpd -DFOREGROUND
CMD ["/sbin/httpd", "-DFOREGROUND"]  # Better becouse faster because ececuted in kernel exec function
```

```
podman build . -t myhttpd
podman inspect myhttpd

podman run -p 8080:80 -d myhttpd
curl http://localhost:8080
```

vi index.html
```
<h1>Welcome to Podman</h1>
```

vi Dockerfile
```
# This is an comment
FROM docker.io/centos:8
RUN yum update && yum install httpd php -y && yum clean all -y
LABEL author Thomas Mundt
LABEL desription This ia Apache PHP Image based on CentOS 8
COPY index.html /var/www/html/  # New
CMD ["/sbin/httpd", "-DFOREGROUND"]  
```

```
podman kill <container>
```

## Use tar files

ls -al
```
src.tar.gz
```

When you use COPY the tar file will only be copied but not extracted
```
# Better use ADD because the files also will be extracted on the image
ADD scr.tar.gz /tmp/
```

ADD is also good for url files
```
ADD https://softxpert.de/files/index.html /var/www/html/
```











