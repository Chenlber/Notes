表结构

```
CREATE TABLE `distribute_range_4_sbtest8` (
  `id` int NOT NULL,
  `BK_ID` int NOT NULL,
  `EBK_CUST_NO` varchar(22) COLLATE utf8mb4_general_ci NOT NULL,
  `INTLG_VLD_STY` char(2) COLLATE utf8mb4_general_ci NOT NULL,
  `DEVICE_ID` varchar(64) COLLATE utf8mb4_general_ci NOT NULL,
  `MEDM_NO` varchar(64) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `PSWD_ALGR_TYP` char(2) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `VLD_PSWD` varchar(100) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `ACM_ERR_TIMES` int DEFAULT NULL,
  `CONT_ACM_ERR_TIMES` int DEFAULT NULL,
  `LAST_ERR_DT` char(8) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `TRACK_FLG` char(10) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `EFF_FLG` char(10) COLLATE utf8mb4_general_ci NOT NULL,
  `CLS_RSN` char(10) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `CRT_TMS` varchar(20) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `UPD_TMS` varchar(20) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `id_v` int GENERATED ALWAYS AS (id+BK_ID-BK_ID) VIRTUAL,
  `BK_ID_v` int GENERATED ALWAYS AS (BK_ID+100-22-78) VIRTUAL,
  PRIMARY KEY (`id`,`BK_ID`),
  KEY `k_8` (`id_v`,`BK_ID_v`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci TDSQL_DISTRIBUTED BY RANGE(BK_ID) (s1 values less than ('20'),s2 values less than (MAXVALUE)) 
```



数据量 64tb 100w,200thread



```
sysbench --threads=200  --time=600 --report-interval=10 /data/lukatai/oltp_first_partition_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_point_select run


SQL statistics:
    queries performed:
        read:                            4323452
        write:                           0
        other:                           0
        total:                           4323452
    transactions:                        4323452 (7204.92 per sec.)
    queries:                             4323452 (7204.92 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      7204.9203
    time elapsed:                        600.0694s
    total number of events:              4323452

Latency (ms):
         min:                                   18.48
         avg:                                   27.76
         max:                                 3013.48
         95th percentile:                       38.25
         sum:                            119999468.30

Threads fairness:
    events (avg/stddev):           21617.2600/3785.01
    execution time (avg/stddev):   599.9973/0.01
```



```
sysbench --threads=200  --time=600 --report-interval=10 /data/lukatai/oltp_first_partition_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_update_index run

SQL statistics:
    queries performed:
        read:                            0
        write:                           41960
        other:                           3319301
        total:                           3361261
    transactions:                        3361261 (5601.60 per sec.)
    queries:                             3361261 (5601.60 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      5601.6035
    time elapsed:                        600.0534s
    total number of events:              3361261

Latency (ms):
         min:                                   18.33
         avg:                                   35.70
         max:                                11571.48
         95th percentile:                       36.24
         sum:                            120000343.36

Threads fairness:
    events (avg/stddev):           16806.3050/2731.26
    execution time (avg/stddev):   600.0017/0.01
```



读写查询where   BK_ID_v=? and id_v=?



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/oltp_first_partition_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_update_non_index run

SQL statistics:
    queries performed:
        read:                            0
        write:                           7481
        other:                           590063
        total:                           597544
    transactions:                        597544 (993.04 per sec.)
    queries:                             597544 (993.04 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      993.0354
    time elapsed:                        601.7349s
    total number of events:              597544

Latency (ms):
         min:                                   18.47
         avg:                                  200.94
         max:                                19188.05
         95th percentile:                      926.33
         sum:                            120071436.42

Threads fairness:
    events (avg/stddev):           2987.7200/503.24
    execution time (avg/stddev):   600.3572/0.32
```

```
sysbench --threads=200  --time=600 --report-interval=10 /data/lukatai/oltp_first_partition_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_read_write --range_selects=off run

SQL statistics:
    queries performed:
        read:                            0
        write:                           13799
        other:                           1100094
        total:                           1113893
    transactions:                        1113893 (3702.46 per sec.)
    queries:                             1113893 (3702.46 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      3702.4628
    time elapsed:                        300.8519s
    total number of events:              1113893

Latency (ms):
         min:                                   18.57
         avg:                                   53.93
         max:                                24883.10
         95th percentile:                      130.13
         sum:                             60067843.71

Threads fairness:
    events (avg/stddev):           5569.4650/792.29
    execution time (avg/stddev):   300.3392/0.23
```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/oltp_first_partition_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_read_write --range_selects=on run

SQL statistics:
    queries performed:
        read:                            224336
        write:                           16590
        other:                           79554
        total:                           320480
    transactions:                        16024  (52.94 per sec.)
    queries:                             320480 (1058.83 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      52.9413
    time elapsed:                        302.6747s
    total number of events:              16024

Latency (ms):
         min:                                  609.72
         avg:                                 3760.00
         max:                                22717.23
         95th percentile:                     8333.38
         sum:                             60250271.66

Threads fairness:
    events (avg/stddev):           80.1200/16.53
    execution time (avg/stddev):   301.2514/0.63

```



