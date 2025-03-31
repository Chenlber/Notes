```
cd /data/application/zookeeper/bin/  
./zkCli.sh -server 9.30.4.49:2118

get /tdsqlzk/filebeat/9.30.4.49@filebeathelper 
get /tdsqlzk/filebeat/9.30.4.49@filebeathelper 
```





![image-20240905162046886](zk使用.assets/image-20240905162046886.png)





get 命令后的路径不存在



![企业微信截图_063f3467-df81-4761-bb9f-3160a8607e37](zk使用.assets/企业微信截图_063f3467-df81-4761-bb9f-3160a8607e37.png)



## gts_check_interval,resv_gts_gap修改

```
resv_gts_gap修改
1、cd /data/application/zookeeper/bin
2、 ./zkCli.sh -server 127.0.0.1:2118
3、查看参数：
get /tdsqlzk/xxx/grouppriv
（xxx是实例id）
4、set /tdsqlzk/xxx/grouppriv '查到的内容'

gts_check_interval修改
1、zk机器cd /data/application/scheduler/conf
2、修改scheduler.xml文件中的gts_check_interval参数
```





```
get /tdsqlzk/group_1724725182_162/grouppriv
```



![企业微信截图_8aae7e5d-5cc4-4c0d-96e3-5dbb8d5965a1](zk使用.assets/企业微信截图_8aae7e5d-5cc4-4c0d-96e3-5dbb8d5965a1.png)
