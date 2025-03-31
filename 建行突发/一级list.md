select count(1) from distribute_range_4_sbtest33 where id_v = id;



```
  sysbench --threads=10  --time=300 --report-interval=1 /data/lukatai/oltp_common_tdsql_distribute_list_4_char_virtual.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=10 --table_size=100000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai  prepare


```







```
CREATE TABLE `distribute_list_4_sbtest1` (
  `id` int NOT NULL,
  `BK_ID` char(3) COLLATE utf8mb4_general_ci NOT NULL,
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
  `id_v` int GENERATED ALWAYS AS (`id`) VIRTUAL,
  `EBK_CUST_NO_v` varchar(20) COLLATE utf8mb4_general_ci GENERATED ALWAYS AS (substr(`EBK_CUST_NO`,1,10)) VIRTUAL,
  PRIMARY KEY (`id`,`BK_ID`),
  KEY `k_1` (`ACM_ERR_TIMES`),
  KEY `k_2` (`id_v`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci TDSQL_DISTRIBUTED BY LIST(BK_ID) (s1 values in ('a11','a12','a13','a14','a15','a16','a17','a18','a19','a20','a31','a32','a33','a34','a35','a36','a37','a38','a39','a40'),s2 values in ('a21','a22','a23','a24','a25','a26','a27','a28','a29','a30','a41','a42','a43','a44','a45','a46','a47','a48','a49','a50'))
```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/oltp_first_partition_list_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_point_select --range_selects=off run

SQL statistics:
    queries performed:
        read:                            194129
        write:                           0
        other:                           0
        total:                           194129
    transactions:                        194129 (644.48 per sec.)
    queries:                             194129 (644.48 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      644.4788
    time elapsed:                        301.2186s
    total number of events:              194129

Latency (ms):
         min:                                   18.78
         avg:                                  309.39
         max:                                16591.51
         95th percentile:                     1453.01
         sum:                             60062427.94

Threads fairness:
    events (avg/stddev):           970.6450/303.19
    execution time (avg/stddev):   300.3121/0.26
```





```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/oltp_first_partition_list_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_update_index --range_selects=off run

SQL statistics:
    queries performed:
        read:                            0
        write:                           12058
        other:                           468071
        total:                           480129
    transactions:                        480129 (1587.19 per sec.)
    queries:                             480129 (1587.19 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      1587.1875
    time elapsed:                        302.5030s
    total number of events:              480129

Latency (ms):
         min:                                   18.46
         avg:                                  125.21
         max:                                18232.31
         95th percentile:                      442.73
         sum:                             60116454.30

Threads fairness:
    events (avg/stddev):           2400.6450/374.71
    execution time (avg/stddev):   300.5823/0.59
```





```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/oltp_first_partition_list_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_update_non_index --range_selects=off run

SQL statistics:
    queries performed:
        read:                            0
        write:                           9182
        other:                           363609
        total:                           372791
    transactions:                        372791 (1241.09 per sec.)
    queries:                             372791 (1241.09 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      1241.0944
    time elapsed:                        300.3728s
    total number of events:              372791

Latency (ms):
         min:                                   18.41
         avg:                                  161.02
         max:                                12630.37
         95th percentile:                      787.74
         sum:                             60025825.46

Threads fairness:
    events (avg/stddev):           1863.9550/418.55
    execution time (avg/stddev):   300.1291/0.10
```





```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/oltp_first_partition_list_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_read_write --range_selects=on run

SQL statistics:
    queries performed:
        read:                            143878
        write:                           11093
        other:                           50569
        total:                           205540
    transactions:                        10277  (34.01 per sec.)
    queries:                             205540 (680.22 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      34.0109
    time elapsed:                        302.1677s
    total number of events:              10277

Latency (ms):
         min:                                  603.53
         avg:                                 5862.59
         max:                                33172.92
         95th percentile:                    12384.09
         sum:                             60249789.96

Threads fairness:
    events (avg/stddev):           51.3850/10.28
    execution time (avg/stddev):   301.2489/0.53
```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/oltp_first_partition_list_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai --oltp_type=oltp_read_write --range_selects=off run

SQL statistics:
    queries performed:
        read:                            229630
        write:                           24684
        other:                           113094
        total:                           367408
    transactions:                        22963  (76.16 per sec.)
    queries:                             367408 (1218.50 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      76.1564
    time elapsed:                        301.5243s
    total number of events:              22963

Latency (ms):
         min:                                  310.70
         avg:                                 2620.28
         max:                                17316.94
         95th percentile:                     5813.24
         sum:                             60169404.16

Threads fairness:
    events (avg/stddev):           114.8150/15.65
    execution time (avg/stddev):   300.8470/0.42
```

