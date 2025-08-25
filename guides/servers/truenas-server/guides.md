# TrueNAS Server Configuration

## Datasets

- controller-server
  - docker-containers
    - Permissions:
      - root: rwe
      - root: rwe
      - other: rwe

## Shares

- UNIX (NFS) Shares
  - Config Service:
    - Enabled Protocols:
      - NFSv3, NFSv4
    - Enable: NFSv3 ownership model for NFSv4
  - Add New Share:
    - Path: /mnt/HDDs/ControllerServer/docker-containers
    - Add: Authorized Hosts and IP Address
      - <controller_ip>
    - Advanced Options:
      - Maproot User:
        - root
