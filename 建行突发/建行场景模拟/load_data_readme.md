## Load_data工具使用说明

### 依赖

- 依赖库信息

```
ubuntu系列：apt-get install expat   
centos系列：yum install expat expat-devel -y
```

---

### 原理

- noshard表和global表对数据文件不切分，只是执行导入sql，区别是对于noshard表只导入一个set，global导入所有set。使用透传语法。
  导入时使用多线程，根据定义的块大小对数据文件进行位置选取，每个线程获取一个文件块范围后通过多线程执行load data infile导入。
- groupshard表,subshard二级分区表和range/list全局分区表的导入首先会根据块大小对数据文件进行位置选取后，每个线程获取一个文件块范围后会对该块数据进行切分，根据要导入到不同的set将块数据分为多个子文件然后执行导入。区别在于切分阶段groupshard表使用每行数据的shardkey进行hash，然后结合每个set的hash范围获得要发往的setname，进而切分为子文件进行通过load
data
infile导入。subshard二级分区表会在groupshard表的基础上再次细化每个set的数据文件，按照分区范围将发往同一个set的文本再次细化为发往一个set的某一个分区的文件。range/list表则根据分区范围或者list范围定位到要发往的set，再切分为文件通过load
data infile发送。
- 当开启了自增列补充数据功能时,对于global表,groupshard表,subshard二级分区表和range/list全局分区表的导入会切分数据文件，通过load_data补充自增列数据后，执行导入。（目前补充数据支持默认缺少自增列，fields参数缺少自增列；对于定长导入缺少自增列仅做报错处理）

---

### 架构设计

主线程根据设定的块大小(假设为128k)先获取128k大小的分块，再往后定位到行结束符，从而获得一个分块range=(begin_pos，end_pos)。由此将一个文件分为多个range存在队列里。再根据设定的线程数生成多个线程，每个线程从队列里取得一块数据的位置，判断表类型如为noshard或者global则直接发送该块数据内的数据，不生成中间文件。如为shard表，全局分区表，二级分区，则按行读取该块数据内的数据，根据发往的set/分区的不同将数据切分到子文件中，再通过load data infile发送。线程处理完一个range后，如导入不出现警告或者报错则删除子文件，再处理其他range。如出现警告或者报错，则将报错子文件存于./tmp_error目录下，将警告子文件存放在./tmp_warning目录下，再根据skip_error参数的值，决定是否再取其他range继续处理。

---

### 功能介绍

- 支持noshard单表，global广播表，groupshard分表，subshard二级分区表以及range/list全局分区表的导入。
- 支持进度条显示，包括实时导入记录数，总进度百分比，实时导入速率，耗时等。
- 支持命令行导入（类似--ip=127.0.0.1）和文件参数导入(配置文件load.ini)。
- 支持自定义导入线程，分割块(chunk_size)大小等参数。
- 支持导入部分字段，使用 --fields ="(id,k,@jump,pad)"参数跳过第三列数据只导入3个字段，或者--fields ="(id,k)"导入两个字段。
- 支持参数设定日志等级，当日志等级设置为debug时（--log_level=2）可以查看详细的报错信息，定位到具体的行。报错或者报警告的子文本会保存在./tmp_error或者./tmp_warning目录下。
- 支持后台运行，通过设置--is_daemon=true开启后台模式，开启后可通过信号量(kill -s SIGUSR1 pid)的方式获得实时的导入进度。同时log日志每隔5min打印进度。
- 支持执行前置sql,通过参数--prefix_sqls能在导入前执行sql，对于设置net_read/write_timeout等参数也可以在此后追加,以分号隔开每个sql。目前默认运行
```
"SET unique_checks = 0; SET foreign_key_checks = 0;SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED"
```
- 支持文件夹导入
- 支持位置参数
- 支持自动重试
- 支持断点续传
- 支持设定出错时是退出线程还是继续执行其他range导入
- 支持参数开启文件列数与字段数检查功能
- 支持密码加密，例如运行./load_data  --password=test --encrypt_pwd会使用默认密钥将密码test加密，返回加密后的密码密文。同时支持指定加密密钥encrypt_secret_key。
- 支持使用加密密码进行导入，例如导入时password设置为密文，则指定use_encrypt_pwd=1后可以顺利导入。同时支持指定解密密钥encrypt_secret_key。
- 支持定长解析导入
- 支持缺少自增列数据导入


---

### 执行条件

