# Controller Server Configuration

## Cockpit

### Install Cockpit

- [Cockpit](https://cockpit-project.org/running)

### Configure

- Install Guide: <https://cockpit-project.org/running#ubuntu>
- cat /etc/os-release
- sudo apt install -t ${VERSION_CODENAME}-backports cockpit
  - ${VERSION_CODENAME} reference the VERSION_CODENAME from os-release file

### Update Netplan Config

- sudo nano /etc/netplan/00-installer-config.yaml
- Add to the end of the file
- renderer: NetworkManager

### Install Additional Cockpit Apps

- Navigator: <https://github.com/45Drives/cockpit-navigator>

## Configure UFW Firewall Rules

- ufw enable
- SSH: ufw allow ssh OR ufw allow 22/tcp
- Cockpit Web UI: ufw allow 9090
- NGINX: ufw allow 80
- NGINX: ufw allow 443
- ufw status

## Install Apps And Mount Network File Shares

### Prepare Directores

- cd /mnt
- mkdir Media
- mkdir MediaServer

### Install Necessary Applications For Mounting

- apt update -y && apt upgrade -y
- apt install nfs-common -y

### Install Docker

- [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

### Mount Share To Directory

- sudo mount <nas_ip>:/mnt/HDDs/servers/controller /mnt/controller
- sudo mount <nas_ip>:/mnt/HDDs/servers /mnt/servers
- sudo mount <nas_ip>:/mnt/HDDs/MediaServer /mnt/MediaServer

### Setup Auto Mount Share On Reboot

- nano /etc/fstab
- Add the following line to the bottom of the file

```text
<nas_ip>:/mnt/HDDs/servers/controller /mnt/controller nfs defaults 0 0
<nas_ip>:/mnt/HDDs/servers /mnt/servers nfs defaults 0 0
<nas_ip>:/mnt/HDDs/MediaServer /mnt/MediaServer nfs defaults 0 0
```

## Local Directories

- /mnt
  - /controller
  - /servers
  - /MediaServer

## NFS Directories Setup

- /mnt/controller
  - /docker
    - /portainer
      - .env
      - docker-compose.yaml
      - /data
    - /stack
      - .env
      - docker-compose.yaml
      - /configs
        - /filebrowser
          - database.db
          - settings.db
        - /kuma
          - /data
        - /mysql
        - /omada
          - /data
          - /logs
        - /semaphore

## Install And Run Docker Containers

- Installation
  - <https://docs.docker.com/engine/install/ubuntu/>
- Run Docker Containers from Docker Compose Files Repo

## Disable Specific Systemctl Jobs Only If Needed

- systemctl disable systemd-networkd-wait-online

## Configure SSH Keys

- Follow guide [Here](/Homelab-Guides/Guides/Guides.md)
- scp -r id_rsa.pub <user>@<server_ip>:~/<user>_rsa.pub
- cat /home/<user>/<user>_rsa.pub >> /home/<user>/.ssh/authorized_keys
- rm -r <user>_rsa.pub

## Update Filebrowser Container settings file

```json
{
  "port": 80,
  "baseURL": "",
  "address": "",
  "log": "stdout",
  "database": "/database.db",
  "root": "/srv"
}
```
