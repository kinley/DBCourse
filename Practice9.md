# Practice 9
==========================================================

## Введение в БД

### Создание триггера.
------------------

В таблице профессоров есть полне "нагрузка", которое подразумевает число занятий,
которое ведет профессор. Чтобы постоянно не подсчитывать, сколько занятий у каждого
профессора, создадим триггер, который будет за нас вести счет.

Напишем функцию, которая будет вычислять количество записей для определенного преподавателя
> CREATE OR REPLACE FUNCTION add_to_prof()  
  RETURNS TRIGGER AS $$  
  DECLARE  
  id1 integer;  
  iid integer;  
  count integer;  
  newcount integer;  
  count2 integer;  
  newcount2 integer;  
  BEGIN  
  IF TG_OP = 'INSERT' THEN  
   id1 := NEW.professors_id;  
     SELECT classes  
     FROM professors p INTO count  
     WHERE p.id = id1;  
   newcount := count + 1;  
     UPDATE professors p  
     SET classes = newcount  
     WHERE p.id = id1;  
   RETURN NEW;  
ELSIF TG_OP = 'UPDATE'  
 THEN  
 id1 = OLD.professors_id;  
 iid = NEW.professors_id;  
 IF id1 <> iid THEN  
  count = (SELECT classes FROM  
          professors p  
          WHERE p.id = id1);  
  newcount = count - 1;  
count2 = (SELECT classes  
         FROM professors p  
         WHERE p.id = iid);  
newcount2 = count2 + 1;  
UPDATE professors p   
SET classes = newcount WHERE p.id = id1;  
UPDATE professors p   
SET classes = newcount2 WHERE p.id = iid;   
END IF;  
RETURN NEW;  
ELSEIF TG_OP='DELETE' THEN   
id1 = OLD.professors_id;  
count = (SELECT classes  
FROM professors p WHERE p.id = id1);  
newcount = count - 1;  
UPDATE professors p  
SET classes = newcount  
WHERE p.id = id1;  
RETURN OLD;  
END IF;  
END;  
 $$ LANGUAGE plpgsql;   

 Она понадобится нам для триггера:  
> CREATE TRIGGER t_classes   
  AFTER INSERT OR UPDATE OR DELETE ON  
  classes FOR EACH ROW EXECUTE PROCEDURE add_to_prof();  
 
<i> теперь каждый раз, когда в таблицу занятий будут добавлять/изменять/удалять запись
наш триггер будет вызывать функцию, которая при каждом определенном дейтствии будет изменять
запись преподавателя в таблице профессоров.</i>