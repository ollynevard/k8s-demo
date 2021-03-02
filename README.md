# Kubernetes demo

An example project to demonstrate development/deployment workflows with Kubernetes.

## Build

Build the images with the `docker` CLI.

```
docker build \
  --file back-end/docker/Dockerfile \
  --target php-dev \
  --tag k8s-demo-back-end-php:dev \
  back-end

docker build \
  --file back-end/docker/Dockerfile \
  --target nginx-dev \
  --tag k8s-demo-back-end-nginx:dev \
  back-end
```

## Run

### Docker

Running the app using the `docker` CLI:

```
docker network create k8s-demo

docker run \
  --rm \
  --detach \
  --network k8s-demo \
  --hostname php \
  --name k8s-demo-back-end-php \
  k8s-demo-back-end-php:dev

docker run \
  --rm \
  --detach \
  --network k8s-demo \
  --hostname nginx \
  --publish 31000:80 \
  --name k8s-demo-back-end-nginx \
  k8s-demo-back-end-nginx:dev
```

View the running app at http://localhost:31000.

Stop and remove the containers and remove the network:

```
docker stop k8s-demo-back-end-php k8s-demo-back-end-nginx

docker network remove k8s-demo
```

## Docker Compose

Run the app using the `docker-compose` CLI (this will also build the images if they don't already exist):

```
docker-compose up --detach
```

View the running app at http://localhost:31000.

Stop and remove the containers and remove the network:

```
docker-compose down
```

## Kuberenetes - CLI

Run the app using the `kubectl` CLI:

```
kubectl create service clusterip php --tcp=9000
kubectl create service nodeport nginx --tcp=80 --node-port=31000
kubectl create deployment php --image=k8s-demo-back-end-php:dev --port=9000
kubectl create deployment nginx --image=k8s-demo-back-end-nginx:dev --port=80
```

View the running app at http://localhost:31000.

Stop and remove services and deployments:

```
kubectl delete service php nginx
kubectl delete deployment php nginx
```

## Kuberenetes - Config

Run the app using the `kubectl` CLI with a configuration file:

```
kubectl apply -f kubernetes.yml
```

View the running app at http://localhost:31000.

Stop and remove services and deployments:

```
kubectl delete -f kubernetes.yml
```
