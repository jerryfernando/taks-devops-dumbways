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
#### 2. Membuat file yang dibutuhkan untuk menjalankan Ansible-Playbook

Lalu setelah itu saya membuat direktori bernamakan `ansible` lalu di dalamnya terdapat file yang berisikan `ansible.cfg`, `Inventory`, `adduser.yml`, `docker.yml`, `docker-compose.yml`, `wayshub-fe.yml`, `nginx.yml`,  . Berikut isi script dari file file tersebut

- Inventory

```ansible
[appserver]
103.175.218.224

[gateway]
103.175.217.130

[all:vars]
ansible_connection=ssh
ansible_port=22
ansible_user="calvin"
ansible_python_interpreter=/usr/bin/python3.8
```

- ansible.cfg

```ansible
[defaults]
inventory = Inventory
private_key_file = /home/ubuntu/.ssh/id_rsa
host_key_checking  = False
timeout = 10
remote_port = 22
interpreter_python = auto_silent
```

- adduser.yml

```ansible
---
- become: true
  gather_facts: false
  hosts: all
  vars:
    - user: calvin2
    - passwd: $5$rgdh8Kfr5ZmRr7m4$TMzhvPQ4ml9D1uxB5eBlaF4CbL7KKtEwrRO/ubMqtfC
  tasks:
    - name: Creating User
      ansible.builtin.user:
        append: true
        groups: sudo
        name: "{{user}}"
        password: "{{passwd}}"
        home: /home/({user})
        state: present
        system: true
        generate_ssh_key: true
        ssh_key_file: .ssh/calvin2
```

- docker.yml

```ansible
---
- become: true
  hosts: all
  gather_facts: false
  tasks:
    - name: Install Prerequisite
      apt:
        update_cache: yes
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
          - gnupg
          - python3-pip
          - python3-venv
          - python3-docker
    - name: Add Docker GPG Key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
    - name: Add Docker Repository
      apt_repository:
        repo: deb https://download.docker.com/linux/ubuntu focal stable
    - name: Install Docker Engine
      apt:
        update_cache: true
        name:
          - docker-ce
          - docker-ce-cli
          - docker-buildx-plugin
    - name: Add User Docker Group
      user:
        name: calvin
        groups: docker
        append: yes
```

- docker-compose.yml

```ansible
version: "3.8"
services:
   frontend:
    container_name: wayshub-fe
    image: calvinnr/wayshub-fe:latest
    stdin_open: true
    ports:
      - 3000:3000
```

- wayshub-fe.yml

```ansible
- become: true
  gather_facts: false
  hosts: appserver
  tasks:
    - name: Pull Image from Docker Hub
      docker_image:
       name: calvinnr/wayshub-fe:latest
       source: pull
    - name: Copy Docker Compose File to Gateway
      copy:
        src: /home/ubuntu/ansible/docker-compose.yml
        dest: /home/calvin
    - name: Run Docker Compose
      command: docker compose up -d
```

- nginx.yml

```ansible
---
- become: true
  gather_facts: false
  hosts: gateway
  tasks:
    - name: Installing NGINX
      apt:
        name: nginx
        state: present
        update_cache: no
    - name: Copy rproxy.conf
      copy:
        src: /home/ubuntu/ansible/rproxy.conf
        dest: /etc/nginx/sites-enabled
    - name: Reload NGINX
      service:
        name: nginx
        state: reloaded
```

- rproxy.conf

```ansible
server {
    server_name calvin.studentdumbways.my.id;
    location / {
    proxy_pass http://103.175.218.224:3000;
    }
}
server {
    server_name ne-appserver.calvin.studentdumbways.my.id;
    location / {
    proxy_pass http://103.175.218.224:9100;
    }
}
server {
    server_name ne-gateway.calvin.studentdumbways.my.id;
    location / {
    proxy_pass http://103.175.217.130:9100;
    }
}
server {
    server_name prometheus.calvin.studentdumbways.my.id;
    location / {
    proxy_pass http://103.175.218.224:9090;
    }
}
server {
    server_name dashboard.calvin.studentdumbways.my.id;
    location / {
    proxy_set_header Host dashboard.calvin.studentdumbways.my.id;
    proxy_pass http://103.175.218.224:13000;
    }
}
```

