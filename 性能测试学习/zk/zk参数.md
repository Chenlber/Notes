

https://tapd.woa.com/tapd_fe/10129291/bug/detail/1010129291114374563









![企业微信截图_4c3bf39f-b6d3-45d6-b9f8-4d26c6a5145d](zk参数.assets/企业微信截图_4c3bf39f-b6d3-45d6-b9f8-4d26c6a5145d.png)



重启zk



![企业微信截图_847c4e2d-c47f-4604-beb9-1107f92f86e7](zk参数.assets/企业微信截图_847c4e2d-c47f-4604-beb9-1107f92f86e7.png)



su - tdsql    cd /data/application/scheduler/bin     ./restart.sh



手动清理：

![image-20240919165809710](zk参数.assets/image-20240919165809710.png)







![企业微信截图_5f1d6362-f7ab-4a28-846d-5b58438def79](zk参数.assets/企业微信截图_5f1d6362-f7ab-4a28-846d-5b58438def79.png)



## 查看参数

在管控机器， 进入/data/application/scheduler/conf目录下，修改scheduler.xml文件中的gts_check_interval，resv_gts_gap

"gts_check _interval"默认值=600

所有管控机器都得改

重启生效
