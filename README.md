# HW-MSSQL

## Задача 1

Создание инстанса БД MSSQL

    docker pull mysql
    
    c:\Oleg\data>docker images
    REPOSITORY                     TAG       IMAGE ID       CREATED        SIZE
    mysql                          latest    0716d6ebcc1a   2 weeks ago    514MB

Образ MySQL создан

Запускаем контейнер, в котором указываем расположение бекапа и данных

    c:\Oleg\data>docker run --name oleg-mysql -e MYSQL_ROOT_PASSWORD=oleg -v c:\oleg\data_mysql:/var/lib/mysql -v c:\oleg\dump_mysql:/home/ -d mysql
    2a212bfab9b3ab06129d83b5449fa3c7e260a028f0ef2ee14a4798c6b4fef8b3
    
    c:\Oleg\data>
    c:\Oleg\data>
    c:\Oleg\data>docker ps -a
    CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS                    PORTS                                       NAMES
    2a212bfab9b3   mysql                    "docker-entrypoint.s…"   4 seconds ago   Up 4 seconds              3306/tcp, 33060/tcp                         oleg-mysql

Подключаемся к контейнеру как в mysql

    docker exec -it oleg-mysql mysql -u root -p

и смотрим базы

    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | sys                |
    +--------------------+
    4 rows in set (0.00 sec)

    mysql>

Создаём базу test_db

    mysql> create database test_db
        -> ;
    Query OK, 1 row affected (0.07 sec)

    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | sys                |
    | test_db            |
    +--------------------+
    5 rows in set (0.00 sec)

Теперь подключаемся к контейнеру в bash и востанавливаем из бекапа

    c:\Oleg\data>docker exec -it oleg-mysql  bash
    root@2a212bfab9b3:/# mysql -u root -p test_db < /home/test_dump.sql.sql
    bash: /home/test_dump.sql.sql: No such file or directory
    root@2a212bfab9b3:/# mysql -u root -p test_db < /home/test_dump.sql
    Enter password:
    root@2a212bfab9b3:/#

Подключаемся к базе и смотрим её содержимое

    mysql> use test_db;
    Reading table information for completion of table and column names
    You can turn off this feature to get a quicker startup with -A
    
    Database changed
    mysql> show tables;
    +-------------------+
    | Tables_in_test_db |
    +-------------------+
    | orders            |
    +-------------------+
    1 row in set (0.00 sec)
    
    mysql> select * from orders;
    +----+-----------------------+-------+
    | id | title                 | price |
    +----+-----------------------+-------+
    |  1 | War and Peace         |   100 |
    |  2 | My little pony        |   500 |
    |  3 | Adventure mysql times |   300 |
    |  4 | Server gravity falls  |   300 |
    |  5 | Log gossips           |   123 |
    +----+-----------------------+-------+
    5 rows in set (0.01 sec)


