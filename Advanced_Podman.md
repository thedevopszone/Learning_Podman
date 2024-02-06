# Advanced containers with Podman

## Links

- https://medium.com/@maros.kukan/running-containers-with-podman-804ca5ecc04a
- https://medium.com/@maros.kukan/advanced-containers-with-podman-f79302de85b0

## Use Directories With Root Permissions Set As A Non Root User

When you try to map directories as a non root user that have root permissions,
you get an error and the container will not start.

Example
```
sudo -i
mkdir /var/mysql

ls -al
drwxr-xr-x.  2 root   root      6 Feb  6 07:28 mysql

exit
```



