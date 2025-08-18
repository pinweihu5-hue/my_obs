
[[db_idx]]


inner join, left join and right join

- 内连接 (inner Join)：这是最常用的连接类型，它返回两个表中满足连接条件的匹配行。如果某个行在一个表中有匹配行而在另一个表中没有，则这些行不会出现在结果集中。例如，如果我们有两个表：orders（包含订单信息）和customers(包含客户信息)，我们可以使用内连接来找出每个订单对应的客户信息。

- 左连接(Left Join)：这种连接会返回左表的所有行，即使右表中没有匹配的行。如果在右表中没有匹配的行，则结果集中的右表列将包含 NULL 值。左连接通常用于从主表中检索数据，并获取与之相关的任何附加信息。

- 右连接 (Right Join)：与左连接相反，右连接返回右表的所有行，即使左表中没有匹配的行。如果在左表中没有匹配的行，则结果集中的左表列将包含 NULL 值。右连接通常用于从辅助表中检索数据，并获取与之相关的任何主要信息。


有關如何 handle join key 上的 null 情況下，要不要撈出來


```sql

-- 這都是有關如何 handle join key 上的 null 情況下，要不要撈出來

-- Insert some sample data
INSERT INTO departments (department_name) VALUES
    ('HR'),
    ('IT'),
    ('Finance'),
    ('RD');


-- employees 有 fk to departments, 因此 依賴於 departments
INSERT INTO employees (employee_name, department_id) VALUES
    ('Alice', 1),
    ('Bob', 2),
    ('Charlie', 3),
    ('David', NULL);



-- left join, 會以左邊的table 為主，如果是 null, 一樣撈
-- 這個例子來說，employees is left table
-- so, David 就算沒有 depart, 還是一樣會撈出來
SELECT employees.employee_id, employees.employee_name, departments.department_name
FROM employees LEFT JOIN departments ON employees.department_id = departments.department_id;


employee_id|employee_name|department_name|
-----------+-------------+---------------+
          1|Alice        |HR             |
          2|Bob          |IT             |
          3|Charlie      |Finance        |
          4|David        |               |
          5|Eve          |               |


-- right join, 會右邊的table 為主，如果是 null, 一樣撈
-- 這個例子來說，departments is right table
-- so, RD 就算沒有 人, 還是一樣會撈出來
SELECT employees.employee_id, employees.employee_name, departments.department_name
FROM employees RIGHT JOIN departments ON employees.department_id = departments.department_id;


employee_id|employee_name|department_name|
-----------+-------------+---------------+
          1|Alice        |HR             |
          2|Bob          |IT             |
          3|Charlie      |Finance        |
           |             |RD             |




-- inner join, 兩都都不是 null 才會撈
SELECT employees.employee_id, employees.employee_name, departments.department_name
FROM employees INNER JOIN departments ON employees.department_id = departments.department_id;

employee_id|employee_name|department_name|
-----------+-------------+---------------+
          1|Alice        |HR             |
          2|Bob          |IT             |
          3|Charlie      |Finance        |




```