# Practice 4

## Введение в БД

### Создание, редактирование и удаление таблиц.


> CREATE SEQUENCE serial;--создадим последовательность


>CREATE TABLE example ( --создадим таблицу для примера использования созданной нами последовательности
	id INTEGER DEFAULT nextval('serial')
);

>ALTER TABLE example
ADD COLUMN name VARCHAR;
--добавим столбец в таблицу

>ALTER TABLE example
ADD CONSTRAIN colName UNIQUE (name);
--добавим ему именнованое ограничение - уникальность

>ALTER TABLE example
ALTER COLUMN colName
SET NOT NULL;
--добавим ограничение NOT NULL

>ALTER TABLE example
ALTER COLUMN colName
SET DEFAULT 'Наименование';
--добавим ограничение  DEFAULT

>ALTER TABLE example
>RENAME COLUMN name TO NewName,
RENAME TO newExample;
--переименуем столбец и саму таблицу

>ALTER TABLE newExample
 ALTER COLUMN colName
 DROP DEFAULT;
 -- уберем ограничение DEFAULT

>ALTER TABLE newExample
ALTER COLUMN colName
DROP NOT NULL;
--уберем ограничение NOT NULL

>ALTER TABLE newExample
DROP CONSTRAIN colName;
--уберем ограничение уникальности

>ALTER TABLE newExample
 DROP COLUMN NewName;
 --удалим столбец

>DROP SEQUENCE serial;
--если мы попытаемся удалить последовательность, которая уже используется, у нас это не получится. Поэтому
--удалим сначала таблицу,а затем уже последовательность.
DROP TABLE example;
DROP SEQUENCE serial;
### Обучение в вузе.

> CREATE DATABASE timing;--создадим нашу бд

>CREATE TYPE PERSONS_TYPE AS ENUM ('Профессор', 'Студент');  
--Перечисления (enum) — это такие типы данных, которые состоят из статических, упорядоченных списков значений.

>CREATE TABLE persons (  
id SERIAL PRIMARY KEY,  
name VARCHAR(50) NOT NULL,  
surname VARCHAR(50) NOT NULL,  
persons_type PERSONS_TYPE NOT NULL  
);

>CREATE TYPE GENDER AS ENUM ('М', 'Ж');-- создадим тип для поля пол

>--создадим таблицу студенты  
CREATE TABLE students (  
	id SERIAL PRIMARY KEY,  
	id_person INTEGER UNIQUE,  
	sex GENDER NOT NULL,  
	group_id INTEGER NOT NULL,  
	town VARCHAR(50) NOT NULL DEFAULT 'Казань',  
	CHECK (group_id >= 1),  
	FOREIGN KEY (id_person) REFERENCES persons(id) ON DELETE SET NULL
);

>--создадим таблицу группы  
CREATE TABLE groups (  
	id SERIAL PRIMARY KEY,  
	group_number VARCHAR(10) NOT NULL,   
    student_count INTEGER NOT NULL, --число студентов в группе  
	faculty VARCHAR(50) NOT NULL, --факультет  
	steward INTEGER , --староста (внешний ключ)  
	FOREIGN KEY (steward) REFERENCES students(id)  ON DELETE SET NULL  );  

>   DROP TABLE IF EXISTS groups CASCADE;  
 --если мы на данном этапе удалим таблицу,  придется воспользоваться каскадным удалением  

> --добавим связь. Теперь удаление или изменение родительского ключа распространается на зависящий от него ключ.  
ALTER TABLE students  
ADD CONSTRAINT stud_group  
FOREIGN KEY (group_id) REFERENCES groups(id)  
 ON UPDATE CASCADE ON DELETE SET NULL;  

>--создадим таблицу профессоров  
CREATE TABLE professors (  
	id SERIAL PRIMARY KEY,  
	id_person INTEGER UNIQUE,  
	faculty VARCHAR(50) NOT NULL,  
	classes INTEGER NOT NULL, --REFERENCES classes(id),  
	department VARCHAR(50) NOT NULL );


>CREATE TYPE REPORT_TYPE AS ENUM ('Зачет', 'Экзамен', 'Курсовая');  
CREATE TYPE SUBJECT_TYPE AS ENUM ('Лекция', 'Практика');  

>--создадим таблицу предметы    
CREATE TABLE subjects (  
	id SERIAL PRIMARY KEY,  
	name VARCHAR(50) NOT NULL,  
	report_type REPORT_TYPE NOT NULL,  
	subject_type SUBJECT_TYPE NOT NULL,  
	hours INTEGER NOT NULL  );
 
>--создадим таблицу занятия  
CREATE TABLE classes (  
	id SERIAL PRIMARY KEY,  
	professors_id INTEGER NOT NULL,  
	group_id INTEGER NOT NULL,  
	subject_id INTEGER NOT NULL,  
	semester INTEGER NOT NULL,  
	audience VARCHAR(10) NOT NULL,  
	start_time VARCHAR(5) NOT NULL,  
	FOREIGN KEY (professors_id)  REFERENCES professors(id),  
	FOREIGN KEY (group_id) REFERENCES groups(id),  
	FOREIGN KEY (subject_id) REFERENCES subjects(id) );