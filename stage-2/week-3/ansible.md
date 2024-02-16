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
## Membuat file yang dibutuhkan untuk menjalankan Ansible-Playbook

sudo nano/vim Inventory
Inventory
```bash
[appserver]
103.127.97.172

[gateway]
103.127.97.222

[all:vars]
ansible_connection=ssh
ansible_port=22
ansible_user="jeri77"
ansible_python_interpreter=/usr/bin/python3.8
```
atau untuk 3 server atau lebih bisa gunankan ini 
```
[appserver]
appserver ansible_host=103.127.97.172 ansible_user=jeri77

[gateway]
gateway ansible_host=103.127.97.222 ansible_user=jeri77

[registry]
registry ansible_host=103.183.75.220 ansible_user=registry-jerry

[all:vars]
ansible_connection=ssh
ansible_port=22
ansible_python_interpreter=/usr/bin/python3
```

sudo nano/vim ansible.cfg
ansible.cfg
```bash
[defaults]
inventory = Inventory
private_key_file = /home/jeri77/.ssh/id_rsa
host_key_checking  = False
timeout = 10
remote_port = 22
interpreter_python = auto_silent

[ssh_connection]
ssh_args = -o ForwardAgent=yes
```

Attach SSH key & ip configuration to all server

sudo nano/vim ssh.yml
```
---
- become: true
  gather_facts: false
  hosts: all
  tasks:
    - name: Copy SSH Private Keys
      copy:
        src: /home/jeri77/.ssh/id_rsa
        dest: /home/jeri77/.ssh

    - name: Copy SSH Pubic Keys
      copy:
        src: /home/jeri77/.ssh/id_rsa.pub
        dest: /home/jeri77/.ssh

    - name: Copy SSH authorized_keys
      copy:
        src: /home/jeri77/.ssh/authorized_keys
        dest: /home/jeri77/.ssh
    

```
atau untuk 3 server code seperti dibawah ini
```
---
- hosts: all
  become: true
  gather_facts: false
  tasks:
    - name: Copy SSH Private Key to registry-jerry's home directory
      copy:
        src: /home/jeri77/.ssh/id_rsa
        dest: /home/registry-jerry/.ssh/id_rsa
        owner: registry-jerry
        group: registry-jerry
        mode: '0600'

    - name: Copy SSH Public Key to registry-jerry's home directory
      copy:
        src: /home/jeri77/.ssh/id_rsa.pub
        dest: /home/registry-jerry/.ssh/id_rsa.pub
        owner: registry-jerry
        group: registry-jerry
        mode: '0644'

    - name: Copy SSH Private Key to jeri77's home directory
      copy:
        src: /home/jeri77/.ssh/id_rsa
        dest: /home/jeri77/.ssh/id_rsa
        owner: jeri77
        group: jeri77
        mode: '0600'

    - name: Copy SSH Public Key to jeri77's home directory
      copy:
        src: /home/jeri77/.ssh/id_rsa.pub
        dest: /home/jeri77/.ssh/id_rsa.pub
        owner: jeri77
        group: jeri77
        mode: '0644'
```




[All server]

- Docker engine
- node exporter

sudo nano/vim docker.yml
```bash
---
- become: true
  hosts: all
  gather_facts: false
  tasks:
    - name: Install Aptitude
      apt:
        name: aptitude
        state: latest
        update_cache: true

    - name: Install Docker Dependencies
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
          - python3-apt
          - lsb-release

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
        name: jeri77
        groups: docker
        append: yes
```

sudo nano/vim node-exporter.yml
```
---
- become: true
  gather_facts: false
  hosts: all
  tasks:
    - name: Install Node-Exporter
      community.docker.docker_container:
        name: node-exporter
        image: prom/node-exporter
        ports:
          - 9100:9100
        restart_policy: unless-stopped
```



[Appserver]

- git repo
- prometheus & grafana

sudo nano/vim git-repo.yml

```bash
---
- hosts: appserver
  gather_facts: no

  tasks:
    - name: Clone Frontend Repository
      git:
        clone: yes
        repo: git@github.com:demo-dumbways/fe-dumbmerch.git
        version: master
        dest: /home/jeri77/fe-dumbmerch
        accept_hostkey: yes
        key_file: /home/jeri77/.ssh/id_rsa
      become: yes

    - name: Clone Backend Repository
      git:
        clone: yes
        repo: git@github.com:demo-dumbways/be-dumbmerch.git
        version: master
        dest: /home/jeri77/be-dumbmerch
        accept_hostkey: yes
        key_file: /home/jeri77/.ssh/id_rsa
      become: yes
```
sudo nano/vim prometheus.yml
```
---
- become: true
  gather_facts: false
  hosts: appserver
  tasks:
    - name: Create Prometheus.yml
      copy:
        dest: /home/jeri77/prometheus.yml
        content: |
          scrape_configs:
            - job_name: capstone
              scrape_interval: 5s
              static_configs:
              - targets:
                - ne-appserver.jerry.studentdumbways.my.id
                - ne-gateway.jerry.studentdumbways.my.id

    - name: Running Prometheus on Top Docker
      community.docker.docker_container:
        name: prometheus
        image: prom/prometheus
        ports:
          - 9090:9090
        restart_policy: unless-stopped
        volumes:
          - /home/jeri77/prometheus.yml:/etc/prometheus/prometheus.yml
```
sudo nano/vim grafana.yml
```

---
- become: true
  gather_facts: false
  hosts: appserver
  tasks:
    - name: Set Permission
      ansible.builtin.file:
        path: ~/grafana
        state: directory
        mode: "0755"

    - name: Set Up Grafana
      community.docker.docker_container:
        name: grafana
        image: grafana/grafana
        ports:
          - 13000:3000
        restart_policy: unless-stopped
        volumes:
          - ~/grafana:/var/lib/grafana
        user: root
```


