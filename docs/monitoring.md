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

## Подключение к Grafana

1. Открыть Grafana: `http://твой-ip:3000`
2. **Configuration → Data Sources → Add data source**
3. Выбрать **Loki**, URL: `http://localhost:3100`
4. **Save & Test**

## Дашборды

Импортируй готовый дашборд из [`dashboards/user-activity.json`](../dashboards/user-activity.json)
