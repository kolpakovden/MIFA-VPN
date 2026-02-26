# Мониторинг через Grafana + Loki

## Установка Loki

```bash
# Скачать бинарник
sudo wget -O /usr/local/bin/loki https://github.com/grafana/loki/releases/download/v3.6.7/loki-linux-amd64
sudo chmod +x /usr/local/bin/loki

# Скопировать конфиги из репозитория
sudo mkdir -p /etc/loki
sudo cp config/loki-config.yaml /etc/loki/loki-config.yaml
sudo cp config/loki.service /etc/systemd/system/loki.service

# Создать папки для данных
sudo mkdir -p /var/lib/loki/{index,chunks,cache}

# Запустить Loki
sudo systemctl daemon-reload
sudo systemctl enable --now loki
```

## Установка Promtail

```bash
# Скопировать конфиг
sudo cp config/promtail-config.yaml /etc/loki/promtail-config.yaml

# Запустить в Docker
docker run -d \
  --name promtail \
  -v /var/log/xray:/var/log/xray:ro \
  -v /etc/loki/promtail-config.yaml:/etc/promtail/config.yaml:ro \
  -v /var/lib/loki/positions.yaml:/var/lib/loki/positions.yaml \
  --network host \
  --restart always \
  grafana/promtail:3.6.7 \
  -config.file=/etc/promtail/config.yaml
```

## Prometheus + Node Exporter

### Установка Node Exporter

```bash
# Скачать и установить
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
tar xvf node_exporter-1.8.2.linux-amd64.tar.gz
sudo mv node_exporter-1.8.2.linux-amd64/node_exporter /usr/local/bin/

# Создать сервис
sudo tee /etc/systemd/system/node_exporter.service > /dev/null <<EOF
[Unit]
Description=Node Exporter
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/node_exporter --web.listen-address=:9101
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now node_exporter
```

### Настройка Prometheus

Добавить в `/etc/prometheus/prometheus.yml`:

```yaml
  - job_name: 'node_custom'
    static_configs:
      - targets: ['localhost:9101']
    scrape_interval: 15s
```

### Готовые запросы для Grafana

- **CPU:** `100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`
- **RAM:** `(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100`
- **Диск:** `(node_filesystem_size_bytes{mountpoint="/"} - node_filesystem_free_bytes{mountpoint="/"}) / node_filesystem_size_bytes{mountpoint="/"} * 100`
- **Сеть (входящий):** `rate(node_network_receive_bytes_total{device="ens3"}[1m])`
- **Сеть (исходящий):** `rate(node_network_transmit_bytes_total{device="ens3"}[1m])`


## Подключение к Grafana

1. Открыть Grafana: `http://твой-ip:3000`
2. **Configuration → Data Sources → Add data source**
3. Выбрать **Loki**, URL: `http://localhost:3100`
4. **Save & Test**

## Дашборды

Импортируй готовый дашборд из [`dashboards/user-activity.json`](../dashboards/user-activity.json)
