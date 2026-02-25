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

## 🚀 Быстрый старт

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

## 📁 Структура

```
pet_vless_telegram/
├── README.md                      # Ты здесь
├── MANIFEST.md                    # Мотивация
├── docs/                          # Документация по модулям
│   ├── monitoring.md
│   ├── telegram-bot.md
│   └── xray-config.md
├── config/                        # Конфиги (примеры)
├── scripts/                       # Скрипты и сервисы
├── dashboards/                    # JSON дашбордов для Grafana
└── .env.example                   # Пример переменных
```

---

## 🛠️ Полезные ссылки

| Раздел | Ссылка |
|--------|--------|
| 📊 Мониторинг (Loki + Grafana) | [`docs/monitoring.md`](docs/monitoring.md) |
| 🤖 Telegram-боты | [`docs/telegram-bot.md`](docs/telegram-bot.md) |
| 🔧 Настройка Xray | [`docs/xray-config.md`](docs/xray-config.md) |
| 📋 Шпаргалка по командам | [`docs/commands.md`](docs/commands.md) |

---

## 📄 Лицензия

MIT — делайте что хотите, но лучше делитесь опытом!
