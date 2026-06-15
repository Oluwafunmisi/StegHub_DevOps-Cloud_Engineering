## WEB STACK IMPLEMENTATION (LEMP STACK)

### Introduction

__LEMP is an open-source web application stack used to develop web applications. The term LEMP is an acronym that represents L for the Linux Operating system, Nginx (pronounced as engine-x, hence the E in the acronym) web server, M for MySQL database, and P for PHP scripting language.__


## Step 0: Prerequisites

__1.__ EC2 Instance of t3.micro type and Ubuntu 26.04 LTS (HVM) was lunched in the eu-north-1 region using the AWS console.

<img src="./images/createlemp.PNG" alt="Luanch Instance" width="800">
<img src="./images/lempview.PNG" alt="Instance" width="800">

__2.__ Created SSH key pair named test to access the instance on port 22

__3.__ The security group was configured with the following inbound rules:

- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.
- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.

<img src="./images/security.PNG" alt="Security" width="800">

__4.__ The default VPC and Subnet was used for the networking configuration.

<img src="./images/subnet.PNG" alt="Subnet" width="800">
