## Install Jenkins with Podman


## Install Podman

Installation of Podman on RHEL7/Centos7
```
sudo subscription-manager repos --enable=rhel-7-server-extras-rpms
sudo yum -y install podman
```

Installation of Podman on RHEL8
```
sudo yum module enable -y container-tools:1.0
sudo yum module install -y container-tools:1.0
```


## Jenkins Installation


Create the volumes
```
podman volume create jenkins-data
```

