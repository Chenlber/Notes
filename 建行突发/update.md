1、分布式场景
2、执行 update带子查询SQL 



db

```
9.40.33.202
9.40.39.196
9.40.42.71
9.40.49.5

```



proxy

```
9.40.30.196
9.40.33.197
9.40.37.138
```



## 一主三备

9.40.30.196 -P15012 -ulukatai -pRoot@@@123 

```
sysbench --mysql-host=9.40.30.196 --mysql-port=15021 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem --range_selects=off --skip_trx=off --tables=10 --table_size=100 --threads=100 --report_interval=1 --time=6000 oltp_common.lua cleanup
sysbench --mysql-host=9.40.30.196 --mysql-port=15021 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem --range_selects=off --skip_trx=off --tables=10 --table_size=100 --threads=100 --report_interval=1 --time=6000 oltp_common.lua prepare
sysbench --mysql-host=9.40.30.196 --mysql-port=15021 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem --range_selects=off --skip_trx=off --tables=10 --table_size=100 --threads=100 --report_interval=1 --time=6000 oltp_read_write.lua run




```



```
sysbench --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15021 --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem --threads=100 --report_interval=1 --time=6000 truncate.lua run
```



log

```
nohup sysbench --mysql-host=9.40.30.196 --mysql-port=15021 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem --range_selects=off --skip_trx=off --tables=10 --table_size=100 --threads=100 --report_interval=1 --time=60000 oltp_read_write.lua run > mem.log 2>&1 &
```



## 一主一备



```
sysbench --mysql-host=9.40.30.196 --mysql-port=15024 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem9 --range_selects=off --skip_trx=off --tables=10 --table_size=1000 --threads=100 --report_interval=1 --time=6000 oltp_common.lua cleanup
sysbench --mysql-host=9.40.30.196 --mysql-port=15024 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem9 --range_selects=off --skip_trx=off --tables=10 --table_size=1000 --threads=100 --report_interval=1 --time=6000 oltp_common.lua prepare
sysbench --mysql-host=9.40.30.196 --mysql-port=15024 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem9 --range_selects=off --skip_trx=off --tables=10 --table_size=1000 --threads=100 --report_interval=1 --time=6000 oltp_read_write.lua run


```

```
sysbench --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15024 --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem9 --threads=100 --report_interval=1 --time=6000 truncate9.lua run
```

```
nohup sysbench --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15024 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem9 --range_selects=off --skip_trx=off --tables=10 --table_size=100 --threads=100 --report_interval=1 --time=60000 oltp_read_write.lua run > mem9.log 2>&1 &
```



## 1range

```
sysbench --mysql-host=9.40.30.196 --mysql-port=15021 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem9 --range_selects=off --skip_trx=off --tables=11 --table_size=1000 --threads=1 --report_interval=1 --time=6000 oltp_common_onlyInsert.lua prepare
```

```
nohup sysbench --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15024 --mysql-ignore-errors=all --mysql-user=lukatai --mysql-password=Root@@@123  --mysql-db=mem9 --range_selects=off --skip_trx=off --tables=1 --table_size=1000 --threads=100 --report_interval=1 --time=60000 oltp_read_write.lua run > mem1range.log 2>&1 &
```



ll -trl /data/coredump



 