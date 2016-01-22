# Practice 8

## Введение в БД

### Расширенные возможности SQL. Представления.
------------------
--посмотрим номер группы, число студентов, факультет и код старосты этой группы.
> SELECT g.group_number, g.student_, g.faculty, s.id  
  FROM students s INNER JOIN groups g ON (s.id = g.steward);

--чтобы не писать постоянно запрос, создадим представление.
> CREATE VIEW Stewards_id AS  
  SELECT g.group_number, g.student_,g.faculty, s.id  
  FROM students s INNER JOIN groups g ON (s.id = g.steward);

--наше представление совпадает с запросом, написанным ранее.
> SELECT *  
> FROM Stewards_id;

--также нам понадобиться представление, с помощью которого мы сможем узнать не только код,но и имя, фамилию старосты.

> CREATE VIEW Stewards AS    
 SELECT s_id.group_number, s_id.student_, s_id.faculty, p.name, p.surname    
FROM persons p INNER JOIN Stewards_id s_id ON (s_id.id = p.id);

> SELECT *    
FROM Stewards    
ORDER BY surname;

--теперь мы сможем узнать имена и фамилии старосты конткретной группы одной  простой командой
> SELECT *    
  FROM Stewards    
  WHERE group_number = '09-308';

Делая выборку из таблицы профессоров, к сожалению, мы можем увидеть только код того или иного преподавателя. Воспользуемся представлением для более подробной информации

> CREATE VIEW professors_ns   
  AS SELECT p.name,p.surname,pr.faculty, pr.classes,pr.department  
  FROM persons p INNER JOIN professors pr  
  ON (pr.id_person = p.id);  

--теперь мы можем увидеть не только код преподавателей, но и имя и фамилию) остортированные по возрастанию
> SELECT *  
  FROM professors_ns  
  WHERE department ILIKE 'КТП'  
  ORDER BY surname ;  

>  SELECT *     
   FROM professors_ns;