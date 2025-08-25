# Game Server Configuration

## AMP Panel Commands

- Update AMP Panel from command line
  - su -l amp
  - ampinstmgr --nocache upgradeall

## Install Playit.gg

```sh
curl -SsL https://playit-cloud.github.io/ppa/key.gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/playit.gpg >/dev/null
echo "deb [signed-by=/etc/apt/trusted.gpg.d/playit.gpg] https://playit-cloud.github.io/ppa/data ./" | sudo tee /etc/apt/sources.list.d/playit-cloud.list
sudo apt update
sudo apt install playit
sudo systemctl start playit
sudo systemctl enable playit
playit setup
```
