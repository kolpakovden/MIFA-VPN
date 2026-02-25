# 🛡️ Pet Project: VLESS + Telegram Monitoring + Grafana

![Version](https://img.shields.io/badge/version-3.0-blue)
![Xray](https://img.shields.io/badge/Xray-25.8.3-green)
![License](https://img.shields.io/badge/license-MIT-orange)
![Telegram](https://img.shields.io/badge/Telegram-bot-26A5E4)
![Grafana](https://img.shields.io/badge/Grafana-dashboard-F46800)

> Полный мониторинг VPN-сервера: от подключений до красивых графиков и управления через Telegram.

---

## О проекте

**Хочешь понять, зачем это всё и почему я это делаю?**  
> Читай [**MANIFEST.md**](MANIFEST.md) — там вся история, эмоции и мотивация.

### Что внутри

| Компонент | Описание |
|-----------|----------|
|  **16 пользователей** |
|  **5 портов** | 443, 8443, 2053, 2083, 50273 |
|  **11 доменов маскировки** | techadvisor, lemonde, github и др. |
|  **Telegram-бот (уведомления)** | О новых подключениях с геолокацией |
|  **Telegram-бот (управление)** | Добавление/удаление пользователей через команды |
|  **Геолокация** | Город, регион, страна, провайдер |
|  **XrayView** | Быстрый просмотр кто куда ходит |
|  **Prometheus + Grafana** | Графики трафика и системных метрик |

---

## Архитектура системы

```
                            ИНТЕРНЕТ
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │      XRAY SERVER       │
                    │    VLESS + Reality     │
                    │  Порты: 443,8443,...   │
                    └───────────┬────────────┘
                                │
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
    ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
    │  Логирование    │ │  Конфигурация   │ │   Метрики       │
    │  access.log     │ │  config.json    │ │  API stats      │
    └────────┬────────┘ └────────┬────────┘ └────────┬────────┘
             │                   │                   │
             ▼                   ▼                   ▼
    ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
    │    Скрипт       │ │     Telegram    │ │    Prometheus   │
    │  уведомлений    │ │     бот         │ │    Exporter     │
    │  (new IPs)      │ │  (управление)   │ │   port 9550     │
    └────────┬────────┘ └────────┬────────┘ └────────┬────────┘
             │                   │                   │
             ▼                   ▼                   ▼
      ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
      │     TG      │     │     TG      │     │   Grafana   │
      │  Уведомл.   │     │  Команды    │     │   Дашборд   │
      └─────────────┘     └─────────────┘     └─────────────┘
```

**Всё работает параллельно.**

---

## Быстрый старт

### 1. Клонируем репозиторий

```bash
git clone https://github.com/твой-логин/pet_vless_telegram.git
cd pet_vless_telegram
```

### 2. Настраиваем Telegram-бота для уведомлений

1. Напиши [@BotFather](https://t.me/botfather) → `/newbot` → получи токен
2. Напиши [@userinfobot](https://t.me/userinfobot) → получи Chat ID
3. Вставь их в скрипт `scripts/check_users.sh`

### 3. Запускаем мониторинг подключений

```bash
chmod +x scripts/check_users.sh
./scripts/check_users.sh
```

### 4. Добавляем в cron

```bash
crontab -e
* * * * * /полный/путь/к/scripts/check_users.sh
```

---

## Telegram-бот для управления сервером

### Установка

```bash
# Переходим в папку проекта
cd /opt
git clone https://github.com/твой-логин/pet_vless_telegram.git
cd pet_vless_telegram

# Создаём виртуальное окружение
python3 -m venv venv
source venv/bin/activate

# Ставим зависимости
pip install python-telegram-bot python-dotenv

# Создаём файл с токеном
cp .env.example .env
nano .env  # вставляем свои токены
```

### Запуск как сервис

```bash
sudo cp scripts/xray-tg-bot.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now xray-tg-bot
```

### Доступные команды

| Команда | Описание | Пример |
|---------|----------|--------|
| `/add Имя` | Добавить нового пользователя | `/add Наташа` |
| `/list` | Список всех пользователей | `/list` |
| `/del email` | Удалить пользователя | `/del natalya@myserver.com` |
| `/key email [порт]` | Получить ключ | `/key natalya@myserver.com 8443` |
| `/restart` | Перезапустить Xray | `/restart` |
| `/help` | Показать помощь | `/help` |

---

## 📊 Мониторинг через Grafana (логи пользователей)

Для отслеживания **какие сайты посещают пользователи** используется стек **Loki + Promtail**.

### 1. Установка Loki

```bash
# Скачиваем Loki
sudo wget -O /tmp/loki.zip https://github.com/grafana/loki/releases/download/v3.6.7/loki-linux-amd64.zip
sudo apt install unzip -y
cd /tmp
sudo unzip -o loki.zip
sudo mv loki-linux-amd64 /usr/local/bin/loki
sudo chmod +x /usr/local/bin/loki

# Создаём папку для конфигов
sudo mkdir -p /etc/loki

# Конфиг Loki
sudo tee /etc/loki/loki-config.yaml > /dev/null <<EOF
auth_enabled: false
server:
  http_listen_port: 3100
ingester:
  lifecycler:
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1
  chunk_idle_period: 5m
  chunk_retain_period: 30s
schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb-shipper
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h
storage_config:
  boltdb_shipper:
    active_index_directory: /var/lib/loki/index
    cache_location: /var/lib/loki/cache
    cache_ttl: 24h
  filesystem:
    directory: /var/lib/loki/chunks
limits_config:
  allow_structured_metadata: false
EOF

# Создаём папки для данных
sudo mkdir -p /var/lib/loki/{index,chunks,cache}

# Systemd сервис
sudo tee /etc/systemd/system/loki.service > /dev/null <<EOF
[Unit]
Description=Loki Log Aggregator
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/loki -config.file=/etc/loki/loki-config.yaml
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now loki
```

### 2. Установка Promtail (через Docker)

```bash
# Конфиг Promtail
sudo tee /etc/loki/promtail-config.yaml > /dev/null <<EOF
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /var/lib/loki/positions.yaml

clients:
  - url: http://localhost:3100/loki/api/v1/push

scrape_configs:
  - job_name: xray
    static_configs:
      - targets: [localhost]
        labels:
          job: xray
          __path__: /var/log/xray/access.log
    pipeline_stages:
      - regex:
          expression: '^(?P<timestamp>\S+ \S+) from (?P<ip>\S+):\d+ accepted tcp:(?P<domain>[^\s]+):\d+ .+ email: (?P<email>\S+)'
      - labels:
          email:
          domain:
          ip:
EOF

# Запуск Promtail в Docker
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

### 3. Подключение к Grafana

1. Открой Grafana: `http://твой-ip:3000`
2. **Configuration → Data Sources → Add data source**
3. Выбери **Loki**
4. URL: `http://localhost:3100`
5. **Save & Test**

### 4. Полезные запросы (LogQL)

```logql
# Все логи за последние 5 минут
{job="xray"}

# Активность по пользователям (график)
sum by (email) (count_over_time({job="xray"}[5m]))

# Топ-10 доменов за день
topk(10, sum by (domain) (count_over_time({job="xray"}[24h])))

# Логи конкретного пользователя
{email="alena@myserver.com"}

# Поиск по IP
{job="xray"} |= "80.83.235.35"
```

---

## 📥 Готовые дашборды

### Дашборд "Активность пользователей" (JSON)

Сохрани как `dashboards/user-activity.json` и импортируй в Grafana:

<details>
<summary>🔽 Нажми для JSON</summary>

```json
{
  "dashboard": {
    "title": "Активность пользователей",
    "panels": [
      {
        "title": "Активность по пользователям",
        "type": "timeseries",
        "gridPos": {"h": 8, "w": 12, "x": 0, "y": 0},
        "targets": [
          {
            "expr": "sum by (email) (count_over_time({job=\"xray\"}[5m]))",
            "legendFormat": "{{email}}",
            "datasource": "Loki"
          }
        ]
      },
      {
        "title": "Топ доменов",
        "type": "barchart",
        "gridPos": {"h": 8, "w": 12, "x": 12, "y": 0},
        "targets": [
          {
            "expr": "topk(10, sum by (domain) (count_over_time({job=\"xray\"}[24h])))",
            "legendFormat": "{{domain}}",
            "datasource": "Loki"
          }
        ]
      },
      {
        "title": "Логи в реальном времени",
        "type": "logs",
        "gridPos": {"h": 12, "w": 24, "x": 0, "y": 8},
        "targets": [
          {
            "expr": "{job=\"xray\"}",
            "datasource": "Loki"
          }
        ]
      }
    ]
  }
}
```
</details>
---

## Примеры уведомлений

### Новое подключение
```
🔔 Новое подключение к VPN!
📍 IP: 80.83.237.47
🏙️ Город: Irkutsk
🌍 Регион: Irkutsk Oblast
🌎 Страна: Russia
📡 Провайдер: Mobile TeleSystems
🕒 Время: 24.02.2026 19:53:01
```

### Добавление пользователя через бота
```
✅ Пользователь добавлен!

👤 Имя: c001zer0
📧 Email: c001zer0@myserver.com
🆔 UUID: 3f7b8a91-6d4c-4e2f-9a1d-8c5b3e7f2a18

🔑 Ключи:
443: vless://3f7b8a91-6d4c...
8443: vless://3f7b8a91-6d4c...
2053: vless://3f7b8a91-6d4c...
2083: vless://3f7b8a91-6d4c...
50273: vless://3f7b8a91-6d4c...
```

---

## Структура проекта

```
pet_vless_telegram/
├── README.md                                  # Техническая документация (установка, команды)
├── MANIFEST.md                                # Душа проекта — зачем это всё
├── config/
│   ├── README_config.md                       # Описание полей конфига (что и как менять)
│   └── example.config.json                    # Пример конфига (без личных данных)
├── scripts/
│   ├── check_users.sh                         # Скрипт уведомлений о подключениях
│   ├── xray-tg-bot.service                    # Systemd сервис для бота
│   ├── xray-exporter.service                  # Systemd сервис для экспортера
│   └── bot.py                                 # Telegram-бот управления
├── .env.example                               # Пример переменных окружения
└── docs/
    └── commands.md                            # Шпаргалка по командам
```

---

## Полезные команды

| Действие | Команда |
|----------|---------|
| Посмотреть активные IP | `cat /tmp/current_ips.txt` |
| Логи Xray | `tail -f /var/log/xray/access.log` |
| Статус Xray | `sudo systemctl status xray` |
| Статус бота (управление) | `sudo systemctl status xray-tg-bot` |
| Статус экспортера | `sudo systemctl status xray-exporter` |
| Статус Grafana | `sudo systemctl status grafana-server` |
| Статус Prometheus | `sudo systemctl status prometheus` |
| Логи бота | `sudo journalctl -u xray-tg-bot -f` |

---

## 🙏 Благодарности

- [@maxgalzer](https://github.com/maxgalzer) за [xray-traffic-bot](https://github.com/maxgalzer/xray-traffic-bot)
- [@Davoyan](https://github.com/Davoyan) за [xray-access-view](https://github.com/Davoyan/xray-access-view)
- [@anatolykopyl](https://github.com/anatolykopyl) за [xray-exporter](https://github.com/anatolykopyl/xray-exporter)
- Всем, кто держит свободный интернет 

---

## 📄 Лицензия

MIT — делайте что хотите, но лучше делитесь опытом!

---

*Сделано для свободного интернета*