0. 执行load_data的用户需要有bin目录下的创建目录、创建文件的权限。
1. TDSQL实例需要做如下设置（导入结束后需手动恢复）：
* 调整复制模式为异步复制
* 开启Set免切设置
* 尽量调大数据库超时参数net_read_timeout/net_write_timeout/innodb_lock_wait_timeout
* 尽量调大数据库binlog拦截参数binlog_write_threshold
* 数据库关闭双1参数sync_binlog/innodb_flush_log_at_trx_commit
* 数据库开启LOCAL方式导入数据参数local_infile
* 数据库磁盘空间检查（预计总量占用2～3倍CSV文件空间）
* MySQL 8.0占用内存过多，需要预留足够内存

---

### 导入指令

导入指令有3种：命令行导入，位置参数导入和配置文件导入（执行./load_data 可以看到help信息。）

- 命令行导入：

```
./load_data --ip=127.0.0.1 --port=15026 --user=test --password=test --db_table=loaddata.sbtest_shard --file=sbtest_4l.txt --field_enclosed=" " --field_terminated=" " --thread_num=128 
```

- 位置参数导入（支持mode0/mode1/mode2/mode3/mode4）

```
/load_data mode0/mode1 proxy_host proxy_port user password db_table shardkey_index file field_terminate filed_enclosed [split_size] [escaped by]
example:
   example:./load_data mode1 10.10.10.10 3336 test test123 shard.table  1 '/tmp/datafile'  ' ' ''
   mode0: 只拆分数据，不进行数据导入。
   mode1：用insert ignore导入的方式，并且skip_error是false，遇见错误直接退出。
   mode2：用insert ignore导入的方式，并且skip_error是true，遇见错误不停止。
   mode3：用replace 导入方式，并且skip_error是false，遇见错误直接退出。
   mode4：用replace 导入方式，并且skip_error是true，遇见错误不停止。
```

- 配置文件导入：

```
./load_data –config=load.ini
其中load.ini格式参考文件load.ini
```
配置文件load.ini格式如下：
```
[Server]
ip=127.0.0.1
port=15006
user=test
password=test
db_table=test.test
file=test.txt
thread_num=2
field_terminated=","    (字段分隔符为双引号)
field_enclosed="""   (字段括起符为双引号)
log_level=2
```

---

### 配置参数说明

常用参数:

