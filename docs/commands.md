# Commands Cheat Sheet

Быстрый справочник по управлению сервером, Xray и мониторингом.

---

## Admin

### Xray

#### Статус / перезапуск
```bash
systemctl status xray
systemctl restart xray
```

#### Проверить конфиг
```bash
xray run -test -config /usr/local/etc/xray/config.json
```

#### Генерация ключей
```bash
xray uuid           # Новый UUID
xray x25519         # Reality-ключи
openssl rand -hex 8 # shortId
```

---

### Telegram Bot (management)

```bash
systemctl status xray-tg-bot
systemctl restart xray-tg-bot
```

---

### Promtail (Docker)

```bash
docker ps | grep promtail
docker restart promtail
docker stop promtail
docker rm promtail
```

---

### Cron (Notification Bot)

```bash
crontab -e
```

Добавить:

```
* * * * * /usr/local/bin/check_users.sh
```

Ручной запуск:

```bash
/usr/local/bin/check_users.sh
```

---

## Monitoring

### Сервисы

```bash
systemctl status loki
systemctl status prometheus
systemctl status xray
systemctl status xray-tg-bot
```

### Prometheus

```bash
curl -s http://localhost:9090/api/v1/targets | python3 -m json.tool
```

### Loki

```bash
# Проверка готовности
curl http://localhost:3100/ready

# Все labels
curl -s http://localhost:3100/loki/api/v1/labels | python3 -m json.tool

# Список пользователей
curl -s "http://localhost:3100/loki/api/v1/label/email/values" | python3 -m json.tool
```

### Активные IP

```bash
cat /tmp/current_ips.txt
```

### Системные ресурсы

```bash
df -h                  # Disk
free -h                # RAM
uptime                 # CPU load
ip -s link             # Network
ss -tunap | grep xray | wc -l  # Активные соединения
```

---

## Debug

### Логи сервисов

```bash
journalctl -u xray -f
journalctl -u loki -f
journalctl -u prometheus -f
journalctl -u xray-tg-bot -f
```

### Логи подключений Xray

```bash
tail -f /var/log/xray/access.log
```

### Docker логи Promtail

```bash
docker logs promtail --tail 50
```

### Debug notification-бота

```bash
tail -f /tmp/debug.log
```

---

**Готово!** 🔥 Теперь `commands.md` выглядит так же круто, как и остальные документы.
