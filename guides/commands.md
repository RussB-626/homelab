# Commands

- Canonical And Ubuntu Status
    - https://status.canonical.com/

- Update And upgrade
  - sudo apt update -y && sudo apt upgrade -y

- Change ubuntu sources:
    - sudo nano /etc/apt/sources.list.d/ubuntu.sources

- Issues with update and upgrade:
    - sudo apt clean
    - sudo apt update
    - sudo apt upgrade

- Change Directory/File Ownership
  - sudo chown -R $USER /path/to/folder

- Reload Systemctl Services
  - sudo systemctl daemon-reload

- Rename Directory
  - mv /home/user/oldname /home/user/newname


Default Sources File - /etc/apt/sources.list.d/ubuntu.sources:
```text
Types: deb
URIs: http://us.archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```