# PostgreSQL
- Настроить hot_standby репликацию с использованием слотов
- Настроить правильное резервное копирование

Для сдачи работы присылаем ссылку на репозиторий, в котором должны обязательно быть Vagranfile и плейбук Ansible, конфигурационные файлы postgresql.conf, pg_hba.conf и recovery.conf, а так же конфиг barman, либо скрипт резервного копирования. Команда "vagrant up" должна поднимать машины с настроенной репликацией и резервным копированием. Рекомендуется в README.md файл вложить результаты (текст или скриншоты) проверки работы репликации и резервного копирования.

___________________________________________________________________________________________________________________
# Домашняя работа

Команда ```vagrant up``` поднимает 3 сервера - master, slave и backup,  также запускает ansible-playbook.
Для проверки - выполнить последовательно команды.

Вход на master

```vagrant ssh master```

Далее под пользователем postgres создадим БД homework, чтобы на её примере проверить репликацию на slave.
```
sudo su
su - postgres
psql
create database homework;
```

![Img_alt](https://github.com/Edo1993/otus_32/blob/master/img/321.png)

Вход на slave

```vagrant ssh slave```

Под пользователем postgres на slave
```
sudo su
su - postgres
psql
\l
```
Команда ```\l``` - выводит список всех доступных баз.
Корректный ответ, чтобы убедиться, что БД скопирована.

```
                                  List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
-----------+----------+----------+-------------+-------------+-----------------------
 homework  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
(4 rows)
```

![Img_alt](https://github.com/Edo1993/otus_32/blob/master/img/322.png)

На сервере backup проверяем работу barman.

```
sudo su
barman check master
barman replication-status master
```

![Img_alt](https://github.com/Edo1993/otus_32/blob/master/img/323.png)

```
barman switch-wal --archive master
barman backup master
barman list-backup master
```

![Img_alt](https://github.com/Edo1993/otus_32/blob/master/img/324.png)
