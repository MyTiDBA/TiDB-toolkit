#### 0 基线版本

该工具基于官方的`0cd918a835ca393b48126a4363be44df4245042f`版本修改。

#### 1 pt-kill

由于TiDB的`kill`命令与MySQL的不同，因此需要简单修改，支持TiDB。

使用方法：

```
# 添加 --tidb参数，便可以kill TiDB的连接
# 例如，每隔10秒探测执行时间超过5秒的查询
pt-kill \
--no-version-check \
--host 10.5.212.17 \
--charset utf8 \
--match-command Query \
--busy-time 5 \
--kill \
--victims all \
--interval 10 \
--tidb 
```

#### 2 pt-query-degist

由于TiDB的慢日志的时间格式与MySQL的慢日志格式不兼容，因此需要简单修改，支持TiDB 3.0格式。

使用方法：

```
# 直接分析慢查询
pt-query-digest slow.log > result.log

# 最近12小时
pt-query-digest  --since=12h  slow.log > result.log

# 时间范围
pt-query-digest slow.log --since '2019-06-18 09:30:00' --until '2019-06-18 10:00:00' > result.log

# 按照SQL类型查询
pt-query-digest --filter '$event->{fingerprint} =~ m/^select/i' slow.log > result.log

# 按用户查询
pt-query-digest --filter '($event->{User} || "") =~ m/^superadmin/i'  test.log > slow.log

# 只查询客户端发送的SQL
pt-query-digest --filter '($event->{Is_internal} || "") =~ m/^false/i'  test.log > slow.log

# 访问特定DB的SQL
pt-query-digest --filter '($event->{DB} || "") =~ m/^test1/i'  test.log > slow.log

# 某个连接发送过来的SQL
pt-query-digest --filter '($event->{Conn_ID} || "") =~ m/^810559/i'  test.log > slow.log

# 查看属性信息
pt-query-digest test.log --filter 'print Dumper $event' --no-report 

# 输出json (可以分析是否一条SQL走了不同的索引等等)
pt-query-digest test.log --output=json

```

