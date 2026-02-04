программа управления брандмауэром (файрволом) для операционных систем Linux, которая контролирует и фильтрует сетевой трафик, защищая сеть от несанкционированного доступа.

> [!WARNING] Существующие дистрибутивы
> ```bash 
 firewalld 
 firewall-config


- Для того чтобы узнать статус службы используются следующие команды:
```bash
systemctl status firewalld
firewall-cmd --state
```

- Включение/отключение службы
```bash
systemctl enable firewalld
systemctl disable firewalld
systemctl start firewalld
systemctl stop firewalld
```

- Перезагрузка
```bash
firewall-cmd --reload
firewall-cmd --complete-reload
```

#### Флаги для работы с зонами
- Список всех зон
```bash
firewall-cmd --list-all-zones
```

- Активные зоны
```bash
firewall-cmd --get-active-zones
```

- Список всех доступных зон
```bash
firewall-cmd --get-zones
```

- Информация о конкретной зоне
```bash
firewall-cmd --zone=public --list-all # Внутренние интерфейсы
firewall-cmd --zone=internal --list all # Внешние интерфейс
```