Список управляющих команд:

    mysql> \h
    
    For information about MySQL products and services, visit:
      http://www.mysql.com/
    For developer information, including the MySQL Reference Manual, visit:
       http://dev.mysql.com/
    To buy MySQL Enterprise support, training, or other products, visit:
      https://shop.mysql.com/
    
    List of all MySQL commands:
    Note that all text commands must be first on line and end with ';'
    ?         (\?) Synonym for `help'.
    clear     (\c) Clear the current input statement.
    connect   (\r) Reconnect to the server. Optional arguments are db and host.
    delimiter (\d) Set statement delimiter.
    edit      (\e) Edit command with $EDITOR.
    ego       (\G) Send command to mysql server, display result vertically.
    exit      (\q) Exit mysql. Same as quit.
    go        (\g) Send command to mysql server.
    help      (\h) Display this help.
    nopager   (\n) Disable pager, print to stdout.
    notee     (\t) Don't write into outfile.
    pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
    print     (\p) Print current command.
    prompt    (\R) Change your mysql prompt.
    quit      (\q) Quit mysql.
    rehash    (\#) Rebuild completion hash.
    source    (\.) Execute an SQL script file. Takes a file name as an argument.
    status    (\s) Get status information from the server.
    system    (\!) Execute a system shell command.
    tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
    use       (\u) Use another database. Takes database name as argument.
    charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
    warnings  (\W) Show warnings after every statement.
    nowarning (\w) Don't show warnings after every statement.
    resetconnection(\x) Clean session context.
    query_attributes Sets string parameters (name1 value1 name2 value2 ...) for the next query to pick up.
    
    For server side help, type 'help contents'
    

Статус Базы Данных

    mysql> \s
    --------------
    mysql  Ver 8.0.26 for Linux on x86_64 (MySQL Community Server - GPL)
    
    Connection id:          13
    Current database:       test_db
    Current user:           root@localhost
    SSL:                    Not in use
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         8.0.26 MySQL Community Server - GPL
    Protocol version:       10
    Connection:             Localhost via UNIX socket
    Server characterset:    utf8mb4
    Db     characterset:    utf8mb4
    Client characterset:    latin1
    Conn.  characterset:    latin1
    UNIX socket:            /var/run/mysqld/mysqld.sock
    Binary data as:         Hexadecimal
    Uptime:                 39 min 18 sec
    
    Threads: 5  Questions: 55  Slow queries: 0  Opens: 183  Flush tables: 3  Open tables: 101  Queries per second avg: 0.023
    --------------
    
Посмотрть таблицы

    mysql> show tables;
    +-------------------+
    | Tables_in_test_db |
    +-------------------+
    | orders            |
    +-------------------+
    1 row in set (0.01 sec)

количество записей с прайс больше 300

    mysql> select * from orders where price > 300;
    +----+----------------+-------+
    | id | title          | price |
    +----+----------------+-------+
    |  2 | My little pony |   500 |
    +----+----------------+-------+
    1 row in set (0.00 sec)
    
    mysql> select count(*) from orders where price > 300;
    +----------+
    | count(*) |
    +----------+
    |        1 |
    +----------+
    1 row in set (0.02 sec)

## Задача 2



        mysql> show variables like 'default_authentication_plugin';
        +-------------------------------+-----------------------+
        | Variable_name                 | Value                 |
        +-------------------------------+-----------------------+
        | default_authentication_plugin | caching_sha2_password |
        +-------------------------------+-----------------------+
        1 row in set (0.21 sec)
        CREATE USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY 'test-pass' PASSWORD EXPIRE INTERVAL 180 DAY;
        
        mysql> select user,host,plugin from mysql.user;
        +------------------+-----------+-----------------------+
        | user             | host      | plugin                |
        +------------------+-----------+-----------------------+
        | root             | %         | caching_sha2_password |
        | test             | %         | mysql_native_password |
        | mysql.infoschema | localhost | caching_sha2_password |
        | mysql.session    | localhost | caching_sha2_password |
        | mysql.sys        | localhost | caching_sha2_password |
        | root             | localhost | caching_sha2_password |
        +------------------+-----------+-----------------------+
        6 rows in set (0.00 sec)
        
        mysql> select user,host,password_last_changed,password_lifetime,password_expired from mysql.user;
        +------------------+-----------+-----------------------+-------------------+------------------+
        | user             | host      | password_last_changed | password_lifetime | password_expired |
        +------------------+-----------+-----------------------+-------------------+------------------+
        | root             | %         | 2021-09-23 15:18:16   |              NULL | N                |
        | test             | %         | 2021-09-23 18:18:38   |               180 | N                |
        | mysql.infoschema | localhost | 2021-09-23 15:18:04   |              NULL | N                |
        | mysql.session    | localhost | 2021-09-23 15:18:04   |              NULL | N                |
        | mysql.sys        | localhost | 2021-09-23 15:18:04   |              NULL | N                |
        | root             | localhost | 2021-09-23 15:18:16   |              NULL | N                |
        +------------------+-----------+-----------------------+-------------------+------------------+
        6 rows in set (0.00 sec)
        
        mysql> select * from Ielect user,host,password_last_changed,password_lifetime,password_expired from mysql.user;
        +------------------+-----------+-----------+-----------+-------------------+------------------+
        | USER             | HOST      | ATTRIBUTE |st_changed | password_lifetime | password_expired |
        +------------------+-----------+-----------+-----------+-------------------+------------------+
        | root             | %         | NULL      |15:18:16   |              NULL | N                |
        | test             | %         | NULL      |18:18:38   |               180 | N                |
        | mysql.infoschema | localhost | NULL      |15:18:04   |              NULL | N                |
        | mysql.session    | localhost | NULL      |15:18:04   |              NULL | N                |
        | mysql.sys        | localhost | NULL      |15:18:04   |              NULL | N                |
        | root             | localhost | NULL      |15:18:16   |              NULL | N                |
        +------------------+-----------+-----------+-----------+-------------------+------------------+
        6 rows in set (0.01 sec)

Дать права на select

        mysql> GRANT SELECT ON test_db.* to 'test';
        Query OK, 0 rows affected (0.07 sec)
        
        mysql> show grants for test
            -> ;
        +-------------------------------------------+
        | Grants for test@%                         |
        +-------------------------------------------+
        | GRANT USAGE ON *.* TO `test`@`%`          |
        | GRANT SELECT ON `test_db`.* TO `test`@`%` |
        +-------------------------------------------+
        2 rows in set (0.01 sec)

##  Задача 3

        mysql> show profiles;
        Empty set, 1 warning (0.03 sec)
        
        mysql> SET profiling = 1
           -> ;
        Query OK, 0 rows affected, 1 warning (0.02 sec)
        
        mysql> show profiles;
        Empty set, 1 warning (0.02 sec)

        
        mysql> show engines;
        +--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
        | Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
        +--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
        | FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
        | MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
        | InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
        | PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
        | MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
        | MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
        | BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
        | CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
        | ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
        +--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
        9 rows in set (0.05 sec)

По умолчинию в БД test_db используется InnoDB


        mysql> show table status;
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        | Name   | Engine | Version | Row_format | Rows | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         |            Update_time         | Check_time | Collation          | Checksum | Create_options | Comment |
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        | orders | InnoDB |      10 | Dynamic    |    5 |           3276 |       16384 |               0 |            0 |         0 |              6 | 2021-09-23 16:20:55 |            2021-09-23 16:20:55 | NULL       | utf8mb4_0900_ai_ci |     NULL |                |         |
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        1 row in set (0.21 sec)


Смена engine и отработка команд:

        mysql> ALTER TABLE orders engine=MyISAM;
        Query OK, 5 rows affected (0.42 sec)
        Records: 5  Duplicates: 0  Warnings: 0
        
        
        mysql> show table status;
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        | Name   | Engine | Version | Row_format | Rows | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         |            Update_time         | Check_time | Collation          | Checksum | Create_options | Comment |
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        | orders | MyISAM |      10 | Dynamic    |    5 |           3276 |       16384 |               0 |            0 |         0 |              6 | 2021-09-24 06:07:08 |        2021-09-23 16:20:55 | NULL       | utf8mb4_0900_ai_ci |     NULL |                |         |
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        1 row in set (0.01 sec)
        mysql> show profiles;
        +----------+------------+----------------------------------+
        | Query_ID | Duration   | Query                            |
        +----------+------------+----------------------------------+
        |        1 | 0.05327150 | show engines                     |
        |        2 | 0.20050525 | show table status                |
        |        3 | 0.00414100 | alter table orders type = MyISAM |
        |        4 | 0.00386075 | ALTER TABLE orders TYPE=MyISAM   |
        |        5 | 0.42798200 | ALTER TABLE orders engine=MyISAM |
        |        6 | 0.00871600 | show table status                |
        +----------+------------+----------------------------------+
        6 rows in set, 1 warning (0.00 sec)
        
        mysql> ALTER TABLE orders engine=InnoDB;
        Query OK, 5 rows affected (0.23 sec)
        Records: 5  Duplicates: 0  Warnings: 0
        
        mysql> show profiles;
        +----------+------------+----------------------------------+
        | Query_ID | Duration   | Query                            |
        +----------+------------+----------------------------------+
        |        1 | 0.05327150 | show engines                     |
        |        2 | 0.20050525 | show table status                |
        |        3 | 0.00414100 | alter table orders type = MyISAM |
        |        4 | 0.00386075 | ALTER TABLE orders TYPE=MyISAM   |
        |        5 | 0.42798200 | ALTER TABLE orders engine=MyISAM |
        |        6 | 0.00871600 | show table status                |
        |        7 | 0.23380275 | ALTER TABLE orders engine=InnoDB |
        +----------+------------+----------------------------------+
        7 rows in set, 1 warning (0.00 sec)
        
        mysql> show table status;
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        | Name   | Engine | Version | Row_format | Rows | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         |        Update_time         | Check_time | Collation          | Checksum | Create_options | Comment |
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        | orders | InnoDB |      10 | Dynamic    |    5 |           3276 |       16384 |               0 |            0 |         0 |              6 | 2021-09-24 06:08:48 |        2021-09-23 16:20:55 | NULL       | utf8mb4_0900_ai_ci |     NULL |                |         |
        +--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+--      -------------------+------------+--------------------+----------+----------------+---------+
        1 row in set (0.01 sec)


##  адача 4


        root@2a212bfab9b3:/etc/mysql# dir
        conf.d  my.cnf  my.cnf.fallback
        root@2a212bfab9b3:/etc/mysql# cat my.cnf
        # Copyright (c) 2017, Oracle and/or its affiliates. All rights reserved.
        #
        # This program is free software; you can redistribute it and/or modify
        # it under the terms of the GNU General Public License as published by
        # the Free Software Foundation; version 2 of the License.
        #
        # This program is distributed in the hope that it will be useful,
        # but WITHOUT ANY WARRANTY; without even the implied warranty of
        # MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
        # GNU General Public License for more details.
        #
        # You should have received a copy of the GNU General Public License
        # along with this program; if not, write to the Free Software
        # Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301 USA
        
        #
        # The MySQL  Server configuration file.
        #
        # For explanations see
        # http://dev.mysql.com/doc/mysql/en/server-system-variables.html
        
        [mysqld]
        pid-file        = /var/run/mysqld/mysqld.pid
        socket          = /var/run/mysqld/mysqld.sock
        datadir         = /var/lib/mysql
        secure-file-priv= NULL
        
        # Custom config should go here
        !includedir /etc/mysql/conf.d/
        root@2a212bfab9b3:/etc/mysql#

Добавление в конфигурационный файл:

        innodb_flush_log_at_trx_commit = 2
 - Скорость IO важнее сохранности данных

        innodb_file_per_table = 1
 - Включение данного параметра требуется в случаях необходимости:
     . освобождения места на диске при удалении таблиц (общий файл
        может только увеличиваться)
     . компрессии таблиц для экономии места на диске

        innodb_log_buffer_size = 1M
 - Размер буффера с незакомиченными транзакциями 1 Мб

        innodb_buffer_pool_size = 40G примерно 30% от хостовой машины
 - Буффер кеширования 30% от ОЗУ


        innodb_log_file_size = 100 M
 - Размер файла логов операций 100 Мб