[gateway]

- firewall
- nginx
- reverse proxy
- ssl certificate

sudo nano/vim firewall.yml

```bash
---
- become: true
  gather_facts: false
  hosts: gateway
  tasks:
    - name: Install UFW
      apt:
        name: ufw
        update_cache: yes
        state: latest

    - name: Enable UFW
      community.general.ufw:
        state: enabled
        policy: allow

    - name: UFW Allow Rules
      community.general.ufw:
        rule: allow
        proto: tcp
        port: "{{ item }}"
      with_items:
      - 22
      - 80
      - 443
      - 3000
      - 5000
      - 5432
      - 3306
      - 9090
      - 9100
      - 8080
      - 13000
    - name: enable ufw
      community.general.ufw:
        state: reloaded
        policy: allow
```
buat file  di home/jer77/ansible/rporxy.conf
```
---

            server {
               server_name jerry.studentdumbways.my.id;
               location / {
               proxy_pass http://103.127.97.172:3000;
               }
            }
            server {
               server_name exporter-appserver.jerry.studentdumbways.my.id;
               location / {
               proxy_pass http://103.127.97.172:9100;
               }
            }
            server {
               server_name exporter-gateway.jerry.studentdumbways.my.id;
               location / {
               proxy_pass http://103.127.97.222:9100;
               }
            }
            server {
               server_name prometheus.jerry.studentdumbways.my.id;
               location / {
               proxy_pass http://103.127.97.172:9090;
               }
            }
            server {
               server_name monitoring.jerry.studentdumbways.my.id;
               location / {
               proxy_set_header Host monitoring.jeri.studentdumbways.my.id;
               proxy_pass http://103.127.97.172:13000;
               }
            }
            server {
               server_name api.jerry.studentdumbways.my.id;
               location / {
               proxy_pass http://103.127.97.172:5000;
               }
            }
            server {
               server_name registry.jerry.studentdumbways.my.id;
               location / {
               proxy_pass http://103.183.75.220:5000;
               }
            }
            server {
               server_name jenkins.jerry.studentdumbways.my.id;
               location / {
               proxy_pass http://103.127.97.222:8080;
               }
            }

```
sudo nano/vim nginx.yml
```
---
- become: true
  gather_facts: false
  hosts: gateway
  tasks:
    - name: "Installing nginx"
      apt:
        name: nginx
        state: latest
        update_cache: yes
    - name: start nginx
      service:
        name: nginx
        state: started
    - name: copy proxy.conf
      copy:
        src: /home/irwan/ansible/rproxy.conf
        dest: /etc/nginx/sites-enabled/
    - name: Install Certbot
      community.general.snap:
        name: certbot
        classic: yes
    - name: Obtain and install SSL certificate with Certbot Nginx fe
      command: certbot --nginx -d jerry.studentdumbways.my.id --non-interactive --agree-tos --email jerifernando88@gmail.com
    - name: Obtain and install SSL certificate with Certbot Nginx be
      command: certbot --nginx -d api.jerry.studentdumbways.my.id --non-interactive --agree-tos --email jerifernando88@gmail.com
    - name: Obtain and install SSL certificate with Certbot Nginx exporterapp
      command: certbot --nginx -d exporter-appserver.studentdumbways.my.id --non-interactive --agree-tos --email jerifernando88@gmail.com
    - name: Obtain and install SSL certificate with Certbot Nginx exportergate
      command: certbot --nginx -d exporter-gateway.studentdumbways.my.id --non-interactive --agree-tos --email jerifernando88@gmail.com
    - name: Obtain and install SSL certificate with Certbot Nginx Prometheus
      command: certbot --nginx -d prometheus.jerry.studentdumbways.my.id --non-interactive --agree-tos --email jerifernando88@gmail.com
    - name: Obtain and install SSL certificate with Certbot Nginx grafana
      command: certbot --nginx -d monitoring-jerry.studentdumbways.my.id --non-interactive --agree-tos --email jerifernando88@gmail.com
    - name: reloaded nginx
      service:
        name: nginx
        state: reloaded
```
