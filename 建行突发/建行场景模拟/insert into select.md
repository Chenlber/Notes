### tbl_wl_jnl_c_242_10001_0

  PRIMARY KEY (`TrxId`,`RPFlg`,`PayInstId`,`Sys_Date`,`Ebnk_Rsrv_Mod_1`),

  UNIQUE KEY `UIDX_WL_C_242_10001_0_1` (`Sec_Trace_Id`,`Sys_Tx_Type`,`Sys_Snd_Serial_No`,`Sys_Date`,`TrxId`),

  KEY `IDX_WL_C_242_10001_0_1` (`Sys_Date_Time`)

### tbl_wl_jnl_a_10001_0

  PRIMARY KEY (`TrxId`,`RPFlg`,`PayInstId`,`Sys_Date`,`Ebnk_Rsrv_Mod_1`),

  UNIQUE KEY `UIDX_WL_A_10001_0_1` (`Sec_Trace_Id`,`Sys_Tx_Type`,`Sys_Snd_Serial_No`,`Sys_Date`,`TrxId`),

  KEY `IDX_WL_A_10001_0_1` (`Sys_Date_Time`)





```
insert into tbl_wl_jnl_a_10001_0 select * from  tbl_wl_jnl_c_242_10001_0 where sys_date_time = "20240909024327";
```







查询时延100ms以内。

碎片化问题，对业务的影响最多集中在几个小时。（可以申报停机窗口）











一键去获取客户的生产端数据库配置数据

数据库参数配置

proxy配置

实例层面的配置，比如版本，set数据，分片数据

