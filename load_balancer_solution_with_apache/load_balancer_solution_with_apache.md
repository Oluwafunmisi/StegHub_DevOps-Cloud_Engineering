# Load Balancer Solution With Apache

A Load Balancer (LB) distributes clients' requests among underlying Web Servers and makes sure that the load is distributed in an optimal way.

The diagrame below shows the architecture of the solution

<img width="800" alt="image-22" src="https://github.com/user-attachments/assets/b0ce4469-0d5d-4648-a817-6d073c895ee3" />

## Task
Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 instance. Make sure that users can be served by Web servers through the Load Balancer.

## Prerequisites

Ensure that the following servers are installedd and configure already.

- Two RHEL9 Web Servers
- One MySQL DB Server (based on Ubuntu 24.04)
- One RHEL9 NFS Server

## Prerequisites Configurations

- Apache (httpd) is up and running on both Web Servers.
- ```/var/www``` directories of both Web Servers are mounted to ```/mnt/apps``` of the NFS Server.
- All neccessary TCP/UDP ports are opened on Web, DB and NFS Servers.
- Client browsers can access both Web Servers by their Public IP addresses or Public DNS names and can open the ```Tooling Website``` (e.g, ```http://<Public-IP-Address-or-Public-DNS-Name>/index.php```)

# Step 1 - Configure Apache As A Load Balancer

## 1. Create an Ubuntu Server 24.04 EC2 instance and name it Project-8-apache-lb

<img width="800" alt="instance" src="https://github.com/user-attachments/assets/1c24fb5f-58ca-4c34-b254-a180dab5ec60" />

## 2. Open TCP port 80 on Project-8-apache-lb by creating an Inbounb Rule in Security Group

<img width="800" alt="inbound role" src="https://github.com/user-attachments/assets/71a6edc2-fc52-443c-86bf-ce3a1325a787" />

## 3. Instal Apache Load Balancer on Project-8-apache-lb and configure it to point traffic coming to LB to both Web Servers.

### i. Install Apache2

- Access the instance

```
ssh -i test.pem ubuntu@16.16.200.36
```
<img width="800" alt="ssh" src="https://github.com/user-attachments/assets/fd8ea47c-6b66-4f0d-aaee-40a25df4ab04" />

- Update and upgrade Ubuntu

```
sudo apt update && sudo apt upgrade
```
<img width="800" alt="uu" src="https://github.com/user-attachments/assets/02ee101e-6715-45c0-8d45-1bcf5f6c05fe" />

- Install Apache

```
sudo apt install apache2 -y
```
<img width="800" alt="apache2" src="https://github.com/user-attachments/assets/e1a4fd30-57b3-459d-b670-2b32ac040d6b" />

```
sudo apt-get install libxml2-dev
```
<img width="800" alt="lib" src="https://github.com/user-attachments/assets/41b8f656-bd08-45fe-81a2-a0912e8a8e40" />

### ii. Enable the following modules

```
sudo a2enmod rewrite

sudo a2enmod  proxy

sudo a2enmod  proxy_balancer

sudo a2enmod  proxy_http

sudo a2enmod  headers

sudo a2enmod  lbmethod_bytraffic
```
<img width="800" alt="proxy" src="https://github.com/user-attachments/assets/56a33de1-49e4-4b6f-abab-97b56dbd77a8" />

### iii. Restart Apache2 Service

```
sudo systemctl restart apache2
sudo systemctl status apache2
```
<img width="800" alt="restart" src="https://github.com/user-attachments/assets/99662b19-cf7c-4bb0-bfad-e29216525f37" />

## Configure Load Balancing

### i. Open the file 000-default.conf in sites-available

```
sudo vi /etc/apache2/sites-available/000-default.conf
```
### ii. Add this configuration into the section ```<VirtualHost *:80>  </VirtualHost>```

```
<Proxy "balancer://mycluster">
               BalancerMember http://172.31.6.62:80 loadfactor=5 timeout=1
               BalancerMember http://172.31.11.103:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
```
<img width="800" alt="host" src="https://github.com/user-attachments/assets/c19f9d8f-6b43-42c4-828b-b4c0bef4f921" />

