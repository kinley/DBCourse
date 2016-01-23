# Practice and Lection 7

## Введение в БД

### Процедуры и функции postgreSQL.
---------------------

Запросы могут производить обобщенное групповое значение полей точно также как и значение одного пол. Это делает с помощью агрегатных функций.

**Агретные функции** -  производят одиночное значение для всей группы таблицы. Имеется список этих функций:

* COUNT - производит номера строк или не-NULL значения полей которые выбрал запрос.
* SUM - производит арифметическую сумму всех выбранных значений данного поля.
* AVG - производит усреднение всех выбранных значений данного поля.
* MAX - производит наибольшее из всех выбранных значений данного поля.
* MIN - производит наименьшее из всех выбранных значений данного поля.

-- узнаем сколько всего студентов учаться в университете.
> SELECT SUM (student_count)                                
  FROM  groups;

--запросим минимальное количество студентов среди всех групп.
>  SELECT MIN(g.student_count)  
   FROM groups g;

--запросим все имеющиеся кафедры и количество профессоров, работающих в них 
 > SELECT DISTINCT  department, prof_count(department) AS i  
  FROM professors;

  **Есть ли в PostgreSQL хранимые процедуры?**
  В чистом виде нет. Однако PostgreSQL имеет очень мощные функции и определяемые пользователями функции,
  которые в своём большинстве совместимы с тем, что в других СУБД называют хранимыми
  процедурами (процедурами и функциями), а в некоторых случаях они могут больше.
  Эти функции могут быть различных типов и могут быть написаны на разных языках программирования.

  Функция может изменять данные или возращать их. Например:

  --функция, которая возвращает количество записей по конкретному значению кафедры
  > CREATE FUNCTION prof_count(str VARCHAR) RETURNS integer AS '    
    DECLARE  
    i integer;  

  >  BEGIN  
    select count(*)  
    from professors p into i  
    where p.department= str;

  >  return i;  
    END;  
    ' LANGUAGE plpgsql;

-- функция, которая заполняет таблицу студенты записями
> CREATE OR REPLACE FUNCTION insert_to_persons(n INOUT integer)AS $$  
  DECLARE  
  BEGIN  
  INSERT INTO persons (name, surname,persons_type) VALUES
  ('Яна', 'Рудковскя','Студент'),  
  ('Булат', 'Хамидуллин','Студент'),  
  ('Ирина', 'Вишневская','Студент'),
  ('Денис','Макаров','Студент'),   
  ('Роман', 'Сафин','Студент'),  
  ('Булат', 'Низамиев','Студент'),  
 ('Булат', 'Сабиров','Студент'),  
 ('Артур','Ганиев','Студент'),  
 ('Татьяна', 'Блинчикова','Студент'),  
 ('Валерий','Федулов','Студент'),  
 ('Михаил','Задорнов', 'Профессор'),  
 ('Азат','Кашапов', 'Профессор'),  
 ('Кристина','Асмус', 'Профессор');  
  END  
  $$  
  LANGUAGE plpgsql;  


Найти и вывести список студентов, прослушавших все курсы, которые читаются  некоторым преподавателем  
1)Узнать, какие курсы вел конкретный преподаватель  
2)Узнать, какие группы были на каждом из этих курсов  
3)Список студентов, состоящих в каждой группе  


> CREATE OR REPLACE FUNCTION two (idd integer)  
  RETURNS TABLE(name VARCHAR, surname VARCHAR ) AS
  $BODY$  
> CREATE OR REPLACE FUNCTION two (idd integer)  
  RETURNS TABLE(name VARCHAR, surname VARCHAR ) AS  
  $BODY$  
  DECLARE  
  rec RECORD;  
  i CURSOR FOR SELECT  group_id  
  FROM prof_clas p_c  
  WHERE p_c.id= idd;  
  BEGIN  
  FOR rec IN EXECUTE 'SELECT p.name, p.surname  
  FROM persons p INNER JOIN (SELECT id_person  
  FROM students  
  WHERE group_id = i) pp  
  ON (p.id = pp.id_person)'  
	LOOP  
		name = rec.name;    
		surname =rec.surname;  
		RETURN next;  
	END LOOP;  
END;  
$BODY$  
  LANGUAGE 'plpgsql' VOLATILE  
  COST 100  
  ROWS 1000;  
ALTER FUNCTION two(idd integer) OWNER TO postgres;  

--сам запрос  
> SELECT *  
  FROM two(5) ;  

--удалим функцию, если она больше не нужна  
> DROP FUNCTION two (idd integer);  
