# Kubernetes demo

An example project to demonstrate development/deployment workflows with Kubernetes.

## Back-end

### PHP

Build development PHP image

```
docker build -f back-end/docker/Dockerfile -t k8s_demo_back_end_app:dev --target app-dev back-end
```

Build production PHP image

```
docker build -f back-end/docker/Dockerfile -t k8s_demo_back_end_app:$(git rev-parse --short HEAD) --target app-prod back-end
```

### Nginx

Build development Nginx image

```
docker build -f back-end/docker/Dockerfile -t k8s_demo_back_end_web:dev --target web-dev back-end
```

Build production Nginx image

```
docker build -f back-end/docker/Dockerfile -t k8s_demo_back_end_web:$(git rev-parse --short HEAD) --target web-prod back-end
```
