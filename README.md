# Home Media Center

Minimal Docker setup for a self-hosted media stack:

- **Plex**: Media server
- **Kometa**: Metadata and collections management
- **Portainer**: Container management UI
- **Watchtower**: Automatic container updates
- **Monitoring Stack**: Prometheus & Grafana for system and network insights

## Structure

- `compose/docker-compose.yaml` – Services definition
- `compose/prometheus/` – Prometheus configuration
- `plex/config/` – Plex app data
- `kometa/config/` – Kometa config

## Prerequisites

- Docker and Docker Compose plugin installed
- Host has access to media folders and transcoding devices
- `.env` file at repo root (see below)

## Configuration & Setup

### 1. Environment Variables

Create a `.env` file with your secrets. See `.env.example`.

### 2. Monitoring Setup (Grafana/Pi-hole)

For detailed instructions on setting up Grafana, connecting your Pi-hole, and monitoring your Raspberry Pi, see [MONITORING_SETUP.md](MONITORING_SETUP.md).

## Quick start

From the repo root:

```sh
# start (detached)
docker compose -f compose/docker-compose.yaml --env-file .env up -d

# stop
docker compose -f compose/docker-compose.yaml down
```

## Services Overview

- **Plex**: `http://HOST:32400/web`
- **Portainer**: `http://HOST:9000` (admin/admin_password)
- **Grafana**: `http://HOST:3000` (admin/admin_password)
- **Prometheus**: `http://HOST:9090`
- **Watchtower**: Background service (logs check updates)
