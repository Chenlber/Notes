```
def dataSource = vars.getObject("insert1").configuredDataSource
dataSource.defaultAutoCommit = null
dataSource.autoCommitOnReturn = false
```



```
./myodbc-installer  -d -a -n "MySQL ODBC 5.1 Driver"  -t "DRIVER=/usr/local/lib/libmyodbc9w.so;SETUP=/usr/local/lib/libmyodbc9w.so"
```