### iii. Restart Apache

```
sudo systemctl restart apache2
```

```bytraffic``` balancing method with distribute incoming load between the Web Servers according to currentraffic load. The proportion in which traffic must be distributed can be controlled bt ```loadfactor``` parameter.

Other methods such as ```bybusyness```, ```byrequests```, ```heartbeat``` can also be adopted.


## 4. Verify that the configuration works

### i. Access the website using the LB's Public IP address or the Public DNS name from a browser

<img width="800" alt="ip" src="https://github.com/user-attachments/assets/7a561e38-ee1f-4f24-9a56-35725d0503d5" />

<img width="800" alt="loaded" src="https://github.com/user-attachments/assets/ea305cd1-e3ec-46ed-bd2c-d5b10ea0272b" />

__Note__: If, ```/var/log/httpd``` was mounted from the Web Server to the NFS Server, unmount them and ensure that each Web Servers has its own log directory.

### ii. Unmount the NFS directory

- Check if the Web Server's log directory is mounted to NSF

```
df -h
sudo umount -f /var/log/httpd
```
If the directory is busy, the services using it needs to be stopped first.
```
sudo systemctl stop httpd
```

- Check that the directory is unmounted
```
df -h
```

### iii. Open two ssh consoles for both Web Server and run the command:

```
sudo tail -f /var/log/httpd/access_log
```
Web Server 1 ```access_log```

<img width="800" alt="web server 1" src="https://github.com/user-attachments/assets/368c10ac-2be9-46c9-8343-a7c7512cd62f" />

Web Server 2 ```access_log```

<img width="800" alt="web server 2" src="https://github.com/user-attachments/assets/73253407-0c24-4ce4-a708-61b0a7fa89ac" />

### iv. Refresh the browser page several times and ensure both Web Servers receive HTTP and GET requests. New records must apear in each web server log files. The number of request to each servers will be approximately the same since ```loadfactor``` is set to the same value for both servers. This means that traffic will be evenly distributed between them.

Web Server 1 ```access_log```

<img width="800" alt="web access 1" src="https://github.com/user-attachments/assets/5b01dd61-1145-4d83-b3b0-091e644b6dbc" />

Web Server 2 ```access_log```

<img width="800" alt="web access 2" src="https://github.com/user-attachments/assets/1e7ff189-6a5b-4d93-8804-10777b470f51" />

# Optional Step - Configure Local DNS Names Resolution

Sometimes it is tedious to remember and switch between IP addresses, especially if there are lots of servers to manage. It is best to configure local domain name resolution. The easiest way is use ```/etc/hosts``` file, although this approach is not very scalable, but it is very easy to configure and shows the concept well.

## Configure the IP address to domain name mapping for our Load Balancer.

### Open the hosts file

```
sudo vi /etc/hosts
```

### Add two records into file with Local IP address and arbitrary name for the Web Servers

```
<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
```
<img width="800" alt="web" src="https://github.com/user-attachments/assets/1aeccdf3-2d07-4db9-8fb7-ab9e42d064d2" />

### Update the LB config file with those arbitrary names instead of IP addresses

```
sudo vi /etc/apache2/sites-available/000-default.conf
```
```
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```
<img width="800" alt="server" src="https://github.com/user-attachments/assets/4a075614-2563-4242-8fba-596fd22d6a35" />

### Try to curl the Web Servers from LB locally

```
curl http://Web1
```
<img width="800" alt="curl 1" src="https://github.com/user-attachments/assets/6a006b7a-0b92-427f-99ba-9e21350d1e93" />

```
curl http://Web2
```
<img width="800" alt="curl 2" src="https://github.com/user-attachments/assets/993835fd-ba96-47e5-bc42-5e6deb43f748" />

This is only internal configuration and also local to the LB server, these names will neither be 'resolvable' from other servers internally nor from the Internet.

### Conclusion

The mod proxy balancer module in Apache HTTP Server offers robust features for load balancing, including support for sticky sessions, health checks, and various load balancing algorithms. Properly configuring these options ensures high availability, scalability, and reliability for web applications.


