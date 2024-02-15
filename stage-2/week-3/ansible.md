# ansible
```
sudo nano install_ansible.sh
```

```
#!/usr/bin/env bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

```
sudo chmod +x install_ansible.sh
```

```
./install_ansible.sh
```
