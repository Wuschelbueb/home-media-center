# Monitoring Stack Setup

This guide explains how to set up and use the comprehensive monitoring stack included in this project. The stack monitors:

* **Mini PC System Stats** (CPU, RAM, Disk, Network)
* **Docker Containers** (Resource usage per container)
* **Raspberry Pi System Stats** (Remote monitoring via Node Exporter)
* **Pi-hole Statistics** (Ads blocked, DNS queries)

## Architecture

The stack consists of the following Docker containers:

* **Prometheus**: The metric collector and time-series database.
* **Grafana**: The visualization dashboard.
* **Node Exporter (Local)**: Exposes Mini PC system metrics.
* **cAdvisor**: Exposes Docker container metrics.
* **Pi-hole Exporter**: Bridges the connection to your remote Pi-hole.

## Setup Instructions

### 1. Host Configuration

Ensure your `.env` file contains the following secrets for the Pi-hole connection:

```bash
PIHOLE_IP=192.xxx.xxx.xxx  # IP of your Pi-hole
PIHOLE_PASSWORD=your_password  # Web Interface Password or API Token
```

### 2. Raspberry Pi Configuration (Remote Target)

To monitor the Raspberry Pi's system stats in the same dashboard, you must run `node-exporter` on the Pi itself.

**Steps:**

1. SSH into your Raspberry Pi:

    ```bash
    ssh user@192.xxx.xxx.xxx
    ```

2. Install Docker (if not installed):

    ```bash
    curl -fsSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    sudo usermod -aG docker $USER
    # Log out and back in if needed
    ```

3. Run Node Exporter:

    ```bash
    docker run -d \
      --name=node_exporter \
      --net=host \
      --pid=host \
      --restart=unless-stopped \
      prom/node-exporter:latest
    ```

### 3. Launching the Stack

The monitoring services are part of the main `docker-compose.yaml`.

```bash
docker compose up -d
```

### 4. Configuring Grafana

1. **Login**: Open `http://<MINI-PC-IP>:3000`. Default credentials are `admin` / `admin`.
2. **Add Data Source**:
    * Go to **Configuration** -> **Data Sources**.
    * Add **Prometheus**.
    * URL: `http://prometheus:9090`.
    * Click **Save & Test**.
3. **Import Dashboards**:
    * Click the **+** icon -> **Import**.
    * Use the following Grafana IDs to download community dashboards:
        * **System Stats (Node Exporter)**: `1860`
            * *Note: Use the "Job" or "Hostname" filter to switch between Mini PC and Raspberry Pi.*
        * **Pi-hole Stats**: `10176`
        * **Docker Stats**: `14282`

## Troubleshooting

* **Pi-hole Connection Failed**: Check if `PIHOLE_PASSWORD` is correct in `.env`. Verify you can reach the Pi-hole web interface from the Mini PC.
* **No Data from Raspberry Pi**: Ensure `node-exporter` is running on the Pi and port `9100` is open.
