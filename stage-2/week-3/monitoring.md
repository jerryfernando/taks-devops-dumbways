# Monitoring
## Monitor resources for *Appserver & Gateway & Registry server
### create all server node exporter mengunakan ansible
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
appserver
![a](https://github.com/jerryfernando/Final-task/assets/23428256/efc52916-6cda-40ac-a7c0-11271392313a)

gateway
![g](https://github.com/jerryfernando/Final-task/assets/23428256/05974da4-4715-49de-9eb4-5e5716b13259)

registry
![r](https://github.com/jerryfernando/Final-task/assets/23428256/d5042702-b13a-4289-b7fb-3b8bb8936ba5)

### create all server prometheus mengunakan ansible
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
                - exporter-registry.jerry.studentdumbways.my.id
                - exporter-appserver.jerry.studentdumbways.my.id
                - exporter-gateway.jerry.studentdumbways.my.id

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
![p](https://github.com/jerryfernando/Final-task/assets/23428256/040db522-0824-4a28-b297-a9888621318f)

### create all server grafana mengunakan ansible

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
![M](https://github.com/jerryfernando/Final-task/assets/23428256/3ab59edf-64fa-45e1-a726-861cdd0d5197)

create prometheus on grafana `Home/Connections/Data sources/Add data source`

![add data](https://github.com/jerryfernando/Final-task/assets/23428256/1a4d7a9f-9bba-46f7-a830-eabf140dccd8)

build
![build](https://github.com/jerryfernando/Final-task/assets/23428256/0d55c9e0-39bc-4de8-9feb-051291185d02)

hasil
![node full](https://github.com/jerryfernando/Final-task/assets/23428256/608ee2e2-4317-43bd-b2bd-18bb646a2b40)

### create  Alerting grafana on telegram 
cara mendapatkan api token dan chat id

(https://t.me/BotFather)
![api telegram](https://github.com/jerryfernando/Final-task/assets/23428256/79b2cc00-0541-4c4f-90ed-4ff64b74f8d3)
( https://t.me/get_id_bot)
![chat id](https://github.com/jerryfernando/Final-task/assets/23428256/656c5091-dcec-4431-99f3-25a6bb09bd6f)

kemudian kita  integration data token dan id chat  pada telegram
![tele](https://github.com/jerryfernando/Final-task/assets/23428256/b6518253-b1ce-4833-ba86-e128f6937f2c)

### create  Alerting grafana on discord 
create channel lalu edit chennel
![edit](https://github.com/jerryfernando/Final-task/assets/23428256/a0e1ca46-0a3f-4f01-bc00-93a8390a5d2a)
pilih integrations
![webhook](https://github.com/jerryfernando/Final-task/assets/23428256/06d23274-a08b-4e2c-b11c-beb15d7da571)
copy webhook url
![cp webhook](https://github.com/jerryfernando/Final-task/assets/23428256/544011e9-71d0-404f-b5d9-1e85eb1e829e)
paste webhook url ke Contact points grafana
![discord](https://github.com/jerryfernando/Final-task/assets/23428256/bffbc701-a994-45a7-a743-60c0be5c4052)

### buat alert cpu,ram, dan storage
change Notification policies jadi discord
![policy](https://github.com/jerryfernando/Final-task/assets/23428256/646baeef-031a-48ed-bf6f-3dcdb76a8228)
create new alert rule
![rule](https://github.com/jerryfernando/Final-task/assets/23428256/45655d11-064f-4ccd-a36a-ae118bb97798)
cpu alert
```
100 - (avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[10m]) * 100) * on(instance) group_left(nodename) (node_uname_info))

```
![cpu alert](https://github.com/jerryfernando/Final-task/assets/23428256/cd4864e6-feaf-46fa-9c1d-c677e0996efc)

ram alert
```
100 * (1 - ((avg_over_time(node_memory_MemFree_bytes{}[10m]) + avg_over_time(node_memory_Cached_bytes{}[10m]) + avg_over_time(node_memory_Buffers_bytes{}[10m])) / avg_over_time(node_memory_MemTotal_bytes{}[10m])))
```
![ram alert](https://github.com/jerryfernando/Final-task/assets/23428256/c3f9ffb5-7699-4187-82d7-edc3d907dc0b)

storage alert 
```
sum(rate(node_disk_read_bytes_total[1m])) by (device, instance) * on(instance) group_left(nodename) (node_uname_info)
```
![storage](https://github.com/jerryfernando/Final-task/assets/23428256/76fc4b0f-920a-41df-b92c-5cd22d8acbc8)

alert rules

![all rule](https://github.com/jerryfernando/Final-task/assets/23428256/1b42bbd5-8b2b-4904-be1b-060d9202c4bf)


notif discord
![notif](https://github.com/jerryfernando/Final-task/assets/23428256/5493baf2-f9b9-4aeb-9f3b-9d59ea47fad7)




