## 死锁的log长什么样子



如何构造一个死锁

```
select * from tt;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
+----+------+
```



```
-- Session 1
START TRANSACTION;
SELECT * FROM tt WHERE id = 1 FOR UPDATE;

-- Session 2
START TRANSACTION;
SELECT * FROM tt WHERE id = 2 FOR UPDATE;

-- Session 1
SELECT * FROM tt WHERE id = 2 FOR UPDATE;

-- Session 2
SELECT * FROM tt WHERE id = 1 FOR UPDATE;
```



![image-20240924141510150](/Users/lukatai/Library/Application Support/typora-user-images/image-20240924141510150.png)





