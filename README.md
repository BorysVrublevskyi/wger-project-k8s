# wger-project-k8s
This is a working kubernetes realization of [wger-project/docker](https://github.com/wger-project/docker)

This project needs a nginx image with nginx.conf configured for wger. So you can build it on your worker node. 

```
cd ./wger-project-k8s/nginx/
docker build -t nginx-wger .
```

If you have multiple worker nodes it's better to push built image to the Docker registry and than edit image name in nginx-deployment.yaml to pull it.

Deploy Wger to Kubernetes

```
kubectl apply -f ./wger-project-k8s/
```

After the first run you need to make [Django Migrations](https://docs.djangoproject.com/en/3.1/topics/migrations/). Place an actual web pod's name to the command.

With one command:

```
kubectl exec -it --namespace=default web-..... -- bash -c "python3 manage.py makemigrations && python3 manage.py migrate"
```

Or attach terminal and run commands manually:

```
kubectl exec --stdin --tty web-..... -- /bin/bash
python3 manage.py makemigrations 
python3 manage.py migrate
```

To have access from outside you can expose service on node's port

```
kubectl expose deployment nginx --type=NodePort --name=nginx-on-node-service
```

Default credentials is admin:admin
