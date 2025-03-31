```
sysbench --threads=2 --events=2 --report-interval=2 /home/sysbench-master/src/lua/oltp_select_yinhe_largeTextInsert_27w.lua  --mysql-host=9.40.30.196,9.40.33.197,9.40.37.138 --mysql-port=15018 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=yinhe run
```



md，没有建立数据库





```
mysqldump -h9.40.33.197 -P15016 -ulukatai -pRoot@@@123 v7pro > v7pro_td20.sql


```





2.5-->2.0 的数据转换有问题