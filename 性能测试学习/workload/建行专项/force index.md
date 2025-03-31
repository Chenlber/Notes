```
txsql> explain
    -> update 
    ->   tbl_wl_jnl_a_10001_0
    ->   force index(UIDX_WL_A_10001_0_1)
    -> set 
    ->   Txn_Status = "33", 
    ->   Card_Txn_Status = "46", 
    ->   Auth_No = case when "18" is null then Auth_No when "56" = '' then Auth_No else "57" end, 
    ->   Sys_Rcv_Sec_Id = "316412", 
    ->   Host_Bus_Dt = "717628", 
    ->   Rcv_Txn_Cd = "945098", 
    ->   Esr_Sec_Id = "352409", 
    ->   Act_Elptm_Tm = "1", 
    ->   Rcv_Elptm_Tm = Rcv_Elptm_Tm - 10, 
    ->   Resp_Code = "YBLA26017194", 
    ->   Resp_Summary = "原交易存在" 
    -> where 
    ->  Sys_Snd_Serial_No="1000000008"
    ->   and Sec_Trace_Id = "1000000000000000000000008" 
    ->   and Sys_Tx_Type = "010008" 
    ->   and Sys_Date = "20240511"
    ->   and Trxid = "96614A1hHiABbGcC" ;
+----+-------------+----------------------+------------+-------+---------------------+---------------------+---------+-------------------------------+------+----------+-------------+
| id | select_type | table                | partitions | type  | possible_keys       | key                 | key_len | ref                           | rows | filtered | Extra       |
+----+-------------+----------------------+------------+-------+---------------------+---------------------+---------+-------------------------------+------+----------+-------------+
|  1 | UPDATE      | tbl_wl_jnl_a_10001_0 | p12        | range | UIDX_WL_A_10001_0_1 | UIDX_WL_A_10001_0_1 | 332     | const,const,const,const,const |    1 |   100.00 | Using where |
+----+-------------+----------------------+------------+-------+---------------------+---------------------+---------+-------------------------------+------+----------+-------------+
1 row in set, 1 warning (0.01 sec)

txsql> 
txsql> explain
    -> update 
    ->   tbl_wl_jnl_a_10001_0
    ->     /*+index(UIDX_WL_A_10001_0_1)*/
    -> set 
    ->   Txn_Status = "33", 
    ->   Card_Txn_Status = "46", 
    ->   Auth_No = case when "18" is null then Auth_No when "56" = '' then Auth_No else "57" end, 
    ->   Sys_Rcv_Sec_Id = "316412", 
    ->   Host_Bus_Dt = "717628", 
    ->   Rcv_Txn_Cd = "945098", 
    ->   Esr_Sec_Id = "352409", 
    ->   Act_Elptm_Tm = "1", 
    ->   Rcv_Elptm_Tm = Rcv_Elptm_Tm - 10, 
    ->   Resp_Code = "YBLA26017194", 
    ->   Resp_Summary = "原交易存在" 
    -> where 
    ->   Trxid = "96614A1hHiABbGcC"
    ->   and Sec_Trace_Id = "1000000000000000000000008" 
    ->   and Sys_Tx_Type = "010008" 
    ->   and Sys_Date = "20240511"  ;
+----+-------------+----------------------+------------+-------+-----------------------------+---------+---------+-------+------+----------+-------------+
| id | select_type | table                | partitions | type  | possible_keys               | key     | key_len | ref   | rows | filtered | Extra       |
+----+-------------+----------------------+------------+-------+-----------------------------+---------+---------+-------+------+----------+-------------+
|  1 | UPDATE      | tbl_wl_jnl_a_10001_0 | p12        | range | PRIMARY,UIDX_WL_A_10001_0_1 | PRIMARY | 126     | const |    1 |   100.00 | Using where |
+----+-------------+----------------------+------------+-------+-----------------------------+---------+---------+-------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

txsql> 
txsql> explain
    -> update 
    ->   tbl_wl_jnl_a_10001_0
    -> set 
    ->   Txn_Status = "33", 
    ->   Card_Txn_Status = "46", 
    ->   Auth_No = case when "18" is null then Auth_No when "56" = '' then Auth_No else "57" end, 
    ->   Sys_Rcv_Sec_Id = "316412", 
    ->   Host_Bus_Dt = "717628", 
    ->   Rcv_Txn_Cd = "945098", 
    ->   Esr_Sec_Id = "352409", 
    ->   Act_Elptm_Tm = "1", 
    ->   Rcv_Elptm_Tm = Rcv_Elptm_Tm - 10, 
    ->   Resp_Code = "YBLA26017194", 
    ->   Resp_Summary = "原交易存在" 
    -> where 
    ->   Trxid = "96614A1hHiABbGcC"
    ->   and Sec_Trace_Id = "1000000000000000000000008" 
    ->   and Sys_Tx_Type = "010008" 
    ->   and Sys_Date = "20240511"  ;
+----+-------------+----------------------+------------+-------+-----------------------------+---------+---------+-------+------+----------+-------------+
| id | select_type | table                | partitions | type  | possible_keys               | key     | key_len | ref   | rows | filtered | Extra       |
+----+-------------+----------------------+------------+-------+-----------------------------+---------+---------+-------+------+----------+-------------+
|  1 | UPDATE      | tbl_wl_jnl_a_10001_0 | p12        | range | PRIMARY,UIDX_WL_A_10001_0_1 | PRIMARY | 126     | const |    1 |   100.00 | Using where |
+----+-------------+----------------------+------------+-------+-----------------------------+---------+---------+-------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
```