```
  --help   说明:获取帮助信息
  --ip=127.0.0.1 说明:proxy的ip地址
  --port=15006   说明:proxy的端口
  --user=test    说明:登陆proxy的用户名
  --password=test 说明:登陆proxy的秘密
  --db_table=test.test 说明:指定需要导入的库名和表名，如test.sbtest
  --file=data200M.txt  说明:导入文件的位置，如--file=/data2/load_new/2.txt;当为目录时，表示文件夹导入
  --field_terminated=" "  说明:字段间隔符，如空格(" "),逗号(","),制表符("\t" )等
  --field_enclosed=" "    说明:字段括起符，如为空格(" "),双引号号("\"")等，注：双引号在命令行和配置文件中有区别
  
  --config=load.ini 说明:配置文件导入模式，配置此参数后，其他直接读取配置文件的参数配置

不常用参数:
  --thread_num=1          说明:导入线程数,默认值为4
  --chunk_size=10         说明:导入块大小（KB）默认值为与文件大小相关的一个分段函数
  --escaped_by="\\"       说明:转义字符，默认值为反斜线，配置文件中需使用escaped_by="\"
  --lines_terminated="\n"  说明:行间隔符,支持"\n","\r","\r\n"等
  --fields_optionally_enclosed=false 说明:是否选择性括住CHAR、VARCHAR和TEXT等字符型字段
  --prefix_sqls="SET foreign_key_checks"   说明:前置运行sql，默认运行"SET unique_checks = 0", "SET foreign_key_checks = 0","SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED"这3条sql。在使用配置文件时，需要去掉引号
  --replace_duplicates=false   说明:是否开启替换模式，替换已存在的记录
  --fields="(id,k,@jump,pad)"   说明:导入部分字段(id,k,@jump,pad),在使用配置文件时，需要去掉引号
  --log_level=5   说明:设置日志等级，默认为LOG_INFO，开启debug日志请将等级设置为2
  --is_daemon=false 说明:是否开启后台运行模式，默认关闭
  --retry=1  说明:设置重试模式。-1表示当导入出现错误时一直重连；0表示不重试；1表示重试一段时间，需结合retry_time一起使用
  --retry_time=10 说明:当retry为1时，指定重试时间,单位是min，默认一个10min
  --skip_error=false 说明:是否跳过错误，即当发生错误时，是停止导入（false）还是跳过错误（true）
  --Breakpoint=false 说明:是否开启断点重传
  --column_check=false 说明:是否开启字段检查功能，注意此功能与导入部分字段功能冲突，无法同时使用. 注：该参数在单表和广播表导入时无效
  --client_timeout=1000 说明:mysql api的超时参数，默认是1000s
  --character_set="gbk" 说明:指定导入文件编码格式，在使用配置文件时，需要去掉引号
  --suffix_sqls="SET column2 = @var1/100" 说明:执行导入sql时追加的后缀信息，通常需要和fields一起使用; 在使用配置文件时，需要去掉引号
  --use_encrypt_pwd=false 说明:是否使用了加密的password，默认未使用。当use_encrypt_pwd=false时表示password为明文;use_encrypt_pwd=true时表示使用了加密的password
  --encrypt_secret_key="abcdefgh" 说明:当password为加密密码时，支持指定解密密钥，不输入时使用默认密钥解密。在使用配置文件时，需要去掉引号。注：密钥长度至少8个字符
  --encrypt_pwd 说明:通过命令行指令获取密码加密后的密文，只在命令行参数时有效
  --set_length="a(1:5),b(6:10)[*2],c(11:15)[/100]" 说明:开启定长解析功能，中括号内可选填四则函数。将数据文件每一行的第1到5字节导入为字段a的值，第6到10字节数据进行*2函数处理再导入为b字段的值，第11到15字节数据进行/100函数处理再导入为字段c的值
  --remove_space_mode=0 说明:当开启定长解析时，设置去除空格的方式。取值为：-1（去除左边空格），0（默认值，不去除左右空格），1（去除右边空格），2（去除左右两边空格）
  --tmp_file_path="./" 说明:自定义临时文件存放路径，默认为load_data二进制文件所在路径
  --log_file_path="/data/log/" 说明:日志文件存放的路径，默认为数据文件同级目录
  --generate_auto_inc_data=false 说明：当缺少自增列数据时，是否自动补充数据，默认进行报错处理不补充数据
  --get_auto_inc_type=0 说明：load_data 获取自增列类型和号段的方式，0: 表示从zk获取自增类型，从ZK/DB获取自增号段（默认参数，loaddata工具需要具有访问zk/db的权限）；1: 表示从DB获取自增类型，从DB获取自增号段（loaddata工具需要具有访问db的权限，且目标表是save_in_db=1 时创建的表）；2: 从PROXY获取自增类型，从PROXY获取自增号段（需要user帐号有xa.auto_inc_table表权限或额外配置admin_user帐号，且目标表是save_in_db=1 时创建的表）；3: 使用PROXY命令获取自增号段（需要proxy版本支持tdsql_autoincval）
  --admin_user="admin_user" 说明: 当需要自动补充自增数据，并且无法直接连接到后端 DB 的时候，普通用户连接 proxy 做透传是没有修改自增表的权限的。因此可以通过该参数来手动指定管理员账号和密码
  --admin_passwd="admin_passwd" 说明: 与 admin_user 对应的密码
  --zk_iplist="127.0.0.1:2181" 说明：当需要补充自增列数据导入时，需要设置zk的iplist信息
  --ignore_lines=2 说明:跳过文件前n行，例如跳过文件前10行则--ignore_lines=10，注意：ignore和断点续传功能冲突，无法同时使用
  --auto_inc_step=1000 说明:当使用generate_auto_inc_data补充自增列时,按auto_inc_step设置的步长取自增号段
```

---

