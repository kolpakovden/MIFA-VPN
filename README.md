# 🛡️ VLESS + Telegram Monitoring + Grafana

![Version](https://img.shields.io/badge/version-3.0-blue)
![Xray](https://img.shields.io/badge/Xray-25.8.3-green)
![License](https://img.shields.io/badge/license-MIT-orange)
![Telegram](https://img.shields.io/badge/Telegram-bot-26A5E4)
![Grafana](https://img.shields.io/badge/Grafana-dashboard-F46800)

> Личный VPN-сервер с полным мониторингом для друзей и семьи.

---

## О проекте

**Зачем это всё?** → [MANIFEST.md](MANIFEST.md)

### Возможности
- 👥 пользователей с уникальными UUID
- 🌐 5 портов (443, 8443, 2053, 2083, 50273)
- 🎭 11 доменов маскировки
- 🤖 Telegram-боты: уведомления о подключениях + управление пользователями
- 📍 Геолокация по IP (город, страна, провайдер)
- 📊 Мониторинг посещений через Grafana + Loki

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
---

## Быстрый старт

```bash
git clone https://github.com/твой-логин/pet_vless_telegram.git
cd pet_vless_telegram
```

### 1. Настройка Xray
- Пример конфига: [`config/example.config.json`](config/example.config.json)
- Документация: [`docs/xray-config.md`](docs/xray-config.md)

### 2. Telegram-боты
- Уведомления о новых подключениях → [`docs/telegram-bot.md`](docs/telegram-bot.md#бот-уведомлений)
- Управление пользователями → [`docs/telegram-bot.md`](docs/telegram-bot.md#бот-управления)

### 3. Мониторинг (Grafana + Loki)
Полная инструкция по установке → **[`docs/monitoring.md`](docs/monitoring.md)**

---

## Структура

```
pet_vless_telegram/
├── README.md                               # Главная документация
├── MANIFEST.md                             # Мотивация и история проекта
├── .env.example                            # Пример переменных окружения
├── .gitignore                              # Игнорируемые файлы
│
├── docs/                                   # Документация по модулям
│   ├── monitoring.md                       # Установка Loki + Promtail
│   ├── telegram-bot.md                     # Инструкции по ботам
│   ├── xray-config.md                      # Настройка Xray
│   └── commands.md                         # Шпаргалка по командам
│
├── config/                                 # Примеры конфигов
│   ├── example.config.json                 # Пример конфига Xray
│   ├── loki-config.yaml                    # Конфиг Loki
│   ├── promtail-config.yaml                # Конфиг Promtail
│   └── loki.service                        # Systemd сервис для Loki
│
├── scripts/                                # Скрипты и сервисы
│   ├── check_users.sh                      # Бот уведомлений о новых IP
│   ├── bot.py                              # Telegram-бот управления
│   ├── xray-tg-bot.service                 # Systemd для бота управления
│   └── xray-exporter.service               # Systemd для экспортера (опционально)
│
└── dashboards/                             # JSON дашбордов для Grafana
    └── user-activity.json                  # Дашборд "Активность пользователей"
```

---

## Полезные ссылки

| Раздел | Ссылка |
|--------|--------|
| 📊 Мониторинг (Loki + Grafana) | [`docs/monitoring.md`](docs/monitoring.md) |
| 🤖 Telegram-боты | [`docs/telegram-bot.md`](docs/telegram-bot.md) |
| 🔧 Настройка Xray | [`docs/xray-config.md`](docs/xray-config.md) |
| 📋 Шпаргалка по командам | [`docs/commands.md`](docs/commands.md) |

---

## Благодарности

- [@maxgalzer](https://github.com/maxgalzer) за [xray-traffic-bot](https://github.com/maxgalzer/xray-traffic-bot)
- [@Davoyan](https://github.com/Davoyan) за [xray-access-view](https://github.com/Davoyan/xray-access-view)
- [@anatolykopyl](https://github.com/anatolykopyl) за [xray-exporter](https://github.com/anatolykopyl/xray-exporter)
- Всем, кто держит свободный интернет 

---

## 📄 Лицензия

MIT — делайте что хотите, но лучше делитесь опытом!

---

*Сделано для свободного интернета*
