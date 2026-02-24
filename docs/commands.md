
## Полезные команды

| Действие | Команда |
|----------|---------|
| Посмотреть текущие активные IP | `cat /tmp/current_ips.txt` |
| Посмотреть старые IP | `cat /tmp/online_ips.txt` |
| Ручной запуск скрипта | `/usr/local/bin/check_users_v3.sh` |
| Логи Xray в реальном времени | `tail -f /var/log/xray/access.log` |
| Перезапуск Xray | `sudo systemctl restart xray` |
| Логи скрипта | `sudo grep check_users /var/log/syslog` |
