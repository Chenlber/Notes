## 使用sysbench自带的造数

### 自己机器上的实验

您好！您的TDSQL已申请成功，感谢您对DevCloud的支持。

IP:9.134.254.214，端口：3306，用户名：tdsql_admin，密码：TYdXA*4679zYgn，如果忘记，可重置密码。

用户：lukatai 密码 Root@123



```
sysbench --threads=1 --time=60 --report-interval=1 /usr/share/sysbench/oltp_read_write.lua  --mysql-host=9.134.254.214 --mysql-port=3306 --mysql-user=lukatai --mysql-password=Root@123 --mysql-db=chengde  --tables=2 --table_size=1000 prepare
```



### 在集群实例上实验



```
sysbench --threads=1 --time=60 --report-interval=1 /home/sysbench-master/src/lua/oltp_read_only.lua  --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15018 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=yinhe tables=2 prepare
```





## 如何设置造数的类型呢？

```lua
#!/usr/bin/env sysbench
-- -------------------------------------------------------------------------- --
-- Bulk insert benchmark: do multi-row INSERTs concurrently in --threads
-- threads with each thread inserting into its own table. The number of INSERTs
-- executed by each thread is controlled by either --time or --events.
-- -------------------------------------------------------------------------- --

require("oltp_common")

cursize=0

function thread_init()
   drv = sysbench.sql.driver()
   con = drv:connect()
end

function event()
--    for i =1 , 108 do
--     -- con:query("INSERT INTO stk_trdacct_bak ( cust_code, CUACCT_bak_code, int_org, market, board, trdacct, trdacct_type, trdacct_excls, trdacct_name, trdacct_status, treg_status, credit_flag, stkpbu, open_date, close_date, update_time) VALUES ('%s', '%s', %d, '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', %d, %d, NOW()) ]], sysbench.rand.string('abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789', 20), sysbench.rand.string('abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789', 20), sysbench.rand.uniform(1, 99999), string.char(sysbench.rand.uniform(65, 67)), string.format('%02d', sysbench.rand.uniform(1, 3)), sysbench.rand.string('abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789', 20), string.char(sysbench.rand.uniform(65, 67)), string.char(sysbench.rand.uniform(65, 67)), sysbench.rand.string('abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789', 32), string.char(sysbench.rand.uniform(65, 67)), string.char(sysbench.rand.uniform(65, 67)), string.char(sysbench.rand.uniform(65, 67)), sysbench.rand.string('abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789', 8), sysbench.rand.uniform(20000101, 20230101), sysbench.rand.uniform(20000101, 20230101);")
--    end

    for i = 1, 10, 1 do
       print("select"..i)
        con:query("CREATE TABLE IF NOT EXISTS stk_trdacct_bak"..i.." ( cust_code VARCHAR(20) COLLATE utf8mb4_bin NOT NULL, CUACCT_bak_code VARCHAR(20) COLLATE utf8mb4_bin NOT NULL, int_org DECIMAL(5, 0) NOT NULL, market CHAR(1) COLLATE utf8mb4_bin NOT NULL, board CHAR(2) COLLATE utf8mb4_bin NOT NULL, trdacct VARCHAR(20) COLLATE utf8mb4_bin NOT NULL, trdacct_type CHAR(1) COLLATE utf8mb4_bin NOT NULL, trdacct_excls CHAR(1) COLLATE utf8mb4_bin NOT NULL, trdacct_name VARCHAR(32) COLLATE utf8mb4_bin NOT NULL, trdacct_status CHAR(1) COLLATE utf8mb4_bin NOT NULL, treg_status CHAR(1) COLLATE utf8mb4_bin NOT NULL, credit_flag CHAR(1) COLLATE utf8mb4_bin NOT NULL, stkpbu VARCHAR(8) COLLATE utf8mb4_bin NOT NULL, open_date DECIMAL(10, 0) NOT NULL, close_date DECIMAL(10, 0) NOT NULL, update_time TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP, id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY (id) ) ENGINE=InnoDB AUTO_INCREMENT=3722666 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin;")        
    end
end

execute_once()


function thread_done(thread_9d)
   con:bulk_insert_done()
   con:disconnect()
end
```



