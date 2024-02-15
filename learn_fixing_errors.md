# Typical Errors


## ERROR: acquiring lock 0 for container

On Rhel
```
[podman@localhost ~]$ podman ps
ERRO[0000] Refreshing container 56b5ea452c8b35f543099b87150bb55a74ef632b2d75fa94b130f589054ff200: acquiring lock 0 for container 56b5ea452c8b35f543099b87150bb55a74ef632b2d75fa94b130f589054ff200: file exists
ERRO[0000] Refreshing volume solrdata: acquiring lock 1 for volume solrdata: file exists
```


Solution
```
sudo loginctl enable-linger <myuser>
```
