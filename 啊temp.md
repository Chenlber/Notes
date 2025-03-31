分布式网联
https://devops.woa.com/console/pipeline/zhiyan-kaynli/p-120e0a519bd54624b58a826ed1f444e9/history/history/21?page=1&pageSize=20
分布式收单
https://devops.woa.com/console/pipeline/zhiyan-kaynli/p-574a23a5bb914221a8e57043bd198732/history/history/2?page=1&pageSize=20
集中式网联

https://devops.woa.com/console/pipeline/zhiyan-kaynli/p-09305b38922e44e08317f69b84737b1d/history/history/4?page=1&pageSize=20


```
**9.192.169.68**


```





待体验
coze
汽水音乐人
mureka
py调试dev环境的机器 



自动化测试
```shell

/data/tools/apache-jmeter-5.6.3/bin/jmeter -Duser.classpath=/data/tools/apache-jmeter-5.6.3/lib/mysql-connector-j-9.0.0.jar -Jjdbc.db_type=mysql -Jjdbc.host_ports=30.215.12.144:15032,30.215.12.154:15032 -Jjdbc.username=test_tdsql -Jjdbc.password=test_tdsql -Jjdbc.db=rcchadb -Jjdbc.driver=com.mysql.cj.jdbc.Driver -Jload_balance=loadbalance: -n -t /data/tools/apache-jmeter-5.6.3/scripts/workload/zhaohang_simulation/jh_wanglian_centralized_auto/00_网联公共数据初始化_centralized.jmx -l kk
```




```

sysbench --mysql-host=30.215.12.154 --mysql-port=15033 --mysql-user=lukatai --mysql-password=Root@@@123 --threads=100 --mysql-db=sysbenchdb  --tables=10 --table_size=1000000 --report_interval=10 --time=7200 /home/sysbench-master/src/lua/oltp_common_noshard.lua  cleanup

sysbench --mysql-host=30.215.12.154 --mysql-port=15033 --mysql-user=lukatai --mysql-password=Root@@@123 --threads=100 --mysql-db=sysbenchdb  --tables=10 --table_size=1000000 --report_interval=10 --time=7200 /home/sysbench-master/src/lua/oltp_common_noshard.lua  prepare

sysbench --mysql-host=30.215.12.154 --mysql-port=15033 --mysql-user=lukatai --mysql-password=Root@@@123 --threads=100 --mysql-db=sysbenchdb  --tables=10 --table_size=1000000 --report_interval=10 --time=7200 /home/sysbench-master/src/lua/oltp_point_select.lua  run

```

233 集群

```
sysbench --mysql-host=9.30.3.233 --mysql-port=15120 --mysql-user=lukatai --mysql-password=Root@@@123 --threads=100 --mysql-db=sysbenchdb  --tables=10 --table_size=1000000 --report_interval=10 --time=7200 /data/luka/sysbench-master/src/lua/oltp_common.lua  prepare

sysbench --mysql-host=9.30.3.233 --mysql-port=15120 --mysql-user=lukatai --mysql-password=Root@@@123 --threads=100 --mysql-db=sysbenchdb  --tables=10 --table_size=1000000 --report_interval=10 --time=7200 /data/luka/sysbench-master/src/lua/oltp_point_select.lua  run

```


```
sysbench --mysql-host=9.134.254.214 --mysql-port=3306 --mysql-user=tdsql_admin --mysql-password=TYdXA*4679zYgn --threads=100 --mysql-db=lukatai  --tables=4 --table_size=10000 --report_interval=10 --time=7200 ./oltp_common.lua  prepare
```
-h9.134.254.214 -P3306 -utdsql_admin -pTYdXA*4679zYgn

重建网联表数据


![[attachments/Pasted image 20250311125039.png]]












index字段，当字段为相同字段的时候，index的可查询性会下降？



一个基本流程



导数结束之后，需要提取出业务要使用的字段，使用csv格式存储起来，方便业务侧使用



存储表结构之后，我点击字段，可以直接看到该字段的属性





The error message "target/maven_pichuli3-1.0-SNAPSHOT.jar中没有主清单属性" translates to "No main manifest attribute in target/maven_pichuli3-1.0-SNAPSHOT.jar". This error occurs when the JAR file does not have a `Main-Class` attribute specified in its `MANIFEST.MF` file, which tells the Java Virtual Machine (JVM) which class contains the `main` method to be executed.

