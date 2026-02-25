# 🛡️ Pet Project: VLESS + Telegram Monitoring + Grafana

![Version](https://img.shields.io/badge/version-2.0-blue)
![Xray](https://img.shields.io/badge/Xray-25.8.3-green)
![License](https://img.shields.io/badge/license-MIT-orange)
![Telegram](https://img.shields.io/badge/Telegram-bot-26A5E4)
![Grafana](https://img.shields.io/badge/Grafana-dashboard-F46800)

> Полный мониторинг VPN-сервера: от подключений до красивых графиков.

---

## 📋 О проекте

Поднял сервер на **VLESS + Reality** для себя и друзей.  
Постепенно дорос до полноценной системы мониторинга:

### ✨ Что внутри

| Компонент | Описание |
|-----------|----------|
| 👥 **14 пользователей** |
| 🌐 **5 портов** | 443, 8443, 2053, 2083, 50273 |
| 🎭 **11 доменов маскировки** | techadvisor, lemonde, github и др. |
| 🤖 **Telegram-бот** | уведомления о новых подключениях |
| 📍 **Геолокация** | город, регион, страна, провайдер |
| 📊 **XrayView** | быстрый просмотр кто куда ходит |
| 📈 **Prometheus + Grafana** | графики трафика и системных метрик |

---

## Быстрый старт

### 1. Клонируем репозиторий

```bash
git clone https://github.com/твой-логин/pet_vless_telegram.git
cd pet_vless_telegram
```

### 2. Настраиваем Telegram-бота

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

## Мониторинг через Grafana

### Установка Prometheus + Grafana

```bash
# Prometheus
sudo apt install -y prometheus prometheus-node-exporter

# Grafana
sudo apt install -y grafana
sudo systemctl enable --now grafana-server
```

### Настройка Xray для сбора метрик

В конфиг Xray (`/usr/local/etc/xray/config.json`) добавь:

```json
"stats": {},
"api": {
    "tag": "api",
    "listen": "127.0.0.1:8080",
    "services": ["StatsService"]
},
"policy": {
    "levels": {
        "0": {
            "statsUserUplink": true,
            "statsUserDownlink": true
        }
    }
}
```

### Установка Xray-экспортера

```bash
sudo wget -O /usr/local/bin/xray-exporter https://github.com/anatolykopyl/xray-exporter/releases/latest/download/xray-exporter_linux_amd64
sudo chmod +x /usr/local/bin/xray-exporter

# Создаем сервис
sudo tee /etc/systemd/system/xray-exporter.service > /dev/null <<EOF
[Unit]
Description=Xray Exporter
After=network.target xray.service

[Service]
Type=simple
User=nobody
Group=nogroup
ExecStart=/usr/local/bin/xray-exporter --xray-endpoint=127.0.0.1:8080 --listen=0.0.0.0:9550
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now xray-exporter
```

### Настройка Prometheus

Добавь в `/etc/prometheus/prometheus.yml`:

```yaml
  - job_name: 'xray'
    static_configs:
      - targets: ['localhost:9550']
    scrape_interval: 15s
```

Перезапусти:

```bash
sudo systemctl restart prometheus
```

---

## Примеры

### Уведомление в Telegram

```
🔔 Новое подключение к VPN!
📍 IP: 80.83.237.47
🏙️ Город: Irkutsk
🌍 Регион: Irkutsk Oblast
🌎 Страна: Russia
📡 Провайдер: Mobile TeleSystems
🕒 Время: 24.02.2026 19:53:01
```

### Графики в Grafana

(скриншоты будут позже или не будут посмотрим=))

---

## Структура проекта

```
pet_vless_telegram/
├── README.md                 # Документация
├── config/
│   ├── config.json           # Личный конфиг (не в git)
│   └── example.config.json   # Пример конфига
├── scripts/
│   └── check_users.sh        # Скрипт мониторинга подключений
└── docs/
    └── commands.md           # Шпаргалка по командам
```

---

## Полезные команды

| Действие | Команда |
|----------|---------|
| Посмотреть активные IP | `cat /tmp/current_ips.txt` |
| Ручной запуск скрипта | `/usr/local/bin/check_users.sh` |
| Логи Xray | `tail -f /var/log/xray/access.log` |
| Статус Xray | `sudo systemctl status xray` |
| Статус Grafana | `sudo systemctl status grafana-server` |
| Статус Prometheus | `sudo systemctl status prometheus` |
| Статус экспортера | `sudo systemctl status xray-exporter` |

---

## Благодарности

- [@maxgalzer](https://github.com/maxgalzer) за [xray-traffic-bot](https://github.com/maxgalzer/xray-traffic-bot)
- [@Davoyan](https://github.com/Davoyan) за [xray-access-view](https://github.com/Davoyan/xray-access-view)
- [@anatolykopyl](https://github.com/anatolykopyl) за [xray-exporter](https://github.com/anatolykopyl/xray-exporter)

---

## 📄 Лицензия

MIT — делайте что хотите, но лучше делитесь опытом!

---

*Сделано для свободного интернета*
