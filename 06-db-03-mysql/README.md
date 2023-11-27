# Домашнее задание к занятию 3. «MySQL» - RomanG

## Задача 1

Используя Docker, поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h`, получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из её вывода версию сервера БД.
```
status --ответ на задание 1.1
```
![mysql.task1.1](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/06-db-03-mysql/jpg/mysql.task1.1.jpg)

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.
```
SELECT count(*) FROM orders WHERE price > 300; --здесь получаю ответ на ЗАДАНИЕ 1 вопрос 2
```

![mysql.task1.2](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/06-db-03-mysql/jpg/mysql.task1.2.jpg)


В следующих заданиях мы будем продолжать работу с этим контейнером.

## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:

- плагин авторизации mysql_native_password
- срок истечения пароля — 180 дней 
- количество попыток авторизации — 3 
- максимальное количество запросов в час — 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James".

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.
```
CREATE USER 'test'@'localhost' IDENTIFIED WITH mysql_native_password BY 'test-pass' WITH MAX_QUERIES_PER_HOUR 100 PASSWORD EXPIRE INTERVAL 180 DAY FAILED_LOGIN_ATTEMPTS 3 ATTRIBUTE '{"fname": "James", "lname": "Pretty"}';  --ЗАДАНИЕ 2.1 СОЗДАЮ ПОЛЬЗОВАТЕЛЯ
```
    
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES, получите данные по пользователю `test` и 
**приведите в ответе к задаче**.
```
SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER = 'test'; --ЗАДАНИЕ 2.2 получаю данные по ПОЛЬЗОВАТЕЛЮ
```

![mysql.task2](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/06-db-03-mysql/jpg/mysql.task2.jpg)

## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.
```
SELECT table_schema, table_name, engine FROM information_schema.tables WHERE table_name = 'orders'; --ЗАДАНИЕ №3.1
```

![mysql.task3.1](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/06-db-03-mysql/jpg/mysql.task3.1.jpg)


Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`,
```
SET profiling = 1;
ALTER TABLE `test_dump`.`orders` ENGINE = MyISAM;

SELECT LAST_INSERT_ID();

SHOW PROFILES;

SHOW PROFILE FOR QUERY 0;
```

![mysql.task3.2.1](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/06-db-03-mysql/jpg/mysql.task3.2.1.jpg)

- на `InnoDB`.
```
ALTER TABLE `test_dump`.`orders` ENGINE = InnoDB;

SELECT LAST_INSERT_ID();

SHOW PROFILES;

SHOW PROFILE FOR QUERY 0;
```

![mysql.task3.2.2](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/06-db-03-mysql/jpg/mysql.task3.2.2.jpg)


## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):

- скорость IO важнее сохранности данных;
- нужна компрессия таблиц для экономии места на диске;
- размер буффера с незакомиченными транзакциями 1 Мб;
- буффер кеширования 30% от ОЗУ;
- размер файла логов операций 100 Мб.

Приведите в ответе изменённый файл `my.cnf`.

```
innodb_flush_log_at_trx_commit = 2
innodb_file_per_table = 1
innodb_log_buffer_size = 1M
innodb_buffer_pool_size = 30% of total memory
innodb_log_file_size = 100M
```

![mysql.task4](https://github.com/RomanVol1/bd-dev-homeworks/blob/main/06-db-03-mysql/jpg/mysql.task4.jpg)


---

### Как оформить ДЗ

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

