### Настройка сетевого моста с помощью встроенной утилиты 

```bash
 ip -c --br a
```

- Обычно enp0s3 - имеет статус UP - значит он как минимум имеет директорию в /etc/net/ifaces/ а также описанный файл options, который можно взять за основу для двух других интерфейсов:

```bash
cp -r /etc/net/ifaces/enp0s3/ /etc/net/ifaces/enp0s8/
cp -r /etc/net/ifaces/enp0s3/ /etc/net/ifaces/enp0s9/
```

- Для применения изменения перезагружаем службу network:
```bash 
systemctl restart network
```

- Создаём директорию для мостового (bridge) интерфейса:
```bash
mkdir /etc/net/ifaces/br0
```

- Описываем файл options для мостового интерфейса:
```bash
vim /etc/net/ifaces/br0/options
```


> [!info]
> TYPE - эта опция определяет тип интерфейса в данном случае bri - сокращение от bridge;
> 
> HOST - этой опции необходимо передать имена сетевых интерфейсов, которые должны быть помещены в данный мост (bridge);
> 
> BOOTPROTO - реализуем возможность задать IPv4 или IPv6 сетевые параметры на данный мостовой интерфейс, и не получит их автоматически ( параметр dhcp);
> 
> BRIDGE_OPTIONS - эта опция принимает дополнительные опции для мостового интерфейса.


- Назначаем IPv4-адрес, который будет выступать в качестве шлюза по умолчанию для cli01 и cli02 в данной локальной сети:
```bash
echo "10.0.10.1/24" > /etc/net/ifaces/enp0s3/ipv4address
```

- Создание сетевого моста: 
```bash
ip link add/del name BR-1 type bridge 
ip link set up BR-1
ip link set dev enp0s3 master BR-1 - nomaster 
bridge link # Проверка сетевого моста 
```

- Просмотр соседей или же ARP таблица: 
```bash
ip neighbor show
```

- Добавление записи в эту таблицу:
```bash
ip neighbor add dev enp0s3 192.168.10.4 lladdr 08:00:27:23:48:f6 nud permanent 
```

- Включение/выключение автоматической записи ARP табилцы: 
```bash
ip link set dev enp0s3 arp off/on
sysctl net.ipv4.ip_forward=1 # Включение пересылки пакетов
```

- Временное назначение ipv4 - адреса в соответствие с топологией:
```bash
ip addr add 10.0.10.101/24 dev enp0s3
```

- Временное назначение шлюза по умолчанию:
```bash
ip route add 0.0.0.0/0 via 10.0.10.1
```

- Назначение в файле: 
```bash
vim /etc/net/ifaces/enp0s3/ipv4route | default via … 
```

- Включение пересылки пакетов в файле: 
```bash
vim /etc/net/sysctl.conf
```

### Настройка сетевого моста с помощью openvswitch

- Включаем и добавляем в автозагрузку службу openvswitch:
```bash
systemctl enable --now openvswitch
```

- Средствами openvswitch создаём мостовой интерфейс (bridge) с именем ovs0:
```bash
ovs-vsctl add-br ovs0
```

- Настраиваем порты доступа:
```bash
ovs-vsctl add-port ovs0 enp0s8 tag=20 # Добавляем порт enp0s8 - смотрящий в сторону cli01 в bridge ovs0 и присваиваем ему тег VLAN-а 20 
ovs-vsctl add-port ovs0 enp0s9 tag=10 # Добавляем порт enp0s9 - смотрящий в сторону cli02  в bridge ovs0 и присваиваем ему тег VLAN-а 10
```

- Настраиваем магистральный порт (enp0s3) в сторону alt-sw02:
```bash
ovs-vsctl add-port ovs0 enp0s3 trunk=10,20 # Добавляем порт enp0s3 - смотрящий в сторону alt-sw02  в bridge ovs0 - и разрешаем передачу по магистральному канало только используемые VLAN-ы
```

- Проверяем:
```bash
ovs-vsctl show
ovs-appctl fdb/show ovs0 # Просмотр ARP таблицы 
ovs-appctl fdb/flush ovs0 # Очистка ARP таблицы 
```

```bash
ovs-vsctl add-port ovs0 enp0s9 vlan_mode=dot1q-tunnel tag=100
```

```bash
ovs-vsctl set bridge ovs0 stp_enable=true / rstp_enable=true
ovs-appctl stp/show | rstp/show
```

```bash
ovs-vsctl set bridge ovs0 other_config:stp-priority=4096 / stp-hello-time=1
```

- Агрегирование каналов с помощью openvswitch: 
```bash
ovs-vsctl add-bond ovs0 bond0 enp0s3 enp0s8
ovs-vsctl set bond0 lacp=active

ovs-vsctl set port bond0 bond_mode=balance-tcp

ovs-vsctl bond/show
```

- Создание зеркала для защиты трафика: 
```bash
ovs-vsctl set bridge ovs0 mirrors=@m -- --id=@mirror0 get port enp0s9 -- --id=@enp0s3 get port enp0s3 -- --id=@m create mirror name=mymirror select-dst-port=@enp0s3 select-src-port=@enp0s3 output-port=@mirror0
```

- Rspam: 
```bash
ovs-vsctl set bridge ovs0 mirrors=@m -- --id=@m create mirror name=mymirror select-all=true select-vlan=10 output-vlan=15
```

- Включаем модуль ядра отвечающий за поддержку стандарта 802.1Q:
```bash
modprobe 8021q
и:
echo "8021q" | tee -a /etc/modules
```

- Проверяем:
```bash
lsmod | grep 8021q
```
