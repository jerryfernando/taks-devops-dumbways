# docker
# 1 install docker
sudo nano docker_install.sh
copy paste dibawah ke file docker_install.sh
```
#!/bin/bash
echo "update dan upgrade server"
sudo apt update; sudo apt upgrade -y
echo "==============================================================================================="
echo "menambahkan Docker's official GPG key:"
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo "==============================================================================================="
echo "menambahkan repository ke Apt sources:"
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
echo "==============================================================================================="
echo "install docker"
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
echo "==============================================================================================="
echo "post-install"
sudo groupadd docker
sudo usermod -aG docker $USER
sudo systemctl status docker
```
ctrl x y enter
lalu ketik perintah dibawah

sudo chmod +x docker_install.sh

./docker_install.sh
