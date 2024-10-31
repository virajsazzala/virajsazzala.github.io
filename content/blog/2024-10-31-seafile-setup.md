+++
title = "Seafile: OSS File Sync & Share"
date = 2024-10-31

[taxonomies]
  tags = ["internet-freedom", "guide"]
[extra]
  toc = true
+++

## [Overview](#overview)

I've always used cloud storage apps like Google Drive, Dropbox, etc. because it was always a hassle to work with documents and other files on-the-go. It was simple and convienient. But, eventually it kept getting annoying realising that now all these documents can now be accessed by some other company, and they aren't completely under my control. It felt like freedom was being traded for convienence. 

So, Recently I spent sometime looking for alternatives and I found a few like NextCloud, Syncthing, OwnCloud, SeaFile, etc.. I've read about and tested a few of these. 

- *Syncthing*: It's good, It works, and has good features for syncing files, but not really what I needed.
- *NextCloud*: It was a bit too much to setup, but It comes with a lot of features that can be installed. But, It was too many features I wouldn't use.
- *SeaFile*: It is simple, really easy to setup with docker and I chose to use this.

Before learning to setup, Here are the Specs of the VPS, I tested this on:

- *RAM*: 1GB
- *STORAGE* 25GB SSD
- *CPU*: Intel Xeon (Cascadelake) (1) @ 2.992GHz (1vCPU)
- *OS*: Debian 12 x64

## [A Simple Setup](#setup)

A simple SeaFile setup with Docker Compose...

> **_NOTE:_** Make sure you have a "*seafile.example.com*" subdomain pointing to your VPS.

1. Install and setup Docker

```sh
# Prerequistes
sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg-agent -y

# Docker GPG Key
curl -fsSL https://download.docker.com/linux/$(awk -F'=' '/^ID=/{ print $NF }' /etc/os-release)/gpg | sudo apt-key add -

# Add Docker software repository
sudo add-apt-repository "deb [arch=$(dpkg --print-architecture)] https://download.docker.com/linux/$(awk -F'=' '/^ID=/{ print $NF }' /etc/os-release) $(lsb_release -cs) stable"

# Install Docker
sudo apt install docker-ce docker-compose containerd.io -y

# Start Docker Service
sudo systemctl enable docker && sudo systemctl start docker

# Add User to Docker group
sudo usermod -aG docker $USER && su - $USER
```
2. Setting Up Seafile

```sh
# Create Seafile Directory
mkdir /opt/seafile
cd /opt/seafile

# Get Seafile `docker-compose.yml` file (Seafile CE 11.0)
wget -O "docker-compose.yml" "https://manual.seafile.com/11.0/docker/docker-compose/ce/11.0/docker-compose.yml"
```

3. Edit Seafile `docker-compose.yml` file

Make sure to update these variables in the file:

- MYSQL_ROOT_PASSWORD
- DB_ROOT_PASSWD
- SEAFILE_ADMIN_EMAIL
- SEAFILE_ADMIN_PASSWORD
- SEAFILE_SERVER_HOSTNAME

Rest can be left to defaults or changed as shown below. I've made a few adjustments to memory due to memory limitations. Hence, change as required.

```sh
services:
  db:
    image: mariadb:10.11
    container_name: seafile-mysql
    environment:
      - MYSQL_ROOT_PASSWORD=YOUR_DB_PASS_HERE  # Set the root's password of MySQL service.
      - MYSQL_LOG_CONSOLE=true
      - MARIADB_AUTO_UPGRADE=1
    volumes:
      - /opt/seafile-mysql/db:/var/lib/mysql  
    networks:
      - seafile-net
    deploy:
      resources:
        limits:
          memory: 128M  # Update based on your memory limit

  memcached:
    image: memcached:1.6.18
    container_name: seafile-memcached
    entrypoint: memcached -m 64  # Leave default, Set to 64 if you have low RAM
    networks:
      - seafile-net
          
  seafile:
    image: seafileltd/seafile-mc:11.0-latest
    container_name: seafile
    ports:
      - "80:80"
      - "443:443"  # Comment Out if you don't require HTTPS (better to have HTTPS)
    volumes:
      - /opt/seafile-data:/shared   
    environment:
      - DB_HOST=db
      - DB_ROOT_PASSWD=YOUR_DB_PASS_HERE  # Should be root's password of MySQL service.
      - TIME_ZONE=Etc/UTC  # Optional, default is UTC. 
      - SEAFILE_ADMIN_EMAIL=YOUR_EMAIL_HERE  
      - SEAFILE_ADMIN_PASSWORD=YOUR_SEAFILE_PASS_HERE  
      - SEAFILE_SERVER_LETSENCRYPT=true   # Use HTTPS or not
      - SEAFILE_SERVER_HOSTNAME=seafile.example.com # Specifies your host name if https 
    depends_on:
      - db
      - memcached
    networks:
      - seafile-net
    deploy:
      resources:
        limits:
          memory: 256M  # Increase if you have more memory 

networks:
  seafile-net:
```

4. Running Seafile Server

```sh
# if `docker-compose.yml` file is in current directory
docker compose up -d

# if `docker-compose.yml` file is elsewhere
docker compose -f /path/to/docker-compose.yml up -d
```

5. Stats Check

```sh
# To check running containers
docker ps

# To check stats of containers
docker stats
```

You can now tweak the memory limits based on stats.

you can refer to [Seafile Manual](https://manual.seafile.com/11.0/docker/deploy_seafile_with_docker/) for more information.

## [Post Setup and Conclusion](#postsetup)

In this article, we went through a simple Seafile Setup on a VPS. 

- You can access Seafile at *https://seafile.example.com*. 
- You can Login with SEAFILE_ADMIN_EMAIL and SEAFILE_ADMIN_PASSWORD from the `docker-compose.yml` file.

After Login, You can:

- Customize the frontend of Seafile.
- Use it to store, edit and do other operations with files.
- Download the Mobile app and connect to the same account.
- And many more!

That's it, Bye!