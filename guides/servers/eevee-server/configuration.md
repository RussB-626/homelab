# Eeevee Server Configuration

## Install Apps And Mount Network File Shares

### Prepare Directories

- cd /mnt
- mkdir eevee

### Install Necessary Applications For Mounting

- apt update -y && apt upgrade -y
- apt install nfs-common -y

### Install Docker

- [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

### Mount Share To Directory

- mount <nas_ip>:/mnt/HDDs/servers/eevee /mnt/eevee

### Auto Mount Share On Reboot

- nano /etc/fstab
- Add the following line to the bottom of the file

text
<nas_ip>:/mnt/HDDs/servers/eevee /mnt/eevee nfs defaults 0 0


## Local Directories

- /mnt
  - /eevee

## NFS Directories Setup

- /mnt/eevee
  - /docker
    - /portainer
      - .env
      - docker-compose.yaml
      - /data
