```
 select count(1)  from rcce1db.tbl_wl_jnl_b_10001_10 where sys_date_time >= '20241225011012' and sys_date_time < '20241225011018';


```



## 单proxy

复现结果

| 并发（单proxy） | 35          | 40       | 48       | 108      |
| --------------- | ----------- | -------- | -------- | -------- |
| 复现            | 10min未复现 | 6min复现 | 5min复现 | 5min复现 |





### 108并发：

测试7min，即可复现

```
191562,HY000 701,"java.sql.SQLException: Got an error writing xa log, transaction aborted. xid='3-56-1314425b2-676ba526-c', errmsg='XA prepare timeout, gtid log has been written'",线程组 联机支付场景  写入c表1 按6秒转移 笨10001_00 42-1,false,0

270509,HY000 701,"java.sql.SQLException: Got an error writing xa log, transaction aborted. xid='3-56-131444cfb-676ba52f-4', errmsg='XA prepare timeout, gtid log has been written'",线程组 联机支付场景  写入c表1 按6秒转移 笨10002_00 60-1,false,0
```



![image-20241225144158675](/Users/lukatai/Library/Application Support/typora-user-images/image-20241225144158675.png)



### 28并发：

测试9min，无xa_prepare问题

![image-20241225144120241](/Users/lukatai/Library/Application Support/typora-user-images/image-20241225144120241.png)





### 48并发

5min即可复现

![image-20241225144524859](/Users/lukatai/Library/Application Support/typora-user-images/image-20241225144524859.png)



### 40并发

6min复现

```
140738,HY000 701,"java.sql.SQLException: Got an error writing xa log, transaction aborted. xid='3-56-13145c6e7-676bad7b-b', errmsg='XA prepare timeout, gtid log has been written'",线程组 联机支付场景  写入c表1 按6秒转移 笨10001_00 24-1,false,0
```



![image-20241225150409569](/Users/lukatai/Library/Application Support/typora-user-images/image-20241225150409569.png)