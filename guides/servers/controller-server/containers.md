# Docker Container Help Doc

## Cloudflare DDNS Docker Container

### Cloudflare Website

[Cloudflare-DDNS](https://hub.docker.com/r/oznu/cloudflare-ddns/)

## MySQL Docker Container

### MySQL Website

[MySQL](https://hub.docker.com/_/mysql)

### MySQL Volumes

- ../data
- ../dumps
  - Not used by docker container
  - Server service will dump backups here

## Omada Docker Container

### Omada Website

[Omada Controller](https://github.com/mbentley/docker-omada-controller)

### Omada Volumes

- ../data
- ../logs

## PHPMyAdmin Docker Container

### PHPMyAdmin Website

[PHPMyAdmin](https://hub.docker.com/_/phpmyadmin)

## Portainer Docker Container

### Portainer Website

[Portainer](https://docs.portainer.io/start/install-ce/server/docker/linux)

### Portainer Volumes

- ../data

## Semaphore Docker Container

### Semaphore Website

[Semaphore](https://docs.semui.co/administration-guide/installation#docker)

### Semaphore Guides

- <https://www.youtube.com/watch?v=NyOSoLn5T5U&t=285s>

### Semaphore Volumes

- ../requirements.txt

### Semaphore Basic Setup

#### 1) SSH Key Set Up

- On the Controller machine
  - Follow the guides for [SSH Key generation](/Guides/Guides.md)
  - Make sure to add the public key to the authorized_keys file on each server
  - the id_rsa file contains the private key needed in Semaphore
    - nano /root/.ssh/id_rsa

#### 2) Github: Repository And SSH Set Up

- Ansible / Playbooks
  - https://github.com/<mygithub>/playbooks

- Create Github SSH Keys:
  - **Path**: Github > User Icon > Settings > SSH and GPG Keys
  - New SSH Key
  - Title: GitHub
  - Key type: Authentication Key
  - Key:
    - Using 1Password create an RSA key with a bit length of 4096
    - Put the saved Public key into the textarea
  - Add SSH key

#### 3) Semaphore: Key store

- New Keys
  1) Key Name: GitHub
      - Type: SSH Key
      - Username: <email address>
      - Private Key:
        - Paste in the private key stored in 1Password
  2) Key Name: SSH
      - Type: SSH Key
      - Username: <user>
      - Private Key:
        - Copy & Paste in the private key from the id_rsa file set up in Section 1 above
  3) Key Name: Sudo User
      - Type: Login with password
      - Login: <user>
      - Password:
        - Paste in the password stored in 1Password

#### 4) Semaphore: Repositories

- New Repository
  - Name: GitHub
  - URL or path: git@github.com:<mygithub>/Homelab-Playbooks.git
  - Branch: main
  - Access Key: GitHub (Section 3.1)

#### 5) Semaphore: Environment

- New Environment
  - Name: all
  - Extra Vars: {}
  - Env. Vars: {}

#### 6) Semaphore: Inventory

- New Inventory
  - Name: Lab Servers
  - User Credentials: SSH (Section 3.2)
  - Sudo Credentials: Sudo User (Section 3.3)
  - Type: Static
  - List Of Server IPs
    - <server_ip>
    - <server_ip>
    - <server_ip>

#### 7) Semaphore: Task Templates

- New Template (Basic Setup Guide For One Task)
  - Type: Task
  - Name: reboot-servers
  - Playbook Filename: playbooks/reboot-servers.yaml (From GitHub, Section 2)
  - Inventory: Lab Servers
  - Repository: GitHub (Section 2)
  - Environment: all (section 5)
  - Save
