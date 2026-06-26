# Task - Implement a Client Server Architecture using MySQL Database Management System (DBMS)

## The following instructions were followed to implement the above task:

### Step 1. Create and configure two linux-based virtual servers (EC2 instance in AWS)

- ```mysql server```
- ```mysql client```

__1.__ Two EC2 Instances of t3.micro type and Ubuntu 24.04 LTS (HVM) was lunched in the eu-north-1 region using the AWS console.

<img width="900" alt="instance" src="https://github.com/user-attachments/assets/edaf4fbf-902b-4846-b6f0-23a793004172" />

__mysql server__

<img width="900" alt="server" src="https://github.com/user-attachments/assets/a0c52fe3-628b-45c2-ba79-71866ebfa024" />

__mysql client__

<img width="900" alt="client" src="https://github.com/user-attachments/assets/b7949f9d-e3a6-4c6b-8e1c-8d2103b8db86" />

The security group inbound rule for both instances was configured with the default SSH on port 22 with source from anywhere.

<img width="900" alt="server rules" src="https://github.com/user-attachments/assets/3b21fa9e-5547-4670-8c7f-78630de5248d" />

<img width="900" alt="client rules" src="https://github.com/user-attachments/assets/f46c2719-3995-48fe-bb87-f225222522cd" />

__2.__ Attached SSH key named __test__ to access the instance on port 22



## Step 2 - On ```mysql server``` Linux Server, install MySQL Server software

MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.

<img width="900" alt="security" src="https://github.com/user-attachments/assets/6d8dd25a-fd28-40cf-afcb-6b82d182a71d" />

__1.__ __Connect to the server instance via aws web console__

<img width="900" alt="server instance" src="https://github.com/user-attachments/assets/30fc4424-8689-4269-8389-77fcffc9c191" />

__2.__ __Update and upgrade Ubuntu__

```
sudo apt update && sudo apt upgrade -y
```
<img width="900" alt="server uu" src="https://github.com/user-attachments/assets/63c512ec-8e2c-492a-b498-0435b7618de4" />

__3.__ __Install MySQL Server software__

```
sudo apt install mysql-server -y
```
<img width="900" alt="mysql server" src="https://github.com/user-attachments/assets/1260d786-4478-45a3-9e6f-3b0aa0a885b8" />

__4.__ __Enable mysql server__

```
sudo systemctl enable mysql
```
<img width="900" alt="enable mysql" src="https://github.com/user-attachments/assets/5ee4a898-49b6-4e38-8bc6-f23cca2cd82b" />

## Step 3 - On ```mysql client``` Linux Server install MySQL Client software.

MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.

<img width="945" height="419" alt="client security" src="https://github.com/user-attachments/assets/4d01704b-30f7-4e4b-b8ee-e278542061ce" />

__1.__ __Connect to the client instance via aws web console__

<img width="900" alt="client instance" src="https://github.com/user-attachments/assets/a3f6ba37-7231-4d77-80f0-65eaa139fd20" />

__2.__ __Update and upgrade Ubuntu__

```
sudo apt update && sudo apt upgrade -y
```
<img width="900" alt="client uu" src="https://github.com/user-attachments/assets/e2235fad-96a9-428c-808a-1b76995bcf3b" />

__3.__ __Install MySQL Client software__

```
sudo apt install mysql-client -y
```
<img width="900" alt="mysql client" src="https://github.com/user-attachments/assets/c5427581-559d-4fca-bdaa-4a9559c2e216" />

## Step 4 - Configure MySQL server to allow connections from remote hosts.

__1.__ The security script of MySQL was run on __mysql server__ by running the command:

```
sudo mysql_secure_installation
```
<img width="900" alt="secure" src="https://github.com/user-attachments/assets/8c8bf76e-8b7c-4b95-bd8b-af8495b5d5e3" />
<img width="900" alt="secures" src="https://github.com/user-attachments/assets/454fcd33-6231-4930-a0b0-7254a1db47cf" />

__2.__ __Access MySQL shell__

```
sudo mysql
```
<img width="900" alt="sudo mysql" src="https://github.com/user-attachments/assets/d8428abd-e1a8-4c6e-8e8a-89e38048adbf" />

__3.__ __On mysql server, create a user named ```client``` and a database named ```test_db```__.

```
CREATE USER 'client'@'%' IDENTIFIED WITH caching_sha2_password BY 'User123!';

CREATE DATABASE test_db;

GRANT ALL ON test_db.* TO 'client'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;
```
<img width="900" alt="server client" src="https://github.com/user-attachments/assets/0134117e-a6a0-4c92-80d7-531e7e97c782" />

__4.__ __Now, configure MySQL server to allow connections from remote hosts__.

```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
Locate ```bind-address = 127.0.0.1```

Replace ```127.0.0.1``` with ```0.0.0.0```

<img width="900" alt="bind" src="https://github.com/user-attachments/assets/aa022544-7d4b-49b2-a330-8d5a77c9d4a5" />


## Step 5 - From ```mysql client``` Linxus Sever, connect remotely to ```mysql server``` Database Engine without using SSH. The mysql utility must be used to perform this action.

```
sudo mysql -u client -h 172.31.16.72 -p
```
Resulted in an error on the client server
<img width="900" alt="client error" src="https://github.com/user-attachments/assets/38bed3e0-eead-4287-a42c-0d6a118ce24e" />

To resolve in mysql server i changed the password to __NewStrongPassword123!__
```
ALTER USER 'client'@'%' IDENTIFIED BY 'NewStrongPassword123!';
FLUSH PRIVILEGES;
```
<img width="900" alt="resolved" src="https://github.com/user-attachments/assets/ef66b058-1a12-4a2b-bd8f-53bdfd2b6f9e" />

## Step 6 - Check that the connection to the remote MySQL server was successfull and can perform SQL queries.

```
show databases;
```
<img width="900" alt="databases" src="https://github.com/user-attachments/assets/76be612f-3359-4af9-9365-d064b9572b49" />

__Create table, insert rows into table and select from the table__

```
CREATE TABLE test_db.kpop_albums (
  album_id INT AUTO_INCREMENT,
  artist VARCHAR(100),
  album_name VARCHAR(200),
  release_year INT,
  PRIMARY KEY (album_id)
);

INSERT INTO test_db.kpop_albums (artist, album_name, release_year)
VALUES ('Tomorrow X Together', 'The Name Chapter: TEMPTATION', 2023);

INSERT INTO test_db.kpop_albums (artist, album_name, release_year)
VALUES ('Yeonjun', 'GGUM (Solo Project)', 2024);

SELECT * FROM test_db.kpop_albums;
```
<img width="900" alt="kpop" src="https://github.com/user-attachments/assets/f8c6ad58-f2bf-4a61-ab69-4287ee1f1d32" />

created duplicate albums

<img width="900" alt="kpop error" src="https://github.com/user-attachments/assets/51efd8ca-5af3-48e8-a97c-cfd7b7b04a3a" />
```
DELETE FROM test_db.kpop_albums
WHERE album_id = 2;
```
<img width="900" alt="delete" src="https://github.com/user-attachments/assets/0c27a988-f3e5-464c-a084-a92657cf83e5" />

<img width="900" alt="finished" src="https://github.com/user-attachments/assets/25888428-d8ea-4b67-bf44-b766f63205d8" />

Congratulations!! you have deloyed a fully functional MySQL Client-Server set up.
