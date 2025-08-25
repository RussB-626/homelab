# Pikachu Server Configuration

## Install Apps And Mount Network File Shares

### Prepare Directories

- cd /mnt
- mkdir pikachu

### Install Necessary Applications For Mounting

- apt update -y && apt upgrade -y
- apt install nfs-common -y

### Install Docker

- [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

### Mount Share To Directory

- mount <nas_ip>:/mnt/HDDs/servers/pikachu /mnt/pikachu

### Auto Mount Share On Reboot

- nano /etc/fstab
- Add the following line to the bottom of the file

```text
<nas_ip>:/mnt/HDDs/servers/pikachu /mnt/pikachu nfs defaults 0 0
```

## Local Directories

- /mnt
  - /eevee

## NFS Directories Setup

- /mnt/eevee
  - /docker
    - /mysql
      - .env
      - docker-compose.yaml
      - /configs
        - /mysql
    - /portainer
      - .env
      - docker-compose.yaml
      - /data
    - /postgres
      - .env
      - docker-compose.yaml
      - /configs
        - /pgadmin
          - /pgadmin
        - /postgres
          - /data
