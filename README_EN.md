<p align="center">
  <img src="picture/LOGO.png" alt="TgtoDrive cloud-drive file management" width="160">
</p>

<h1 align="center">TgtoDrive</h1>

<p align="center">
  <a href="https://hub.docker.com/r/walkingd/tgto123">
    <img src="https://img.shields.io/docker/pulls/walkingd/tgto123?style=for-the-badge&logo=docker&label=Docker%20Pulls" alt="Docker Pulls">
  </a>
  <a href="https://hub.docker.com/r/walkingd/tgto123">
    <img src="https://img.shields.io/badge/Docker%20Image-walkingd%2Ftgto123-2496ED?style=for-the-badge&logo=docker" alt="Docker Image">
  </a>
  <img src="https://img.shields.io/badge/Version-8.6.4-6C63FF?style=for-the-badge" alt="Version">
  <img src="https://img.shields.io/badge/Arch-amd64%20%7C%20arm64-111827?style=for-the-badge" alt="Architecture">
</p>

<p align="center">
  <a href="README.md">中文</a> | English
</p>

<p align="center">
  <strong><span style="font-size: 1.25em;">Telegram Community: <a href="https://telegram.me/TgtoDriveChat">https://telegram.me/TgtoDriveChat</a></span></strong>
</p>

<p align="center">
  <strong>A cloud-drive file management tool with share-link transfers, film/TV and music organization within your drives, full and incremental STRM generation, and reverse-proxy / 302 direct-link playback for Emby, Feiniu Video and Feiniu Music.</strong>
</p>

<p align="center">
  <strong>This software does not provide any movies, music or other content resources. Files, cloud-drive accounts and share links must be supplied by the user. Only manage content you own or are authorized to use.</strong>
</p>

<p align="center">
  <strong>This software is completely free and will remain free. If this project helps you, please click the ⭐ Star button in the upper-right corner to support it.</strong>
</p>

---

## Key Features

TgtoDrive helps you manage existing files in your personal cloud drives, with tools for transfers, organization and media-library maintenance.

| Feature | Description |
| --- | --- |
| Cloud-drive transfers | Save files from your share links to selected folders, with support for cross-drive import, local-file fast import and file-list import. |
| Film/TV organization | Use TMDB information to identify, classify, rename and archive existing media files, with rules for handling duplicates. |
| Music organization | Use MusicBrainz information to organize existing music by region, artist, album and track, with consistent folders and filenames. |
| STRM generation | Generate complete STRM libraries or update them after organization, sync subtitles and artwork, and clean up invalid entries. |
| Reverse proxy and 302 playback | Play existing cloud media through direct links in Emby, Feiniu Video and Feiniu Music. |
| File and library management | Maintain files and media libraries with cleanup tools, an Emby dashboard, poster refresh, task notifications, logs and organization history. |

## Supported Cloud Drives

| Cloud drive | Main capabilities |
| --- | --- |
| 115 | Transfers, film/TV organization, music organization, STRM generation and file cleanup |
| 123 | Transfers, file import, film/TV organization, music organization and STRM generation |
| Guangya | Transfers, film/TV organization, music organization and STRM generation |
| Tianyi | Transfers and file cleanup |

## Screenshots

| Emby Dashboard | Emby and Feiniu Reverse Proxy |
| --- | --- |
| ![Emby Dashboard](picture/Emby看板.png) | ![Emby and Feiniu Reverse Proxy](picture/Emby反代.png) |

| 115 Film Organizer | 123 Film Organizer |
| --- | --- |
| ![115 Film Organizer](picture/115网盘-网盘整理功能.png) | ![123 Film Organizer](picture/123云盘-网盘整理功能.png) |

| 115 STRM Generation | 123 STRM Generation |
| --- | --- |
| ![115 STRM Generation](picture/115网盘-STRM生成.png) | ![123 STRM Generation](picture/123云盘-STRM生成.png) |

## Quick Start

### 1. Requirements

- Docker 20.10+.
- Docker Compose 2.x.
- A NAS or Linux server that can run continuously.
- Telegram API connectivity if you enable Telegram task notifications.

### 2. Create a deployment folder

```bash
mkdir -p tgtodrive
cd tgtodrive
mkdir -p db strm
```

### 3. Create `docker-compose.yml`

```yaml
version: '3'

services:
  tgtodrive-service:
    image: walkingd/tgto123:latest
    container_name: TgtoDrive
    network_mode: host  # Simplifies port access and direct-link playback
    environment:
      # Basic settings
      - TZ=Asia/Shanghai
      # Required: Web console login credentials
      - ENV_WEB_PASSPORT=admin
      - ENV_WEB_PASSWORD=password
    volumes:
      # Persist databases and logs at /app/db inside the container
      - ./db:/app/db
      # Replace the host path with your STRM folder, keeping /app/strm as the container path
      - /vol1/1000/Emby/strm:/app/strm
      # Optional: replace the host path with your personal files folder for local fast import
      - /path/to/local/files:/app/upload

    restart: always
```

### 4. Start the service

```bash
docker-compose pull  # Pull the latest image
docker-compose up -d # Start in the background
```

Open the Web console:

```text
http://YOUR_SERVER_IP:12366
```

Log in with `ENV_WEB_PASSPORT` and `ENV_WEB_PASSWORD` from your compose file.

## Getting Started

1. Log in to the Web console and add the cloud-drive accounts you want to use.
2. Configure the proxy, TMDB key and task notifications in Global Settings as needed.
3. Use the transfer pages to save your files, or choose source folders, library folders and organization rules for files already in your drives.
4. To connect a media library, select cloud-drive folders and set the playback address in STRM Generation.
5. For 302 playback, select a service in **Emby and Feiniu Reverse Proxy** and enter its upstream address and listen port. Emby requires an API key; Feiniu Music requires a mapping between local music folders and cloud-drive folders. Connect clients through the proxy port, and map that port when not using host networking.
6. Save your settings and check logs and task records for progress.

## Usage Notes

- Keep organizer source and target folders separate; they must not be identical or nested. Keep music folders outside the film organizer's scan area.
- The music source folder is a staging area: recognized audio is organized, unrecognized audio stays in place, and non-music files are moved to the cloud-drive recycle bin. Music organization itself does not generate STRM files.
- Feiniu Music plays original cloud files. Local music folders and cloud-drive folders must contain matching files; NAS transcoding is not supported.
- If a transfer or playback fails, check the cloud-drive login, folder settings, playback address and port access, then inspect the logs.

## Common Commands

Check status:

```bash
docker compose ps
```

Follow logs:

```bash
docker compose logs -f
```

Update:

```bash
docker compose pull
docker compose up -d
```

Stop:

```bash
docker compose down
```

Recommended backup items:

- `db/`: configuration, history, logs and task state.
- `strm/`: generated STRM media library.

## Scope of Use

This project is for personal cloud-drive file management and does not provide movies, music or other content resources. Only transfer, organize and play content you own or are authorized to use, and follow the relevant service terms.

---

<p align="center">
  <strong>If TgtoDrive helps you, please leave a Star or join the community to share your feedback.</strong>
</p>
