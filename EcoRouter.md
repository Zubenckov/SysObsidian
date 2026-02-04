### Базовая Настройка

> [!INFO] Режимы:
```bash
enable
configure terminal
```

>[!WARNING] Смена имени маршрутизатора:
```bash
hostname <имя>
```
Отключение 10 минутного вылета:
```bash
line console 0
exec-timeout 0
exit
```

```bash
line vty 0 871
exec-timeout 0
```
Установка пароля на Enable:
```bash
enable secret P@ssw0rd # Пароль для режима администрирования - en
```
Зашифровать не зашифрованные пароли
```bash
service password-encryption
```
Создание/установка пароля на юзера:
```bash
username <Имя> # создаем/заходим под юзера
password <пароль> # задаем пароль для администратора
role admin # выдаем роль админа
```
Сохранение конфигураций:
```bash
write 
```
> [!WARNING] Можно писать в любом режиме

Установка баннера:
```bash
banner motd TEXT
```

### Настройка OSPFv2:
```bash
router ospf <№>
	ospf router-id <IP>
	network <net_ip> <reverser> area <>
```
Исключение интерфейсов из процесса ospf:
```bash
ospf  passive-intrafce <имя интерфейса>
```
Настройка аутентификации
```bash
ip ospf authentication
area <№> authentication
```
Более быстрый способ:
```bash
default passive-interface
no passive interface <имя интерфейса>
```
Настройка dead и hello:
```bash
ip ospf dead-interval
ip ospf hello-interval <№> # Число в секундах от 1-65535
```
Создание ключа аутентификации для ospf:
```bash
ip ospf authentication-key <Ключ>
ip ospf message-diggest-key <id-ключа> md5 <Ключ>
```
###  Создание/редактирование интерфейса:
```bash
interface <int name>
	ip address <ip addr>/<prefix>
	description "TEXT" # можно написать описание в кавычках
```
###  Включение порта:
```bash
port <port>
service-instance <S-I-name>
	encapsulation untagged
	connect ip interface <int_name>
```
### Настройка NAT
```bash
ip nat pool <pool_name> <local_ip1> <local_ip2>
```
На интерфейсе:
```bash
	interface <int_name>
		ip nat inside(внутренний)/outside(внешний)
	ip nat source dynamic inside-to-outside pool <pool_name> overload interface <int_name>(через внешний интерфейс)
```
Проверка трансляции адресов через NAT:
```bash
show ip nat translation
```