# Learning Podman

## Install Podman

```

```








## Configure Podman

Set subuid and subgid
```
touch /etc/subuid /etc/subgid
chmod 644 /etc/subuid /etc/subgid

usermod --add-subuids 100000-165535 --add-subgids 100000-165535 tmundt
# or
---
vi /etc/subuid
tmundt:100000:65536

vi /etc/subgui
tmundt:100000:65536
---

# then
podman system migrate

```


```
echo net.ipv4.ip_unprivileged_port_start = 80 | sudo tee /etc/sysctl.d/90-unprivileged_port_start.conf

sudo sysctl --system
```


## Commands

```
podman images
podman volumes

```




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

## EXPOSE

Inspect the Port
```
podman pull docker.io/tomcat

# Search for ExposedPorts:
podman inspect docker.io/tomcat | more


"ExposedPorts": {
  "8080/tcp": {}
}
```



```
# This is an comment
FROM docker.io/centos:8
RUN yum update && yum install httpd php -y && yum clean all -y
LABEL author Thomas Mundt
LABEL desription This ia Apache PHP Image based on CentOS 8
COPY index.html /var/www/html/
EXPOSE 80 # NEW
CMD ["/sbin/httpd", "-DFOREGROUND"]  

```

You can also use -P to expose all exposed Ports with a random port number
```
podman rub -P -d myhttpd

podman port -l
```

## VOLUME

```
FROM docker.io/centos:8
RUN yum update && yum install httpd php -y && yum clean all -y
LABEL author Thomas Mundt
LABEL desription This ia Apache PHP Image based on CentOS 8
COPY index.html /var/www/html/
EXPOSE 80 
VOLUME /data # NEW
CMD ["/sbin/httpd", "-DFOREGROUND"]  


```


## WORKDIR

```
FROM docker.io/centos:8
RUN yum update && yum install httpd php -y && yum clean all -y
LABEL author Thomas Mundt
LABEL desription This ia Apache PHP Image based on CentOS 8
COPY index.html /var/www/html/
EXPOSE 80 
VOLUME /data
WORKDIR /var/www/html # NEW
CMD ["/sbin/httpd", "-DFOREGROUND"]  


```


## ENV

```
FROM docker.io/centos:8
RUN yum update && yum install httpd php -y && yum clean all -y
LABEL author Thomas Mundt
LABEL desription This ia Apache PHP Image based on CentOS 8
COPY index.html /var/www/html/
EXPOSE 80 
VOLUME /data
WORKDIR /var/www/html
ENV APIKEY abcd12345 # Or: ENV APIKEY=abcd12345
CMD ["/sbin/httpd", "-DFOREGROUND"]  
```

Overwrite while running container
```
podman run -it --rm -e APIKEY=888888 mythhpd bash
```

## USER

Not good because running as root
```
FROM docker.io/centos:7
WORKDIR /opt/src # If directory not exists it will be created
ADD src.tar.gz /opt/src/
EXPOSE 80
CMD python -m SimpleHTTPServer 80
```

```
podman build -t mypyapp:v1 .
podman run -d -P myapp:v1
podman ps
podman top funny_crazy
```

```
Priveleged Ports are from 1 to 1024

```

Better as non root and none priveleged port
```
FROM docker.io/centos:7
WORKDIR /opt/src # If directory not exists it will be created
ADD src.tar.gz /opt/src/
RUN chown -R 1500 /opt/src
EXPOSE 8080
USER 1500
CMD python -m SimpleHTTPServer 8080
```

```
podman top <container_name>

#You can also check on your host
ps -ef | grep SimpleHTTPServer

podman port -l
```


## ENTRYPOINT

```
FROM docker.io/alpine:latest
CMD ["ping", "-c3", "localhost"]
```

### Systemd

Create Container
```
podman run -d --name sonarqube -p 9000:9000/tcp \
-v sonarqube-config:/opt/sonarqube:Z \
docker.io/sonarqube:latest
```



Rootfull
```
podman top sonarqube hpid pid user args
kill -9 6563
podman generate systemd --help
podman generate systemd sonarqube > /etc/systemd/system/sonarqube-container.service
systemctl daemon-reload
systemctl status sonarqube-container
kill -9 888
journalctl -u sonarqube-container

systemctl is-enabled sonarqube-container
```


rootless
```
mkdir -p /home/podman/.config/systemd/user/
podman generate systemd sonarqube > /home/podman/.config/systemd/user/sonarqube-container.service

vi /home/podman/.config/systemd/user/sonarqube-container.service

[Install]
WantedBy=default.target


systemctl --user daemon-reload
systemctl --user start sonarqube-container
systemctl --user enable sonarqube-container

When logged out the container will stop

Fix:

sudo loginctl enable-linger tmundt

```


