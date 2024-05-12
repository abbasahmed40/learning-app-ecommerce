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
