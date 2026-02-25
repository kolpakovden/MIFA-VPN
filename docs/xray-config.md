#  Настройка Xray

## Конфигурация сервера

Основной конфиг Xray находится в `/usr/local/etc/xray/config.json`.  
Пример конфига с комментариями: [`config/example.config.json`](../config/example.config.json)

### Базовые параметры

| Поле | Описание |
|------|----------|
| `port` | Порт входящего подключения |
| `protocol` | Всегда `vless` для этого проекта |
| `id` | UUID пользователя (генерируется командой `xray uuid`) |
| `email` | Идентификатор пользователя в логах |
| `flow` | Для Reality обязательно `xtls-rprx-vision` |

### Генерация ключей

```bash
# UUID для нового пользователя
xray uuid

# Ключи Reality (private + public)
xray x25519

# ShortID (hex)
openssl rand -hex 8
```

### Проверка конфига

```bash
sudo xray run -test -config /usr/local/etc/xray/config.json
```

### Перезапуск Xray

```bash
sudo systemctl restart xray
sudo systemctl status xray
```

---

## Логи

Для работы мониторинга в конфиге должна быть секция:

```json
"log": {
    "loglevel": "info",
    "access": "/var/log/xray/access.log",
    "dnsLog": false
}
```
