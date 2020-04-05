# PostgreSQL
- Настроить hot_standby репликацию с использованием слотов
- Настроить правильное резервное копирование

Для сдачи работы присылаем ссылку на репозиторий, в котором должны обязательно быть Vagranfile и плейбук Ansible, конфигурационные файлы postgresql.conf, pg_hba.conf и recovery.conf, а так же конфиг barman, либо скрипт резервного копирования. Команда "vagrant up" должна поднимать машины с настроенной репликацией и резервным копированием. Рекомендуется в README.md файл вложить результаты (текст или скриншоты) проверки работы репликации и резервного копирования.

___________________________________________________________________________________________________________________
# Домашняя работа

Статьи, которые помогли
1) [Работа с PostgreSQL: настройка и масштабирование - есть хорошая часть про барман](http://www.zaweel.ru/2016/07/postgresql_22.html#barman)

2) [Настройка потоковой репликации PostgreSQL](https://www.dmosk.ru/miniinstruktions.php?mini=postgresql-replication)

3) [ Как создавать резервные копии, восстанавливать и переносить базы данных PostgreSQL с помощью Barman в CentOS 7](https://www.codeflow.site/ru/article/how-to-back-up-restore-and-migrate-postgresql-databases-with-barman-on-centos-7)

4) [Настройка MASTER-SLAVE репликации в PostgreSQL](http://snakeproject.ru/rubric/article.php?art=freebsd_postgresql_master_slave_replication_19.06.18)


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
