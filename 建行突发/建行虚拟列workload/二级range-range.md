```
sysbench --threads=200  --time=300 --report-interval=1 /data/lukatai/secondry/oltp_common_tdsql_distributed_range_range_virtual.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai2p  prepare


```



```
CREATE TABLE second_partition_sbtest%d(
   `id` int NOT NULL,
   `BK_ID` int NOT NULL,
   `EBK_CUST_NO` int NOT NULL,
   `INTLG_VLD_STY` char(2) NOT NULL,
   `DEVICE_ID` varchar(64) NOT NULL,
    `MEDM_NO` varchar(64) DEFAULT NULL,
    `PSWD_ALGR_TYP` char(2) DEFAULT NULL,
    `VLD_PSWD` varchar(100) DEFAULT NULL,
    `ACM_ERR_TIMES` int DEFAULT NULL,
    `CONT_ACM_ERR_TIMES` int DEFAULT NULL,
    `LAST_ERR_DT` char(8) DEFAULT NULL,
    `TRACK_FLG` char(10) DEFAULT NULL,
    `EFF_FLG` char(10) NOT NULL,
    `CLS_RSN` char(10) DEFAULT NULL,
    `CRT_TMS` varchar(20) DEFAULT NULL,
    `UPD_TMS` varchar(20) DEFAULT NULL,
    `id_v` int GENERATED ALWAYS AS (id+BK_ID-BK_ID) VIRTUAL,
    `BK_ID_v` int GENERATED ALWAYS AS (BK_ID+100-5-95) VIRTUAL,
     PRIMARY KEY (`id`,`BK_ID`,`EBK_CUST_NO`),
     KEY v_IDX (id_v,BK_ID_v)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci
    PARTITION BY RANGE(EBK_CUST_NO)(
      PARTITION s1 VALUES LESS THAN (10),
      PARTITION s2 VALUES LESS THAN (20),
      PARTITION s3 VALUES LESS THAN (30),
      PARTITION s4 VALUES LESS THAN (40),
      PARTITION s5 VALUES LESS THAN (50),
      PARTITION s6 VALUES LESS THAN (60),
      PARTITION s7 VALUES LESS THAN (70),
      PARTITION s8 VALUES LESS THAN (80),
      PARTITION s9 VALUES LESS THAN (90),
      PARTITION s10 VALUES LESS THAN (100),
      PARTITION s11 VALUES LESS THAN (110),
      PARTITION s12 VALUES LESS THAN (120),
      PARTITION s13 VALUES LESS THAN (130),
      PARTITION s14 VALUES LESS THAN (140),
      PARTITION s15 VALUES LESS THAN (150),
      PARTITION s16 VALUES LESS THAN (maxvalue))
    TDSQL_DISTRIBUTED BY RANGE(`BK_ID`)(
      s1 VALUES LESS THAN (50),
      s2 VALUES LESS THAN (100)
)
```









```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_tdsql_distributed_range_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai2p --oltp_type=oltp_point_select --range_selects=off run

SQL statistics:
    queries performed:
        read:                            243722
        write:                           0
        other:                           0
        total:                           243722
    transactions:                        243722 (811.64 per sec.)
    queries:                             243722 (811.64 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      811.6361
    time elapsed:                        300.2848s
    total number of events:              243722

Latency (ms):
         min:                                   19.04
         avg:                                  246.32
         max:                                11485.87
         95th percentile:                     1013.60
         sum:                             60033621.25

Threads fairness:
    events (avg/stddev):           1218.6100/111.78
    execution time (avg/stddev):   300.1681/0.09

```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_tdsql_distributed_range_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai2p --oltp_type=oltp_update_index --range_selects=off run

SQL statistics:
    queries performed:
        read:                            0
        write:                           28248
        other:                           0
        total:                           28248
    transactions:                        28248  (93.38 per sec.)
    queries:                             28248  (93.38 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      93.3761
    time elapsed:                        302.5185s
    total number of events:              28248

Latency (ms):
         min:                                   22.05
         avg:                                 2130.56
         max:                                60396.11
         95th percentile:                     8955.74
         sum:                             60183989.18

Threads fairness:
    events (avg/stddev):           141.2400/30.36
    execution time (avg/stddev):   300.9199/0.62
```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_tdsql_distributed_range_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai2p --oltp_type=oltp_update_non_index --range_selects=off run

SQL statistics:
    queries performed:
        read:                            0
        write:                           44938
        other:                           0
        total:                           44938
    transactions:                        44938  (149.31 per sec.)
    queries:                             44938  (149.31 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      149.3127
    time elapsed:                        300.9657s
    total number of events:              44938

Latency (ms):
         min:                                   20.96
         avg:                                 1337.24
         max:                                34523.13
         95th percentile:                     5709.50
         sum:                             60092829.87

Threads fairness:
    events (avg/stddev):           224.6900/40.10
    execution time (avg/stddev):   300.4641/0.27
```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_tdsql_distributed_range_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai2p --oltp_type=oltp_read_write --range_selects=off run


```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_tdsql_distributed_range_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai2p --oltp_type=oltp_read_write --range_selects=on run
```

