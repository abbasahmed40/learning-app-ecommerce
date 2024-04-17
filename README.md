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
docker build -t <acr-name>/ecom-web:v1 .

docker push <acr-name>/ecom-web:v1






