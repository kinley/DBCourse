# Lection 5
---------------------

## Введение в БД
---------------------

### Ограничение значений ваших данных (PostgreSQL)
---------------------
**Транзакция** - неделимая последовательность операций манипулирования данными, выполняющаяся как единое целое и переводящая базу данных из одного целостного состояния в другое целостное состояние.

****
**ACID** - акроним, который описывает требования к транзакционной системе.

* Atomicity – Атомарность. Такое требование гарантирует, что никакая транзакция не будет зафиксирована в системе частично. Будут либо выполнены все её подоперации, либо не выполнено ни одной.
*	Consistency – Согласованность. Здесь имеется в виду согласованность данных друг с другом, целостность данных, а также внутренняя непротиворечивость. Целостность базы данных означает соответствие имеющейся в базе данных информации её внутренней логике, структуре и всем явно заданным правилам. каждая успешная транзакция, сохраняет только допустимый результат.
*	Isolation – Изолированность. Во время выполнения транзакции параллельные транзакции не должны оказывать влияние на её результат.
*	Durability - Долговечность. Независимо от проблем на нижних уровнях (к примеру, обесточивание системы или сбои в оборудовании) изменения, сделанные успешно завершённой транзакцией, должны остаться сохранёнными после возвращения системы в работу.

### Ограничения
---------------------
Вернемся к нашему примеру: table_ students: name, surname, group_id;  
При работе с данной таблицей могут встретиться 3 ситуации:  
1)	Данные, которых нет  
2)	Данные, которых не стало  
3)	Вообще нет данных  



###Решение:  
Использовать ограничения таблицы и строки.  

**Ограничения** - это часть определений таблицы, ограничивающее значения, которые вы можете вводить в столбцы. Раньше единственным ограничением, которые вы могли вводить, были тип данных и размер вводимых значений, совместимых с теми столбцами в которые эти значения помещаются.  

 *Column constr:*  
1.	NOT NULL – данных не быть не может.  
2.	Unique – уникальность (данные паспорта).  
3.	CHECK- (возраст не может быть меньше 0, равным 0, больше 150)  
  Check(expr): (colName>0) and (colname<180)  
4.	Default (funct(int x=0))  
Ограничениям можно давать имена. Constraint price_check CHECK (price>0)  
5.	Primary key(not null and unique).  
6.	References(colName)  
> Student name Varchar not null default  
Passport char(10) not null unuque

*Constraints (ограничения на таблицу):*  
1.	Primary key(c1 или множество ключей c2,c3,…-при отношении много ко многим).  
2.	Unique (c1,c2) когда пары отношений не могут совпадать   
3.	Foreign key (c1,c2) References (ссылка) tblName (c3,c4)  

**Триггер** – что дает? Что мы не можем вставить данные.   Если мы захотели удалить группу.  

* `On delete restrict` (приложению запрещается удалять или изменять (для `on update restrict`) родительский ключ, когда существует один или несколько ссылающихся на него дочерних ключей.);  
* `On delete cascade` (распространяет операции удаления и изменения родительского ключа на зависящие от него дочерние ключи);  
* `On delete set null` (при удалении родительского ключа или его изменении (для `on update set null`) столбцы дочернего ключа будут устанавливаться в значение NULL во всех строках дочерней таблицы, которые ссылаются на удаляемую/изменяемую строку родительской таблицы);  
* `On delete set default` (похоже на set null за тем исключением, что значение каждого столбца дочернего ключа устанавливается не в null а в значение по умолчанию для данного столбца.);  


### Последовательность.  

<i>Мы знаем, чтобы задать значение по умолчанию, необходимо использовать DEFAULT.  
При этом, если задуматься, то задавать PK ручками - не единственное (и далеко не лучшее) решение. По-хорошему, это поле должно формировать автоматически на уровне БД. Есть различные варианты решения этой задачи. Например, в MySQL к полю добавляется модификатор AUTOINCREMENT, за счет чего поле само начинает отвечать за увеличение значений. При этом, это не соответствует спецификации. Стандартом является другое решение, основанное на том, что DEFAULT может принимать не только конкретные значения, а выражения....(line truncated)...
Для этого познакомимся с таким типом данных (который так же является стандартом SQL), как последовательность - sequence. </i>


> CREATE SEQUENCE seqName;  

ЗАМЕТКА. На самом деле, sequence сам по себе тоже таблица, и мы можем сделать к ней запрос и взглянуть на внутреннюю структуру. Посмотрев, можно увидеть, что sequence может быть настроен различным образом.

Как работать с sequence? Для этого есть специализированные функции. Например, nextval. Попробуйте запрос:   

> SELECT * nextval(‘seqName’);   

Теперь это можно применить к нашей таблице, например:  

> CREATE SEQUENCE ‘serial’;   
CREATE TABLE products (   
product_no INTEGER DEFAULT nextval(‘serial’);  
