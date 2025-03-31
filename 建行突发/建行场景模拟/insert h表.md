48并发

先往一个日期分区插入

1000条数据commit一次

总数据量：2/3亿数据

intel上测试





### tbl_wl_jnl_h_2410_10001_0

  PRIMARY KEY (`TrxId`,`RPFlg`,`PayInstId`,`Sys_Date`,`Ebnk_Rsrv_Mod_1`),

  UNIQUE KEY `UIDX_WL_H_2210_10001_0_1` (`Sec_Trace_Id`,`Sys_Tx_Type`,`Sys_Snd_Serial_No`,`Sys_Date`,`TrxId`),

  KEY `IDX_WL_H_2210_10001_0_1` (`Sys_Date_Time`)



  `TrxId` varchar(31) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL,

  `RPFlg` varchar(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL,

`Sys_Date_Time` varchar(14) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL,

  `Sys_Date` varchar(8) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL,

  `Ebnk_Rsrv_Mod_1` varchar(2) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL,









查询单表体积

```
select ROUND((data_length + index_length) / 1024 / 1024, 2) AS `Size (MB)` from information_schema.tables where table_name = 'tbl_wl_jnl_h_2410_10001_1' and TABLE_SCHEMA = 'rcc_inserth';
select ROUND((data_length + index_length) / 1024 / 1024, 2) AS `Size (MB)` from information_schema.tables where table_name = 'tbl_wl_jnl_h_2410_10001_2' and TABLE_SCHEMA = 'rcc_inserth';
select ROUND((data_length + index_length) / 1024 / 1024, 2) AS `Size (MB)` from information_schema.tables where table_name = 'tbl_wl_jnl_h_2410_10001_3' and TABLE_SCHEMA = 'rcc_inserth';

```

```

SELECT 
    table_schema AS `Database`, 
    table_name AS `Table`, 
    ROUND((data_length + index_length) / 1024 / 1024, 2) AS `Size (MB)`
FROM 
    information_schema.TABLES
WHERE 
    table_name = 'tbl_wl_jnl_h_2410_10001_3' and TABLE_SCHEMA = 'rcc_inserth';

```





```
ALTER TABLE tbl_wl_jnl_h_2410_10001_2 COMPRESSION="None";
OPTIMIZE TABLE tbl_wl_jnl_h_2410_10001_2;
```





环境信息：

海光二代+麒麟（复现环境）

http://9.192.173.149/tdsqlpcloud/index.php/instance/manage/detail?mid=/tdsqlzk/group_1724725182_162&cluster=tdsql_sdghtj11q

赤兔密码：
admin/CSIGcsig_2023##

机器密码：
root/CSIGcsig_2023##

mysql -h9.192.169.254 -P15006 -ulukatai -pRoot@@@123 -c -A

库 inserth* 



海光4代+tlinx3.1环境（对比复现环境，排除麒麟影响）
http://30.234.172.204/tdsqlpcloud/index.php/instance/manage/detail?mid=/tdsqlzk/group_1729404328_142&cluster=tdsql_sln283y7a

赤兔密码：
admin/CSIGcsig_2023##

机器密码：
root/CSIGcsig_2023##

mysql -h30.234.177.86 -P15017 -ulukatai -pRoot@@@123 -c -A

库：inserth* 



intel+tlinux（测试lz4压缩比例的环境，无建行业务）

http://9.29.187.69/tdsqlpcloud/index.php/instance/manage/detail?mid=/tdsqlzk/group_1705991217_44976&cluster=tdsql_s609xse2w

赤兔密码：
admin/CSIGcsig_2023##

机器密码：
root/CSIGcsig_2023##

mysql -h9.40.30.196 -P15012 -ulukatai -pRoot@@@123 -c

inserth库 



## 验证解决方案

观察一下性能和报错。数据盘和日志盘都需要观测

赵欣昊 抓 碎片率脚本



bin temp_dir 指向/data（log）盘，  这个更改操作对管控的影响



2->1	

![image-20241022154143377](/Users/lukatai/Library/Application Support/typora-user-images/image-20241022154143377.png)



下一个测试场景：在没有lvm的情况下，测试







### 学习

![image-20241022170940990](/Users/lukatai/Library/Application Support/typora-user-images/image-20241022170940990.png)





fio



xfs_fsr 回收碎片





fallocate -l 500G /data1/river/big_file_f00G





### dd

低级别的数据复制和转换。它可以用来创建磁盘镜像、备份数据、恢复数据等

```
dd if=<输入文件> of=<输出文件> [options]
```



dd if=/dev/sda of=disk.img

dd if=disk.img of=/dev/sda

#### 清空磁盘

dd if=/dev/zero of=/dev/sda bs=1M









```
[riverxiao@MNET-CSIG-GZ-37 ~/data]$ cat interval_check.sh 
#!/bin/bash


cmd="df -h "
echo "Execute CMD:  ---- $cmd ----"
for host in `cat all_hosts`; do echo "---- $host -----"; ssh -n root@$host "date; $cmd" 2>&1 ; done

echo ""
echo ""

cmd="xfs_db -c freesp -r /dev/mapper/dbvg-dbdata"
echo "Execute CMD:  ---- $cmd ----"
for host in `cat all_hosts`; do echo "---- $host -----"; ssh -n root@$host "date; $cmd" 2>&1 ; done

echo ""
echo ""
cmd="grep 'Page cleaner took' /data1/tdengine/log/400*/dblogs/mysqld.err*"
echo "Execute CMD:  ---- $cmd ----"
for host in `cat all_hosts`; do echo "---- $host -----"; ssh -n root@$host "date; $cmd" 2>&1 ; done

echo ""
echo ""
cmd="grep  gone /data/tdsql_run/400*/mysqlagent/log/sys_report_400*.log.2024-10-26.*"
echo "Execute CMD:  ---- $cmd ----"
for host in `cat all_hosts`; do echo "---- $host -----"; ssh -n root@$host "date; $cmd" 2>&1 ; done
```



grep -A4 "Oct 31" rcc_inserth_H1_Vol_dfh.log  | egrep 'Oct|dbvg-dbdata'



grep -A4 "Oct 31" rcc_inserth_H1_Vol_dfh.log  | egrep 'dbvg-dbdata' 不带时间 



从21：00 到 03：59 的数据， 只输出了/data1 磁盘使用量

egrep -A4 "Wed Oct 30 2[1-4]|Thu Oct 31 0[0-3]" rcc_inserth_H1_Vol_dfh.log  | egrep 'dbvg-dbdata' |awk '{print $3}'







---

重启mysqld

pgrep -f mysql | xargs kill -9

binlog不可以私自动











压缩相关

理解其中的数据

![image-20241111152402964](/Users/lukatai/Library/Application Support/typora-user-images/image-20241111152402964.png)



页压缩

ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=4





