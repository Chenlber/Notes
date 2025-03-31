https://my.oschina.net/u/4150306/blog/5574141

TDSQL 高可用一般是通过探活检测，其利用一个 agent 模块定期的连接 DB，并向 TDSQL 自建的一个心跳表中写入数据，这样无论是磁盘坏块，磁盘满了还是 DB 重启导致 DB 不可用，agent 都能准确的判断出来，