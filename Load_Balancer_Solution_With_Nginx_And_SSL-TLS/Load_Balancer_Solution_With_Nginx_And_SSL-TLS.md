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

__Update the hosts file__

```
sudo vi /etc/hosts
```
<img width="800" alt="ip" src="https://github.com/user-attachments/assets/dbbc28c8-46c3-432a-af5e-ae9af72d80e5" />

### 3. Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

__Update the instance__

```
sudo apt update && sudo apt upgrade -y
```
<img width="800" alt="uu" src="https://github.com/user-attachments/assets/ae6c21bc-6f51-47da-b417-432359a5890f" />

__Install Nginx__

```
sudo apt install nginx
```
<img width="800" alt="nginx" src="https://github.com/user-attachments/assets/a9119a26-7960-4a86-8eda-0da4a509cc8f" />

### 4. Configure Nginx LB using the Web Servers' name defined in /etc/hosts

This [blog](https://linuxize.com/post/how-to-edit-your-hosts-file/) provides more information about /etc/hosts

__Open the default Nginx configuration file__

```
sudo vi /etc/nginx/nginx.conf
```


__Insert the following configuration in http section__

```
    upstream myproject {
       server Web1 weight=5;
       server Web2 weight=5;
    }

    server {
        listen 80;
        server_name ww.domain.com;

        location / {
            proxy_pass http://myproject;
        }
    }
    # comment out this line
    # include /ete/nginx/sites-enabled/
```
<img width="800" alt="insert" src="https://github.com/user-attachments/assets/cebe324c-f127-45e6-8896-9f74c7143a73" />

__Test the server configuration__

```
sudo nginx -t
```
<img width="800" alt="test" src="https://github.com/user-attachments/assets/16e20c9f-3a6b-4d89-9615-a17099b137cc" />

__Restart Nginx and ensure the service is up and running__

```
sudo systemctl restart nginx

sudo systemctl status nginx
```
<img width="800" alt="restart" src="https://github.com/user-attachments/assets/f06e6e18-e812-46ee-9215-28b01bee56a4" />

# Part 2 - Register a new domain name and configure secured connection using SSL/TLS certificates

In order to get a valid SSL certificate we need to register a new domain name, we can do it using any Domain name registrar - a company that manages reservation of domain names. The most popular ones are: [Godaddy.com](https://www.godaddy.com/en-uk), [Domain.com](https://www.domain.com/), [Bluehost.com](https://www.bluehost.com/).


### 1. Register a new domain name with any registrar of your choice in any domain zone. (e.g .com, .net, .org, .edu, info, .xyz or any other)

[Cloudns.net](https://www.cloudns.net/) is the domain name registrar used for this project.

<img width="800" alt="domain" src="https://github.com/user-attachments/assets/13dc3dde-e643-4233-b181-7ee847e75a59" />

### 2. Assign an Elastic IP to our Nginx LB server and associate our domain name with this Elastic IP

This is necessary in order to have a static IP address that does not change after reboot.

<img width="800" alt="elastic" src="https://github.com/user-attachments/assets/c8862157-dcfa-42a0-a11e-49b73a817d6b" />

__Associate the elastic IP with Nginx LB__

<img width="800" alt="associate" src="https://github.com/user-attachments/assets/22da9794-9291-4ca9-96be-ee636aa16a83" />

<img width="800" alt="confirm" src="https://github.com/user-attachments/assets/1a40e041-7571-4be1-a502-30d63ae28124" />

### 3. Update or create A record your registrar to point to Nginx LB using the elastic IP

<img width="800" alt="dns" src="https://github.com/user-attachments/assets/fccc7e45-2806-4dde-8ec5-6e9a235a8ec6" />

<img width="800" alt="dns done" src="https://github.com/user-attachments/assets/5a54b345-1720-43d5-b3c8-06bffb4f29a2" />

__Use [DNS checker] (https://dnschecker.org/#A/www.misi.cloud-ip.cc) to Verify the DNS record__

<img width="800" alt="dns checker" src="https://github.com/user-attachments/assets/3c96f95c-8097-45e0-8771-7e95c6ad10c0" />

### 4. Configure Nginx to recognize your new domain name

Update your ``nginx.conf`` with ``server_name www.<your-domain-name.com`` instead of ``server_name www.domain.com``

In our case, the server_name is ``www.misi.cloud-ip.cc``

```
sudo vi /etc/nginx/nginx.conf
```
<img width="800" alt="www update" src="https://github.com/user-attachments/assets/8a44ed41-e8e2-4b15-8d11-0775b0c20735" />

__Restart Nginx__

```
sudo systemctl restart nginx
```

__Check that the Web Server can be reach from a browser with the new domain name using HTTP protocol__.

```
http://<your-domain-name.com>
```
<img width="800" alt="steghub" src="https://github.com/user-attachments/assets/898cf015-498f-4ccd-b1e8-db8537a95fca" />

### 5. Install [certbot](https://certbot.eff.org/) and request for an SSL/TLS certificate

__Ensure [snapd](https://snapcraft.io/snapd) service is active and running__

```
sudo systemctl status snapd
```
<img width="800" alt="snap" src="https://github.com/user-attachments/assets/38f40c2e-5d76-4d51-9f9e-42c26bdf63f6" />

__Install certbot__

```
sudo snap install --classic certbot
```
<img width="800" alt="certbot" src="https://github.com/user-attachments/assets/32580fbe-b7b5-4ab1-9142-cb4881ba9604" />

__Create a Symlink in `/usr/bin` for Certbot__: Place a symbolic link in this `PATH` to make it easier to run `certbot` from the `command line` without needing to specify its full path.

```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
Follow the certbot instructions you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from `nginx.conf` file so ensure you have updated it on step 4.

```
sudo certbot --nginx  # Obtain certificate
```
<img width="800" alt="certificate" src="https://github.com/user-attachments/assets/ec7ed22c-2c1c-4533-9492-793e39071f60" />

### Test secured access to your Web Solution by trying to reach `https://<your-domain-name.com>`.

You shall be able to access your wesite using HTTPS protocol (Uses `TCP port 443`) and see a padlock image in your browsers' search string. `Click on the padlock icon` and you can see the detail of the certificate issued for the website.

<img width="800" alt="cert test" src="https://github.com/user-attachments/assets/8249c27c-c78a-4749-83f5-bd2e4f5ff196" />

<img width="800" alt="cert test 2" src="https://github.com/user-attachments/assets/75e43189-51f8-4782-9b71-468b8be66e22" />

<img width="800" alt="cert test 3" src="https://github.com/user-attachments/assets/ce80b763-3e7c-4070-89fa-1445138c912f" />

### 6. Set up periodical renewal of your SSL/TLS certificate

By default, `LetsEncrypt` certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

__Test the renewal command in `dry-run` mode__

```
sudo certbot renew --dry-run
```
<img width="800" alt="renew" src="https://github.com/user-attachments/assets/75df7e0e-6ab5-4501-8227-37b05879451a" />

__Best pracice is to have a scheduled job that runs `renew` command periodically. Configure a `cronjob` to run the command twice a day__

__Edit the `crontab` file__

```
crontab -e
```
<img width="800" alt="cron" src="https://github.com/user-attachments/assets/86ef3b91-546e-46fb-8b6c-39e2b2a836df" />

__Add the following line to scheduled a job that runs renew command twice daily__

```
* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1
```
<img width="800" alt="cron update" src="https://github.com/user-attachments/assets/0b94abed-a6d8-4b6d-8d98-d20069844e47" />

You can always change the interval of the cronjob if twice a day is too often by adjusting the schedule expression.

Resources on cron configuration:

[#30 - Job Scheduling (cronjob/crontab) on Linux CentOS 8](https://www.youtube.com/watch?v=4g1i0ylvx3A)

[Online cron expression editor](https://crontab.guru/)
