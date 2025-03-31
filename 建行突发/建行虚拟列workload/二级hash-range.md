```
sysbench --threads=200  --time=300 --report-interval=1 /data/lukatai/secondry/oltp_common_subpartition_hash_range_virtual.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai_hash_range  prepare


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
    `VLD_PSWD_v` varchar(100)  GENERATED ALWAYS AS (VLD_PSWD) VIRTUAL,
     PRIMARY KEY (`id`,`BK_ID`,`EBK_CUST_NO`),
     KEY v_IDX (id_v,VLD_PSWD_v)
    ) shardkey=BK_ID ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci
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
      PARTITION s16 VALUES LESS THAN (maxvalue)
)
```





```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_subpartition_hash_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai_hash_range --oltp_type=oltp_point_select --range_selects=off run
```





```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_subpartition_hash_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai_hash_range --oltp_type=oltp_update_index --range_selects=off run
```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_subpartition_hash_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai_hash_range --oltp_type=oltp_update_non_index --range_selects=off run
```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_subpartition_hash_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai_hash_range --oltp_type=oltp_read_write --range_selects=off run
```



```
sysbench --threads=200  --time=300 --report-interval=10 /data/lukatai/secondry/oltp_subpartition_hash_range_testsuite.lua  --mysql-host=9.240.53.37 --mysql-port=15196 --tables=64 --table_size=1000000 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=lukatai_hash_range --oltp_type=oltp_read_write --range_selects=off run
```

