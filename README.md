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















