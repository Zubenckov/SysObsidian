
> [!tldr] Используемые пакеты  
>- openvswitch  

- Для работы, все используемые физические интерфейсы должны быть включены 
> [!WARNING] Включаем сервис openvswitch и добавляем в автозапуск  
>- systemctl enable —now openvswitch.service 

 - Создаём папку моста /etc/net/ifaces/ovs0 и папку виртуального интерфейса /etc/net/ifaces/mgmt (имена могут быть любым) 
 ```bash
- mkdir /etc/net/ifaces/ovs0  
- mkdir /etc/net/ifaces/mgmt 
``` 

- Создаём файл options в каждой папке и редактируем  
> [!tip] Файл конфигурации /etc/net/ifaces/ovs0/options  
 >- TYPE=ovsbr # Тип интерфейса — bridge  
 >- HOST='ens33 ens34 ens36' # Указываем порты  

> [!info] Файл конфигурации /etc/net/ifaces/mgmt/options  
>- TYPE=ovsport # Тип интерфейса — port  
>- BOOTPROTO=static # тип адреса (static | dhcp)  
>- BRIDGE=ovs0 # Мост к которому привяжем (существующий)  
>- VID=300 # Vlan ID  

- Создаём и заполняем /etc/net/ifaces/mgmt/ipv4address и /etc/net/ifaces/mgmt/ipv4route для назначения адреса на интерфейс mgmt  
  
- Перезагружаем сервис network  
```bash
 - systemctl restart network  
```

Чтобы настройки сохранились после перезагрузки, изменяем параметр в /etc/net/ifaces/default/options  

```bash
- OVS_REMOVE=no # Выключаем удаление настроек  
```

Для работы необходимо включить модуль ядра 8021q  
```bash
modprobe 8021q # Временно вкл модуль  
```

```bash
echo "8021q" | tee -a /etc/modules # Постоянное вкл модуля  
```

> [!info] Производим настройку интерфейсов  
>- ovs-vsctl set port ens33 trunk=100,200 # Настроить trunk на vlan's. Если не указывать vlan, то пропустит всё  
>- ovs-vsctl set port ens34 tag=100 # Настроить access на vlan  
>- ovs-vsctl set port ens35 tag=200  
>- ovs-vsctl show # Вывод настроек
