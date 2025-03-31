https://tapd.woa.com/tapd_fe/10129291/story/detail/1010129291119334860
 @lukatai(台晨光) 这个单在22.4.3转测后回归，可以先看一下有没有啥问题





jmter自动化后在add





问题版本

22.3.13





sysbench --mysql-host=9.40.30.196 --mysql-port=15012 --mysql-user=lukatai --mysql-password=Root@@@123 --mysql-db=ccb_broad_table  --report_interval=5 --time=600 ./t.lua run