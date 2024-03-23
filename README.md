





## Deploy Pre-Requisites

1. Install FirewallD

```
sudo yum install -y firewalld
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo systemctl status firewalld
```

## Deploy and Configure Database

1. Install MariaDB

```
sudo yum install -y mariadb-server
sudo vi /etc/my.cnf
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

2. Configure firewall for Database

```
sudo firewall-cmd --permanent --zone=public --add-port=3306/tcp
sudo firewall-cmd --reload
```

3. Configure Database

```
$ mysql
MariaDB > CREATE DATABASE ecomdb;
MariaDB > CREATE USER 'ecomuser'@'localhost' IDENTIFIED BY 'ecompassword';
MariaDB > GRANT ALL PRIVILEGES ON *.* TO 'ecomuser'@'localhost';
MariaDB > FLUSH PRIVILEGES;
```

> ON a multi-node setup remember to provide the IP address of the web server here: `'ecomuser'@'web-server-ip'`

4. Load Product Inventory Information to database

Create the db-load-script.sql

```
cat > db-load-script.sql <<-EOF
USE ecomdb;
CREATE TABLE products (id mediumint(8) unsigned NOT NULL auto_increment,Name varchar(255) default NULL,Price varchar(255) default NULL, ImageUrl varchar(255) default NULL,PRIMARY KEY (id)) AUTO_INCREMENT=1;

Kubernetes Resume Challenge - E-commerce Application Deployment

## Introduction
This repository documents my completion of the Kubernetes Resume Challenge, focusing on deploying an e-commerce application using Kubernetes and containerization techniques.

## Steps

- **Step 1: Certification**
  - Completed KodeKloud CKAD Course.
- **Step 2: Containerize Your E-Commerce Website and Database**
  - A. Web Application Containerization
    - Created Dockerfile.
    - Built and pushed Docker image to Azure Container Registry.
  - B. Database Containerization
    - Prepared database initialization script.
- **Step 3: Set Up Kubernetes on a Public Cloud Provider**
  - Created Kubernetes cluster on Azure (AKS).


## Step 4: Deploy Your Website to Kubernetes

- Created Kubernetes deployment yaml file (`web-deployment.yaml`) specifying necessary environment variables and mounts for the database connection.
- Applied the deployment to the Kubernetes cluster.






