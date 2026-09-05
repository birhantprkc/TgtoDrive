<p align="center">
  <img src="picture/LOGO.png" alt="TgtoDrive 网盘文件管理工具" width="160">
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
  中文 | <a href="README_EN.md">English</a>
</p>

<p align="center">
  <strong><span style="font-size: 1.25em;">Telegram 交流群：<a href="https://telegram.me/TgtoDriveChat">https://telegram.me/TgtoDriveChat</a></span></strong>
</p>

<p align="center">
  <strong>网盘文件管理增强工具：提供网盘转存、盘内影视与音乐整理、STRM 全量与增量生成，以及 Emby / 飞牛影视 / 飞牛音乐反向代理与 302 直链播放。</strong>
</p>

<p align="center">
  <strong>本软件不提供任何影视、音乐或其他资源。待管理文件、网盘账号与分享链接均由用户自行提供，请仅处理本人拥有或已获得合法授权的内容。</strong>
</p>

<p align="center">
  <strong>本软件完全免费，后续也不会收费，如果这个项目对你有帮助，请点击右上角 ⭐ Star 支持一下！</strong>
</p>

---

## 主要功能

TgtoDrive 帮助用户管理个人网盘中的已有文件，让转存、整理和媒体库维护更方便。

| 功能 | 说明 |
| --- | --- |
| 网盘转存 | 将用户提供的分享链接中的文件转存到指定目录，支持跨盘文件导入、本地文件秒传和文件清单导入。 |
| 盘内影视整理 | 根据 TMDB 信息识别影视文件，自动分类、重命名、归档，并按规则处理重复文件。 |
| 盘内音乐整理 | 根据 MusicBrainz 信息识别音乐，按地区、艺术家、专辑和曲目整理，统一目录与文件命名。 |
| 网盘 STRM 生成 | 支持全量生成和整理后增量更新，可同步字幕、封面等配套文件，并清理失效项。 |
| 反代与 302 播放 | 支持 Emby、飞牛影视和飞牛音乐，通过网盘直链播放已有媒体文件。 |
| 文件与媒体库管理 | 提供文件清理、Emby 看板、海报维护、任务通知、运行日志和整理记录等辅助功能。 |

## 支持的网盘

| 网盘 | 主要能力 |
| --- | --- |
| 115 网盘 | 转存、影视整理、音乐整理、STRM 生成与文件清理 |
| 123 云盘 | 转存、文件导入、影视整理、音乐整理与 STRM 生成 |
| 光鸭云盘 | 转存、影视整理、音乐整理与 STRM 生成 |
| 天翼云盘 | 转存与文件清理 |

## 界面预览

| Emby 看板 | Emby 与飞牛反代 |
| --- | --- |
| ![Emby 看板](picture/Emby看板.png) | ![Emby 与飞牛反代](picture/Emby反代.png) |

| 115 影视整理 | 123 影视整理 |
| --- | --- |
| ![115 影视整理](picture/115网盘-网盘整理功能.png) | ![123 影视整理](picture/123云盘-网盘整理功能.png) |

| 115 STRM 生成 | 123 STRM 生成 |
| --- | --- |
| ![115 STRM 生成](picture/115网盘-STRM生成.png) | ![123 STRM 生成](picture/123云盘-STRM生成.png) |

## 快速部署

### 1. 准备环境

- Docker 20.10+。
- Docker Compose 2.x。
- 一台可长期运行的 NAS / Linux 服务器。
- 如需使用 Telegram 任务通知，请确保容器可以访问 Telegram API。

### 2. 创建部署目录

```bash
mkdir -p tgtodrive
cd tgtodrive
mkdir -p db strm
```

### 3. 编写 `docker-compose.yml`

```yaml
version: '3'

services:
  tgtodrive-service:
    image: walkingd/tgto123:latest
    container_name: TgtoDrive
    network_mode: host  # 推荐 host 模式以简化端口映射和直链访问
    environment:
      # --- 基础配置 ---
      - TZ=Asia/Shanghai
      # 必填：WEB管理页面的登录账号密码
      - ENV_WEB_PASSPORT=admin
      - ENV_WEB_PASSWORD=password
    volumes:
      # 数据库与日志持久化，右侧固定为/app/db
      - ./db:/app/db
      # STRM输出目录：用于保存 /app/strm 下生成内容,/vol1/1000/Emby/strm 改成你的目录，右侧固定为/app/strm
      - /vol1/1000/Emby/strm:/app/strm
      # [可选] 本地文件秒传目录：左侧改为 NAS 上的个人文件目录，不需要可去掉
      - /path/to/local/files:/app/upload

    restart: always
```

### 4. 启动服务

```bash
docker-compose pull  # 拉取最新镜像
docker-compose up -d # 后台启动
```

访问 Web 管理台：

```text
http://你的服务器IP:12366
```

首次登录使用 `docker-compose.yml` 中配置的 `ENV_WEB_PASSPORT` 和 `ENV_WEB_PASSWORD`。

## 开始使用

1. 登录 Web 管理台，添加需要使用的网盘账号。
2. 在「全局设置」中按需配置代理、TMDB Key 和任务通知。
3. 使用转存页面保存自己的文件，或为盘内已有文件设置影视、音乐整理目录与规则。
4. 如需接入媒体库，在「STRM 生成」中选择网盘目录并设置播放地址。
5. 如需 302 播放，在「Emby与飞牛反代」中选择服务类型，填写上游地址与监听端口；Emby 需填写 API Key，飞牛音乐需配置本地音乐目录与网盘目录的对应关系。客户端通过反代端口访问服务，非 host 网络需映射该端口。
6. 保存配置后，可在日志和任务记录中查看运行情况。

## 使用提示

- 整理源目录与目标目录应分开设置，不能相同或互相嵌套；音乐目录也应避开影视源目录的扫描范围。
- 音乐源目录是待整理区：已识别的音频会归档，未识别的音频保留原处，非音乐文件会移入网盘回收站。音乐整理本身不生成 STRM。
- 飞牛音乐播放网盘原文件；本地音乐目录与网盘目录中的文件应保持对应，不支持 NAS 转码播放。
- 无法转存或播放时，请检查网盘登录状态、目录配置及播放地址和端口是否可访问，并查看运行日志。

## 常用命令

查看运行状态：

```bash
docker compose ps
```

查看日志：

```bash
docker compose logs -f
```

更新镜像：

```bash
docker compose pull
docker compose up -d
```

停止服务：

```bash
docker compose down
```

备份时建议至少保留：

- `db/`：配置、历史、日志、任务状态。
- `strm/`：生成的 STRM 媒体库。

## 使用范围

本项目用于个人网盘文件管理，不提供任何影视、音乐或其他资源。请仅转存、整理和播放本人拥有或已获授权的内容，并遵守相关服务条款。

---

<p align="center">
  <strong>如果 TgtoDrive 对你有帮助，欢迎 Star 支持，或加入交流群反馈使用体验。</strong>
</p>
