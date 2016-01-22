# Lection 4

## Введение в БД

### Создание, редактирование и удаление таблиц.
---------------------

Таблицы похожи на таблицы на бумаге. Количество столбцов и имена фиксированны. Количество строк зависит от вставленных данных.
Подключимся к консоли:
> psql -U postgres  

После подключения к консоли мы не находимся в БД. К ней нам необходимо подключиться (\connect):  
> \c dbName   

Кстати, возможно, он выведет ошибку на этапе подключения к консоли с ошибкой, что не выбрана БД:  

> psql -U userName -d dbName

Выведем список таблиц (\desribe):
> \d  

На данный момент у нас нет таблиц в БД, так что ответ будет пустой.

Так же мы можем посмотреть описание отдельной таблицы:  
> \d tableName

К именованию таблиц есть два подхода - наименование сущности в единственном, либо множественном числе. Оба подхода имеют место быть. Важно придерживаться одного из них и не смешивать. Например, в Ruby on Rails используется множественное число для имен таблиц. Мы будем использовать этот же подход.

**<u>Оператор CREATE:</u>**  
Для создания таблиц используется команда CREATE TABLE.
На данный момент нам достаточно следующего понимания команды:  
> CREATE TABLE tblName (
	col1Name col1Type [, …]
);


Например:

> CREATE TABLE students (   
	id SERIAL,  
	id_person,  
	sex CHAR(1),  
	group_id INTEGER,  
	town VARCHAR(50));

Писать в консоли такие скрипты не удобно. Поэтому есть альтернативный способ - загружать скрипт из файла в БД:
> psql -U postgres dbName < filename.sql

<i>ВАЖНО! При выполнении DROP TABLE данные теряются!  
ЗАМЕЧАНИЕ. PostgreSQL, в отличие от, например, MySQL, является СУБД со строгой типизацией. И при попытке вставить текстовое значение в числовое поле не будет неявных преобразований типов (что соответствует стандарту SQL92). И при попытке сменить тип поля необходимо будет использовать специальные механизмы явного преобразования типов.  </i>

**<u> Оператор ALTER: </u>**


Для различных модификаций таблиц используется команда ALTER TABLE.   Например:  

-- добавление столбца  
> ALTER TABLE tblName ADD COLUMN colName colType;  
ALTER TABLE tblName DROP COLUMN colName;   

-- добавление именованного ограничения (UNIQUE, PRIMARY KEY)  
> ALTER TABLE tblName ADD CONSTRAIN cnstrName UNIQUE (colName);  
ALTER TABLE tblName DROP CONSTRAIN cnstrName;  

-- изменение NOT NULL   
> ALTER TABLE tblName ALTER COLUMN colName SET NOT NULL;  
ALTER TABLE tblName ALTER COLUMN colName DROP NOT NULL;

-- изменение DEFAULT  
> ALTER TABLE tblName ALTER COLUMN colName SET DEFAULT 42;  
ALTER TABLE tblName ALTER COLUMN colName DROP DEFAULT;  

-- изменение типа столбца  
> ALTER TABLE tblName ALTER COLUMN colName TYPE typeName;  

-- переименование столбца  
> ALTER TABLE tblName RENAME COLUMN oldName TO newName;  

-- переименование таблицы  
> ALTER TABLE tblName RENAME TO newTableName;  

<i>ЗАМЕТКА. Команды можно перечислять через запятую в рамках одного ALTER TABLE: </i> 
> ALTER TABLE products  
ADD COLUMN colName colType,  
ALTER COLUMN someOtherCol SET DEFAULT 1;  
  
