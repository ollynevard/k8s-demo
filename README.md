# Kubernetes demo

An example project to demonstrate development/deployment workflows with Kubernetes.

## Docker

Steps to manually build and run the app with the `docker` CLI.

1. Build the images

   ```
   docker build \
     --file back-end/docker/Dockerfile \
     --target app-dev \
     --tag k8s_demo_back_end_app:dev \
     back-end

   docker build \
     --file back-end/docker/Dockerfile \
     --target web-dev \
     --tag k8s_demo_back_end_web:dev \
     back-end
   ```

2. Create the network and create and run the containers

   ```
   docker network create k8s_demo

   docker run \
     --rm \
     --detach \
     --network k8s_demo \
     --hostname back_end_app \
     --name k8s_demo_back_end_app \
     k8s_demo_back_end_app:dev

   docker run \
     --rm \
     --detach \
     --network k8s_demo \
     --hostname back_end_web \
     --publish 8080:80 \
     --env BACK_END_APP_HOSTNAME=back_end_app \
     --env BACK_END_APP_PORT=9000 \
     --name k8s_demo_back_end_web \
     k8s_demo_back_end_web:dev
   ```

   View the running app at http://localhost:8080

3. Stop and remove the containers and remove the network

   ```
   docker stop k8s_demo_back_end_app k8s_demo_back_end_web

   docker network remove k8s_demo
   ```

## Docker Compose

Steps to build and run the app using `docker-compose`

1. Build the images, create the network, and create and run the containers

   ```
   docker-compose --project-name k8s_demo up --detach
   ```

   View the running app at http://localhost:8080

2. Stop and remove the containers and remove the network

   ```
   docker-compose --project-name k8s_demo down
   ```
