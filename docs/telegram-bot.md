# Telegram Bots

Проект использует **два независимых бота**:

- **Notification Bot** — уведомляет о новых подключениях
- **Management Bot** — управление пользователями Xray через Telegram

---

## Notification Bot (`check_users.sh`)

### Назначение

- Проверяет `access.log` Xray каждую минуту
- Определяет **новые IP**
- Отправляет уведомление в Telegram
- Показывает **город, страну и провайдера**
- Работает через **cron**

### Setup

#### Получить данные Telegram

- Создать бота через [@BotFather](https://t.me/botfather)
- Узнать `chat_id` через [@userinfobot](https://t.me/userinfobot)

#### Настроить скрипт

Открыть:

```bash
scripts/check_users.sh
```

Указать:

```bash
BOT_TOKEN="your_bot_token"
CHAT_ID="your_chat_id"
```

#### Сделать исполняемым

```bash
chmod +x scripts/check_users.sh
```

#### Добавить в cron

```bash
crontab -e
```

Добавить строку:

```bash
* * * * * /absolute/path/to/scripts/check_users.sh
```

### Example Notification

```
New VPN connection!
IP: 80.83.237.47
City: Irkutsk
Region: Irkutsk Oblast
Country: Russia
ISP: Mobile TeleSystems
Time: 24.02.2026 19:53:01
```

---

## Management Bot (`bot.py`)

### Назначение

Позволяет администратору:

- Добавлять пользователей
- Удалять пользователей
- Получать ключи подключения
- Перезапускать Xray
- Просматривать список пользователей

### Available Commands

| Команда | Описание | Пример |
|---------|----------|--------|
| `/add Name` | Добавить пользователя | `/add Natasha` |
| `/list` | Список пользователей | `/list` |
| `/del email` | Удалить пользователя | `/del user@server.com` |
| `/key email [port]` | Получить ключ | `/key user@server.com 8443` |
| `/restart` | Перезапуск Xray | `/restart` |
| `/help` | Справка | `/help` |

---

### Installation

#### Перейти в директорию проекта

```bash
cd /opt/pet_vless_telegram
```

#### Создать виртуальное окружение

```bash
python3 -m venv venv
source venv/bin/activate
```

#### Установить зависимости

```bash
pip install python-telegram-bot python-dotenv
```

#### Настроить переменные окружения

```bash
cp .env.example .env
nano .env
```

Указать:

```bash
BOT_TOKEN="your_token"
CHAT_ID="your_chat_id"
```

---

### Run as systemd Service

```bash
sudo cp scripts/xray-tg-bot.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now xray-tg-bot
```

Проверить статус:

```bash
sudo systemctl status xray-tg-bot
```

### Logs

```bash
sudo journalctl -u xray-tg-bot -f
```

---

## Architecture Summary

```
Xray → access.log → check_users.sh → Telegram
Admin → Telegram → bot.py → Xray config/API
```
