# Kubernetes Resume Challenge - E-commerce Application Deployment

## Introduction
This repository documents my completion of the Kubernetes Resume Challenge, focusing on deploying an e-commerce application using Kubernetes and containerization techniques. I used AKS to host my app and Azure container registry to store my image.

## Implementation

### Web application and database containerization

Create a Dockerfile with the content below and place it in the same directory where the application source code is located in:

```
# php:7.4-apache as the base image
FROM php:7.4-apache

# Install mysqli extension for PHP
RUN docker-php-ext-install mysqli

# Copy the application source code to /var/www/html/
COPY . /var/www/html/

ENV DB_HOST=mysql-service

# Expose port 80 to allow traffic to the web server
EXPOSE 80
CMD ["apache2-foreground"]
```
Execute this command to build and push the image
```
docker build -t abbasahmed40/ecom-web:v1 .

docker push abbasahmed40/ecom-web:v1

```
### Deploy to AKS cluster

```
az aks get-credentials --resource-group App_Deployment  --name App_Cluster

```

First  create namespace

```
kubectl create ns webapp
```

than db initlization script , 


```
kubectl apply -f db-configmap.yaml

```

now deploy secrets, pvc manifest file 

```
kubectl apply -f db-secret.yaml

kubectl apply -f db-pvc.yaml

```


now deploy database deployment manifest file

```
kubectl apply -f db-deployment.yaml
```

To deploy website first deploy secret, configmap

```
kubectl apply -f web-configmap.yaml

kubectl apply -f web-secret.yaml
 
```

finally  deploying website and expose it using kubernetes service

```

kubectl apply -f website-deployment.yaml

```

to mannually scale the app


```
kubectl scale deployment/ecom-web-deployment --replicas 6 -n webapp

```

For Autoscalling App, adding resource limts and request

```
resources:
  limits:
    cpu: "150m"
  requests:
    cpu: "50m"
```
now implementing hpa

```
kubectl autoscale deployment ecom-web-deployment --cpu-percent=50 --min=2 --max=10 -n webapp

```
to get some load on app, install apache benchmark, and run

```
  
ab -r -n 100000 -c 500 http://$IP/

```

we can get status of our hpa

```
kubectl get hpa -n webapp

```

now for liveness and rediness, i get help from blog please check out the detailed [blog here](https://biplab24.hashnode.dev/deploying-a-simple-e-commerce-website-on-aks)
\n adding new endpoint ready.php and healthcheck.php to my repo.After adding rebuild and push docker image and add the code to webiste-deployment.yaml manifest file 

```
livenessProbe:
  httpGet:
    path: /healthcheck.php
    port: 80
  initialDelaySeconds: 3
  periodSeconds: 3
readinessProbe:
  httpGet:
    path: /ready.php
    port: 80

```
### Implementing CI/CD Pipeline using AKS

First of all i configured service connection for aks and acr, update the website-deployment file so that the image with the latest tag can be deployed on. The file is avalible in the repo using azure-pipeline-1.yaml

Pipeline consits of two stages one for build and one for deploymennt on AKS cluster, also i configured the self hosted Agents. Make sure dockers and kubectl installs on agent machine.

