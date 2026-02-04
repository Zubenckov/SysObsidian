 > [!tldr] Используемые пакеты  
> - `ansible`  
> - `sshpass` - Доп. пакет для авторизации по паролю в ssh  
    
> [!info] Каталоги и файлы  
> 
> - `/etc/ansible/` - Главный каталог  
> - `ansible.cfg` - Конфигурационный файл  
> - `hosts` - Файл с группами/объектами по умолчанию  
> - `group_vars/` - Каталог c файлами переменных групп  
 > - `host_vars/` - Каталог c файлами переменных хостов  
> - `group_vars/all.yml` - Переменные для всех объектов  
      
> Ansible выполняет все действия на указанных машинах используя доступ по ssh. Потому важно указать пользователя и файл ключа/пароль в файле переменных группы или всех объектов.  
      
---  

[Справка]([https://habr.com/ru/articles/305400/](https://vk.com/away.php?to=https%3A%2F%2Fhabr.com%2Fru%2Farticles%2F305400%2F&cc_key=))  

---  
      
# Команды  
      
```bash  
    ansible <prams> <group | object>  
    ... -i </path/to/file> # Задать файл инвентаря  
    ... -a <param> # Аргумент модуля  
    ... -m <module> # Выполнить модуль  
    ping # Эхо запрос  
    shell -a'some command'# Выполнить команду  
    copy -a 'src=/path/to/main_file dest=/path/to/dir' # Скопироваеть файл  
    setup -a 'filter=ansible_some_key'# Сбор всех фактов или по фильтру  
      
    ansible-playbook <playbook_name.yml>  
    ... -i </path/to/file># Задать файл инвентря  
    ... -e <var=value># Задать переменные # —extra-vars  
    ... -l <host | host1:host2:..> # Задать конкретный хост исполнения  
```  
      
---  
      
# Файлы 
## Инвентарный файл Hosts  
      
> Инвентарный файл по умолчанию - `hosts`, но его можно изменить, явно указывая в команде или изменив файл конфигурации. Инвентарь можно описать в `.yml`, `.yaml` или `.ini` файлах. Если явно не указывать адрес объекта, то ansible обратится по доменному имени объекта. По умолчанию Ansible использует текущего пользователя для доступа, но можно явно указать другого или изменить его в `ansible.cfg`. Можно указать `*` для обозначения любого символа.  
      
> [!example] inventory.yml  
```yml  
GroupName: # Группа  
hosts: # Хосты  
web1: # Хост  
ansible_ssh_host: "192.168.33.11" # Адрес хоста  
ansible_ssh_user: root # SSH-пользователь  
web2: # Обращение о этому имени как по доменному  
 ```  
      
> [!example] inventory.ini  
> ```ini  
># Без группы  
> # Явно указываем адрес и пользователя  
> [host1.example.org](https://vk.com/away.php?to=http%3A%2F%2Fhost1.example.org&cc_key=) ansible_ssh_host=192.168.33.11 ansible_ssh_user=root  
> [host2.example.org](https://vk.com/away.php?to=http%3A%2F%2Fhost2.example.org&cc_key=) ansible_ssh_host=192.168.33.12 ansible_ssh_user=root  
>  
> # Группа  
> [GroupName-1]  
> # Хосты  
> web1  
> web2  
>  
> [GroupName-2]  
> web3  
>  
> # Дочерняя группа, объединяющая другие группы  
> [ChildGroup:children]  
> GroupName-1  
> GroupName-2  
> ```  
      
## Файл переменных групп/хостов  
      
> Ansible будет искать файлы в Каталогах переменных с именем группы/хоста. Если не найдёт, то ничего не произойдёт. Если найдёт, то переменная группы будет значением по умолчанию, а переменная хоста будет приоритетной.  
      
> [!example] all.yml  
> ```yml  
> # Обязательно указать пользователя и интерпретатор  
> ansible_user: user # Пользователь  
> ansible_python_interpreter: /use/bin/python3 # Интерпретатор Python  
>  
> # SSH парольный доступ  
> ansible_ssh_user: sshuer # Пользователь SSH  
> ansible_ssh_pass: P@ssw0rd # Пароль SSH-доступа  
> ansible_ssh_port: 2023 # Порт SSH-доступа  
>  
> # SSH доступ по ключу  
> ansible_ssh_user: sshuer # Пользователь SSH  
> ansible_ssh_private_key_file: /home/sshuser/.ssh/[id_rsa.pub](https://vk.com/away.php?to=http%3A%2F%2Fid_rsa.pub&cc_key=) # Файл ключа SSH-доступа  
> ansible_ssh_port: 2023 # Порт SSH-доступа  
>  
> # Для ESR и Cisco IOS  
> ansible_network_os: ios # ОС устройства  
> ansible_network_connection: network_cli # Тип сетевого доступа. Сетевая консоль  
>  
> some_var: "some value" # Название переменной, как и значение, может быть любым  
>  
> some_dict: # Комплексная переменная  
> var1: "text"  
> var2: 30  
> var3: /etc/ansible  
> ```  
      
---  
      
# PlayBook  
      
> Все задачи playbook будут выполнены последовательно и только в том случае, если это необходимо (что исключит повторной настройки одного и того же). Если задачу нужно выполнить только, если были внесены изменения (например обновление конфигурации), то можно использовать обработчики. Далее перечислю все модулю, которые знаю. Знаком `*` будут отмечены обязательные параметры.  
      
```yml  
  ---  
   - hosts: group_hosts  
   # Можно создать пустой блок для хостов.  
   # Задачи не указаны, но файты о
```
  [![Пособие по Ansible](https://sun9-39.userapi.com/impg/hFNPY3nXz2WyNzhsHwDrkA9DHrkL9jAk8p4LvA/zmV4NRNIGJA.jpg?size=537x240&quality=96&sign=640bf1a5869ff87a737e16656dbd1eee&type=share)](https://vk.com/away.php?to=https%3A%2F%2Fhabr.com%2Fru%2Farticles%2F305400%2F&el=snippet)
[Пособие по Ansible](https://vk.com/away.php?to=https%3A%2F%2Fhabr.com%2Fru%2Farticles%2F305400%2F&el=snippet "Пособие по Ansible")[habr.com](https://vk.com/away.php?to=https%3A%2F%2Fhabr.com%2Fru%2Farticles%2F305400%2F&el=snippet)
```yml
- хостах будут собраны.  
- name: PlayBookName # Имя плейбука  
hosts: all # *Группа/объект исполнения  
    remote_user: sshuser # Пользователь удалённого доступа  
    gather_facts: yes # Получение файтов о хостах  
    become: true # *Включить  
      
    tasks: # *Набор задач  
    - name: Установка пакетов # Имя задачи. Не обязательно  
    apt_rpm:  
    name: # *Имена пакетов  
    - docker-ce  
    - python3-module-pip  
    state: present # *Установить (absent - удалить)  
    update_cache: true # Обновить списки пакетов  
    ignore_errors: true # Игнорировать ошибки  
      
    - name: Копирование локального файла на хост  
    copy:  
    src: "/path/to/main_file" # *Локальный файл  
    dest: "/path/to/dir" # *Путь копирования  
    mode: 0640 # Уровень доступа  
    owner=root # Владелец  
    group=root # Группа  
    notify: # Триггер обработчика(ов)  
    - Restart service # *Имя обработчика  
    - # Может быть несколько  
    - name: Копирование шаблона на хост  
    template: # Аргументы как у модуля copy  
    src: "template/file" # *Локальный файл  
    dest: "/path/to/dir" # *Путь копирования  
      
    - name: Добавить строку в файл  
    lineinfile:  
    line: "{{ ansible_fqdn }} - {{ ansible_default_ipv4.address }}" # *  
    dest: "/path/to/file" # *Локальный файл  
    create: yes # Создать файл? Обычно ищет существующий  
    deligate_to: localhost # Делигировать задачу на host  
      
    - name: Управелние сервисом  
    systemd:  
    name: docker # *Имя сервиса  
    state: started # *Состояние сервиса (restarted | stopped)  
    enabled: true # Автозапуск  
    register: result # Записать результат в переменную  
    ignore_errors: True # Игнорировать ошибки  
      
    - name: Выполнение команд  
    command:  
    cmd: pip3 install docker-py  
      
    - name: Вывод ошибки  
    fail:  
    msg: "Досдная ошибка" # Вывод сообщения  
    when: result|failed # Если result=failed, то выполнить блок  
      
    - name: Запуск Docker контейнера  
    docker_container: # Создать Docker контейнер  
    name: app1 # *Имя контейнер  
    hostname: "{{ ansible_hostname }}" # Имя машины  
    image: aratashi/app # *Образ  
    ports: # Открыть порты  
    - "80:80"  
      
    - name: Установка из репозитория  
    git:  
    repo: "https://some-repository" # *Адрес репозитория  
    dest: "/path/to/file" # *Директория установки  
    tag: deploy # Тег коммита  
      
    handlers: # Обработчики  
    - name: Restart service # *Имя обработчика  
    systemd: # *Какое-л действие  
    name: some-service  
    state: restarted    
```  
## Заполнение шаблонов  
> Ansible использует [Jinja2]([http://jinja.pocoo.org/docs/](https://vk.com/away.php?to=http%3A%2F%2Fjinja.pocoo.org%2Fdocs%2F&cc_key=)), систему шаблонов для Python. Внутри Jinja2-шаблона можно использовать любую переменную, которая определена Ansible'ом. Переменные вставляются с помощью двойных фигурных скобок, для ansible `{{ ansible_... }}`, для хостов `{{ hostvars[var] }}`. Jinja2 также поддерживает условия, циклы и прочее. Обычно, шаблоны хранят в каталоге `templates/` директории Ansible.