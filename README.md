# Snipe-IT-Open-Source-IT-Asset-Management-Setup-on-Ubuntu-24.04
 
This guide walks you through the process of setting up Snipe-IT (an open-source IT asset management system) on Ubuntu 24.04 using Docker. We will use Docker to simplify the installation process and ensure that the system is running the latest version (7.0.8) of Snipe-IT.

---

## **Pre-requisitese**

Before starting, ensure that you have the following:

a. A fresh Ubuntu 24.04 server or machine.

b. Root access or sudo privileges.

c. Basic knowledge of terminal commands.

---

## **Step 1: Install Docker and Docker-Compose**

### **1.1 Update the system**
```bash
sudo apt-get update
```
```bash
sudo apt-get upgrade -y
```

### **1.2 Install required dependencies**
```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```

### **1.3 Add Docker GPG key**
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

### **1.4 Add Docker repository**
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
```

### **1.5 Update APT**
```bash
sudo apt update
```

### **1.6 Check Docker package availability**
```bash
apt-cache policy docker-ce
```

### **1.7 Install Docker**
```bash
sudo apt install docker-ce -y
```

### **1.8 Verify Docker Installation**
```bash
sudo systemctl status docker
```

### **1.9 Update and Upgrade Packages**
```bash
sudo apt-get update && sudo apt-get upgrade -y
```

### **1.10 Remove Docker Desktop (Optional)**
```bash
sudo apt remove docker-desktop
```
```bash
rm -r $HOME/.docker/desktop
```
```bash
sudo rm /usr/local/bin/com.docker.cli
```
```bash
sudo apt purge docker-desktop
```

### **1.11 Install additional packages (for newer Docker versions)**
```bash
sudo apt-get install ca-certificates curl gnupg lsb-release -y
```
```bash
sudo mkdir -p /etc/apt/keyrings
```

### **1.12 Add Docker GPG key to the system**
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

### **1.13 Add Docker’s official repository**
```bash
echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### **1.14 Check available Docker versions**
```bash
apt-cache madison docker-ce | awk '{ print $3 }'
```

### **1.15 Install Docker-Compose (Optional but recommended)**
```bash
sudo apt-get install docker-compose -y
```

### **1.16 Test Docker Installation**
```bash
sudo docker run hello-world
```

---

## **Step 2: Setup Snipe-IT with Docker**

### **2.1 Create Directories for MySQL and Snipe-IT**
```bash
sudo -i
```
```bash
mkdir /root/snipe_mysql
```
```bash
mkdir /root/snipe-it
```
```bash
cd /root/snipe-it
```

### **2.2 Create the `docker-compose.yml` File**
```bash
nano docker-compose.yml
```

Add the following content into the `docker-compose.yml` file:

```yaml
version: "1"
services:
  mysql:
    image: mysql:5
    container_name: snipe_mysql
    restart: always
    volumes:
      - /root/snipe_mysql:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=your_root_password
      - MYSQL_USER=snipe_it
      - MYSQL_PASSWORD=your_db_password
      - MYSQL_DATABASE=snipe_db

  snipeit:
    image: linuxserver/snipe-it:latest
    container_name: snipe-it
    restart: always
    depends_on:
      - mysql
    volumes:
      - /root/snipe_it:/config
    environment:
      - APP_URL=http://your_IP_Address:8084    # Change your IP address here
      - MYSQL_PORT_3306_TCP_ADDR=mysql
      - MYSQL_PORT_3306_TCP_PORT=3306
      - MYSQL_DATABASE=snipe_db
      - MYSQL_USER=snipe_it
      - MYSQL_PASSWORD=your_db_password
      - PGID=1000
      - PUID=1000
    ports:
      - "8084:80"
```

### **2.2.1 Change your IP address at:**
```bash
- APP_URL=http://your_IP_Address:8084    # Replace 'your_IP_Address' with your actual IP address
```
For example:
```bash
- APP_URL=http://192.168.100.131:8084
```

### **2.3 Start Snipe-IT with Docker**
```bash
sudo docker-compose up -d
```

---

## **Step 3: Set Up Snipe-IT Web Interface**

### **3.1 Access Snipe-IT in the Browser**
In your browser, go to:

```
http://your_IP_Address:8084
```

For example:
```
http://192.168.100.131:8084
```

### **3.2 Setup the Database Table**
Click the button **Next: Create Database Table**.

### **3.3 Create User**
On the next page, click **Next: Create User** and fill in the following details:

```text
Site Name: AMS - Xitiz Tech Services
Email Domain: xitiztechservice.com
Email: admin@xitiztechservice.com
First Name: Xitiz
Last Name: Basnet
Username: xitiz.basnet
Password: (Enter a strong password)
Confirm Password: (Enter the same password)
```

Click **Next: Save User** to continue.

---

## **Step 4: Final Steps**

### **4.1 Successful Installation**
If everything is set up correctly, you will be redirected to the **Snipe-IT Dashboard**.

```text
Congratulations! You have successfully installed Snipe-IT on Ubuntu 24.04 using Docker.
```

---

## **Step 5: Troubleshooting and Cleanup**

### **5.1 Check Logs for Errors**
```bash
docker-compose logs snipeit
```
```bash
docker-compose logs mysql
```

### **5.2 Stop the Docker Containers**
```bash
sudo docker-compose down
```

### **5.3 Remove Containers and Associated Volumes**
```bash
sudo docker-compose down -v
```

---
