# GPNrepo

Install Prometheus and install Node_Exporter

#Gaang

INSTALL PROMETHEUS

useradd --no-create-home --shell /bin/false prometheus
useradd --no-create-home --shell /bin/false node_exporter

mkdir /etc/prometheus
mkdir /var/lib/prometheus

chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus

cd /tmp
wget https://github.com/FosterG4/GPNrepo/raw/master/file/prometheus-2.20.0.linux-amd64.tar.gz
tar xzvf prometheus-2.20.0.linux-amd64.tar.gz
cp prometheus-2.20.0.linux-amd64/prometheus /usr/local/bin
cp prometheus-2.20.0.linux-amd64/promtool /usr/local/bin

chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool

cp -R prometheus-2.20.0.linux-amd64/consoles /etc/prometheus
cp -R prometheus-2.20.0.linux-amd64/console_libraries /etc/prometheus

chown -R prometheus:prometheus /etc/prometheus/consoles
chown -R prometheus:prometheus /etc/prometheus/console_libraries

vim /etc/prometheus/prometheus.yml 

global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'prometheus_master'
    scrape_interval: 5s
    static_configs:
      - targets: ['ip:9090']

vim /etc/systemd/system/prometheus.service

[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target


systemctl daemon-reload

systemctl start prometheus
systemctl enable prometheus
INSTALL NODE EXPORTER

cd /tmp
wget https://github.com/FosterG4/GPNrepo/raw/master/file/node_exporter-1.0.1.linux-amd64.tar.gz
tar -xvf node_exporter-1.0.1.linux-amd64.tar.gz
sudo mv node_exporter-1.0.1.linux-amd64/node_exporter /usr/local/bin/

sudo useradd -rs /bin/false node_exporter
sudo vim /etc/systemd/system/node_exporter.service


[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target




sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl status node_exporter
sudo systemctl enable node_exporter
http://<server-IP>:9100/metrics

vim /etc/prometheus/prometheus.yml



  - job_name: 'node_namehere'
    scrape_interval: 5s
    static_configs:
      - targets: ['ip:9100']



systemctl restart prometheus




global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'prometheus_master'
    scrape_interval: 5s
    static_configs:
      - targets: ['ip:9090']
  - job_name: 'node_namehere'
    scrape_interval: 5s
    static_configs:
      - targets: ['ip:8118']
