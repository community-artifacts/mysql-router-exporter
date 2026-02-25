# mysql-router-exporter

[![lint](https://github.com/community-artifacts/mysql-router-exporter/actions/workflows/lint.yml/badge.svg)](https://github.com/community-artifacts/mysql-router-exporter/actions/workflows/lint.yml)
[![release](https://github.com/community-artifacts/mysql-router-exporter/actions/workflows/release.yml/badge.svg)](https://github.com/community-artifacts/mysql-router-exporter/actions/workflows/release.yml)

Prometheus exporter for [MySQL Router](https://dev.mysql.com/doc/mysql-router/8.0/en/), exposing metrics via the REST API.

> Supported MySQL Router versions: see [mysqlrouter-go](https://github.com/rluisr/mysqlrouter-go#supported-version)

## Getting Started

### Prerequisites

Enable the REST API on your MySQL Router ([instructions](https://github.com/rluisr/mysqlrouter-go#supported-version)).

### Binary

1. Download the binary from the [releases page](https://github.com/community-artifacts/mysql-router-exporter/releases).
2. Move it to `/usr/local/bin/`.
3. Create a systemd unit:

```ini
[Unit]
Description=mysql-router-exporter
Documentation=https://github.com/community-artifacts/mysql-router-exporter
After=network-online.target

[Service]
Type=simple
Environment="MYSQLROUTER_EXPORTER_URL=https://router.example.com:8443"
Environment="MYSQLROUTER_EXPORTER_USER=user"
Environment="MYSQLROUTER_EXPORTER_PASS=pass"
ExecStart=/usr/local/bin/mysql-router-exporter

[Install]
WantedBy=multi-user.target
```

### Container

```bash
docker pull ghcr.io/community-artifacts/mysql-router-exporter:latest
```

See all available tags on the [packages page](https://github.com/community-artifacts/mysql-router-exporter/pkgs/container/mysql-router-exporter).

## Configuration

Configuration can be provided via environment variables or CLI flags.

### Environment Variables

| Name                          | Required | Description               |
| ----------------------------- | -------- | ------------------------- |
| `MYSQLROUTER_EXPORTER_URL`    | yes      | MySQL Router REST API URL |
| `MYSQLROUTER_EXPORTER_USER`   | no       | Username for REST API     |
| `MYSQLROUTER_EXPORTER_PASS`   | no       | Password for REST API     |
| `MYSQLROUTER_TLS_CACERT_PATH` | no       | TLS CA cert path          |
| `MYSQLROUTER_TLS_CERT_PATH`   | no       | TLS cert path             |
| `MYSQLROUTER_TLS_KEY_PATH`    | no       | TLS key path              |

### CLI Flags

```
      --url=                   MySQL Router REST API URL [$MYSQLROUTER_EXPORTER_URL]
      --user=                  Username for REST API [$MYSQLROUTER_EXPORTER_USER]
      --pass=                  Password for REST API [$MYSQLROUTER_EXPORTER_PASS]
  -p, --listen-port=           Listen port (default: 9152)
      --service-name=          Service name for MySQL Router [$MYSQLROUTER_EXPORTER_SERVICE_NAME]
      --tls-ca-cert-path=      TLS CA cert path [$MYSQLROUTER_TLS_CACERT_PATH]
      --tls-cert-path=         TLS cert path [$MYSQLROUTER_TLS_CERT_PATH]
      --tls-key-path=          TLS key path [$MYSQLROUTER_TLS_KEY_PATH]
  -k, --skip-tls-verify        Skip TLS verification
      --collect-interval=      Collection interval in seconds (default: 2)
  -v, --version                Show version
  -h, --help                   Show help message
```

## Collector Flags

Additional metrics can be enabled with the following flags. These collectors increase CPU usage.

See the [MySQL Router REST API Reference](https://dev.mysql.com/doc/mysql-router/8.0/en/mysql-router-rest-api-reference.html) for details.

| Flag                                                  | Description                          |
| ----------------------------------------------------- | ------------------------------------ |
| `--collect.metadata.status`                           | Collect metadata status metrics      |
| `--collect.route.connections.byte_from_server`        | Collect bytes from server per route  |
| `--collect.route.connections.byte_to_server`          | Collect bytes to server per route    |
| `--collect.route.connections.time_started`             | Collect connection start times       |
| `--collect.route.connections.time_connected_to_server` | Collect server connection times      |
| `--collect.route.connections.time_last_sent_to_server` | Collect last sent to server times    |
| `--collect.route.connections.time_received_from_server`| Collect last received from server times |

## Prometheus

```yaml
scrape_configs:
  - job_name: "mysqlrouter"
    static_configs:
      - targets:
          - mysqlrouter.local:9152
```

## Grafana Dashboard

![Grafana Dashboard](img/grafana.png "Grafana Dashboard")

[Download dashboard](https://grafana.com/grafana/dashboards/10741)
