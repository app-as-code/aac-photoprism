<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="PhotoPrism logo" width="128">
  </picture>
</p>

<h1 align="center">aac-photoprism</h1>

<p align="center">Self-hosted deployment definition for PhotoPrism, an AI-assisted photo management application.</p>

<p align="center">
  <a href="https://github.com/photoprism/photoprism"><img alt="Upstream" src="https://img.shields.io/badge/upstream-photoprism%2Fphotoprism-blue?logo=github"></a>
  <a href="https://hub.docker.com/r/photoprism/photoprism"><img alt="Image" src="https://img.shields.io/badge/image-photoprism%2Fphotoprism%3A20220121-2496ED?logo=docker&logoColor=white"></a>
  <a href="https://github.com/photoprism/photoprism/blob/develop/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-AGPL--3.0-green"></a>
</p>

## About

PhotoPrism is a self-hosted photo management application that indexes your photo library
and makes it searchable by date, location, people and content. It uses machine learning to
recognize faces, labels and scenes, and offers a web interface with a map view, albums and
sharing. Originals are read in place from a mounted folder, so the photo library stays under
your control.

Key features (as provided by upstream):

- Automatic indexing of large photo libraries with search by date, place and label
- Face recognition and AI-based labeling of photos
- Map view based on GPS metadata
- Albums and sharing with other users
- Support for RAW and video formats through FFmpeg
- Import folder for adding new photos to the library

## Upstream

- Project: <https://github.com/photoprism/photoprism>
- Website: <https://photoprism.app>
- Documentation: <https://docs.photoprism.app>
- Container image: `photoprism/photoprism:20220121` (Docker Hub)
- Database: `mariadb:12.3.1-ubi10-rc` (deployed as `aac-photoprism-db`)
- License: AGPL-3.0 (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. The
service is configured to reserve an NVIDIA GPU, so the host needs the NVIDIA container
runtime. For a manual run, the external networks used for routing must exist first:

```bash
docker network create services-secured
docker network create services-exposed

# Provide the values listed under "Configuration" in .env / stack.env
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or create the `.env` and `stack.env` files yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `PHOTOPRISM_ADMIN_USER` | `admin` | Name of the initial administrator account |
| `PHOTOPRISM_ADMIN_PASSWORD` | `CHANGE_ME` (secret) | Password of the initial administrator account |
| `PHOTOPRISM_AUTH_MODE` | `password` | Authentication mode |
| `PHOTOPRISM_SITE_URL` | `CHANGE_ME` | Public URL of the PhotoPrism instance |
| `PHOTOPRISM_DEFAULT_TLS` | `true` | Serve the built-in TLS endpoint |
| `PHOTOPRISM_DISABLE_TLS` | `false` | Disable TLS in the application |
| `PHOTOPRISM_INIT` | `https gpu tensorflow` | Initialization flags for the container |
| `PHOTOPRISM_EXPERIMENTAL` | `true` | Enable experimental features |
| `PHOTOPRISM_DETECT_NSFW` | `true` | Detect sensitive content in photos |
| `PHOTOPRISM_FFMPEG_ENCODER` | `nvidia` | FFmpeg encoder used for video transcoding |
| `PHOTOPRISM_ORIGINALS_LIMIT` | `10000` | Maximum size of originals that are processed (MB) |
| `PHOTOPRISM_READONLY` | `false` | Keep the originals folder read-only |
| `PHOTOPRISM_HTTP_COMPRESSION` | `gzip` | HTTP compression method |
| `PHOTOPRISM_LOG_LEVEL` | `info` | Application log level |
| `PHOTOPRISM_UMASK` | `0000` | File creation mask |
| `PHOTOPRISM_UID` / `PHOTOPRISM_GID` | `1000` | User and group ID the application runs as |
| `PUID` / `PGID` | `1000` | User and group ID of the container |
| `TZ` | `Europe/Berlin` | Time zone |
| `PHOTOPRISM_DATABASE_DRIVER` | `mariadb` | Database driver |
| `PHOTOPRISM_DATABASE_SERVER` | `aac-photoprism-db:3306` | Database server (dependency service) |
| `PHOTOPRISM_DATABASE_NAME` | `photoprism` | Database name |
| `PHOTOPRISM_DATABASE_USER` | `photoprism` | Database user |
| `PHOTOPRISM_DATABASE_PASSWORD` | `CHANGE_ME` (secret) | Database password |
| `MARIADB_ROOT_PASSWORD` | `CHANGE_ME` (secret) | Root password of the MariaDB dependency |
| `MARIADB_DATABASE` | `photoprism` | Database created by the MariaDB dependency |
| `MARIADB_USER` | `photoprism` | Database user created by the MariaDB dependency |
| `MARIADB_PASSWORD` | `CHANGE_ME` (secret) | Password of the MariaDB user |
| `HOMEPAGE_WIDGET_PASSWORD` | `CHANGE_ME` (secret) | Credential for the Homepage dashboard widget |

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `2342` | TCP | Web interface (`web`) |

Volumes:

| Mount | Description |
|---|---|
| `storage` -> `/photoprism/storage` | Thumbnails, sidecar files and database backups |
| `originals` -> `/photoprism/originals` | Photo library (bind mount; host path is set per deployment) |
| `import_folder` -> `/photoprism/import` | Import folder for new photos (bind mount; host path is set per deployment) |
| `db` -> `/var/lib/mysql` | MariaDB data (database dependency) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-photoprism>.

## Credits and license

PhotoPrism is developed by the PhotoPrism project and licensed under AGPL-3.0. This
repository only contains the deployment definition for it and is not a fork of the
application. The logo is provided by the selfh.st icon collection; see
[`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for icon sources and licenses.
