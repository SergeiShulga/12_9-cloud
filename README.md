# 12_9-cloud
### Домашнее задание к занятию «Базы данных в облаке» - "Сергей Шульга"

### Задание 1

Создание кластера
Перейдите на главную страницу сервиса Managed Service for PostgreSQL.

Создайте кластер PostgreSQL со следующими параметрами:

класс хоста: s2.micro, диск network-ssd любого размера;

хосты: нужно создать два хоста в двух разных зонах доступности и указать необходимость публичного доступа, то есть публичного IP адреса, для них;

установите учётную запись для пользователя и базы.

Остальные параметры оставьте по умолчанию либо измените по своему усмотрению.

Нажмите кнопку «Создать кластер» и дождитесь окончания процесса создания, статус кластера = RUNNING. Кластер создаётся от 5 до 10 минут.
Подключение к мастеру и реплике

'''
psql "host=rc1a-jlmupvxu2nxzx70j.mdb.yandexcloud.net,rc1b-bldu8ngmaxzci0hp.mdb.yandexcloud.net \
    port=6432 \
    sslmode=verify-full \
    dbname=db1 \
    user=user1 \
    target_session_attrs=read-write"
'''
![alt text](https://github.com/SergeiShulga/12-5/blob/main/img/001.png)

Используйте инструкцию по подключению к кластеру, доступную на вкладке «Обзор»: cкачайте SSL-сертификат и подключитесь к кластеру с помощью утилиты psql, указав hostname всех узлов и атрибут target_session_attrs=read-write.

Проверьте, что подключение прошло к master-узлу.

select case when pg_is_in_recovery() then 'REPLICA' else 'MASTER' end;
Посмотрите количество подключенных реплик:
select count(*) from pg_stat_replication;
Проверьте работоспособность репликации в кластере
Создайте таблицу и вставьте одну-две строки.
CREATE TABLE test_table(text varchar);
insert into test_table values('Строка 1');
Выйдите из psql командой \q.

Теперь подключитесь к узлу-реплике. Для этого из команды подключения удалите атрибут target_session_attrs и в параметре атрибут host передайте только имя хоста-реплики. Роли хостов можно посмотреть на соответствующей вкладке UI консоли.

Проверьте, что подключение прошло к узлу-реплике.

select case when pg_is_in_recovery() then 'REPLICA' else 'MASTER' end;
Проверьте состояние репликации
select status from pg_stat_wal_receiver;
Для проверки, что механизм репликации данных работает между зонами доступности облака, выполните запрос к таблице, созданной на предыдущем шаге:
select * from test_table;
В качестве результата вашей работы пришлите скриншоты:

1) Созданной базы данных; 2) Результата вывода команды на реплике select * from test_table;.

### Задание 2*
Создайте кластер, как в задании 1 с помощью Terraform.

В качестве результата вашей работы пришлите скришоты:

1) Скриншот созданной базы данных. 2) Код Terraform, создающий базу данных.

Задания, помеченные звёздочкой, — дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.
