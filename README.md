# Домашнее задание к занятию 1 `«Disaster recovery и Keepalived»` - `Шадрин Петр`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания! 
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

Файл приложен в формате pkt (Задание выполнялось в версии 8.2.1.0118)

<img width="1768" height="897" alt="image" src="https://github.com/user-attachments/assets/5e42e535-ef5a-4393-b72d-fa3a99b2d7e6" />

---

### Задание 2

```
cat /usr/local/bin/check_apache.sh

#!/bin/bash

# Параметры для проверки
PORT=80
CHECK_FILE="/var/www/html/index.html"
TIMEOUT=3

# Проверяем доступность порта
if ! nc -z -w $TIMEOUT localhost $PORT >/dev/null 2>&1; then
    echo "Port $PORT is not accessible"
    exit 1
fi

# Проверяем существование index.html
if [ ! -f "$CHECK_FILE" ]; then
    echo "File $CHECK_FILE does not exist"
    exit 1
fi

# Проверяем, что файл не пустой (опционально)
if [ ! -s "$CHECK_FILE" ]; then
    echo "File $CHECK_FILE is empty"
    exit 1
fi

# Если все проверки пройдены
exit 0
```


```
# cat /etc/keepalived/keepalived.conf

vrrp_script chk_apache {
    script "/usr/local/bin/check_apache.sh"
    interval 3  # проверка каждые 3 секунды
    timeout 1    # таймаут выполнения скрипта
    fall 2       # после 2 неудачных проверок сервер считается нерабочим
    rise 2       # после 2 успешных проверок сервер считается рабочим
}

vrrp_instance VI_1 {
        state MASTER
        interface ens18
        virtual_router_id 139
        priority 255
        advert_int 1

        virtual_ipaddress {
              10.1.1.139/24
        }

        track_script {
        chk_apache  # отслеживаем состояние скрипта
        }
}


<img width="1585" height="812" alt="image" src="https://github.com/user-attachments/assets/da59c6b1-bd0c-40dc-8d86-5ae2f1f3d29c" />
```
