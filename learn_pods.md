# How To Create Pods


In two commands when you want to ad multible containers
```
podman pod create --name nginx -p 80:80 -p 443:443

mkdir $HOME/nginx-config
mkdir $HOME/nginx-ssl
podman run -dt -pod nginx --name nginx -v $HOME/nginx-config:/etc/nginx/conf.d:Z -v $HOME/nginx-ssl:/etc/nginx/ssl:Z docker.io/library/nginx:1.25.3

# Is --name working here
```

In one command
```
sudo sysctl net.ipv4.ip_unprivileged_port_start=80

podman run -dt --pod new:nginx  --name nginx -p 80:80 docker.io/library/nginx:1.25.3

# Is --name working here
```