To resolve this issue, you need to ensure that your JAR file is built with the correct `Main-Class` attribute in the `MANIFEST.MF` file. Here's how you can do it using Maven:

1. **Update your `pom.xml` file**: You need to configure the Maven JAR plugin to include the `Main-Class` attribute in the manifest file. Add the following configuration to your `pom.xml`:

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.apache.maven.plugins</groupId>
               <artifactId>maven-jar-plugin</artifactId>
               <version>3.2.0</version>
               <configuration>
                   <archive>
                       <manifest>
                           <addClasspath>true</addClasspath>
                           <mainClass>org.example.App</mainClass>
                       </manifest>
                   </archive>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

   Make sure to replace `org.example.App` with the fully qualified name of your main class if it's different.

2. **Rebuild your JAR file**: After updating the `pom.xml`, rebuild your project using Maven. You can do this by running the following command in your project's root directory:

   ```bash
   mvn clean package
   ```

   This command will clean the previous build artifacts and create a new JAR file with the correct `MANIFEST.MF` file.

3. **Run your JAR file**: Once the JAR file is rebuilt, you can run it using the `java -jar` command as you did before:

   ```bash
   java -jar -DsubstrTxnSeq=4 -DldgrDealFlag=1 -DsysDateTime=20241101000000 -DldgrDealFlagUpdate=0 -DbatchSizes=500 target/maven_pichuli3-1.0-SNAPSHOT.jar
   ```

By following these steps, your JAR file should now have the necessary `Main-Class` attribute, and the JVM will be able to locate and execute the `main` method in your `App` class.





![image-20241118162953751](啊temp.assets/image-20241118162953751-1846162.png)





see 哪里的log

![image-20241118170446538](啊temp.assets/image-20241118170446538.png)

![[attachments/Pasted image 20250311125039.png]]

![[Pasted image 20250311125039.png]]
x锁

s锁

![image-20241118162953751](啊temp.assets/image-20241118162953751.png)





--只陈述客观事实



