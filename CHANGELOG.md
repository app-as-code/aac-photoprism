# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Changed

- Upstream image tag is defined in `service.yml`; update it to a current PhotoPrism release.

## [0.0.1]

### Added

- Initial App-as-Code service definition for PhotoPrism with a MariaDB dependency.
- Web interface on port 2342 with Traefik routing and automatic DNS.
- GPU reservation for the PhotoPrism container.
- README with configuration reference and project icon.
