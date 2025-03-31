## 修改event_threads

查看







event_threads参数（只修改压测proxy）在/data/tdsql_run/15021/gateway/conf/instance_15021.cnf文件里，改为cpu核数，这里需要根据端口修改，修改完成后使用tdsql用户进行重启，命令su - tdsql，重启 在/data/tdsql_run/15021/gateway/bin路径下执行./restart_proxy.sh instance_15021，重启后可以通过命令ps -ef | grep 15021查询是否重启完成

