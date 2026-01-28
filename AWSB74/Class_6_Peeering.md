# Configuring AWS Peering Between VPC
## Installing Node Exporter
- Create three VPC with diff IP Ranges and Deploy VM to it.
- In two VM's install node exporter using below steps.
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz
tar xzvf node_exporter-1.10.2.linux-amd64.tar.gz
cd node_exporter-1.10.2.linux-amd64/
sudo cp node_exporter /usr/local/bin
sudo useradd node_exporter --no-create-home --shell /bin/false
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter

sudo vi /etc/systemd/system/node_exporter.service 
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target

sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter
```

## Installing Prometheus
```
apt update && apt install -y unzip tree jq net-tools
sudo useradd --no-create-home --shell /bin/false prometheus
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus
wget https://github.com/prometheus/prometheus/releases/download/v3.4.0/prometheus-3.4.0.linux-amd64.tar.gz -P /tmp
cd /tmp
tar xvf /tmp/prometheus-3.4.0.linux-amd64.tar.gz
sudo cp /tmp/prometheus-3.4.0.linux-amd64/prometheus /usr/local/bin/
sudo cp /tmp/prometheus-3.4.0.linux-amd64/promtool /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
ls -al /usr/local/bin/
```
## Prometheus Config File
```
sudo nano /etc/prometheus/prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
    - targets: ['localhost:9090','13.223.92.251:9090']
  - job_name: 'awsb74_servers'
    scrape_interval: 5s
    static_configs:
    - targets: ['10.74.1.100:9100','98.86.149.172:9100']
  - job_name: 'awsb73_servers'
    scrape_interval: 5s
    static_configs:
    - targets: ['10.73.1.100:9100','52.3.223.111:9100']
  - job_name: 'awsb73_servers'
    scrape_interval: 5s
    static_configs:
    - targets: ['172.22.1.100:9100','ec2-18-118-126-24.us-east-2.compute.amazonaws.com:9100']
```


