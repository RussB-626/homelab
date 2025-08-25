# Guides

## SSH Guides

### SSH Website Guides

- <https://www.geeksforgeeks.org/scp-command-in-linux-with-examples/>
- <https://www.youtube.com/watch?v=U_uiVyF6MEs&t=617s>

### SSH Commands

- SSH Into A Remote Machine
  - ssh <user>@<server_ip>

- Copy Folders / Files From PC to Remote Machine
  - Copy Folder Contents To Folder: scp -r .\test-folder\ <user>@<server_ip>:~/new-folder
  - Copy Folder: scp -r .\test-folder\ <user>@<server_ip>:~
  - Note ~ denotes users home directory /home/<user>

- Permissions Denied Error:
  - sudo chown -R <user> ~/my-path

### SSH Keygen

- Create SSH Keys
  - ssh-keygen
  - Save to default directory:
    - C:\Users\<user>\.ssh\id_rsa
    - /root/.ssh/id_rsa
    - /home/<user>/.ssh/id_rsa
  - passphrase: ...

- Copy Public Key to Remote Machine
  - scp -r .ssh\id_rsa.pub <user>@<server_ip>:~/<user>_rsa.pub
    - created a file called <user>_rsa.pub and copied contents of id_rsa.pub into the new file

- Add SSH Key to authorized_keys file
  - Linux Commands:
    - Write To: cat /path/to/file > to /path/to/file
    - Append To: cat /path/to/file >> to /path/to/file
  - Procedure:
    - cat /home/<user>/<user>_rsa.pub >> /home/<user>/.ssh/authorized_keys

- Once Finished: rm -r /home/<user>/<user>_rsa.pub

### SSH Keys From 1Password

- Copy Public Key Into Server Client
  - SSH into Server Client
  - Step 1
    - sudo nano /home/<user>/.ssh/authorized_keys
    - append to the end of the file the public key from 1Pass
  - Step 2
    - sudo nano /root/.ssh/authorized_keys
    - append to the end of the file the public key from 1Pass
- Copy Private Key Into SSH Client
  - In directory C:\Users\<user>\.ssh
  - create file id_rsa
  - copy and paste in the private key from 1Pass

---

## Resize Partitions

- sudo lsblk
- sudo pvs
- sudo pvresize /dev/nvme0n1p3
- sudo lvextend -r -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
- sudo growpart /dev/nvme0n1 3

---

## Folder Ownership

- Check Users/Groups IDs
  - nano /etc/passwd
  - id -u <username> OR id -g <username>
  - id = uid=#(username) gid=#(username) groups=#(username)...
  - grep <username> /etc/passwd

- Change Directory Permissions
  - chown username:group directory
