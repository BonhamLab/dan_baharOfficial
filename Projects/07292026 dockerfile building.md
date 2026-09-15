## Building the Dockerfile on your local computer with Docker

Docker is a container engine that works locally.

```
docker build -t waafle:1.4.3 -f ~/envs/waafle143_dockerfile ~/envs
```
 
docker build = do the recipe; -t waafle:1.4.3 = name the finished container; -f waafle143_dockerfile = which dockerfile to use; the . = the files are in this folder.

## Building the Dockerfile on dogen with Podman

Podman is a container engine that works on dogen. Check if Podman is installed with: `podman --version`

```
podman build --format=docker -t waafle_container -f ~/envs/waafle143_dockerfile ~/envs
```

podman build = do the recipe; --format=docker = a special Podman flag needed for all runs; -t waafle_container = name the finished container; -f ~/envs/waafle143_dockerfile = which Dockerfile to use; ~/envs = the files are in this folder.
