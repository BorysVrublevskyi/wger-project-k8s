# wger-project-k8s
This is a working kubernetes realization of [wger-project/docker](https://github.com/wger-project/docker)

## Prepare Nginx Docker Image

This project needs a Nginx image with `nginx.conf` configured for wger. So you can build it on your **worker node**. 

```bash
cd ./wger-project-k8s/nginx/
docker build -t nginx-wger .
```

If you have multiple worker nodes it's better to push image to the Docker registry and than edit image's name in `nginx-deployment.yaml` to pull it.

## Deploy Wger to Kubernetes

```bash
kubectl apply -f ./wger-project-k8s/
```
It might take some time for the service to become available. Deploy will end when you'll see **"Booting worker with pid: NNN"** in log of web pod. So don't panic when you will see "Done in NN s." but error 502 will be still appearing.

## Expose service on node port

To have access from the outside you can expose service on node's port

```bash
kubectl expose deployment nginx --type=NodePort --name=nginx-on-node-service
```

Default credentials is admin:adminadmin

## Debug and diagnostic

### Tools

* [Kompose](https://kubernetes.io/docs/tasks/configure-pod-container/translate-compose-kubernetes/) - Translate a Docker Compose File to Kubernetes Resources.

* [LazyDocker](https://github.com/jesseduffield/lazydocker) - A simple terminal UI for both docker and docker-compose, written in Go with the gocui library.

* [Dockly](https://github.com/lirantal/dockly) - Immersive terminal interface for managing docker containers and services written on NodeJS.

* [K9S](https://github.com/derailed/k9s) - Kubernetes CLI to manage your clusters in style.

<!-- ## Django Migrations

[Django Migrations](https://docs.djangoproject.com/en/3.1/topics/migrations/). Place an actual web pod's name to the command and run it.

```bash
kubectl exec -it --namespace=default web-..... -- bash -c "python3 manage.py makemigrations && python3 manage.py migrate && python3 manage.py migrate --fake-initial && yarn install"
``` -->

### Commands

```bash
# Attach terminal
kubectl exec --stdin --tty PODNAME -- /bin/bash

# Watch logs of pod
kubectl logs --follow PODNAME

# Get Services
kubectl get svc

# Check server's reply with redirections follow
curl -LI WorkerNodeIP:NodePort
curl -LI 10.96.191.134:8000

# Pod restart
kubectl delete pod PODNAME
```

## Delete and cleanup

```bash
# On master node
kubectl delete -f ./wger-project-k8s

# On worker node
docker system prune -a

# On NFS server
rm -rf /media/k8s/{data,media,postgres-data,static}/*
```
