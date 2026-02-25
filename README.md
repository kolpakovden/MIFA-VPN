# 🛡️ VLESS + Telegram Monitoring + Grafana

![Version](https://img.shields.io/badge/version-3.0-blue)
![Xray](https://img.shields.io/badge/Xray-25.8.3-green)

> Мониторинг VPN-сервера: уведомления в Telegram + логи в Grafana.

---

## О проекте

**Зачем это всё?** → [MANIFEST.md](MANIFEST.md)

**Возможности:**
- 👥 16 пользователей
- 🌐 5 портов (443, 8443, 2053, 2083, 50273)
- 🎭 11 доменов маскировки
- 🤖 Telegram-боты (уведомления + управление)
- 📍 Геолокация по IP
- 📊 Grafana + Loki (логи посещений)

---

## Быстрый старт

```bash
git clone https://github.com/твой-логин/pet_vless_telegram.git
cd pet_vless_telegram
```

### Telegram-бот уведомлений
1. Получи токен у [@BotFather](https://t.me/botfather) и Chat ID у [@userinfobot](https://t.me/userinfobot)
2. Вставь их в `scripts/check_users.sh`
3. Добавь в cron: `* * * * * /path/to/scripts/check_users.sh`

### Telegram-бот управления
```bash
cd /opt/pet_vless_telegram
python3 -m venv venv
source venv/bin/activate
pip install python-telegram-bot python-dotenv
cp .env.example .env  # вставь токен и chat_id
sudo cp scripts/xray-tg-bot.service /etc/systemd/system/
sudo systemctl enable --now xray-tg-bot
```

**Команды бота:** `/add`, `/list`, `/del`, `/key`, `/restart`

---

## Мониторинг через Grafana

### Установка Loki
```bash
# Скачать и установить Loki
wget -O /tmp/loki.zip https://github.com/grafana/loki/releases/download/v3.6.7/loki-linux-amd64.zip
cd /tmp && unzip loki.zip && sudo mv loki-linux-amd64 /usr/local/bin/loki
sudo chmod +x /usr/local/bin/loki
# Конфиг и сервис — см. файлы в репозитории
```

### Promtail (через Docker)
```bash
docker run -d --name promtail -v /var/log/xray:/var/log/xray:ro -v /etc/loki/promtail-config.yaml:/etc/promtail/config.yaml:ro --network host --restart always grafana/promtail:3.6.7 -config.file=/etc/promtail/config.yaml
```

### Подключение к Grafana
- **Data Source:** Loki → URL `http://localhost:3100`
- **Примеры запросов:**
  ```logql
  {job="xray"}                                   # все логи
  sum by (email) (count_over_time({job="xray"}[5m]))  # активность
  topk(10, sum by (domain) (count_over_time({job="xray"}[24h])))  # топ доменов
  ```

### Готовые дашборды
- `dashboards/user-activity.json` — активность, топ доменов, логи

---

## Структура

```
pet_vless_telegram/
├── README.md
├── MANIFEST.md
├── config/
│   ├── example.config.json
│   └── promtail-example.yaml
├── scripts/
│   ├── check_users.sh
│   ├── bot.py
│   └── *.service
├── dashboards/
│   └── user-activity.json
└── .env.example
```

---

## Полезные команды

| Действие | Команда |
|----------|---------|
| Активные IP | `cat /tmp/current_ips.txt` |
| Логи Xray | `tail -f /var/log/xray/access.log` |
| Статус Xray | `sudo systemctl status xray` |
| Статус бота | `sudo systemctl status xray-tg-bot` |
| Статус Loki | `sudo systemctl status loki` |
| Логи бота | `sudo journalctl -u xray-tg-bot -f` |

---

## 📄 Лицензия

MIT — делайте что хотите, но лучше делитесь опытом!
