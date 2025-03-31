
本文关注如何后台执行sql，并且可以获取到sql到结果



mysql -h 9.192.169.254 -P 15010 -u lukatai -pRoot@@@123 rccf_opti -e "select 1;"


nohup 


vvv




```shell
#!/bin/bash

# 定义范围和任务数
START_ID=10000000000000000
END_ID=20000000799999995
STEP=1000000000000000
TASKS=10

# 启动并行任务
for ((i=0; i<TASKS; i++))
do
    LOWER=$((START_ID + i * STEP))
    UPPER=$((LOWER + STEP - 1))

    mysql -h host -u user -p password -D database <<EOF &
    DELETE FROM your_table
    WHERE Sys_Date_Time < '2023-01-01 00:00:00' AND Sec_Trace_Id BETWEEN $LOWER AND $UPPER
    LIMIT 10000;
    EOF
done
```

 Shell 脚本中，`EOF` 是 **End Of File** 的缩写，用于表示 **Here Document**（嵌入文档）的结束标记。它的作用是告诉 Shell 脚本，从 `<<EOF` 开始到 `EOF` 之间的内容是一个多行输入块，通常用于向命令传递多行输入。
 
在你的脚本中，EOF 用于向 mysql 命令传递多行 SQL 语句。以下是关键点：
1. `<<EOF`：
表示开始一个 Here Document，后面的内容会被作为输入传递给前面的命令（这里是 mysql）。
EOF 是一个标记符，可以是任意字符串（如 END、SQL 等），但必须与结束标记一致。
2. `EOF`：
表示 Here Document 的结束标记。脚本会从 <<EOF 开始读取内容，直到遇到单独的 EOF 为止。
3. `&`：
表示将命令放到后台执行，从而实现并行删除。