- grafana.yml

```ansible
- become: true
  gather_facts: false
  hosts: appserver
  tasks:
    - name: Run Docker Grafana
      command: docker run -d --name=grafana -p 13000:3000 grafana/grafana
```

- prometheus.yml

```ansible
global:
  scrape_interval:     10s
# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'appserver'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    static_configs:
      - targets: ['ne-appserver.calvin.studentdumbways.my.id','ne-gateway.calvin.studentdumbways.my.id']
```

- prom.yml

```ansible
- become: true
  gather_facts: false
  hosts: appserver
  tasks:
    - name: Making Directory Monitoring
      command: mkdir monitoring
    - name: Copy Prometheus Configuration
      copy:
       src: /home/ubuntu/ansible/prometheus.yml
       dest: /home/calvin/monitoring
    - name: Run Docker Prometheus
      command: docker run -d -p 9090:9090 --name prometheus \ -v /home/calvin/monitoring/prometheus.yml:/opt/bitnami/prometheus/conf/prometheus.yml \ bitnami/prometheus:latest
```

- node-exporter.yml

```ansible
- become: true
  gather_facts: false
  hosts: all
  tasks:
    - name: Run Node-Exporter
      command: docker run -d -p 9100:9100 --name node-exporter bitnami/node-exporter:latest
```

#### 3. Hasil Ansible-Playbook

Berikut Hasil dari `adduser.yml`, `docker.yml`, `wayshub-fe.yml`, `nginx.yml`

<img width="1440" alt="Screenshot 2023-10-09 at 00 51 19" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/1e412e83-c811-49c7-bb77-6d908d81fded">

> adduser.yml

<img width="1440" alt="Screenshot 2023-10-09 at 01 58 19" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/6a9b55dc-bd38-44b2-b7f2-ae28092e8409">

> docker.yml

<img width="1440" alt="Screenshot 2023-10-09 at 02 39 04" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/f14bbd79-3a2a-4d67-baeb-19fbb16ea85d">

> wayshub-fe.yml

<img width="1440" alt="Screenshot 2023-10-09 at 03 31 41" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/c82ea113-d36a-433c-b0e0-47bad27f44dd">

> nginx.yml

<img width="1440" alt="Screenshot 2023-10-09 at 03 32 23" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/150045d0-814e-4032-8e6c-5c8a6d7d6178">

> node-exporter.yml

<img width="1440" alt="Screenshot 2023-10-09 at 04 07 56" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/0e527cdb-e56c-44ec-9992-c45acaedeecd">

> prom.yml

<img width="1440" alt="Screenshot 2023-10-09 at 04 16 26" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/94f7e6de-0a70-4e89-82f0-886140c5aa7d">

> grafana.yml

#### 4. Hasil Deploy On Top Docker

<img width="1440" alt="Screenshot 2023-10-09 at 02 39 25" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/5a5dc78a-e4f2-4ad5-be88-0dd51761300d">

> Wayshub-Frontend

<img width="1440" alt="Screenshot 2023-10-09 at 04 20 14" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/522f154d-0180-4433-a258-93c3382c3b74">

> Node-Exporter Appserver

<img width="1440" alt="Screenshot 2023-10-09 at 04 20 25" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/ab811f3f-f43a-4d56-a57c-4d523f501888">

> Node-Exporter Gateway

<img width="1440" alt="Screenshot 2023-10-09 at 04 17 04" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/2fb6cf48-d779-48ee-8ca3-ae95e33d4185">

> Prometheus

<img width="1440" alt="Screenshot 2023-10-09 at 04 17 44" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/51f9b40d-c9e1-4cf6-887c-359f22cb8ad6">

> Grafana