该问题已经复现，具体复现细节见[【企微文档】商铺更新内存bug复现--时间线 ](https://doc.weixin.qq.com/doc/w3_AYYAzAbYAF0qTc0GNGtRhKHShhuGC?scode=AJEAIQdfAAoJPFz1u4AYYAzAbYAF0)

测试侧对该问题影响性分析：在autocommit=0的情况，做大量占用内存的操作且不提交（比如大量导数且不显式提交）， 会导致实例oom，触发实例重启，后面进入实例的recover阶段，但recover处理仍不可恢复，需要重做实例才可以恢复。

建议项目组与前线交付团队：

确保在我们的tdsql-mysql开发手册中，对与autocommit=0的使用范围和影响中加入此场景的影响描述，比如‘“如果在autocommit=0的情况下进行大量占用内存的操作且不提交会导致实例oom，触发实例重启，后进入实例的recover阶段仍不可恢复，需要重做实例才可恢复。”



疑惑：刚才想了一下，我们开发侧在后续有没有办法对这种极限场景的资源使用做一些限制或者告警规避，客户在编码过程中如果出现失误autocommit设置错误发布到生产环境后，业务上量导致oom，并且实例重启也不能恢复，只能重做实例，这里的成本有点高。



---

我的理解这种操作方式是不合理的，但是可能存在客户误操作，在autocommit=0的情况下导入大量数据导致设备oom且不可自行恢复，你们







```
INSERT INTO users (id, name, email)
VALUES (1, 'John Doe', 'john.doe@example.com')
ON DUPLICATE KEY UPDATE
name = VALUES(name),
email = VALUES(email);


INSERT INTO users (id, name, email)
VALUES (1, 'HaHa', 'haha.doe@example.com')
ON DUPLICATE KEY UPDATE
name = VALUES(name),
email = VALUES(email);
```











debug

tracing

profile

火焰图是采样时间

执行时间相对比较短

uprob







sysbench --mysql-host=9.40.33.197,9.40.30.196,9.40.37.138 --mysql-port=15012 --mysql-user=test_tdsql --mysql-password=test_tdsql --mysql-db=anytxn --threads=100 --report_interval=1 --time=600 --thread_init_timeout=300 /home/sysbench-master/src/lua/sysbench_lua/workload/chengde_bank/oltp_select_spilder_stability_12.lua run



















···

```
[2024-09-11 17:21:16 511982] INFO topic=group_1724725182_162&tid=354063&con=0x7facfe01f000&qid=13074461-1726045792-31868&splited=0&clientIP=9.250.178.253:49606&proxyHost=9.192.172.132:15006&sql_size=559&sql_type=3&sub_sql_type=4&sql=%0Aupdate %0A  tbl_wl_jnl_a_10001_0%0Aset %0A  Txn_Status %3D "45", %0A  Card_Txn_Status %3D "94", %0A  Auth_No %3D case when "58" is null then Auth_No when "60" %3D '' then Auth_No else "29" end, %0A  Sys_Rcv_Sec_Id %3D "918292", %0A  Host_Bus_Dt %3D "974977", %0A  Rcv_Txn_Cd %3D "240027", %0A  Esr_Sec_Id %3D "756141", %0A  Act_Elptm_Tm %3D "1", %0A  Rcv_Elptm_Tm %3D Rcv_Elptm_Tm - 10, %0A  Resp_Code %3D "YBLA65027037", %0A  Resp_Summary %3D "原交易存在" %0Awhere %0A  Trxid %3D "4917766fEec7eb03" %0A  and Sec_Trace_Id %3D "1000000000000000000088794" %0A  and Sys_Tx_Type %3D "098794" %0A  and Sys_Date %3D "20221221"&db=rcc_lianji_deng&user=lukatai&9.192.176.68:4004=2&backend=9.192.176.68:4004&autocommit=0&new_connnum=0&conn_tc=0&select_result_sum=0&affect_num=258&hold_conns=2&resultcode=0&mc_request=0&event_timecost=0.270&timecost=62.820
```



[2024-09-11 17:21:16 511982] INFO topic=group_1724725182_162&tid=354063&con=0x7facfe01f000&qid=13074461-1726045792-31868&splited=0&clientIP=9.250.178.253:49606&proxyHost=9.192.172.132:15006&sql_size=559&sql_type=3&sub_sql_type=4&sql=%0Aupdate %0A  tbl_wl_jnl_a_10001_0%0Aset %0A  Txn_Status %3D "45", %0A  Card_Txn_Status %3D "94", %0A  Auth_No %3D case when "58" is null then Auth_No when "60" %3D '' then Auth_No else "29" end, %0A  Sys_Rcv_Sec_Id %3D "918292", %0A  Host_Bus_Dt %3D "974977", %0A  Rcv_Txn_Cd %3D "240027", %0A  Esr_Sec_Id %3D "756141", %0A  Act_Elptm_Tm %3D "1", %0A  Rcv_Elptm_Tm %3D Rcv_Elptm_Tm - 10, %0A  Resp_Code %3D "YBLA65027037", %0A  Resp_Summary %3D "原交易存在" %0Awhere %0A  Trxid %3D "4917766fEec7eb03" %0A  and Sec_Trace_Id %3D "1000000000000000000088794" %0A  and Sys_Tx_Type %3D "098794" %0A  and Sys_Date %3D "20221221"&db=rcc_lianji_deng&user=lukatai&9.192.176.68:4004=2&backend=9.192.176.68:4004&autocommit=0&new_connnum=0&conn_tc=0&select_result_sum=0&affect_num=258&hold_conns=2&resultcode=0&mc_request=0&event_timecost=0.270&timecost=62.820







### 五、客户场景测试集验证（业务workload）

客户场景测试集验证：共发现**0**个bug，解决**0**个，拒绝**0**个，未解决**0**个。

在本次版本迭代测试中，性能工程团队根据金证、人民银行、中建数科等**10余**客户现网问题进行沉淀，累计用例共**88例**现网问题用例进行测试回归，详见测试计划（https://zhiyan.woa.com/testx/2186/planning/#/testx/tdsqlv2/plan/plans/268051/exec），主要分类与发现的问题如下：

集中式复杂sql场景：累计用例10个，发现问题0个

分布式复杂sql场景：累计用例51个，发现问题0个

大文本插入场景：累计用例2个，发现问题0个

虚拟列复杂sql查询场景：累积用例25个，发现问题0个

建行收单业务仿真场景测试：累计场景8个，累计用例13个，发现问题0个
