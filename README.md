### Задача 1
Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.  
Приведите получившуюся команду или docker-compose-манифест.  
[docker-compose.yml](https://github.com/Svalker1989/PostgreSQL/blob/main/docker-compose.yml)  
  
### Задача 2
В БД из задачи 1:  
* создайте пользователя test-admin-user и БД test_db;
* в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
* предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
* создайте пользователя test-simple-user;
* предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.
Таблица orders:  
  
id (serial primary key);  
наименование (string);  
цена (integer).  

Таблица clients:  
  
id (serial primary key);  
фамилия (string);  
страна проживания (string, index);  
заказ (foreign key orders).  
Приведите:  
  
* итоговый список БД после выполнения пунктов выше;
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z2_1.PNG)  
* описание таблиц (describe);
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z2_2___.PNG)  
* SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z2_3_.PNG)  
* список пользователей с правами над таблицами test_db.
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z2_4.PNG)
### Задача 3
Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:  
  
Таблица orders
  
```
Наименование	цена  
Шоколад	10  
Принтер	3000  
Книга	500  
Монитор	7000  
Гитара	4000
```  
Таблица clients  
  
```
ФИО	Страна проживания
Иванов Иван Иванович	USA
Петров Петр Петрович	Canada
Иоганн Себастьян Бах	Japan
Ронни Джеймс Дио	Russia
Ritchie Blackmore	Russia
```
Используя SQL-синтаксис:  
вычислите количество записей для каждой таблицы.  
Приведите в ответе:  
- запросы,  
- результаты их выполнения.
Наполняем orders  
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z3_2.PNG)  
Наполняем clients  
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z3_1.PNG)
Количество записей для каждой таблицы  
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z3_3.PNG)  
  
### Задача 4
Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.  
Используя foreign keys, свяжите записи из таблиц, согласно таблице:  
```
ФИО	Заказ
Иванов Иван Иванович	Книга
Петров Петр Петрович	Монитор
Иоганн Себастьян Бах	Гитара
```
Приведите SQL-запросы для выполнения этих операций.  
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z4_1.PNG)  
Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.  
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z4_2.PNG)  
Подсказка: используйте директиву UPDATE.  
  
### Задача 5
Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).
![](https://github.com/Svalker1989/PostgreSQL/blob/main/Z5.PNG)  
Приведите получившийся результат и объясните, что значат полученные значения.
Ответ:  
Seq Scan последовательное сканирование записей  
cost - стоимость запроса. Это время, которое проходит, прежде чем начнётся этап вывода данных, например для сортирующего узла это время сортировки.  
rows - Ожидаемое число строк, которое должен вывести этот узел плана. При этом так же предполагается, что узел выполняется до конца.  
width - Ожидаемый средний размер строк, выводимых этим узлом плана (в байтах).  
filter -  Это означает, что узел плана проверяет это условие для каждого просканированного им узла и выводит только те строки, которые удовлетворяют ему.  
### Задача 6
Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).
Бэкапим так:  
В контейнере  
`pg_dumpall -U postgres > /tmp/postgres/backup/test-db_1.out`  
Из хостовой ВМ  
`docker exec 2e9094901a93 pg_dumpall -U postgres  > /root/PostgreSQL/db-backup/test-db_1.out`  
Остановите контейнер с PostgreSQL, но не удаляйте volumes.  
`docker stop 2e9094901a93`  
Поднимите новый пустой контейнер с PostgreSQL.  
Для создания нового пустого контейнера без удаления старого, поменял имя контейнера и сервиса.  
`docker-compose -f ./docker-compose_2.yml up -d`  
Восстановите БД test_db в новом контейнере.  
`docker exec 61ebe0c86404 psql -U postgres -f /tmp/postgres/backup/test-db_1.out postgres`
Приведите список операций, который вы применяли для бэкапа данных и восстановления.
