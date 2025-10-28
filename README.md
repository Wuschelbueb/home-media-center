# Home Media Center

Minimal Docker setup for a self-hosted media stack:
- Plex (media server)
- Kometa (metadata/collections management)

## Structure
- `compose/docker-compose.yaml` – services definition
- `plex/config/` – Plex app data (mounted to container `/config`)
- `kometa/config/` – Kometa config, overlays, and logs (mounted to container `/config`)

## Prerequisites
- Docker and Docker Compose plugin installed
- Host has access to:
  - `/dev/dri` for Intel/VAAPI hardware transcoding (optional)
  - Media folders mounted at:
    - `/mnt/synology-nas-media-files/english-movies`
    - `/mnt/synology-nas-media-files/german-movies`
    - `/mnt/synology-nas-media-files/tv-shows`
  - Transcode scratch disk at `/mnt/transcode`
- `.env` file at repo root (used by both services)

Example `.env` (adjust to your system):
```
PUID=1000
PGID=1000
TZ=US/Eastern
# Add other variables as needed
```

## Quick start
From the `compose/` folder:

```sh
# start (detached)
docker compose up -d

# stop and remove containers (keeps data)
docker compose down
```

## Common tasks
- Update images and restart:
  ```sh
  docker compose pull && docker compose up -d
  ```
- Check logs:
  ```sh
  # Plex
  docker compose logs -f plex

  # Kometa
  docker compose logs -f kometa
  ```

## Notes
- Network mode is `host` for both services; no ports are published via Compose. Access Plex at the host's IP on its default port (e.g., http://HOST:32400/web).
- Hardware transcoding requires proper drivers and permissions. On many systems, adding your user to the `video` (and sometimes `render`) group helps.
- All persistent app data lives under this repository (`plex/config`, `kometa/config`). Media and transcode paths are external mounts.
