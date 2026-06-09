# 🌐 Network Observability & Traffic Analytics with Akvorado

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20Docker-lightgrey)
![Vendor](https://img.shields.io/badge/vendor-Juniper%20Networks-green)

This project implements a robust network observability and traffic analytics platform using **[Akvorado](https://github.com/akvorado/akvorado)** tailored for **Juniper Infrastructure**. It collects, processes, and visualizes NetFlow/IPFIX (J-Flow) data from Juniper network devices to provide real-time visibility into network traffic patterns, bandwidth usage, and security insights.

The system is designed for enterprise networks, ISPs, and SOC environments where traffic visibility and anomaly detection are critical.

## 🎯 Objectives
- **Data Collection:** Collect NetFlow v9 / IPFIX (J-Flow) data from Juniper network devices.
- **Real-Time Visibility:** Provide deep, real-time visibility into network traffic streams.
- **Traffic Analytics:** Analyze top talkers, ASNs, geolocations (countries), and specific protocols.
- **Network Diagnostics:** Improve troubleshooting and decrease Mean Time To Resolution (MTTR).
- **Cybersecurity:** Support security monitoring, DDoS visibility, and anomaly detection.
- **Capacity Planning:** Enable proactive capacity planning through historical traffic analytics.

## 🧰 Tech Stack
- **Ingestion & UI:** Akvorado
- **OS:** Linux (Ubuntu 22.04 / 24.04 recommended)
- **Containerization:** Docker / Docker Compose
- **Protocols:** NetFlow v9 / IPFIX (J-Flow)
- **Network Hardware:** Juniper Networks (MX, EX, QFX, SRX series)
- **Database Backend:** ClickHouse (via Akvorado) & Kafka

## 🏗️ Architecture
The system follows a high-performance flow-based observability pipeline:

1. **Exporters (Juniper Routers/Switches):** Devices sample traffic and export IPFIX/J-Flow packets to the Akvorado server.
2. **Ingester (Akvorado Orchestrator):** Receives UDP flows, decodes them, and enriches the data (GeoIP, ASN, SNMP interface names).
3. **Message Broker (Kafka):** Buffers incoming flow data to handle high-volume traffic spikes.
4. **Data Warehouse (ClickHouse):** Stores the enriched flow records for blazing-fast analytical queries.
5. **Visualization (Akvorado Console):** Provides a web-based UI/dashboard for network engineers to query and visualize the data.

---

## 🚀 Installation & Setup

### 1. System Requirements
- **OS:** Ubuntu 22.04 / 24.04 (recommended)
- **RAM:** Minimum 4GB (8GB+ recommended for production)
- **CPU:** 2+ cores
- **Storage:** 150GB+ HDD/SSD (Fast SSD recommended for ClickHouse)
- **Network:** Open UDP ports for NetFlow/IPFIX (e.g., `UDP/2055`)

### 2. Install Docker & Docker Compose
*Note: This utilizes the official Docker repository for the latest version.*

```bash
# Update system and install dependencies
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl gnupg

# Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Setup the repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine and Docker Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Enable and start Docker
sudo systemctl enable docker
sudo systemctl start docker
