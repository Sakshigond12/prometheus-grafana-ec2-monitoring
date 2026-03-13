# Real-Time Infrastructure Monitoring using Prometheus and Grafana on EC2

# 📖Project Overview

This project demonstrates how to implement real-time infrastructure monitoring using Prometheus and Grafana on AWS EC2.
The monitoring system collects CPU, memory, and disk usage metrics from multiple EC2 servers using Node Exporter. Prometheus collects these metrics and Grafana visualizes them through dashboards.

---
#  🏗️Architecture
 ![Architecture Diagram](https://github.com/Sakshigond12/prometheus-grafana-ec2-monitoring/blob/main/architechture%20digram.png?raw=true)

---

# 📂Project Structure

```bash
prometheus-grafana-ec2-monitoring
│
├── README.md
├── prometheus.yml
│
├── architecture
│   └── architecture-diagram.png
│
├── screenshots
      ├── prometheus_targets.png
      ├── node_exporter_metrics.png
      ├── grafana_dashboard.png
      ├── cpu_memory_disk_graphs.pn
      └── alert_rule.png
```      
      
      
# 🛠️Technologies Used

- AWS EC2
- Prometheus
- Grafana
- Node Exporter
- Linux (Ubuntu)

---

# ⚙ Setup Steps

### 1 Launch 3 EC2 Instance 

Create Ubuntu EC2 instance and connect using SSH
```bash
ssh -i key.pem ubuntu@your-ec2-ip
```



 1 monitoring instance - install Prometheus & grafana

 2 app-server 1 instance - install node_exporter

 3 app-server 2 instance - install node_exporter
---

---
### 2 Security Group Ports
     http - 80
     prometheus - 9090
     grafana - 3000
     node_exporter - 9100
---    

### 3 Install Prometheus


Step 1 Update the System
```bash
sudo apt update
```

```bash
    sudo useradd --no-create-home --shell /bin/false prometheus
    sudo mkdir /etc/prometheus
    sudo mkdir /var/lib/prometheus
    sudo chown prometheus:prometheus /var/lib/prometheus
    cd /tmp/ 
    wget https://github.com/prometheus/prometheus/releases/download/v2.53.1/prometheus-2.53.1.linux-amd64.tar.gz
    tar -xvf prometheus-2.53.1.linux-amd64.tar.gz
    cd prometheus-2.53.1.linux-amd64
    sudo mv console* /etc/prometheus
    sudo mv prometheus.yml /etc/prometheus
    sudo chown -R prometheus:prometheus /etc/prometheus
    sudo mv prometheus /usr/local/bin/
    sudo chown prometheus:prometheus /usr/local/bin/prometheus
``` 
Edit configuration files:
```bash 
    sudo nano /etc/prometheus/prometheus.yml
    - job_name: "node_exporter"
  static_configs:
    - targets: ["SERVER_IP:9100"]
```

```bash  
    sudo nano /etc/systemd/system/prometheus.service
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
```

```bash
    sudo systemctl daemon-reload
    sudo systemctl start prometheus
    sudo systemctl enable prometheus
    sudo systemctl status Prometheus
```
---
Add port 9090 to sg and access through browser

Start Prometheus
./prometheus --config.file=prometheus.yml

Open
http://EC2-IP:9090

---
### 4 Install Grafana
```bash
sudo apt update
```
```bash
https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/
```
```bash
   sudo service prometheus status
   sudo service prometheus start
   sudo service prometheus status
   sudo apt-get install -y apt-transport-https wget
   sudo mkdir -p /etc/apt/keyrings/
   wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
   echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
   sudo apt-get update
   sudo apt-get install grafana -y
   sudo systemctl daemon-reload
   sudo systemctl start grafana-server
   sudo systemctl status grafana-server
```
----

Open
http://EC2-IP:3000

Login
username: admin  
password: admin

---

### 5 Install Node Exporter

```bash
 cd /tmp
 wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
  sudo tar xvfz node_exporter-1.8.1.linux-amd64.tar.gz
 sudo mv node_exporter-1.8.1.linux-amd64/node_exporter /usr/local/bin/
 sudo useradd -rs /bin/false node_exporter
```
Edit configuration file:

```bash
 sudo nano /etc/systemd/system/node_exporter.service
 
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
```
 
```bash
    sudo systemctl daemon-reload
    sudo systemctl start node_exporter
    sudo systemctl enable node_exporter
    sudo systemctl status node_exporter
 ```
---
# 📊Monitoring Metrics

This project monitors

- CPU Usage
- Memory Usage
- Disk Usage
- Network Traffic

---

# 🚨Alerting

Configure alert rules :

- CPU Usage greater than 70%
- High Memory Usage
- Disk usage threshold exceeded
---

#  📸 Project output

### 🔎 Prometheus Targets
![Prometheus Targets](https://github.com/Sakshigond12/prometheus-grafana-ec2-monitoring/blob/b29fef877f1de7e228239f1e9b7f9679184499b7/screenshort/prometheus_targets.png.png)

### 📊 Node Exporter Metrics
![Node Exporter Metrics](https://github.com/Sakshigond12/prometheus-grafana-ec2-monitoring/blob/b29fef877f1de7e228239f1e9b7f9679184499b7/screenshort/node_exporter_metrics.png.png)

### 📈 Grafana Dashboard
![Grafana Dashboard](https://github.com/Sakshigond12/prometheus-grafana-ec2-monitoring/blob/b29fef877f1de7e228239f1e9b7f9679184499b7/screenshort/grafana_dashboard.png.png)

### 💻 CPU, Memory and Disk Graphs
![CPU Memory Disk](https://github.com/Sakshigond12/prometheus-grafana-ec2-monitoring/blob/b29fef877f1de7e228239f1e9b7f9679184499b7/screenshort/cpu_memory_disk_graphs.png.png)

### 🚨 Alert Rule Configuration
![Alert Rule](https://github.com/Sakshigond12/prometheus-grafana-ec2-monitoring/blob/b29fef877f1de7e228239f1e9b7f9679184499b7/screenshort/alert_rule.png.png)


---

# 📚 Conclusion

This project demonstrates how to build a real-time infrastructure monitoring system using Prometheus and Grafana on AWS EC2. It helps organizations detect issues early, improve system reliability, and reduce downtime.

---
## 👩‍💻 Author

**Sakshi Gond**

- AWS & DevOps Enthusiast
- Interested in Cloud, Monitoring, and Automation

🔗 GitHub: https://github.com/Sakshigond12








