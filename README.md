# ☁️ Cloud Infrastructure Monitoring with Prometheus & Grafana on GCP

A centralized monitoring solution built on Google Cloud Platform using **Prometheus**, **Grafana**, and **Node Exporter**. The setup monitors multiple Linux virtual machines and visualizes system metrics such as CPU, memory, disk, and network usage through Grafana dashboards.

## Features

* Custom VPC and subnet architecture
* Multi-VM monitoring
* Prometheus metrics collection
* Grafana dashboards
* Node Exporter integration
* Infrastructure stress testing using stress-ng

## Tech Stack

* Google Cloud Platform (GCP)
* Compute Engine
* VPC Networking
* Ubuntu Linux
* Prometheus
* Grafana
* Node Exporter
* stress-ng

## Architecture

Add architecture diagram here.

```md
![Architecture Diagram](images/architecture.png)
```

---

## Prerequisites

* Google Cloud Project
* Compute Engine API enabled
* Three Ubuntu VMs
* SSH access to all instances

---

## Network Setup

### VPC

Create a custom VPC:

```text
Name: monitoring-vpc
```

### Subnets

```text
subnet-workers : 10.0.1.0/24
subnet-master  : 10.0.2.0/24
```

### Firewall Rules

Allow:

```text
SSH          : TCP 22
Node Exporter: TCP 9100
Prometheus   : TCP 9090
Grafana      : TCP 3000
```

---

## VM Configuration

### Worker Nodes

* worker-vm-1
* worker-vm-2

Configuration:

```text
Machine Type: e2-micro
OS: Ubuntu LTS
Subnet: subnet-workers
```

### Master Node

* master-vm

Configuration:

```text
Machine Type: e2-micro
OS: Ubuntu LTS
Subnet: subnet-master
```

---

# Install Node Exporter (Worker Nodes)

Download Node Exporter:

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
tar xvf node_exporter-1.8.1.linux-amd64.tar.gz
sudo mv node_exporter-1.8.1.linux-amd64/node_exporter /usr/local/bin/
```

Create and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

Verify:

```bash
sudo systemctl status node_exporter
```

---

# Install Prometheus (Master Node)

Create the Prometheus configuration:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'worker-vms'
    static_configs:
      - targets:
          - '10.0.1.3:9100'
          - '10.0.1.4:9100'
```

Start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

Verify:

```bash
sudo systemctl status prometheus
```

Open:

```text
http://<master-external-ip>:9090/targets
```

Both worker nodes should show **UP**.

---

# Install Grafana (Master Node)

Install Grafana:

```bash
sudo apt-get update
sudo apt-get install -y grafana
```

Enable and start:

```bash
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

Verify:

```bash
sudo systemctl status grafana-server
```

Access:

```text
http://<master-external-ip>:3000
```

Default credentials:

```text
Username: admin
Password: admin
```

---

# Configure Grafana

## Add Prometheus Data Source

```text
Connections → Data Sources → Add Data Source
```

Select:

```text
Prometheus
```

URL:

```text
http://localhost:9090
```

Save and test.

---

## Import Dashboard

Import dashboard:

```text
ID: 1860
```

Select the Prometheus data source and click **Import**.

---

# Stress Testing

Install stress-ng:

```bash
sudo apt-get install -y stress-ng
```

Run CPU and memory load:

```bash
nohup stress-ng --cpu 2 --vm 1 --vm-bytes 200M --timeout 0 &
```

Monitor results through Grafana dashboards.

---

# Screenshots

## VPC Configuration

Add screenshot here.

## Firewall Rules

Add screenshot here.

## Prometheus Targets

Add screenshot here.

## Grafana Dashboard

Add screenshot here.

## Stress Test Results

Add screenshot here.

---

# Author

Cloud Infrastructure Monitoring using Prometheus, Grafana, and Node Exporter on Google Cloud Platform.