```
sysbench --threads=1 --time=10 --report-interval=1 /home/sysbench-master/src/lua/yinhe_createData.lua  --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15018 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=yinhe run
```



仅执行一次 --events=1 

```
sysbench --threads=1 --events=1 --report-interval=1 /home/sysbench-master/src/lua/yinhe_createData.lua  --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15018 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=yinhe run
```



## 根据表结构造数

固定数据

```
 INSERT INTO stk_trdacct_bak (
        cust_code, CUACCT_bak_code, int_org, market, board, trdacct, trdacct_type, 
        trdacct_excls, trdacct_name, trdacct_status, treg_status, credit_flag, 
        stkpbu, open_date, close_date, update_time
      ) VALUES （wqe,qwe,1,w,we,qwer,q,q,w,w,w,w,qwe,12,122,now());
```

随机数据

```
require("oltp_common")
function thread_init()
    drv = sysbench.sql.driver()
    con = drv:connect()
end

function thread_done()
    con:disconnect()
end

math.randomseed(os.time())

function generate_random_string(length)
    local charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
    local result = {}
    for i = 1, length do
        local rand_index = math.random(1, #charset)
        table.insert(result, charset:sub(rand_index, rand_index))
    end
    return table.concat(result)
end

-- function generate_random_number_string(length)
--     local charset = "0123456789"
--     local result = {}
--     for i = 1, length do
--         local rand_index = math.random(1, #charset)
--         table.insert(result, charset:sub(rand_index, rand_index))
--     end
--     return table.concat(result)
-- end

function event()
    for i = 1, 3600000 do
        local query = string.format([[
      INSERT INTO stk_trdacct_bak (
        cust_code, CUACCT_bak_code, int_org, market, board, trdacct, trdacct_type,
        trdacct_excls, trdacct_name, trdacct_status, treg_status, credit_flag,
        stkpbu, open_date, close_date, update_time
      ) VALUES ('%s', '%s', %d, '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', %d, %d, NOW())
    ]], generate_random_string(20), generate_random_string(20), math.random(1, 99999), generate_random_string(1),
            generate_random_string(2), generate_random_string(20), generate_random_string(1), generate_random_string(1),
            generate_random_string(32), generate_random_string(1), generate_random_string(1), generate_random_string(1),
            generate_random_string(8), math.random(20000101, 20230101), math.random(20000101, 20230101)
        )
        con:query(query)
    end
end

```

仅保留主键索引，插入360w数据耗时(咋还使用的单线程，怪不得慢)

```
SQL statistics:
    queries performed:
        read:                            0
        write:                           3600000
        other:                           0
        total:                           3600000
    transactions:                        1      (0.00 per sec.)
    queries:                             3600000 (1293.58 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      0.0004
    time elapsed:                        2782.9667s
    total number of events:              1

Latency (ms):
         min:                              2782966.21
         avg:                              2782966.21
         max:                              2782966.21
         95th percentile:                   100000.00
         sum:                              2782966.21

Threads fairness:
    events (avg/stddev):           1.0000/0.00
    execution time (avg/stddev):   2782.9662/0.00
```





```
sysbench --threads=16 --events=1 --report-interval=1 /home/sysbench-master/src/lua/yinhe_createData2.lua  --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15018 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=yinhe run
```

```
sysbench --threads=64 --events=1 --report-interval=1 /home/sysbench-master/src/lua/yinhe_createData.lua  --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15012 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=yinhe run
```



---





devcloud

```
sysbench --threads=1 --events=1 --report-interval=1 /usr/share/sysbench/oltp_common_yinhe.lua  --mysql-host=9.134.254.214 --mysql-port=3306 --mysql-user=tdsql_admin --mysql-password=TYdXA*4679zYgn --mysql-db=lukatai prepare
```







## 多线程造数

目前的数据，还不足以





