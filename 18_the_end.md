1. Информация о всех гномах, входящих в отряд, вместе с информацией об отряде.

``` sql
select * from dwarves d inner join squads s on d.squad_id = s.squad_id;
```

2. Выдать всех гномов с профессией "miner", не входящих ни в один отряд

``` sql
select * from dwarves where profession = 'miner' and squad_id is NULL;
```

3. Получить все задачи с наивысшим приоритетом, которые находятся в статусе
   "pending".

``` sql
select * from tasks where status = 'pending' and pripority = (
    select max(priority) from tasks
)
```

4. Подсчитать количество предметов, которыми владеет гном, для тех гномов,
   которые владеют хотя бы одним предметом

``` sql
select d.name, count(*) item_amount
from dwarves d
inner join items on items.owner_id = d.dwarf_id
group by d.dwarf_id
```

5. Выдать все отряды и количество гномов в них. В том числе и те отряды, в
   которых гномов нет

``` sql
select s.squad_id, count(*) from squads s left join dwarves d on s.squad_id = d.dwarf_id group by s.squad_id;
```

6. Получить список профессий с наибольшим количеством незавершённых задач
   ("pending" и "in_progress") у гномов этих профессий.

``` sql
select d.profession from dwarves d
inner join tasks t on d.dwarf_id = t.assigned_to
group by d.profession
having count(*) = (
    select count(*) from tasks t
    where t.status in ('pending', 'process')
	group by t.task_id
);
```

7. Для каждого типа предмета указать средний возраст гнома, владеющего ими

``` sql
select i.type, avg(d.age) from items i
inner join dwarves d on i.owner_id = d.dwarf_id
group by i.type;
```

8. Найти всех гномов старше среднего возраста (по всем гномам в базе), не владеющими никакими предметами.

``` sql
select d.dwarf_id from dwarves d left join items i on d.dwarf_id = i.owner_id
where d.age > (
    select avg(d.age) from dwarves d
) and i.owner_id is null;
```
