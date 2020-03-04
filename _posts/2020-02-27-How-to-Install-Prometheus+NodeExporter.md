---
layout: post
title: "How to Install Prometeus and Node-Exporter (on Ubuntu 18)"
date: 2020-02-27
categories: [recipes]
---

Prometheus is an open-source systems monitoring and alerting toolkit originally built at SoundCloud.
This recipe describes how to install prometheus and node exporter (a famous metrics collector/exporter for hardware and OS metrics exposed by *NIX kernels)
on Ubuntu systems with systemd.

# Create a user

```
useradd -m -s /bin/bash prometheus
passwd prometheus
```

# Install Prometheus

```
su - prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.16.0/prometheus-2.16.0.linux-amd64.tar.gz

tar -xf prometheus-2.16.0.linux-amd64.tar.gz
mv prometheus-2.16.0.linux-amd64/ prometheus/

mkdir -p ~/prometheus/data


wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz
mv node_exporter-0.18.1.linux-amd64 node_exporter
```


## Configure prometheus to scrape the node_exporter metrics

```
vim prometheus/prometheus.yml
```

and insert

```
- job_name: 'node_exporter'
    static_configs:
    - targets: ['localhost:9100']
```

in section ```scrape_config```

```
exit
```

# Configure prometheus and node_exporter to startup via systemd

```
vim /etc/systemd/system/prometheus.service
```

Insert:
```
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
User=prometheus
Restart=on-failure

#Change this line if you download the 
#Prometheus on different path user
ExecStart=/home/prometheus/prometheus/prometheus \
  --config.file=/home/prometheus/prometheus/prometheus.yml \
  --storage.tsdb.path=/home/prometheus/prometheus/data

[Install]
WantedBy=multi-user.target
```


```
vim /etc/systemd/system/node_exporter.service
```

Insert:
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
ExecStart=/home/prometheus/node_exporter/node_exporter

[Install]
WantedBy=default.target
```

## Start them and enable auto start on system startup:
```
systemctl daemon-reload

systemctl start prometheus
systemctl enable prometheus

systemctl start node_exporter
systemctl enable node_exporter
```

## Tests systemd config:
```
systemctl status prometheus
systemctl status node_exporter
```

Browse to ```localhost:9090``` to see the servers metrics.

# References:

- source [https://www.howtoforge.com/tutorial/how-to-install-prometheus-and-node-exporter-on-centos-8/][howtoforge]
- Node-Exporter Dashboard: [https://grafana.com/grafana/dashboards/11074][grafana-dashboard]


[howtoforge]: https://www.howtoforge.com/tutorial/how-to-install-prometheus-and-node-exporter-on-centos-8/
[grafana-dashboard]: https://grafana.com/grafana/dashboards/11074