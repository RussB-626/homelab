# Docker Commands

## Installation

- <https://docs.docker.com/engine/install/ubuntu/>

## General Docker Commands

- Docker Inspect Command
  - docker inspect #Container
- Shell into Container
  - docker exec -it <container> sh

## Run Docker Containers

- docker compose up -d
- docker compose logs
- docker compose config
- docker compose down

## Update Docker Images

- docker compose pull
- docker compose up -d
- docker image prune -af

## Shelled Into Container

- Check IP
  - curl ip.me

- Curl does not exist, must install
  - apt-get update -y; apt-gety install curl -y
  - If apt-get does not work: apk update; apk add curl
