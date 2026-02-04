#### Вход  
```bash
> commit # Применить  
> confirm # Подтвердить  
> restore # Отмена неподтвержденных изменений  
> rollback # Откат подтвержденных изменений  
> config # Режим конфигурации НЕ conf t как в Cisco  
```  
#### Баннеры  
```bash
> banner login "Unauthorized connection is prosecuted by the law of the Russian Federation" # баннер до аутентификации пользователя  
> banner exec "Hello! This is an vESR!" # баннер после аутентификации  
```  
#### Пользователи  
```bash
> username <name> # создание пользователя  
> password <pas> # задача пароля  
> privilege <№_1-15> # 10-14 настройка системы, но не полный функционал  
> exit  
```  
#### IP Адресация  
```bash
> interface <int_name> # Заходим на интерфейс  
> description <text> # Можно задать описание, но это необязательно до 31 символа, текст можно печатать как в "кавычках" так и без, но желательно в "кавычках"  
> ip firewall disable # Отключение Firewall'a  
> ip address <ip_address>/<prefix> # Задаем не маску, а именно префикс пример: 192.168.0.0/32  
```  
#### IPv6  
```bash
> ipv6 enable # Включение IPv6 на устройстве
> ipv6 address <ipv6_address>/<prefix> # Задается также на интерфейсе  
```  
#### Настройка шлюза  
```bash
> ip route <network>/<prefix> <ip_address_nexthop> # Настройка маршрута IPv4  
> ipv6 route <prefix>/<network> <ipv6_address_nexthop> # Настройка маршрута IPv6
```

### Настройка DHCP
```bash
> service dhcp enable # Включение сервиса DHCP
> dhcp pool <pool_name> # Созадние DHCP Пула
> 	network <network>/prefix # Указание сети и префикса
> 	default-router <ip_address> # Указание шлюза
> 	dns-server <ip_address> # Укзаание DNS-Сервера

> ip dhcp excluded-address <ip_start> <ip end> # Исключение диапазона адресов из пула
```
### Настройка GRE-туннеля
```bash
> interface Tunnel0 # Созадние туннельного интерфейса
> ip address <ip_address>/<prefix> # Назначение IP-Адреса
> tunnel source <source_ip> # Указание исходного IP
> tunnel destination <dest_ip> # Указание целевого IP
> commit # Сохранение изменений
> confirm # Подтверждение сохранения изменений
```
### Проверка и отладка
```bash
> show running-config # Показ текущей конфигурации
> show ip interface brief # Информация об интерфейсах
> show ip route # Просмотр маршрутов
> show ip ospf neighbor # Соседи OSPF
> ping <ip_address> # Проверка связи
> traceroute <ip_address> # Трассировка маршрута
```