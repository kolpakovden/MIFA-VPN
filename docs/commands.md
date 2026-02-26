## Система

| Команда | Описание |
|---------|----------|
| `sudo systemctl status xray` | Статус Xray |
| `sudo systemctl restart xray` | Перезапуск Xray |
| `sudo journalctl -u xray -f` | Логи Xray в реальном времени |
| `sudo systemctl status loki` | Статус Loki |
| `sudo systemctl status promtail` | Статус Promtail |
| `sudo systemctl status xray-tg-bot` | Статус Telegram-бота управления |
| `sudo journalctl -u xray-tg-bot -f` | Логи бота |

---

## Xray

| Команда | Описание |
|---------|----------|
| `xray uuid` | Сгенерировать новый UUID |
| `xray x25519` | Сгенерировать ключи Reality |
| `openssl rand -hex 8` | Сгенерировать shortId |
| `xray run -test -config /usr/local/etc/xray/config.json` | Проверить конфиг |
| `tail -f /var/log/xray/access.log` | Логи подключений |
| `xrayview` | Статистика посещений |
| `xrayview --online` | Кто онлайн сейчас |

---

## Мониторинг

| Команда | Описание |
|---------|----------|
| `cat /tmp/current_ips.txt` | Текущие активные IP |
| `curl http://localhost:3100/ready` | Проверка Loki |
| `curl http://localhost:3100/loki/api/v1/labels` | Метки в Loki |
| `docker logs promtail --tail 20` | Логи Promtail |
| `docker restart promtail` | Перезапуск Promtail |

---

## Telegram-бот уведомлений

| Команда | Описание |
|---------|----------|
| `crontab -e` | Редактировать cron |
| `* * * * * /usr/local/bin/check_users.sh` | Добавить в cron |
| `/usr/local/bin/check_users.sh` | Ручной запуск |
| `tail -f /tmp/debug.log` | Отладка скрипта (если включена) |

---

## Docker

| Команда | Описание |
|---------|----------|
| `docker ps \| grep promtail` | Статус контейнера |
| `docker stop promtail` | Остановить |
| `docker rm promtail` | Удалить |
| `docker logs promtail --tail 50` | Логи |
| `docker restart promtail` | Перезапустить |

---

## Системные ресурсы

| Команда | Описание |
|---------|----------|
| `df -h` | Свободное место на диске |
| `free -h` | Использование RAM |
| `uptime` | Нагрузка CPU |
| `top -b -n 1 \| head -15` | Топ процессов |
| `ip -s link` | Статистика сети |
| `ss -tunap \| grep xray \| wc -l` | Активные соединения Xray |

---

## Prometheus

| Команда | Описание |
|---------|----------|
| `sudo systemctl status prometheus` | Статус сервиса |
| `sudo journalctl -u prometheus -f` | Логи в реальном времени |
| `curl -s http://localhost:9090/api/v1/targets \| python3 -m json.tool` | Проверка целей |

---

## Loki + Promtail

| Команда | Описание |
|---------|----------|
| `sudo systemctl status loki` | Статус Loki |
| `docker logs promtail --tail 30` | Логи Promtail |
| `curl -s http://localhost:3100/loki/api/v1/labels \| python3 -m json.tool` | Все метки в Loki |
| `curl -s "http://localhost:3100/loki/api/v1/label/email/values" \| python3 -m json.tool` | Список пользователей |
