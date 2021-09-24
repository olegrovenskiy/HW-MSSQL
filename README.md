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