### 举例说明
假设有表test：
```
CREATE TABLE `test` (
  `a` int(30) NOT NULL,
  `b` int(30) DEFAULT NULL,
  `c` int(30) NOT NULL,
  PRIMARY KEY (`a`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
1. 已有数据文件test1.txt格式如下：
```
"1","11","111"
"2","22","222"
```
将test.txt导入test表可以使用下面指令导入:
```
./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.test --file=/data1/test1.txt --field_enclosed="\"" --field_terminated="," --log_level=2;
```
2. 已有数据文件test2.txt格式如下：
```
"1","aaa","11","111"
"2","bbb","22",,"222"
```
想要跳过test2.txt的第二列数据，将数据导入表test，可以使用以下指令：
```
./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.test --file=/data1/test1.txt --field_enclosed="\"" --field_terminated="," --fields="(a,@jump,b,c)" --log_level=2;
```
3. 已有数据文件test3.txt格式如下：
```
"1",,"111"
"2",,"222"
```
想将第二列的空值导入表中为NULL，可以使用fields参数结合suffix_sqls参数来实现，具体指令如下：
```
./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.test --file=/data1/test3.txt --field_enclosed="\"" --field_terminated="," --fields="(a, @b, c)" --suffix_sqls="SET b = NULLIF(@b,'')" --log_level=2;
```
4. 获取密码加密后的密文
```
./load_data  --password=test --encrypt_pwd --encrypt_secret_key="abcdefgh"  说明:使用密钥"abcdefgh"将密码test加密，encrypt_secret_key不指定时将使用默认密钥
```
5. 定长解析导入，假设已有数据文件test.txt内容如下:
```
111112222233333
222223333344444
333334444455555
```
表结构为：
```
CREATE TABLE `test` (
  `a` char(5) CHARACTER SET utf8 COLLATE utf8_bin NOT NULL,
  `b` char(5) CHARACTER SET utf8 COLLATE utf8_bin DEFAULT NULL,
  `c` char(5) CHARACTER SET utf8 COLLATE utf8_bin DEFAULT NULL,
  PRIMARY KEY (`a`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COLLATE=utf8_bin
```
想要将每一行的第1-5字节的数据导入字段a，第6-10字节导入字段b,第11-15字段导入字段c，且进行左右去空格，可以使用如下指令
```
./load_data --ip=127.0.0.1 --password=test --user=test --port=15037 --db_table=vv.test --file=./test.txt --set_length="a(1:5),b(6:10),c(11:15)" --remove_space_mode=2 --log_level=2
```
6. 指定日志文件和临时文件路径，已有数据文件test1.txt格式如下：
```
"1","11","111"
"2","22","222"
```
将test.txt导入test表,指定临时文件存放在./tmp目录下，日志文件存放在./log目录下，可以使用下面指令导入:
```
./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.test --file=/data1/test1.txt --field_enclosed="\"" --field_terminated="," --log_level=2 --log_file_path="./log" --tmp_file_path="./tmp";
```
7. 缺少自增列数据导入，已有数据文件tt1.txt格式如下：
```
1 1
2 2
3 3
```
tt1表结构为：
```
CREATE TABLE `tt1` (
  `a` int NOT NULL,
  `b` int NOT NULL,
  `c` int NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`a`),
  KEY `c_1` (`c`)
) ENGINE=InnoDB AUTO_INCREMENT=14004 DEFAULT CHARSET=utf8mb3 COLLATE=utf8_bin shardkey=a
```
将tt1.txt导入tt1表,设置自动生成数据，可以使用下面指令导入:
```
./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.tt1 --file=/data1/tt1.txt --field_enclosed="" --field_terminated=" " --log_level=2 --generate_auto_inc_data=1 --zk_iplist="127.0.0.1:2181";
```
8. 定长解析导入支持函数，假设已有数据文件test.txt内容如下:
```
100200300
110220330
111222333
```
表结构为：
```
CREATE TABLE `test` (
  `a` int NOT NULL,
  `b` int DEFAULT NULL,
  `c` int DEFAULT NULL,
  PRIMARY KEY (`a`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COLLATE=utf8_bin shardkey=a
```
想要将每一行的第1-3字节的数据，进行*2函数处理再导入字段a，第4-6字节进行+100函数处理再导入字段b,设置字段c为固定值123，且进行左右去空格，可以使用如下指令
```
./load_data --ip=127.0.0.1 --password=test --user=test --port=15037 --db_table=vv.test --file=./test.txt --set_length="a(1:3)[*2],b(4:6)[+100]" --remove_space_mode=2 --log_level=2 --suffix_sqls=",c=123"
```
9. 定长解析模式下，缺少自增列数据导入，已有数据文件tt1.txt格式如下：
```
111111
222222
333333
444444
555555
```
表结构为：
```
CREATE TABLE `test` (
  `a` int NOT NULL,
  `b` int DEFAULT NULL,
  `c` int DEFAULT NULL,
  `d` int NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`a`,`c`),
  KEY `auto` (`d`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COLLATE=utf8_bin shardkey=a
```
将tt1.txt文件中的数据导入test表，设置自动生成数据，可以使用如下的指令
```
./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.test --file=/data1/tt1.txt --set_length="a(1:2),b(3:4),c(5:6)" --remove_space_mode=2 --log_level=2 --generate_auto_inc_data=1 --zk_iplist="127.0.0.1:2181";

无法连接 zk，但是可以连接 DB

./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.test --file=/data1/tt1.txt --set_length="a(1:2),b(3:4),c(5:6)" --remove_space_mode=2 --log_level=2 --generate_auto_inc_data=1 --get_auto_inc_type=1;

无法连接 zk 和 db，但是可以使用管理员权限（具有xa.auto_inc_table权限）的账号时

./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.test --file=/data1/tt1.txt --set_length="a(1:2),b(3:4),c(5:6)" --remove_space_mode=2 --log_level=2 --generate_auto_inc_data=1 --get_auto_inc_type=2 --admin_user=admin_user --admin_passwd=admin_passwd;

无法连接 zk 和 db，但是 proxy 支持获取自增号段的命令

./load_data  --ip=127.0.0.1 --port=15009  --user=test --password=test --db_table=loaddata.test --file=/data1/tt1.txt --set_length="a(1:2),b(3:4),c(5:6)" --remove_space_mode=2 --log_level=2 --generate_auto_inc_data=1 --get_auto_inc_type=3;

```

### 问题定位

+ 问题描述: load data failed, errmsg: ret: -1, errno: 2013: info: query error[gone away agin]
  * 问题原因:导入连接中断，可以尝试降低线程数和块大小再重新导入。或者增大client_timeout参数的值

+ 问题描述:"Local_infile is OFF! Please open local_infile permission in Chitu"
  * 问题原因:导入权限未开启，需要通过赤兔开启导入权限。
  
+ 问题描述:配置文件设置参数prefix_sqls="CREATE DATABASE IF NOT EXISTS test"，运行时query sql failed
  * 问题原因:配置文件中prefix_sqls参数设置时，需要去掉双引号。

---

### 日志说明

- 日志位置：与数据文件的同级目录下（当导入文件夹时，日志文件在导入文件夹下的log子目录里）
- 默认情况下，日志等级为LOG_INFO，只会显示info和warning，error信息，想要查看更多信息，可以通过--log_level参数将日志等级设为2，即可查看debug信息。
- load_data会产生两个日志文件：文件名.discard.log和文件名.log
    * 文件名.discard.log:收集不符合要求的行数据。比如将"abc"导入对应字段类型是int的表中，会认为该行数据有问题，将该条数据写入discard.log。如果开启column_check后，多列或者少列的错误行会记录到此日志。此外对于导入阶段能定位到具体行号的告警，其对应的有问题的行数据也会写入此日志。
    * 文件名.log:导入阶段整体的运行流程日志会在文件名.log中体现，同时该日志也会有整体导入进度的信息，导入进度每隔5min打印一次，尾部也会打印进度信息。

---

### 其他

- 当用户未设置chunk_size参数时，chunk_size默认值为如下的分段函数, 
    * 当文件<=1280k时，chunk_size=128k (说明：实际导入时，如果设置的chunk_size小于128K时，会使用128K作为导入块大小)
    * 当1280k<文件<100M时，chunk_size=(文件/10k)k 
    * 当100M<=文件<=10G时，chunk_size=(文件/100k)k 
    * 当10G<文件时，chunk_size=100M
- 线程数设置值可能与实际通过proxy或者set上观察到的导入并行度有所差别。线程数设置值可以认为是proxy同时执行导入线程的最大值，实际运行时线程由于需要进行切分数据、错误数据搬迁等，导致同一时间proxy上观察到的导入并行度小于线程数
- 配置文件里面的值如果是空格、标点符号和特殊符号需要加双引号，例如配置文件中配置分隔符为逗号,正确的配置是field_terminated="," 错误的配置是field_terminated=,
- 参数设置和命令行稍有区别，配置文件中可以不用多加转义字符，例如配置文件为escaped_by="\\",区别于命令行的--escaped_by="\\\\"
- 暂不支持json格式数据导入
- bit类型数据导入需指定正确的编码方式
- 在使用命令行参数时，谨慎使用含有$1,$#等特殊字符。如必须使用时需要加上反斜杠
- 对于命令行参数，如果只输入参数名的部分字符，在符合唯一识别且正确的情况下能自动补全参数名
- 为了兼容命令行中使用双引号里使用！的问题，在执行导入命令行指令前需要关闭histexpand模式（执行set +H指令）
- 非noshard表和广播表在进行缺少自增列数据的定长导入时，均进行报错处理
- 开启--generate_auto_inc_data=true进行自增列补充数据时，实际导入文件大小会大于数据文件大小
- 在使用参数set_length时，必填的参数field_terminated可以不填
- 在导入有自增列的表时，请尽量保持空表导入，避免自增列重复
- 定长字段只支持增加+-*/四则运算，使用时放在set_length参数中，放在截取字段信息之后，用中括号括起。
- auto_inc_step参数在大于表的自增步长时生效,该参数可控制每次获取自增号段的范围



