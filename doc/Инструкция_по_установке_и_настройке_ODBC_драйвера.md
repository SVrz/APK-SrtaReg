## Инструкция по установке и настройке unixODBC драйвера

Для обработки SQL запросов к БД АПК «СРТА-Регистратор» из
технологической сети используется механизм ODBC. В данном решении
используется программный драйвер - unixODBC.

Для установки требуется подключение АПК к сети с доступом к репозитариям
ОС и ПО.

Далее, будут представлены основные шаги по установке, с комментариями
там, где это будет необходимо.

### Основные этапы установки и настройки:

1\. Установка драйвера unixODBC.

2\. Настройка драйвера unixODBC.

3\. Проверка работоспособности ODBC.

<br>

#### 1. Установка драйвера unixODBC

1.1. Обновить apt:
```$xslt
# apt upgrade
```

1.2. Установить необходимые драйвера и ПО:

```$xslt
# apt-get install unixodbc odbc-postgresql
```

1.3. Проверить установленный драйвер:

```$xslt
# odbcinst -q -d
```

выведет:

```$xslt
[PostgreSQL ANSI]
[PostgreSQL Unicode]
```
```$xslt
# odbcinst -j
```

Выведет:

```$xslt
unixODBC 2.3.6

DRIVERS............: /etc/odbcinst.ini
SYSTEM DATA SOURCES: /etc/odbc.ini
FILE DATA SOURCES..: /etc/ODBCDataSources
USER DATA SOURCES..: /home/apk_user/.odbc.ini
SQLULEN Size.......: 8
SQLLEN Size........: 8
SQLSETPOSIROW Size.: 8
```

<br>

#### 2. Настройка драйвера unixODBC

2.1. Вносим настройки в файл **/etc/odbc.ini**

```$xslt
[PostgreSQL-connector\] 
Description = PostgreSQL connection
Driver = PostgreSQL Unicode
Database = logger
Servername = localhost
UserName = postgres
Password = postgres
Port = 5432
Protocol = 13
ReadOnly = No
RowVersioning = No
ShowSystemTables = No
ShowOidColumn = No
FakeOidIndex = No
ConnSettings =
```

Правим в **etc/odbс.ini** в полях (ставим полный путь до драйвера):

```$xslt
Driver = /usr/lib/x86_64-linux-gnu/odbc/psqlodbcw.source
Servername = 127.0.0.1
```

<br>

#### 3. Проверка работоспособности ODBC

3.1. Проверка установок.

```$xslt
# echo "select version()" | isql -v PostgreSQL-connector
```

\- ответ должен отобразить версию ПО (данные могут отличаться в
зависимости от версий)


```$xslt
PostgreSQL 13.8 (Debian 13.8-0+deb11u1) on x86_64-pc-linux-gnu,
compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
```

3.2. Проверка SQL запросом к таблице БД:

```$xslt
isql -v PostgreSQL-connector
```


\- выбрать все записи из таблицы данных “log\_srta”

```$xslt
SELECT * FROM log_srta;
```

Если механизм настроен и готов к работе, система выведет данные из
таблицы.

Разработчиками ПО АПК «СРТА-Регистратор» создана программа для установки
на клиентское оборудование, формирующее постоянное циклическое SQL
обращение к БД АПК для тестирования работы доступа через механизм ODBC в
сетевой инфраструктуре предприятия.
