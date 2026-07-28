# Load Balancer Solution With Nginx and SSL/TLS

A Load Balancer (LB) distributes clients' requests among underlying Web Servers and makes sure that the load is distributed in an optimal way.
In this project, we will configure an [Nginx](https://www.f5.com/go/product/welcome-to-nginx) Load Balancer Solution.

It is extremely important to ensure that connections to our Web Solutions are secure and information is [encrypted in transit](https://security.berkeley.edu/data-encryption-transit-guideline). Connection over secured HTTP (HTTPS protocol), it's purpose and what is required to implement it will be covered.

## Task
This project consist of two parts:
1. Configure Nginx as a Load Balancer
2. Register a new domain name and configure secure connection

The diagram below shows the architecture of the solution

<img width="800" alt="image-42-1536x934" src="https://github.com/user-attachments/assets/10dc928b-87ea-4d03-b128-5c96900b9a2d" />

# Part 1 - Configure Nginx As A Load Balancer

 ### 1. Create an EC2 VM based on Ubuntu Server 24.04 LTS and name it nginx LB

 <img width="800" alt="create instance" src="https://github.com/user-attachments/assets/f6e5345b-2d9d-4ea0-9e9e-ab16452eb43f" />

 __Open TCP port 80 for HTTP connections and TCP port 443 for secured HTTPS connections__

 <img width="800" alt="rules" src="https://github.com/user-attachments/assets/ea1892e7-f5ea-4d08-8f56-2c800801223f" />

 ### 2. Update ``/etc/hosts`` file for local DNS with Web Servers' names (e.g ``web1`` and ``web2``) and their local IP addresses

__Access the instance__

```
ssh -i test.pem ubuntu@16.16.193.156
```
<img width="800" alt="ssh" src="https://github.com/user-attachments/assets/9546346a-e129-4a0e-afeb-1ff9dbe255b6" />

