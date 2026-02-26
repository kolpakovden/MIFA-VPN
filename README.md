# 🛡️ VLESS + Telegram Monitoring + Grafana

Self-hosted VPN server with full monitoring and Telegram control.

---

## Overview

Проект объединяет:

- **Xray (VLESS + Reality)** — VPN-сервер
- **Telegram-боты** — управление пользователями и уведомления
- **Grafana + Loki** — мониторинг пользовательской активности
- **Prometheus + Node Exporter** — системные метрики сервера

> Зачем это всё? → [`MANIFEST.md`](MANIFEST.md)

---

## Features

| | |
|---|---|
| **VPN** | Уникальные UUID для пользователей, 5 портов (443, 8443, 2053, 2083, 50273), 11 доменов маскировки |
| **Telegram** | Уведомления о новых подключениях + бот управления (`/add`, `/remove`, `/list`) |
| **Геолокация** | Определение города, страны, провайдера по IP |
| **Логи** | Мониторинг посещений через Grafana + Loki |
| **Система** | CPU / RAM / Disk / Network через Prometheus + Node Exporter |

---

## Архитектура

```
Internet
   │
   ▼
Xray (VLESS + Reality)
   │
   ├── access.log ──► Promtail ──► Loki ──► Grafana
   │
   ├── API stats ──► Prometheus ──► Grafana
   │
   └── Telegram Bot (notifications + management)
```

---

## Quick Start

```bash
git clone https://github.com/your-username/pet_vless_telegram.git
cd pet_vless_telegram
```

---

## Setup Guide

### Xray

- Пример конфига: [`config/example.config.json`](config/example.config.json)
- Документация: [`docs/xray-config.md`](docs/xray-config.md)

### Telegram Bots

- Бот уведомлений: [`docs/telegram-bot.md#бот-уведомлений`](docs/telegram-bot.md#бот-уведомлений)
- Бот управления: [`docs/telegram-bot.md#бот-управления`](docs/telegram-bot.md#бот-управления)

### Monitoring

- **User Activity** — Loki + Promtail (логи посещений)
- **System Metrics** — Prometheus + Node Exporter (CPU, RAM, Disk, Network)

Полная инструкция: [`docs/monitoring.md`](docs/monitoring.md)

---

## Project Structure

```
pet_vless_telegram/
├── README.md                               # Главная документация
├── MANIFEST.md                             # Мотивация и история
├── .env.example                            # Пример переменных
├── .gitignore                              # Игнорируемые файлы
│
├── docs/                                   # Документация
│   ├── monitoring.md                       # Loki + Prometheus
│   ├── telegram-bot.md                     # Инструкции по ботам
│   ├── xray-config.md                      # Настройка Xray
│   └── commands.md                         # Шпаргалка по командам
│
├── config/                                 # Примеры конфигов
│   ├── example.config.json                 # Xray (пример)
│   ├── loki-config.yaml                     # Конфиг Loki
│   ├── promtail-config.yaml                  # Конфиг Promtail
│   └── loki.service                          # Systemd сервис
│
├── scripts/                                # Скрипты
│   ├── check_users.sh                       # Бот уведомлений
│   ├── bot.py                               # Бот управления
│   ├── xray-tg-bot.service                   # Systemd для бота
│   └── xray-exporter.service                 # (опционально)
│
└── dashboards/                             # JSON дашбордов
    └── user-activity.json                   # Дашборд для Grafana
```

---

## 📊 Dashboards

Импортируй дашборд [`dashboards/user-activity.json`](dashboards/user-activity.json) в Grafana.

После импорта ты получишь:

- Топ доменов
- Активность пользователей
- Логи в реальном времени
- CPU / RAM / Disk
- Сетевую нагрузку

---

## Documentation

| Раздел | Ссылка |
|--------|--------|
| Мониторинг | [`docs/monitoring.md`](docs/monitoring.md) |
| Telegram-боты | [`docs/telegram-bot.md`](docs/telegram-bot.md) |
| Настройка Xray | [`docs/xray-config.md`](docs/xray-config.md) |
| Шпаргалка по командам | [`docs/commands.md`](docs/commands.md) |

---

## Credits

- [@maxgalzer](https://github.com/maxgalzer) за [xray-traffic-bot](https://github.com/maxgalzer/xray-traffic-bot)
- [@Davoyan](https://github.com/Davoyan) за [xray-access-view](https://github.com/Davoyan/xray-access-view)
- [@anatolykopyl](https://github.com/anatolykopyl) за [xray-exporter](https://github.com/anatolykopyl/xray-exporter)
- [@Globchansky](https://github.com/Globchansky) за [xray-stats-exporter](https://github.com/Globchansky/xray-stats-exporter)
- [@mintel](https://github.com/mintel) за [promtail-static](https://github.com/mintel/promtail-static)
- [@grafana](https://github.com/grafana) за [Loki](https://github.com/grafana/loki) и [Grafana](https://github.com/grafana/grafana)
- [@XTLS](https://github.com/XTLS) за [Xray-core](https://github.com/XTLS/Xray-core)

---

## 📄 License

MIT — делайте что хотите, но лучше делитесь опытом!

---

*Сделано для свободного интернета* 🌐
