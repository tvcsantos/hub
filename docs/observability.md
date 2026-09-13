# Observability

This stack provides a lightweight, fully open-source observability setup for a Raspberry Pi:

- Grafana for dashboards, exploration, and alerts
- Grafana Alloy for host metrics, Docker logs, and OTLP ingestion
- VictoriaMetrics single-node for metrics and time-series storage
- VictoriaLogs for log storage and querying

## Start

```bash
cp observability/sample.env observability/.env
$EDITOR observability/.env
docker compose --env-file observability/.env -f observability/docker-compose.yml up -d
```

Grafana is available at `http://<docker-host>:3000`. Its admin credentials come from `.env`. Grafana provisions VictoriaMetrics and VictoriaLogs automatically.

Alloy exposes OTLP on ports `4317` (gRPC) and `4318` (HTTP). VictoriaMetrics and VictoriaLogs are exposed on ports `8428` and `9428` respectively.

## Home Assistant

Use Home Assistant's InfluxDB integration to push state changes directly to VictoriaMetrics. Add this to Home Assistant's `configuration.yaml`, replacing the address with the Raspberry Pi's LAN address:

```yaml
influxdb:
 api_version: 1
 host: 192.168.1.50
 port: 8428
 ssl: false
 database: home_assistant
 username: ""
 password: ""
```

VictoriaMetrics accepts the InfluxDB line protocol on port `8428`. This push path records Home Assistant state changes without requiring the Prometheus integration.

## OpenTelemetry

Point instrumented services at Alloy with:

```text
OTEL_EXPORTER_OTLP_ENDPOINT=http://<docker-host>:4318
```

Alloy forwards OTLP metrics to VictoriaMetrics. Docker container logs are discovered through the read-only Docker socket and sent to VictoriaLogs.

The Compose named volumes preserve data across container upgrades. Back up the `observability_victoriametrics-data`, `observability_victorialogs-data`, and `observability_grafana-data` volumes before rebuilding the host.
