

## CONNECT BY 与 START WITH ；WITH RECURSIVE

orcle支持CONNECT BY 与 START WITH 

pg和tdsql支持WITH RECURSIVE

### CONNECT BY 与 START WITH

`CONNECT BY` 是 Oracle 数据库中的一个关键字，用于实现层次查询（Hierarchical Query）。层次查询允许你在一个表中查找具有父子关系的数据，例如组织结构、产品分类、文件目录等。

`CONNECT BY` 子句通常与 `START WITH` 子句一起使用，`START WITH` 指定查询的起始行，`CONNECT BY` 指定如何递归地查找子行。

以下是一个基本的层次查询的语法：

```sql
SELECT column1, column2, ...
FROM table_name
START WITH condition1
CONNECT BY condition2;
```

- `START WITH condition1`：指定查询的起始行。
- `CONNECT BY condition2`：指定如何递归地查找子行。

### 示例

假设有一个员工表 `employees`，其中包含员工的ID、姓名和经理的ID（即员工的上级）。

```sql
CREATE TABLE employees (
    employee_id NUMBER PRIMARY KEY,
    employee_name VARCHAR2(100),
    manager_id NUMBER
);

INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (1, 'Alice', NULL);
INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (2, 'Bob', 1);
INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (3, 'Charlie', 1);
INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (4, 'David', 2);
INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (5, 'Eve', 2);
```

在这个表中，Alice 是最高层的经理，Bob 和 Charlie 是 Alice 的下属，David 和 Eve 是 Bob 的下属。

你可以使用 `CONNECT BY` 来查询这个层次结构：

```sql
SELECT employee_id, employee_name, LEVEL
FROM employees
START WITH manager_id IS NULL
CONNECT BY PRIOR employee_id = manager_id;
```

在这个查询中：

- `START WITH manager_id IS NULL` 指定从最高层的经理（即没有上级的员工）开始。
- `CONNECT BY PRIOR employee_id = manager_id` 指定如何递归地查找子行。`PRIOR` 关键字表示在递归过程中，当前行的 `employee_id` 应该等于上一行的 `manager_id`。

`LEVEL` 是一个伪列，用于表示当前行在层次结构中的深度。最高层的行的 `LEVEL` 为 1，下一层的行的 `LEVEL` 为 2，依此类推。

### 输出结果

```
EMPLOYEE_ID  EMPLOYEE_NAME  LEVEL
-----------  -------------  -----
1            Alice          1
2            Bob            2
3            Charlie        2
4            David          3
5            Eve            3
```

这个结果展示了员工的层次结构，Alice 在最高层，Bob 和 Charlie 在第二层，David 和 Eve 在第三层。

通过使用 `CONNECT BY`，你可以方便地查询和处理具有层次关系的数据。

### WITH RECURSIVE

`WITH RECURSIVE` 是 SQL 标准中的一个关键字，用于编写递归公用表表达式（CTE，Common Table Expression）。递归 CTE 允许你在 SQL 查询中定义递归查询，以便处理层次结构或树形结构的数据。递归 CTE 在许多现代关系数据库管理系统（RDBMS）中都得到了支持，包括 PostgreSQL、MySQL（从版本 8.0 开始）、SQL Server（从版本 2005 开始）等。

递归 CTE 的基本语法如下：

```sql
WITH RECURSIVE cte_name (column1, column2, ...) AS (
    -- 非递归部分（锚点成员）
    initial_query
    UNION ALL
    -- 递归部分（递归成员）
    recursive_query
)
SELECT * FROM cte_name;
```

- `cte_name` 是公用表表达式的名称。
- `initial_query` 是非递归部分，通常用于定义递归的起始点。
- `recursive_query` 是递归部分，引用 `cte_name` 并递归地处理数据。

### 示例

假设有一个员工表 `employees`，其中包含员工的ID、姓名和经理的ID（即员工的上级）。

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100),
    manager_id INT
)TDSQL_DISTRIBUTED BY HASH(`employee_id`);

INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (1, 'Alice', NULL);
INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (2, 'Bob', 1);
INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (3, 'Charlie', 1);
INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (4, 'David', 2);
INSERT INTO employees (employee_id, employee_name, manager_id) VALUES (5, 'Eve', 2);
```

在这个表中，Alice 是最高层的经理，Bob 和 Charlie 是 Alice 的下属，David 和 Eve 是 Bob 的下属。

你可以使用 `WITH RECURSIVE` 来查询这个层次结构：

```sql
WITH RECURSIVE employee_hierarchy AS (
    -- 非递归部分：选择最高层的经理
    SELECT employee_id, employee_name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL
    UNION ALL
    -- 递归部分：选择下属员工
    SELECT e.employee_id, e.employee_name, e.manager_id, eh.level + 1
    FROM employees e
    INNER JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT employee_id, employee_name, manager_id, level
FROM employee_hierarchy;
```

在这个查询中：

- 非递归部分选择最高层的经理（即没有上级的员工），并将其层次级别设为 1。
- 递归部分选择下属员工，并将其层次级别设为上级员工的层次级别加 1。

### 输出结果

```
employee_id  employee_name  manager_id  level
-----------  -------------  ----------  -----
1            Alice          NULL        1
2            Bob            1           2
3            Charlie        1           2
4            David          2           3
5            Eve            2           3
```

这个结果展示了员工的层次结构，Alice 在最高层，Bob 和 Charlie 在第二层，David 和 Eve 在第三层。

通过使用 `WITH RECURSIVE`，你可以方便地查询和处理具有层次关系的数据。递归 CTE 提供了一种简洁而强大的方式来处理递归查询，而不需要编写复杂的存储过程或递归函数。