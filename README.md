# RedisRedis 设计与实现
Contents
1 第一部分：内部数据结构3
1.1 简单动态字符串. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 3
1.1.1 sds 的用途. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 3
1.1.2 Redis 中的字符串. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 4
1.1.3 优化追加操作. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 5
1.1.4 sds 模块的API . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 7
1.1.5 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 8
1.2 双端链表. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 8
1.2.1 双端链表的应用. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 9
1.2.2 双端链表的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 10
1.2.3 迭代器. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 11
1.2.4 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 12
1.3 字典. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 12
1.3.1 字典的应用. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 12
1.3.2 字典的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 14
1.3.3 创建新字典. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 17
1.3.4 添加键值对到字典. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 18
1.3.5 添加新元素到空白字典. . . . . . . . . . . . . . . . . . . . . . . . . . . . 20
1.3.6 添加新键值对时发生碰撞处理. . . . . . . . . . . . . . . . . . . . . . . . 21
1.3.7 添加新键值对时触发了rehash 操作. . . . . . . . . . . . . . . . . . . . . 22
1.3.8 Rehash 执行过程. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 24
1.3.9 渐进式rehash . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 28
1.3.10 字典的收缩. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 30
1.3.11 字典其他操作. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 31
1.3.12 字典的迭代. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 31
1.3.13 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 32
1.4 跳跃表. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 32
1.4.1 跳跃表的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 33
1.4.2 跳跃表的应用. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 34
1.4.3 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 35
2 第二部分：内存映射数据结构37
2.1 整数集合. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 37
2.1.1 整数集合的应用. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 37
2.1.2 数据结构和主要操作. . . . . . . . . . . . . . . . . . . . . . . . . . . . . 38
2.1.3 intset 运行实例. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 38
2.1.4 升级. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 41
2.1.5 关于升级. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 43
i
2.1.6 关于元素移动. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 43
2.1.7 其他操作. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 43
2.1.8 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 44
2.2 压缩列表. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 44
2.2.1 ziplist 的构成. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 45
2.2.2 节点的构成. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 46
2.2.3 创建新ziplist . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 49
2.2.4 将节点添加到末端. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 49
2.2.5 将节点添加到某个/某些节点的前面. . . . . . . . . . . . . . . . . . . . . 52
2.2.6 删除节点. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 53
2.2.7 遍历. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 54
2.2.8 查找元素、根据值定位节点. . . . . . . . . . . . . . . . . . . . . . . . . 54
2.2.9 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 54
3 第三部分：Redis 数据类型57
3.1 对象处理机制. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 57
3.1.1 redisObject 数据结构，以及Redis 的数据类型. . . . . . . . . . . . . . 58
3.1.2 命令的类型检查和多态. . . . . . . . . . . . . . . . . . . . . . . . . . . . 60
3.1.3 对象共享. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 61
3.1.4 引用计数以及对象的销毁. . . . . . . . . . . . . . . . . . . . . . . . . . . 62
3.1.5 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 63
3.2 字符串. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 63
3.2.1 字符串编码. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 63
3.2.2 编码的选择. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 64
3.2.3 字符串命令的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 64
3.3 哈希表. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 64
3.3.1 字典编码的哈希表. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 65
3.3.2 压缩列表编码的哈希表. . . . . . . . . . . . . . . . . . . . . . . . . . . . 66
3.3.3 编码的选择. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 66
3.3.4 哈希命令的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 66
3.4 列表. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 66
3.4.1 编码的选择. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 67
3.4.2 列表命令的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 67
3.4.3 阻塞的条件. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 67
3.4.4 阻塞. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 68
3.4.5 阻塞因LPUSH 、RPUSH 、LINSERT 等添加命令而被取消. . . . . . . 69
3.4.6 先阻塞先服务（FBFS）策略. . . . . . . . . . . . . . . . . . . . . . . . . 72
3.4.7 阻塞因超过最大等待时间而被取消. . . . . . . . . . . . . . . . . . . . . 73
3.5 集合. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 73
3.5.1 编码的选择. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 74
3.5.2 编码的切换. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 74
3.5.3 字典编码的集合. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 74
3.5.4 集合命令的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 75
3.5.5 求交集算法. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 75
3.5.6 求并集算法. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 76
3.5.7 求差集算法. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 76
3.6 有序集. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 77
3.6.1 编码的选择. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 78
3.6.2 编码的转换. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 78
3.6.3 ZIPLIST 编码的有序集. . . . . . . . . . . . . . . . . . . . . . . . . . . . 78
3.6.4 SKIPLIST 编码的有序集. . . . . . . . . . . . . . . . . . . . . . . . . . . 79
ii
4 第四部分：功能的实现81
4.1 事务. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 81
4.1.1 事务. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 81
4.1.2 开始事务. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 82
4.1.3 命令入队. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 82
4.1.4 执行事务. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 84
4.1.5 在事务和非事务状态下执行命令. . . . . . . . . . . . . . . . . . . . . . . 86
4.1.6 事务状态下的DISCARD 、MULTI 和WATCH 命令. . . . . . . . . . . 86
4.1.7 带WATCH 的事务. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 87
4.1.8 WATCH 命令的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . 87
4.1.9 WATCH 的触发. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 88
4.1.10 事务的ACID 性质. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 90
4.1.11 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 92
4.2 订阅与发布. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 92
4.2.1 频道的订阅与信息发送. . . . . . . . . . . . . . . . . . . . . . . . . . . . 92
4.2.2 订阅频道. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 93
4.2.3 发送信息到频道. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 94
4.2.4 退订频道. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 95
4.2.5 模式的订阅与信息发送. . . . . . . . . . . . . . . . . . . . . . . . . . . . 95
4.2.6 订阅模式. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 96
4.2.7 发送信息到模式. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 97
4.2.8 退订模式. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 98
4.2.9 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 98
4.3 Lua 脚本. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 99
4.3.1 初始化Lua 环境. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 99
4.3.2 脚本的安全性. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 100
4.3.3 脚本的执行. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 101
4.3.4 EVAL 命令的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 102
4.3.5 EVALSHA 命令的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . 104
4.3.6 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 105
4.4 慢查询日志. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 105
4.4.1 相关数据结构. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 106
4.4.2 慢查询日志的记录. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 107
4.4.3 慢查询日志的操作. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 108
4.4.4 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 108
5 第五部分：内部运作机制109
5.1 数据库. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 109
5.1.1 数据库的结构. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 109
5.1.2 数据库的切换. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 110
5.1.3 数据库键空间. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 110
5.1.4 键空间的操作. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 111
5.1.5 键的过期时间. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 116
5.1.6 过期时间的保存. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 116
5.1.7 设置生存时间. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 117
5.1.8 过期键的判定. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 118
5.1.9 过期键的清除. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 119
5.1.10 过期键的惰性删除策略. . . . . . . . . . . . . . . . . . . . . . . . . . . . 120
5.1.11 过期键的定期删除策略. . . . . . . . . . . . . . . . . . . . . . . . . . . . 122
5.1.12 过期键对AOF 、RDB 和复制的影响. . . . . . . . . . . . . . . . . . . . 122
5.1.13 数据库空间的收缩和扩展. . . . . . . . . . . . . . . . . . . . . . . . . . . 123
iii
5.1.14 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 124
5.2 RDB . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 124
5.2.1 保存. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 125
5.2.2 SAVE 、BGSAVE 、AOF 写入和BGREWRITEAOF . . . . . . . . . . 126
5.2.3 载入. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 127
5.2.4 RDB 文件结构. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 127
5.2.5 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 131
5.3 AOF . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 131
5.3.1 AOF 命令同步. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 132
5.3.2 命令传播. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 133
5.3.3 缓存追加. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 136
5.3.4 文件写入和保存. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 136
5.3.5 AOF 保存模式. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 136
5.3.6 AOF 保存模式对性能和安全性的影响. . . . . . . . . . . . . . . . . . . . 138
5.3.7 AOF 文件的读取和数据还原. . . . . . . . . . . . . . . . . . . . . . . . . 139
5.3.8 AOF 重写. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 140
5.3.9 AOF 重写的实现. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 141
5.3.10 AOF 后台重写. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 143
5.3.11 AOF 后台重写的触发条件. . . . . . . . . . . . . . . . . . . . . . . . . . 144
5.3.12 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 145
5.4 事件. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 145
5.4.1 文件事件. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 145
5.4.2 时间事件. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 149
5.4.3 时间事件应用实例：服务器常规操作. . . . . . . . . . . . . . . . . . . . 150
5.4.4 事件的执行与调度. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 151
5.4.5 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 153
5.5 服务器与客户端. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 153
5.5.1 初始化服务器. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 154
5.5.2 客户端连接到服务器. . . . . . . . . . . . . . . . . . . . . . . . . . . . . 157
5.5.3 命令的请求、处理和结果返回. . . . . . . . . . . . . . . . . . . . . . . . 158
5.5.4 命令请求实例：SET 的执行过程. . . . . . . . . . . . . . . . . . . . . . 158
5.5.5 小结. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 159
6 关于161
7 通过捐款支持本书163
iv
Redis 设计与实现, 第一版
本书的目标是以简明易懂的方式讲解Redis 的内部运行机制，通过阅读本书，你可以了解到
Redis 从数据结构到服务器构造在内的几乎所有知识。
本书基于Redis 2.6 版本编写，知识点覆盖了该版本绝大部分服务器单机功能。多机功能方面
的内容（比如replication 、sentinel 和cluster）将在未来的新版本中加入。
为了保证内容的简洁性，本书会尽量以高抽象层次的角度来观察Redis ，并将代码的细节留给
读者自己去考究。
如果读者只是对Redis 的内部运作机制感兴趣，但并不想深入代码，那么只阅读本书就足够了。
另一方面，对于需要深入研究Redis 代码的读者，本书附带了一份带有详细注释的Redis 2.6
源代码，可以配合本书一并使用。
Contents 1
Redis 设计与实现, 第一版
2 Contents
第1 章
内部数据结构
Redis 和其他很多key-value 数据库的不同之处在于，Redis 不仅支持简单的字符串键值对，它
还提供了一系列数据结构类型值，比如列表、哈希、集合和有序集，并在这些数据结构类型上
定义了一套强大的API 。
通过对不同类型的值进行操作，Redis 可以很轻易地完成其他只支持字符串键值对的key-value
数据库很难（或者无法）完成的任务。
在Redis 的内部，数据结构类型值由高效的数据结构和算法进行支持，并且在Redis 自身的构
建当中，也大量用到了这些数据结构。
这一部分将对Redis 内存所使用的数据结构和算法进行介绍。
1.1 简单动态字符串
Sds （Simple Dynamic String，简单动态字符串）是Redis 底层所使用的字符串表示，它被用
在几乎所有的Redis 模块中。
本章将对sds 的实现、性能和功能等方面进行介绍，并说明Redis 使用sds 而不是传统C 字符
串的原因。
1.1.1 sds 的用途
Sds 在Redis 中的主要作用有以下两个：
1. 实现字符串对象（StringObject）；
2. 在Redis 程序内部用作char* 类型的替代品；
以下两个小节分别对这两种用途进行介绍。
3
Redis 设计与实现, 第一版
实现字符串对象
Redis 是一个键值对数据库（key-value DB），数据库的值可以是字符串、集合、列表等多种类
型的对象，而数据库的键则总是字符串对象。
对于那些包含字符串值的字符串对象来说，每个字符串对象都包含一个sds 值。
Note: “包含字符串值的字符串对象” ，这种说法初听上去可能会有点奇怪，但是在Redis 中，
一个字符串对象除了可以保存字符串值之外，还可以保存long 类型的值，所以为了严谨起见，
这里需要强调一下：当字符串对象保存的是字符串时，它包含的才是sds 值，否则的话，它就
是一个long 类型的值。
举个例子，以下命令创建了一个新的数据库键值对，这个键值对的键和值都是字符串对象，它
们都包含一个sds 值：
redis> SET book "Mastering C++ in 21 days"
OK
redis> GET book
"Mastering C++ in 21 days"
以下命令创建了另一个键值对，它的键是字符串对象，而值则是一个集合对象：
redis> SADD nosql "Redis" "MongoDB" "Neo4j"
(integer) 3
redis> SMEMBERS nosql
1) "Neo4j"
2) "Redis"
3) "MongoDB"
将sds 代替C 默认的char* 类型
因为char* 类型的功能单一，抽象层次低，并且不能高效地支持一些Redis 常用的操作（比
如追加操作和长度计算操作），所以在Redis 程序内部，绝大部分情况下都会使用sds 而不是
char* 来表示字符串。
性能问题在稍后介绍sds 定义的时候就会说到，因为我们还没有了解过Redis 的其他功能模
块，所以也没办法详细地举例说那里用到了sds ，不过在后面的章节中，我们会经常看到其他
模块（几乎每一个）都用到了sds 类型值。
目前来说，只要记住这样一个事实即可：在Redis 中，客户端传入服务器的协议内容、aof 缓
存、返回给客户端的回复，等等，这些重要的内容都是由都是由sds 类型来保存的。
1.1.2 Redis 中的字符串
在C 语言中，字符串可以用一个\0 结尾的char 数组来表示。
比如说，hello world 在C 语言中就可以表示为"hello world\0" 。
这种简单的字符串表示在大多数情况下都能满足要求，但是，它并不能高效地支持长度计算和
追加（append）这两种操作：
. 每次计算字符串长度（strlen(s)）的复杂度为(N) 。
4 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
. 对字符串进行N 次追加，必定需要对字符串进行N 次内存重分配（realloc）。
在Redis 内部，字符串的追加和长度计算并不少见，而APPEND 和STRLEN 更是这两种操
作在Redis 命令中的直接映射，这两个简单的操作不应该成为性能的瓶颈。
另外，Redis 除了处理C 字符串之外，还需要处理单纯的字节数组，以及服务器协议等内容，
所以为了方便起见，Redis 的字符串表示还应该是二进制安全的：程序不应对字符串里面保存
的数据做任何假设，数据可以是以\0 结尾的C 字符串，也可以是单纯的字节数组，或者其他
格式的数据。
考虑到这两个原因，Redis 使用sds 类型替换了C 语言的默认字符串表示：sds 既可以高效地
实现追加和长度计算，并且它还是二进制安全的。
sds 的实现
在前面的内容中，我们一直将sds 作为一种抽象数据结构来说明，实际上，它的实现由以下两
部分组成：
typedef char *sds;
struct sdshdr {
// buf 已占用长度
int len;
// buf 剩余可用长度
int free;
// 实际保存字符串数据的地方
char buf[];
};
其中，类型sds 是char * 的别名(alias)，而结构sdshdr 则保存了len 、free 和buf 三个
属性。
作为例子，以下是新创建的，同样保存hello world 字符串的sdshdr 结构：
struct sdshdr {
len = 11;
free = 0;
buf = "hello world\0"; // buf 的实际长度为len + 1
};
通过len 属性，sdshdr 可以实现复杂度为(1) 的长度计算操作。
另一方面，通过对buf 分配一些额外的空间，并使用free 记录未使用空间的大小，sdshdr 可
以让执行追加操作所需的内存重分配次数大大减少，下一节我们就会来详细讨论这一点。
当然，sds 也对操作的正确实现提出了要求——所有处理sdshdr 的函数，都必须正确地更新
len 和free 属性，否则就会造成bug 。
1.1.3 优化追加操作
在前面说到过，利用sdshdr 结构，除了可以用(1) 复杂度获取字符串的长度之外，还可以减
少追加(append) 操作所需的内存重分配次数，以下就来详细解释这个优化的原理。
1.1. 简单动态字符串5
Redis 设计与实现, 第一版
为了易于理解，我们用一个Redis 执行实例作为例子，解释一下，当执行以下代码时，Redis
内部发生了什么：
redis> SET msg "hello world"
OK
redis> APPEND msg " again!"
(integer) 18
redis> GET msg
"hello world again!"
首先，SET 命令创建并保存hello world 到一个sdshdr 中，这个sdshdr 的值如下：
struct sdshdr {
len = 11;
free = 0;
buf = "hello world\0";
}
当执行APPEND 命令时，相应的sdshdr 被更新，字符串" again!" 会被追加到原来的
"hello world" 之后：
struct sdshdr {
len = 18;
free = 18;
// 空白的地方为预分配空间，共18 + 18 + 1 个字节
buf = "hello world again!\0 ";
}
注意，当调用SET 命令创建sdshdr 时，sdshdr 的free 属性为0 ，Redis 也没有为buf 创建
额外的空间——而在执行APPEND 之后，Redis 为buf 创建了多于所需空间一倍的大小。
在这个例子中，保存"hello world again!" 共需要18 + 1 个字节，但程序却为我们分配了
18 + 18 + 1 = 37 个字节——这样一来，如果将来再次对同一个sdshdr 进行追加操作，只要
追加内容的长度不超过free 属性的值，那么就不需要对buf 进行内存重分配。
比如说，执行以下命令并不会引起buf 的内存重分配，因为新追加的字符串长度小于18 ：
redis> APPEND msg " again!"
(integer) 25
再次执行APPEND 命令之后，msg 的值所对应的sdshdr 结构可以表示如下：
struct sdshdr {
len = 25;
free = 11;
// 空白的地方为预分配空间，共18 + 18 + 1 个字节
buf = "hello world again! again!\0 ";
}
sds.c/sdsMakeRoomFor 函数描述了sdshdr 的这种内存预分配优化策略，以下是这个函数的
伪代码版本：
def sdsMakeRoomFor(sdshdr, required_len):
# 预分配空间足够，无须再进行空间分配
if (sdshdr.free >= required_len):
return sdshdr
6 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
# 计算新字符串的总长度
newlen = sdshdr.len + required_len
# 如果新字符串的总长度小于SDS_MAX_PREALLOC
# 那么为字符串分配2 倍于所需长度的空间
# 否则就分配所需长度加上SDS_MAX_PREALLOC 数量的空间
if newlen < SDS_MAX_PREALLOC:
newlen *= 2
else:
newlen += SDS_MAX_PREALLOC
# 分配内存
newsh = zrelloc(sdshdr, sizeof(struct sdshdr)+newlen+1)
# 更新free 属性
newsh.free = newlen - sdshdr.len
# 返回
return newsh
在目前版本的Redis 中，SDS_MAX_PREALLOC 的值为1024 * 1024 ，也就是说，当大小小于
1MB 的字符串执行追加操作时，sdsMakeRoomFor 就为它们分配多于所需大小一倍的空间；当
字符串的大小大于1MB ，那么sdsMakeRoomFor 就为它们额外多分配1MB 的空间。
Note: 这种分配策略会浪费内存吗？
执行过APPEND 命令的字符串会带有额外的预分配空间，这些预分配空间不会被释放，除非
该字符串所对应的键被删除，或者等到关闭Redis 之后，再次启动时重新载入的字符串对象将
不会有预分配空间。
因为执行APPEND 命令的字符串键数量通常并不多，占用内存的体积通常也不大，所以这一
般并不算什么问题。
另一方面，如果执行APPEND 操作的键很多，而字符串的体积又很大的话，那可能就需要修
改Redis 服务器，让它定时释放一些字符串键的预分配空间，从而更有效地使用内存。
1.1.4 sds 模块的API
sds 模块基于sds 类型和sdshdr 结构提供了以下API ：
1.1. 简单动态字符串7
Redis 设计与实现, 第一版
函数作用算法复杂度
sdsnewlen 创建一个指定长度的sds ，接受一个C 字符串作
为初始化值
O(N)
sdsempty 创建一个只包含空白字符串"" 的sds O(N)
sdsnew 根据给定C 字符串，创建一个相应的sds O(N)
sdsdup 复制给定sds O(N)
sdsfree 释放给定sds O(N)
sdsupdatelen 更新给定sds 所对应sdshdr 结构的free 和len O(1)
sdsclear 清除给定sds 的内容，将它初始化为"" O(1)
sdsMakeRoomFor 对sds 所对应sdshdr 结构的buf 进行扩展O(N)
sdsRemoveFreeSpace 在不改动buf 的情况下，将buf 内多余的空间释
放出去
O(N)
sdsAllocSize 计算给定sds 的buf 所占用的内存总数O(1)
sdsIncrLen 对sds 的buf 的右端进行扩展（expand）或修剪
（trim）
O(1)
sdsgrowzero 将给定sds 的buf 扩展至指定长度，无内容的部
分用\0 来填充
O(N)
sdscatlen 按给定长度对sds 进行扩展，并将一个C 字符串
追加到sds 的末尾
O(N)
sdscat 将一个C 字符串追加到sds 末尾O(N)
sdscatsds 将一个sds 追加到另一个sds 末尾O(N)
sdscpylen 将一个C 字符串的部分内容复制到另一个sds 中，
需要时对sds 进行扩展
O(N)
sdscpy 将一个C 字符串复制到sds O(N)
sds 还有另一部分功能性函数，比如sdstolower 、sdstrim 、sdscmp ，等等，基本都是标准
C 字符串库函数的sds 版本，这里不一一列举了。
1.1.5 小结
. Redis 的字符串表示为sds ，而不是C 字符串（以\0 结尾的char*）。
. 对比C 字符串，sds 有以下特性：
– 可以高效地执行长度计算（strlen）；
– 可以高效地执行追加操作（append）；
– 二进制安全；
. sds 会为追加操作进行优化：加快追加操作的速度，并降低内存分配的次数，代价是多占
用了一些内存，而且这些内存不会被主动释放。
1.2 双端链表
链表作为数组之外的一种常用序列抽象，是大多数高级语言的基本数据类型，因为C 语言本身
不支持链表类型，大部分C 程序都会自己实现一种链表类型，Redis 也不例外——它实现了一
个双端链表结构。
双端链表作为一种常见的数据结构，在大部分的数据结构或者算法书里都有讲解，因此，这一
章关注的是Redis 双端链表的具体实现，以及该实现的API ，而对于双端链表本身，以及双端
链表所对应的算法，则不做任何解释。
8 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
读者如果有需要的话，可以参考维基百科的双端链表词条，里面提供了关于双端链表的一些基
本信息。
另外，一些书籍，比如《算法：C 语言实现》和《数据结构与算法分析》则提供了关于双端链
表的更详细的信息。
1.2.1 双端链表的应用
双端链表作为一种通用的数据结构，在Redis 内部使用得非常多：它既是Redis 列表结构的底
层实现之一，还被大量Redis 模块所使用，用于构建Redis 的其他功能。
实现Redis 的列表类型
双端链表还是Redis 列表类型的底层实现之一，当对列表类型的键进行操作——比如执行
RPUSH 、LPOP 或LLEN 等命令时，程序在底层操作的可能就是双端链表。
redis> RPUSH brands Apple Microsoft Google
(integer) 3
redis> LPOP brands
"Apple"
redis> LLEN brands
(integer) 2
redis> LRANGE brands 0 -1
1) "Microsoft"
2) "Google"
Note: Redis 列表使用两种数据结构作为底层实现：
1. 双端链表
2. 压缩列表
因为双端链表占用的内存比压缩列表要多，所以当创建新的列表键时，列表会优先考虑使用压
缩列表作为底层实现，并且在有需要的时候，才从压缩列表实现转换到双端链表实现。
后续章节会对压缩链表和Redis 类型做更进一步的介绍。
Redis 自身功能的构建
除了实现列表类型以外，双端链表还被很多Redis 内部模块所应用：
. 事务模块使用双端链表来按顺序保存输入的命令；
. 服务器模块使用双端链表来保存多个客户端；
. 订阅/发送模块使用双端链表来保存订阅模式的多个客户端；
. 事件模块使用双端链表来保存时间事件（time event）；
类似的应用还有很多，在后续的章节中我们将看到，双端链表在Redis 中发挥着重要的作用。
1.2. 双端链表9
Redis 设计与实现, 第一版
1.2.2 双端链表的实现
双端链表的实现由listNode 和list 两个数据结构构成，下图展示了由这两个结构组成的一
个双端链表实例：
listNode
prev value next
listNode
prev value next
listNode
prev value next
list
head
tail
dup
free
match
len
其中，listNode 是双端链表的节点：
typedef struct listNode {
// 前驱节点
struct listNode *prev;
// 后继节点
struct listNode *next;
// 值
void *value;
} listNode;
而list 则是双端链表本身：
typedef struct list {
// 表头指针
listNode *head;
// 表尾指针
listNode *tail;
// 节点数量
unsigned long len;
// 复制函数
void *(*dup)(void *ptr);
// 释放函数
void (*free)(void *ptr);
// 比对函数
int (*match)(void *ptr, void *key);
} list;
注意，listNode 的value 属性的类型是void * ，说明这个双端链表对节点所保存的值的类型
不做限制。
对于不同类型的值，有时候需要不同的函数来处理这些值，因此，list 类型保留了三个函数指
10 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
针——dup 、free 和match ，分别用于处理值的复制、释放和对比匹配。在对节点的值进行处
理时，如果有给定这些函数，那么它们就会被调用。
举个例子：当删除一个listNode 时，如果包含这个节点的list 的list->free 函数不为空，
那么删除函数就会先调用list->free(listNode->value) 清空节点的值，再执行余下的删除
操作（比如说，释放节点）。
另外，从这两个数据结构的定义上，也可以它们的一些行为和性能特征：
. listNode 带有prev 和next 两个指针，因此，对链表的遍历可以在两个方向上进行：从
表头到表尾，或者从表尾到表头。
. list 保存了head 和tail 两个指针，因此，对链表的表头和表尾进行插入的复杂度都为
(1) ——这是高效实现LPUSH 、RPOP 、RPOPLPUSH 等命令的关键。
. list 带有保存节点数量的len 属性，所以计算链表长度的复杂度仅为(1) ，这也保证
了LLEN 命令不会成为性能瓶颈。
以下是用于操作双端链表的API ，它们的作用以及算法复杂度：
函数作用算法复杂度
listCreate 创建一个新链表O(1)
listRelease 释放一个链表，以及该链表所包含的节点O(N)
listDup 创建一个给定链表的副本O(N)
listRotate 取出链表的表尾节点，将它插入到表头O(1)
listAddNodeHead 将一个包含给定值的节点添加到链表的表头O(1)
listAddNodeTail 将一个包含给定值的节点添加到链表的表尾O(1)
listInsertNode 将一个包含给定值的节点添加到某个节点的之前或之后O(1)
listDelNode 删除给定节点O(1)
listSearchKey 在链表中查找和给定key 匹配的节点O(N)
listIndex 给据给定索引，返回列表中相应的节点O(N)
listLength 返回给定链表的节点数量O(1)
listFirst 返回链表的表头节点O(1)
listLast 返回链表的表尾节点O(1)
listPrevNode 返回给定节点的前一个节点O(1)
listNextNode 返回给定节点的后一个节点O(1)
listNodeValue 返回给定节点的值O(1)
1.2.3 迭代器
Redis 为双端链表实现了一个迭代器，这个迭代器可以从两个方向对双端链表进行迭代：
. 沿着节点的next 指针前进，从表头向表尾迭代；
. 沿着节点的prev 指针前进，从表尾向表头迭代；
以下是迭代器的数据结构定义：
typedef struct listIter {
// 下一节点
listNode *next;
// 迭代方向
int direction;
1.2. 双端链表11
Redis 设计与实现, 第一版
} listIter;
direction 记录迭代应该从那里开始：
. 如果值为adlist.h/AL_START_HEAD ，那么迭代器执行从表头到表尾的迭代；
. 如果值为adlist.h/AL_START_TAIL ，那么迭代器执行从表尾到表头的迭代；
以下是迭代器的操作API ，它们的作用以及算法复杂度：
函数作用算法复杂度
listGetIterator 创建一个列表迭代器O(1)
listReleaseIterator 释放迭代器O(1)
listRewind 将迭代器的指针指向表头O(1)
listRewindTail 将迭代器的指针指向表尾O(1)
listNext 取出迭代器当前指向的节点O(1)
1.2.4 小结
. Redis 实现了自己的双端链表结构。
. 双端链表主要有两个作用：
– 作为Redis 列表类型的底层实现之一；
– 作为通用数据结构，被其他功能模块所使用；
. 双端链表及其节点的性能特性如下：
– 节点带有前驱和后继指针，访问前驱节点和后继节点的复杂度为O(1) ，并且对链表
的迭代可以在从表头到表尾和从表尾到表头两个方向进行；
– 链表带有指向表头和表尾的指针，因此对表头和表尾进行处理的复杂度为O(1) ；
– 链表带有记录节点数量的属性，所以可以在O(1) 复杂度内返回链表的节点数量（长
度）；
1.3 字典
字典（dictionary），又名映射（map）或关联数组（associative array）， 它是一种抽象数据结
构，由一集键值对（key-value pairs）组成，各个键值对的键各不相同，程序可以将新的键值对
添加到字典中，或者基于键进行查找、更新或删除等操作。
本章先对字典在Redis 中的应用进行介绍，接着讲解字典的具体实现方式，以及这个字典实现
要解决的问题，最后，以对字典迭代器的介绍作为本章的结束。
1.3.1 字典的应用
字典在Redis 中的应用广泛，使用频率可以说和SDS 以及双端链表不相上下，基本上各个功
能模块都有用到字典的地方。
其中，字典的主要用途有以下两个：
1. 实现数据库键空间（key space）；
12 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
2. 用作Hash 类型键的其中一种底层实现；
以下两个小节分别介绍这两种用途。
实现数据库键空间
Redis 是一个键值对数据库，数据库中的键值对就由字典保存：每个数据库都有一个与之相对
应的字典，这个字典被称之为键空间（key space）。
当用户添加一个键值对到数据库时（不论键值对是什么类型），程序就将该键值对添加到键空
间；当用户从数据库中删除一个键值对时，程序就会将这个键值对从键空间中删除；等等。
举个例子，执行FLUSHDB 可以清空键空间上的所有键值对数据：
redis> FLUSHDB
OK
执行DBSIZE 则返回键空间上现有的键值对：
redis> DBSIZE
(integer) 0
还可以用SET 设置一个字符串键到键空间，并用GET 从键空间中取出该字符串键的值：
redis> SET number 10086
OK
redis> GET number
"10086"
redis> DBSIZE
(integer) 1
后面的《数据库》一章会对键空间以及数据库的实现作详细的介绍，到时我们将看到，大部分
针对数据库的命令，比如DBSIZE 、FLUSHDB 、:ref:RANDOMKEY ，等等，都是构建于对
字典的操作之上的；而那些创建、更新、删除和查找键值对的命令，也无一例外地需要在键空
间上进行操作。
用作Hash 类型键的其中一种底层实现
Redis 的Hash 类型键使用以下两种数据结构作为底层实现:
1. 字典；
2. 压缩列表；
因为压缩列表比字典更节省内存，所以程序在创建新Hash 键时，默认使用压缩列表作为底层
实现，当有需要时，程序才会将底层实现从压缩列表转换到字典。
当用户操作一个Hash 键时，键值在底层就可能是一个哈希表：
redis> HSET book name "The design and implementation of Redis"
(integer) 1
redis> HSET book type "source code analysis"
(integer) 1
1.3. 字典13
Redis 设计与实现, 第一版
redis> HSET book release-date "2013.3.8"
(integer) 1
redis> HGETALL book
1) "name"
2) "The design and implementation of Redis"
3) "type"
4) "source code analysis"
5) "release-date"
6) "2013.3.8"
《哈希表》章节给出了关于哈希类型键的更多信息，并介绍了压缩列表和字典之间的转换条件。
介绍完了字典的用途，现在让我们来看看字典数据结构的定义。
1.3.2 字典的实现
实现字典的方法有很多种：
. 最简单的就是使用链表或数组，但是这种方式只适用于元素个数不多的情况下；
. 要兼顾高效和简单性，可以使用哈希表；
. 如果追求更为稳定的性能特征，并且希望高效地实现排序操作的话，则可以使用更为复
杂的平衡树；
在众多可能的实现中，Redis 选择了高效且实现简单的哈希表作为字典的底层实现。
dict.h/dict 给出了这个字典的定义：
/*
* 字典
**
每个字典使用两个哈希表，用于实现渐进式rehash
*/
typedef struct dict {
// 特定于类型的处理函数
dictType *type;
// 类型处理函数的私有数据
void *privdata;
// 哈希表（2 个）
dictht ht[2];
// 记录rehash 进度的标志，值为-1 表示rehash 未进行
int rehashidx;
// 当前正在运作的安全迭代器数量
int iterators;
} dict;
以下是用于处理dict 类型的API ，它们的作用及相应的算法复杂度：
14 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
操作函数算法复杂度
创建一个新字典dictCreate O(1)
添加新键值对到字典dictAdd O(1)
添加或更新给定键的值dictReplace O(1)
在字典中查找给定键所在的节点dictFind O(1)
在字典中查找给定键的值dictFetchValue O(1)
从字典中随机返回一个节点dictGetRandomKey O(N)
根据给定键，删除字典中的键值对dictDelete O(1)
清空并释放字典dictRelease O(N)
清空并重置（但不释放）字典dictEmpty O(N)
缩小字典dictResize O(N)
扩大字典dictExpand O(N)
对字典进行给定步数的rehash dictRehash O(N)
在给定毫秒内，对字典进行rehash dictRehashMilliseconds O(N)
注意dict 类型使用了两个指针分别指向两个哈希表。
其中，0 号哈希表（ht[0]）是字典主要使用的哈希表，而1 号哈希表（ht[1]）则只有在程序
对0 号哈希表进行rehash 时才使用。
接下来两个小节将对哈希表的实现，以及哈希表所使用的哈希算法进行介绍。
哈希表实现
字典所使用的哈希表实现由dict.h/dictht 类型定义：
/*
* 哈希表
*/
typedef struct dictht {
// 哈希表节点指针数组（俗称桶，bucket）
dictEntry **table;
// 指针数组的大小
unsigned long size;
// 指针数组的长度掩码，用于计算索引值
unsigned long sizemask;
// 哈希表现有的节点数量
unsigned long used;
} dictht;
table 属性是一个数组，数组的每个元素都是一个指向dictEntry 结构的指针。
每个dictEntry 都保存着一个键值对，以及一个指向另一个dictEntry 结构的指针：
/*
* 哈希表节点
*/
typedef struct dictEntry {
// 键
void *key;
1.3. 字典15
Redis 设计与实现, 第一版
// 值
union {
void *val;
uint64_t u64;
int64_t s64;
} v;
// 链往后继节点
struct dictEntry *next;
} dictEntry;
next 属性指向另一个dictEntry 结构，多个dictEntry 可以通过next 指针串连成链表，从
这里可以看出，dictht 使用链地址法来处理键碰撞：当多个不同的键拥有相同的哈希值时，哈
希表用一个链表将这些键连接起来。
下图展示了一个由dictht 和数个dictEntry 组成的哈希表例子：
dictht
table
size: 4
sizemask: 3
used: 3
dictEntry**
(bucket)
0
1
2
3
dictEntry
key1 value1 next
dictEntry
key2 value2 next
dictEntry
key3 value3 next
NULL
NULL
NULL
NULL
如果再加上之前列出的dict 类型，那么整个字典结构可以表示如下：
dict
type
privdata
ht[2]
rehashidx: -1
iterators: 0
dictht
table
size: 4
sizemask: 3
used: 3
ht[0]
dictht
table
size: 0
sizemask: 0
used: 0
ht[1]
dictEntry**
(bucket)
0
1
2
3
NULL
dictEntry
key1 value1 next
dictEntry
key2 value2 next
dictEntry
key3 value3 next
NULL
NULL
NULL
NULL
16 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
在上图的字典示例中，字典虽然创建了两个哈希表，但正在使用的只有0 号哈希表，这说明字
典未进行rehash 状态。
哈希算法
Redis 目前使用两种不同的哈希算法：
1. MurmurHash2 32 bit 算法：这种算法的分布率和速度都非常好，具体信息请参考MurmurHash
的主页：http://code.google.com/p/smhasher/ 。
2. 基于djb 算法实现的一个大小写无关散列算法： 具体信息请参考
http://www.cse.yorku.ca/~oz/hash.html 。
使用哪种算法取决于具体应用所处理的数据：
. 命令表以及Lua 脚本缓存都用到了算法2 。
. 算法1 的应用则更加广泛：数据库、集群、哈希键、阻塞操作等功能都用到了这个算法。
1.3.3 创建新字典
dictCreate 函数创建并返回一个新字典：
dict *d = dictCreate(&hash_type, NULL);
d 的值可以用图片表示如下：
1.3. 字典17
Redis 设计与实现, 第一版
dict
type
privdata
ht[2]
rehashidx
iterators
dictht
table
size: 0
sizemask: 0
used: 0
ht[0]
dictht
table
size: 0
sizemask: 0
used: 0
ht[1]
NULL
NULL
新创建的两个哈希表都没有为table 属性分配任何空间：
. ht[0]->table 的空间分配将在第一次往字典添加键值对时进行；
. ht[1]->table 的空间分配将在rehash 开始时进行；
1.3.4 添加键值对到字典
根据字典所处的状态，将一个给定的键值对添加到字典可能会引起一系列复杂的操作：
. 如果字典为未初始化（也即是字典的0 号哈希表的table 属性为空），那么程序需要对0
号哈希表进行初始化；
. 如果在插入时发生了键碰撞，那么程序需要处理碰撞；
. 如果插入新元素使得字典满足了rehash 条件，那么需要启动相应的rehash 程序；
当程序处理完以上三种情况之后，新的键值对才会被真正地添加到字典上。
整个添加流程可以用下图表示：
18 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
dictAdd
键已经存在？
返回 NULL ，
表示添加失败
是
ht[0]
未分配任何空间？
否
初始化 ht[0]
是
需要 rehash ？
否
开始渐进式 rehash
需要，
并且 rehash 未进行
rehash
正在进行中？
不需要，
或者 rehash 正在进行
选择ht[1] 作为新键值对的添加目标
是
选择ht[0] 作为新键值对的添加目标
否
根据给定键，计算出哈希值，以及索引值
创建新 dictEntry ，并保存给定键值对
根据索引值，将新节点添加到目标哈希表
1.3. 字典19
Redis 设计与实现, 第一版
在接下来的三节中，我们将分别看到添加操作如何在以下三种情况中执行：
1. 字典为空;
2. 添加新键值对时发生碰撞处理；
3. 添加新键值对时触发了rehash 操作；
1.3.5 添加新元素到空白字典
当第一次往空字典里添加键值对时，程序会根据dict.h/DICT_HT_INITIAL_SIZE 里指定的大
小为d->ht[0]->table 分配空间（在目前的版本中，DICT_HT_INITIAL_SIZE 的值为4 ）。
以下是字典空白时的样子：
dict
type
privdata
ht[2]
rehashidx
iterators
dictht
table
size: 0
sizemask: 0
used: 0
ht[0]
dictht
table
size: 0
sizemask: 0
used: 0
ht[1]
NULL
NULL
以下是往空白字典添加了第一个键值对之后的样子：
20 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
dict
type
privdata
ht[2]
rehashidx
iterators
dictht
table
size: 4
sizemask: 3
used: 1
ht[0]
dictht
table
size: 0
sizemask: 0
used: 0
ht[1]
dictEntry**
(bucket)
0
1
2
3
NULL
NULL
dictEntry
key1 value1 next
NULL
NULL
NULL
1.3.6 添加新键值对时发生碰撞处理
在哈希表实现中，当两个不同的键拥有相同的哈希值时，我们称这两个键发生碰撞（collision），
而哈希表实现必须想办法对碰撞进行处理。
字典哈希表所使用的碰撞解决方法被称之为链地址法：这种方法使用链表将多个哈希值相同的
节点串连在一起，从而解决冲突问题。
假设现在有一个带有三个节点的哈希表，如下图：
添加碰撞节点之前
dictEntry**
(bucket)
0
1
2
3
dictEntry
key1 value1 next
dictEntry
key2 value2 next
dictEntry
key3 value3 next
NULL
NULL
NULL
NULL
1.3. 字典21
Redis 设计与实现, 第一版
对于一个新的键值对key4 和value4 ，如果key4 的哈希值和key1 的哈希值相同，那么它们
将在哈希表的0 号索引上发生碰撞。
通过将key4-value4 和key1-value1 两个键值对用链表连接起来，就可以解决碰撞的问题：
添加碰撞节点之后
dictEntry**
(bucket)
0
1
2
3
dictEntry
key2 value2 next
dictEntry
key3 value3 next
dictEntry
key4 value4 next
NULL
dictEntry
key1 value1 next NULL
NULL
NULL
1.3.7 添加新键值对时触发了rehash 操作
对于使用链地址法来解决碰撞问题的哈希表dictht 来说，哈希表的性能依赖于它的大小（size
属性）和它所保存的节点的数量（used 属性）之间的比率：
. 比率在1:1 时，哈希表的性能最好；
. 如果节点数量比哈希表的大小要大很多的话，那么哈希表就会退化成多个链表，哈希表
本身的性能优势就不再存在；
举个例子，对于下面这个哈希表，平均每次失败查找只需要访问1 个节点（非空节点访问2
次，空节点访问1 次）：
22 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
bucket
0
1
2
3
4
5
6
7
Entry
Entry
NULL
Entry
NULL
Entry
NULL
NULL
NULL
NULL
NULL
NULL
而对于下面这个哈希表，平均每次失败查找需要访问5 个节点：
1.3. 字典23
Redis 设计与实现, 第一版
bucket
0
1
2
3
4
5
6
7
Entry
Entry
Entry
Entry
Entry
Entry
Entry
Entry
Entry Entry Entry Entry NULL
Entry Entry NULL
Entry Entry Entry Entry NULL
Entry Entry Entry Entry NULL
Entry NULL
Entry Entry Entry Entry NULL
Entry Entry Entry NULL
Entry Entry Entry Entry NULL
为了在字典的键值对不断增多的情况下保持良好的性能，字典需要对所使用的哈希表（ht[0]）
进行rehash 操作：在不修改任何键值对的情况下，对哈希表进行扩容，尽量将比率维持在1:1
左右。
dictAdd 在每次向字典添加新键值对之前，都会对哈希表ht[0] 进行检查，对于ht[0] 的
size 和used 属性，如果它们之间的比率ratio = used / size 满足以下任何一个条件的话，
rehash 过程就会被激活：
1. 自然rehash ：ratio >= 1 ，且变量dict_can_resize 为真。
2. 强制rehash ： ratio 大于变量dict_force_resize_ratio （目前版本中，
dict_force_resize_ratio 的值为5 ）。
Note: 什么时候dict_can_resize 会为假？
在前面介绍字典的应用时也说到过，一个数据库就是一个字典，数据库里的哈希类型键
也是一个字典，当Redis 使用子进程对数据库执行后台持久化任务时（比如执行BGSAVE
或BGREWRITEAOF 时）， 为了最大化地利用系统的copy on write 机制， 程序会暂时将
dict_can_resize 设为假，避免执行自然rehash ，从而减少程序对内存的触碰（touch）。
当持久化任务完成之后，dict_can_resize 会重新被设为真。
另一方面，当字典满足了强制rehash 的条件时，即使dict_can_resize 不为真（有BGSAVE
或BGREWRITEAOF 正在执行），这个字典一样会被rehash 。
1.3.8 Rehash 执行过程
字典的rehash 操作实际上就是执行以下任务：
1. 创建一个比ht[0]->table 更大的ht[1]->table ；
2. 将ht[0]->table 中的所有键值对迁移到ht[1]->table ；
24 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
3. 将原有ht[0] 的数据清空，并将ht[1] 替换为新的ht[0] ；
经过以上步骤之后，程序就在不改变原有键值对数据的基础上，增大了哈希表的大小。
作为例子，以下四个小节展示了一次对哈希表进行rehash 的完整过程。
1. 开始rehash
这个阶段有两个事情要做：
1. 设置字典的rehashidx 为0 ，标识着rehash 的开始；
2. 为ht[1]->table 分配空间，大小至少为ht[0]->used 的两倍；
这时的字典是这个样子：
dict
type
privdata
ht[2]
rehashidx: 0
iterators
dictht
table
size: 4
sizemask: 3
used: 4
ht[0]
dictht
table
size: 8
sizemask: 7
used: 0
ht[1]
dictEntry**
(bucket)
0
1
2
3
dictEntry**
(bucket)
0
1
2
3
4
5
6
7
dictEntry
key1 value1 next
dictEntry
key2 value2 next
dictEntry
key3 value3 next
dictEntry
key4 value4 next
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
2. Rehash 进行中
在这个阶段，ht[0]->table 的节点会被逐渐迁移到ht[1]->table ，因为rehash 是分多次进
行的（细节在下一节解释），字典的rehashidx 变量会记录rehash 进行到ht[0] 的哪个索引
1.3. 字典25
Redis 设计与实现, 第一版
位置上。
以下是rehashidx 值为2 时，字典的样子：
dict
type
privdata
ht[2]
rehashidx: 2
iterators
dictht
table
size: 4
sizemask: 3
used: 1
ht[0]
dictht
table
size: 8
sizemask: 7
used: 3
ht[1]
dictEntry**
(bucket)
0
1
2
3
dictEntry**
(bucket)
0
1
2
3
4
5
6
7
dictEntry
key3 value3 next
NULL
NULL
NULL
dictEntry
key1 value1 next
dictEntry
key2 value2 next
dictEntry
key4 value4 next
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
注意除了节点的移动外，字典的rehashidx 、ht[0]->used 和ht[1]->used 三个属性也产生
了变化。
3. 节点迁移完毕
到了这个阶段，所有的节点都已经从ht[0] 迁移到ht[1] 了：
26 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
dict
type
privdata
ht[2]
rehashidx: 3
iterators
dictht
table
size: 4
sizemask: 3
used: 0
ht[0]
dictht
table
size: 8
sizemask: 7
used: 4
ht[1]
dictEntry**
(bucket)
0
1
2
3
dictEntry**
(bucket)
0
1
2
3
4
5
6
7
NULL
NULL
NULL
NULL
dictEntry
key1 value1 next
dictEntry
key2 value2 next
dictEntry
key3 value3 next
dictEntry
key4 value4 next
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
4. Rehash 完毕
在rehash 的最后阶段，程序会执行以下工作：
1. 释放ht[0] 的空间；
2. 用ht[1] 来代替ht[0] ，使原来的ht[1] 成为新的ht[0] ；
3. 创建一个新的空哈希表，并将它设置为ht[1] ；
4. 将字典的rehashidx 属性设置为-1 ，标识rehash 已停止；
以下是字典rehash 完毕之后的样子：
1.3. 字典27
Redis 设计与实现, 第一版
dict
type
privdata
ht[2]
rehashidx: -1
iterators
dictht
table
size: 8
sizemask: 7
used: 4
ht[0]
dictht
table
size: 0
sizemask: 0
used: 0
ht[1]
dictEntry**
(bucket)
0
1
2
3
4
5
6
7
NULL dictEntry
key1 value1 next
dictEntry
key2 value2 next
dictEntry
key3 value3 next
dictEntry
key4 value4 next
NULL
NULL
NULL
NULL
NULL
NULL
NULL
NULL
对比字典rehash 之前和rehash 之后，新的ht[0] 空间更大，并且字典原有的键值对也没有被
修改或者删除。
1.3.9 渐进式rehash
在上一节，我们了解了字典的rehash 过程，需要特别指出的是，rehash 程序并不是在激活之
后就马上执行直到完成的，而是分多次、渐进式地完成的。
假设这样一个场景：在一个有很多键值对的字典里，某个用户在添加新键值对时触发了rehash
过程，如果这个rehash 过程必须将所有键值对迁移完毕之后才将结果返回给用户，这样的处理
方式将是非常不友好的。
另一方面，要求服务器必须阻塞直到rehash 完成，这对于Redis 服务器本身也是不能接受的。
为了解决这个问题，Redis 使用了渐进式（incremental）的rehash 方式：通过将rehash 分散
到多个步骤中进行，从而避免了集中式的计算。
渐进式rehash 主要由_dictRehashStep 和dictRehashMilliseconds 两个函数进行：
. _dictRehashStep 用于对数据库字典、以及哈希键的字典进行被动rehash ；
. dictRehashMilliseconds 则由Redis 服务器常规任务程序（server cron job）执行，用
于对数据库字典进行主动rehash ；
_dictRehashStep
每次执行_dictRehashStep ，ht[0]->table 哈希表第一个不为空的索引上的所有节点就会全
部迁移到ht[1]->table 。
28 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
在rehash 开始进行之后（d->rehashidx 不为-1），每次执行一次添加、查找、删除操作，
_dictRehashStep 都会被执行一次：
dictAdd
正在进行rehash ？
dictFind dictDelete dictGetRandomKey
_dictRehashStep
是
将 ht[0] 第一个不为空索引上的所有节点迁移至ht[1]
因为字典会保持哈希表大小和节点数的比率在一个很小的范围内，所以每个索引上的节点数量
不会很多（从目前版本的rehash 条件来看，平均只有一个，最多通常也不会超过五个），所以
在执行操作的同时，对单个索引上的节点进行迁移，几乎不会对响应时间造成影响。
dictRehashMilliseconds
dictRehashMilliseconds 可以在指定的毫秒数内，对字典进行rehash 。
当Redis 的服务器常规任务执行时，dictRehashMilliseconds 会被执行，在规定的时间内，
尽可能地对数据库字典中那些需要rehash 的字典进行rehash ，从而加速数据库字典的rehash
进程（progress）。
其他措施
在哈希表进行rehash 时，字典还会采取一些特别的措施，确保rehash 顺利、正确地进行：
. 因为在rehash 时，字典会同时使用两个哈希表，所以在这期间的所有查找、删除等操作，
除了在ht[0] 上进行，还需要在ht[1] 上进行。
. 在执行添加操作时，新的节点会直接添加到ht[1] 而不是ht[0] ，这样保证ht[0] 的节
点数量在整个rehash 过程中都只减不增。
1.3. 字典29
Redis 设计与实现, 第一版
1.3.10 字典的收缩
上面关于rehash 的章节描述了通过rehash 对字典进行扩展（expand）的情况，如果哈希表的
可用节点数比已用节点数大很多的话，那么也可以通过对哈希表进行rehash 来收缩（shrink）
字典。
收缩rehash 和上面展示的扩展rehash 的操作几乎一样，它执行以下步骤：
1. 创建一个比ht[0]->table 小的ht[1]->table ；
2. 将ht[0]->table 中的所有键值对迁移到ht[1]->table ；
3. 将原有ht[0] 的数据清空，并将ht[1] 替换为新的ht[0] ；
扩展rehash 和收缩rehash 执行完全相同的过程，一个rehash 是扩展还是收缩字典，关键在于
新分配的ht[1]->table 的大小：
. 如果rehash 是扩展操作，那么ht[1]->table 比ht[0]->table 要大；
. 如果rehash 是收缩操作，那么ht[1]->table 比ht[0]->table 要小；
字典的收缩规则由redis.c/htNeedsResize 函数定义：
/*
* 检查字典的使用率是否低于系统允许的最小比率
**
是的话返回1 ，否则返回0 。
*/
int htNeedsResize(dict *dict) {
long long size, used;
// 哈希表已用节点数量
size = dictSlots(dict);
// 哈希表大小
used = dictSize(dict);
// 当哈希表的大小大于DICT_HT_INITIAL_SIZE
// 并且字典的填充率低于REDIS_HT_MINFILL 时
// 返回1
return (size && used && size > DICT_HT_INITIAL_SIZE &&
(used*100/size < REDIS_HT_MINFILL));
}
在默认情况下，REDIS_HT_MINFILL 的值为10 ，也即是说，当字典的填充率低于10% 时，程
序就可以对这个字典进行收缩操作了。
字典收缩和字典扩展的一个区别是：
. 字典的扩展操作是自动触发的（不管是自动扩展还是强制扩展）；
. 而字典的收缩操作则是由程序手动执行。
因此，使用字典的程序可以决定何时对字典进行收缩：
. 当字典用于实现哈希键的时候，每次从字典中删除一个键值对，程序就会执行一次
htNeedsResize 函数，如果字典达到了收缩的标准，程序将立即对字典进行收缩；
. 当字典用于实现数据库键空间（key space） 的时候， 收缩的时机由
redis.c/tryResizeHashTables 函数决定，具体信息请参考《数据库》一章的《数据
库空间的收缩和扩展》小节；
30 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
1.3.11 字典其他操作
除了添加操作和伸展/收缩操作之外，字典还定义了其他一些操作，比如常见的查找、删除和更
新。
因为链地址法哈希表实现的相关信息可以从任何一本数据结构或算法书上找到，这里不再对字
典的其他操作进行介绍，不过前面对创建字典、添加键值对、收缩和扩展rehash 的讨论已经涵
盖了字典模块的核心内容。
1.3.12 字典的迭代
字典带有自己的迭代器实现——对字典进行迭代实际上就是对字典所使用的哈希表进行迭代：
. 迭代器首先迭代字典的第一个哈希表，然后，如果rehash 正在进行的话，就继续对第二
个哈希表进行迭代。
. 当迭代哈希表时，找到第一个不为空的索引，然后迭代这个索引上的所有节点。
. 当这个索引迭代完了，继续查找下一个不为空的索引，如此循环，一直到整个哈希表都迭
代完为止。
整个迭代过程可以用伪代码表示如下：
def iter_dict(dict):
# 迭代0 号哈希表
iter_table(ht[0]->table)
# 如果正在执行rehash ，那么也迭代1 号哈希表
if dict.is_rehashing(): iter_table(ht[1]->table)
def iter_table(table):
# 遍历哈希表上的所有索引
for index in table:
# 跳过空索引
if table[index].empty():
continue
# 遍历索引上的所有节点
for node in table[index]:
# 处理节点
do_something_with(node)
字典的迭代器有两种：
. 安全迭代器：在迭代进行过程中，可以对字典进行修改。
. 不安全迭代器：在迭代进行过程中，不对字典进行修改。
以下是迭代器的数据结构定义：
/*
* 字典迭代器
*/
1.3. 字典31
Redis 设计与实现, 第一版
typedef struct dictIterator {
dict *d; // 正在迭代的字典
int table, // 正在迭代的哈希表的号码（0 或者1）
index, // 正在迭代的哈希表数组的索引
safe; // 是否安全？
dictEntry *entry, // 当前哈希节点
*nextEntry; // 当前哈希节点的后继节点
} dictIterator;
以下函数是这个迭代器的API ，它们的作用及相关算法复杂度：
函数作用算法复杂度
dictGetIterator 创建一个不安全迭代器。O(1)
dictGetSafeIterator 创建一个安全迭代器。O(1)
dictNext 返回迭代器指向的当前节点，如果迭代完毕，返回
NULL 。
O(1)
dictReleaseIterator 释放迭代器。O(1)
1.3.13 小结
. 字典由键值对构成的抽象数据结构。
. Redis 中的数据库和哈希键都基于字典来实现。
. Redis 字典的底层实现为哈希表，每个字典使用两个哈希表，一般情况下只使用0 号哈希
表，只有在rehash 进行时，才会同时使用0 号和1 号哈希表。
. 哈希表使用链地址法来解决键冲突的问题。
. Rehash 可以用于扩展或收缩哈希表。
. 对哈希表的rehash 是分多次、渐进式地进行的。
1.4 跳跃表
跳跃表（skiplist）是一种随机化的数据，由William Pugh 在论文《Skip lists: a probabilistic
alternative to balanced trees》中提出，这种数据结构以有序的方式在层次化的链表中保存元
素，它的效率可以和平衡树媲美——查找、删除、添加等操作都可以在对数期望时间下完成，
并且比起平衡树来说，跳跃表的实现要简单直观得多。
以下是一个典型的跳跃表例子（图片来自维基百科）：
从图中可以看到，跳跃表主要由以下部分构成：
32 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
. 表头（head）：负责维护跳跃表的节点指针。
. 跳跃表节点：保存着元素值，以及多个层。
. 层：保存着指向其他元素的指针。高层的指针越过的元素数量大于等于低层的指针，为了
提高查找的效率，程序总是从高层先开始访问，然后随着元素值范围的缩小，慢慢降低层
次。
. 表尾：全部由NULL 组成，表示跳跃表的末尾。
因为跳跃表的定义可以在任何一本算法或数据结构的书中找到，所以本章不介绍跳跃表的具体
实现方式或者具体的算法，而只介绍跳跃表在Redis 的应用、核心数据结构和API 。
1.4.1 跳跃表的实现
为了适应自身的功能需要，Redis 基于William Pugh 论文中描述的跳跃表进行了以下修改：
1. 允许重复的score 值：多个不同的member 的score 值可以相同。
2. 进行对比操作时，不仅要检查score 值，还要检查member ：当score 值可以重复时，
单靠score 值无法判断一个元素的身份，所以需要连member 域都一并检查才行。
3. 每个节点都带有一个高度为1 层的后退指针，用于从表尾方向向表头方向迭代：当执行
ZREVRANGE 或ZREVRANGEBYSCORE 这类以逆序处理有序集的命令时，就会用到
这个属性。
这个修改版的跳跃表由redis.h/zskiplist 结构定义：
typedef struct zskiplist {
// 头节点，尾节点
struct zskiplistNode *header, *tail;
// 节点数量
unsigned long length;
// 目前表内节点的最大层数
int level;
} zskiplist;
跳跃表的节点由redis.h/zskiplistNode 定义：
typedef struct zskiplistNode {
// member 对象
robj *obj;
// 分值
double score;
// 后退指针
struct zskiplistNode *backward;
// 层
struct zskiplistLevel {
// 前进指针
1.4. 跳跃表33
Redis 设计与实现, 第一版
struct zskiplistNode *forward;
// 这个层跨越的节点数量
unsigned int span;
} level[];
} zskiplistNode;
以下是操作这两个数据结构的API ，它们的作用以及相应的算法复杂度：
函数作用复杂度
zslCreateNode 创建并返回一个新的跳跃表节点最坏O(1)
zslFreeNode 释放给定的跳跃表节点最坏O(1)
zslCreate 创建并初始化一个新的跳跃表最坏O(N)
zslFree 释放给定的跳跃表最坏O(N)
zslInsert 将一个包含给定score 和member
的新节点添加到跳跃表中
最坏O(N) 平均O(logN)
zslDeleteNode 删除给定的跳跃表节点最坏O(N)
zslDelete 删除匹配给定member 和score 的
元素
最坏O(N) 平均O(logN)
zslFirstInRange 找到跳跃表中第一个符合给定范围
的元素
最坏O(N) 平均O(logN)
zslLastInRange 找到跳跃表中最后一个符合给定范
围的元素
最坏O(N) 平均O(logN)
zslDeleteRangeByScore 删除score 值在给定范围内的所
有节点
最坏O(N2)
zslDeleteRangeByRank 删除给定排序范围内的所有节点最坏O(N2)
zslGetRank 返回目标元素在有序集中的排位最坏O(N) 平均O(logN)
zslGetElementByRank 根据给定排位，返回该排位上的元
素节点
最坏O(N) 平均O(logN)
1.4.2 跳跃表的应用
和字典、链表或者字符串这几种在Redis 中大量使用的数据结构不同，跳跃表在Redis 的唯一
作用，就是实现有序集数据类型。
跳跃表将指向有序集的score 值和member 域的指针作为元素，并以score 值为索引，对有序
集元素进行排序。
举个例子，以下代码就创建了一个带有3 个元素的有序集：
redis> ZADD s 6 x 10 y 15 z
(integer) 3
redis> ZRANGE s 0 -1 WITHSCORES
1) "x"
2) "6"
3) "y"
4) "10"
5) "z"
6) "15"
在底层实现中，Redis 为x 、y 和z 三个member 分别创建了三个字符串，并为6 、10 和15
34 Chapter 1. 内部数据结构
Redis 设计与实现, 第一版
分别创建三个double 类型的值，然后用一个跳跃表将这些指针有序地保存起来，形成这样一
个跳跃表：
zskipNode
(head)
score
NULL
robj
NULL
zskipNode
score
6
robj
x
zskipNode
score
10
robj
y
zskipNode
score
15
robj
z
NULL
NULL
NULL
为了展示的方便，在图片中我们直接将member 和score 值包含在表节点中，但是在实际的定
义中，因为跳跃表要和另一个实现有序集的结构（字典）分享member 和score 值，所以跳跃
表只保存指向member 和score 的指针。更详细的信息，请参考《有序集》章节。
1.4.3 小结
. 跳跃表是一种随机化数据结构，它的查找、添加、删除操作都可以在对数期望时间下完
成。
. 跳跃表目前在Redis 的唯一作用就是作为有序集类型的底层数据结构（之一，另一个构
成有序集的结构是字典）。
. 为了适应自身的需求，Redis 基于William Pugh 论文中描述的跳跃表进行了修改，包括：
1. score 值可重复。
2. 对比一个元素需要同时检查它的score 和memeber 。
3. 每个节点带有高度为1 层的后退指针，用于从表尾方向向表头方向迭代。
1.4. 跳跃表35
Redis 设计与实现, 第一版
36 Chapter 1. 内部数据结构
第2 章
内存映射数据结构
虽然内部数据结构非常强大，但是创建一系列完整的数据结构本身也是一件相当耗费内存的工
作，当一个对象包含的元素数量并不多，或者元素本身的体积并不大时，使用代价高昂的内部
数据结构并不是最好的办法。
为了解决这一问题，Redis 在条件允许的情况下，会使用内存映射数据结构来代替内部数据结
构。
内存映射数据结构是一系列经过特殊编码的字节序列，创建它们所消耗的内存通常比作用类似
的内部数据结构要少得多，如果使用得当，内存映射数据结构可以为用户节省大量的内存。
不过，因为内存映射数据结构的编码和操作方式要比内部数据结构要复杂得多，所以内存映射
数据结构所占用的CPU 时间会比作用类似的内部数据结构要多。
这一部分将对Redis 目前正在使用的两种内存映射数据结构进行介绍。
2.1 整数集合
整数集合（intset）用于有序、无重复地保存多个整数值，它会根据元素的值，自动选择该用什
么长度的整数类型来保存元素。
举个例子，如果在一个intset 里面，最长的元素可以用int16_t 类型来保存，那么这个intset
的所有元素都以int16_t 类型来保存。
另一方面，如果有一个新元素要加入到这个intset ，并且这个元素不能用int16_t 类型来保存
——比如说，新元素的长度为int32_t ，那么这个intset 就会自动进行“升级” ：先将集合中现
有的所有元素从int16_t 类型转换为int32_t 类型，接着再将新元素加入到集合中。
根据需要，intset 可以自动从int16_t 升级到int32_t 或int64_t ，或者从int32_t 升级到
int64_t 。
37
Redis 设计与实现, 第一版
2.1.1 整数集合的应用
Intset 是集合键的底层实现之一，如果一个集合：
1. 只保存着整数元素；
2. 元素的数量不多；
那么Redis 就会使用intset 来保存集合元素。
具体的信息请参考《集合》。
2.1.2 数据结构和主要操作
以下是intset.h/intset 类型的定义：
typedef struct intset {
// 保存元素所使用的类型的长度
uint32_t encoding;
// 元素个数
uint32_t length;
// 保存元素的数组
int8_t contents[];
} intset;
encoding 的值可以是以下三个常量的其中一个（定义位于intset.c ）：
#define INTSET_ENC_INT16 (sizeof(int16_t))
#define INTSET_ENC_INT32 (sizeof(int32_t))
#define INTSET_ENC_INT64 (sizeof(int64_t))
contents 数组是实际保存元素的地方，数组中的元素有以下两个特性：
. 没有重复元素；
. 元素在数组中从小到大排列；
contents 数组的int8_t 类型声明比较容易让人误解，实际上，intset 并不使用int8_t 类型
来保存任何元素，结构中的这个类型声明只是作为一个占位符使用：在对contents 中的元素
进行读取或者写入时，程序并不是直接使用contents 来对元素进行索引，而是根据encoding
的值，对contents 进行类型转换和指针运算，计算出元素在内存中的正确位置。在添加新元
素，进行内存分配时，分配的容量也是由encoding 的值决定。
下表列出了处理intset 的一些主要操作，以及这些操作的算法复杂度：
操作函数复杂度
创建intset intsetNew (1)
删除intset 无无
添加新元素（不升级） intsetAdd O(N)
添加新元素（升级） intsetUpgradeAndAdd O(N)
按索引获取元素_intsetGet (1)
按索引设置元素_intsetSet (1)
查找元素，返回索引intsetSearch O(lgN)
删除元素intsetRemove O(N)
38 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
2.1.3 intset 运行实例
让我们跟踪一个intset 的创建和添加过程，籍此了解intset 的运作方式。
创建新intset
intset.c/intsetNew 函数创建一个新的intset ，并为它设置初始化值：
intset *is = intsetNew();
// intset->encoding = INTSET_ENC_INT16;
// intset->length 0;
// intset->contents = [];
注意encoding 使用INTSET_ENC_INT16 作为初始值。
添加新元素到intset
创建intset 之后，就可以对它添加新元素了。
添加新元素到intset 的工作由intset.c/intsetAdd 函数完成，它需要处理以下三种情况：
1. 元素已存在于集合，不做动作；
2. 元素不存在于集合，并且添加新元素并不需要升级；
3. 元素不存在于集合，但是要在升级之后，才能添加新元素；
并且，intsetAdd 需要维持intset->contents 的以下性质：
1. 确保数组中没有重复元素；
2. 确保数组中的元素按从小到大排序；
整个intsetAdd 的执行流程可以表示为下图：
2.1. 整数集合39
Redis 设计与实现, 第一版
intsetAdd
集合当前的编码类型
是否适用于新元素？
调用
intsetUpgradeAndAdd
升级集合
并将新元素
添加到升级后的集合中
不适用
元素已经存在于集合？
适用
添加失败，元素已存在
是
为新元素分配内存
并对contents 数组中现有的元素进行移动，
确保新元素会被放到有序数组正确的位置上
否
将新元素的值保存到 contents 数组中
更新length 计数器
以下两个小节分别演示添加操作在升级和不升级两种情况下的执行过程。
添加新元素到intset （不需要升级）
如果intset 现有的编码方式适用于新元素，那么可以直接将新元素添加到intset ，无须对intset
进行升级。
以下代码演示了将三个int16_t 类型的整数添加到集合的过程，以及在添加过程中，集合的状
态：
intset *is = intsetNew();
intsetAdd(is, 10, NULL);
// is->encoding = INTSET_ENC_INT16;
// is->length = 1;
// is->contents = [10];
intsetAdd(is, 5, NULL);
// is->encoding = INTSET_ENC_INT16;
// is->length = 2;
// is->contents = [5, 10];
intsetAdd(is, 12, NULL);
40 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
// is->encoding = INTSET_ENC_INT16;
// is->length = 3;
// is->contents = [5, 10, 12]
因为添加的三个元素都可以表示为int16_t ，因此is->encoding 一直都是INTSET_ENC_INT16
。
另一方面，is->length 和is->contents 的值则随着新元素的加入而被修改。
添加新元素到intset （需要升级）
当要添加新元素到intset ，并且intset 当前的编码并不适用于新元素的编码时，就需要对inset
进行升级。
以下代码演示了带升级的添加操作的执行过程：
intset *is = intsetNew();
intsetAdd(is, 1, NULL);
// is->encoding = INTSET_ENC_INT16;
// is->length = 1;
// is->contents = [1]; // 所有值使用int16_t 保存
intsetAdd(is, 65535, NULL);
// is->encoding = INTSET_ENC_INT32; // 升级
// is->length = 2;
// is->contents = [1, 65535]; // 所有值使用int32_t 保存
intsetAdd(is, 70000, NULL);
// is->encoding = INTSET_ENC_INT32;
// is->length = 3;
// is->contents = [1, 65535, 70000];
intsetAdd(is, 4294967295, NULL);
// is->encoding = INTSET_ENC_INT64; // 升级
// is->length = 4;
// is->contents = [1, 65535, 70000, 4294967295]; // 所有值使用int64_t 保存
在添加65535 和4294967295 之后，encoding 属性的值，以及contents 数组保存值的方式，
都被改变了。
2.1.4 升级
在添加新元素时，如果intsetAdd 发现新元素不能用现有的编码方式来保存，它就会将升级集
合和添加新元素的任务转交给intsetUpgradeAndAdd 来完成：
2.1. 整数集合41
Redis 设计与实现, 第一版
添加新元素到 intset
intset 需要升级？
intsetAdd
不需要
intsetUpgradeAndAdd
需要
intsetUpgradeAndAdd 需要完成以下几个任务：
1. 对新元素进行检测，看保存这个新元素需要什么类型的编码；
2. 将集合encoding 属性的值设置为新编码类型，并根据新编码类型，对整个contents 数
组进行内存重分配。
3. 调整contents 数组内原有元素在内存中的排列方式，让它们从旧编码调整为新编码。
4. 将新元素添加到集合中。
整个过程中，最复杂的就是第三步，让我们用一个例子来理解这个步骤。
升级实例
假设有一个intset ，里面包含三个用int16_t 方式保存的数值，分别是1 、2 和3 ，它的结
构如下：
intset->encoding = INTSET_ENC_INT16;
intset->length = 3;
intset->contents = [1, 2, 3];
其中，intset->contents 在内存中的排列如下：
bit 0 15 31 47
value | 1 | 2 | 3 |
现在，我们要要将一个长度为int32_t 的值65535 加入到集合中，intset 需要执行以下步骤：
1. 将encoding 属性设置为INTSET_ENC_INT32 。
2. 根据encoding 属性的值，对contents 数组进行内存重分配。
重分配完成之后，contents 在内存中的排列如下：
42 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
bit 0 15 31 47 63 95 127
value | 1 | 2 | 3 | ? | ? | ? |
contents 数组现在共有可容纳4 个int32_t 值的空间。
3. 因为原来的3 个int16_t 值还“挤在”contents 前面的48 个位里，所以程序需要对它们
进行移动和类型转换，从而让它们适应集合的新编码方式。
首先是移动3 ：
bit 0 15 31 47 63 95 127
value | 1 | 2 | 3 | ? | 3 | ? |
| ^
| |
+-------------+
int16_t -> int32_t
接着移动2 ：
bit 0 15 31 47 63 95 127
value | 1 | 2 | 2 | 3 | ? |
| ^
| |
+-------+
int16_t -> int32_t
最后，移动1 ：
bit 0 15 31 47 63 95 127
value | 1 | 2 | 3 | ? |
| ^
V |
int16_t -> int32_t
4. 最后，将新值65535 添加到数组：
bit 0 15 31 47 63 95 127
value | 1 | 2 | 3 | 65535 |
^|
add
将intset->length 设置为4 。
至此，集合的升级和添加操作完成，现在的intset 结构如下：
intset->encoding = INTSET_ENC_INT32;
intset->length = 4;
intset->contents = [1, 2, 3, 65535];
2.1.5 关于升级
关于升级操作，还有两点需要提醒一下：
2.1. 整数集合43
Redis 设计与实现, 第一版
第一，从较短整数到较长整数的转换，并不会更改元素里面的值。
在C 语言中，从长度较短的带符号整数到长度较长的带符号整数之间的转换（比如从int16_t
转换为int32_t ）总是可行的（不会溢出）、无损的。
另一方面，从较长整数到较短整数之间的转换可能是有损的（比如从int32_t 转换为int16_t
）。
因为intset 只进行从较短整数到较长整数的转换（也即是，只“升级” ，不“降级” ），因此， “升
级”操作并不会修改元素原有的值。
第二，集合编码元素的方式，由元素中长度最大的那个值来决定。
就像前面演示的例子一样，当要将一个int32_t 编码的新元素添加到集合时，集合原有的所有
int16_t 编码的元素，都必须转换为int32_t 。
尽管这个集合真正需要用int32_t 长度来保存的元素只有一个，但整个集合的所有元素都必须
转换为这种类型。
2.1.6 关于元素移动
在进行升级的过程中，需要对数组内的元素进行“类型转换”和“移动”操作。
其中，移动不仅出现在升级（intsetUpgradeAndAdd）操作中，还出现其他对contents 数组内
容进行增删的操作上，比如intsetAdd 和intsetRemove ，因为这种移动操作需要处理intset
中的所有元素，所以这些函数的复杂度都不低于O(N) 。
2.1.7 其他操作
以下是一些关于intset 其他操作的讨论。
读取
有两种方式读取intset 的元素，一种是_intsetGet ，另一种是intsetSearch ：
. _intsetGet 接受一个给定的索引pos ，并根据intset->encoding 的值进行指针运算，
计算出给定索引在intset->contents 数组上的值。
. intsetSearch 则使用二分查找算法，判断一个给定元素在contents 数组上的索引。
写入
除了前面介绍过的intsetAdd 和intsetUpgradeAndAdd 之外，_intsetSet 也对集合进行写
入操作：它接受一个索引pos ，以及一个new_value ，将contents 数组pos 位置的值设为
new_value 。
删除
删除单个元素的工作由intsetRemove 操作，它先调用intsetSearch 找到需要被删除的元素
在contents 数组中的索引，然后使用内存移位操作，将目标元素从内存中抹去，最后，通过
内存重分配，对contents 数组的长度进行调整。
44 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
降级
Intset 不支持降级操作。
Intset 定位为一种受限的中间表示， 只能保存整数值， 而且元素的个数也不能超过
redis.h/REDIS_SET_MAX_INTSET_ENTRIES （目前版本值为512 ） 这些条件决定了它被保
存的时间不会太长，因此对它进行太复杂的操作，没有必要。
当然，如果内存确实十分紧张的话，给intset 添加降级功能也是可以实现的，不过这可能会让
intset 的代码增长一倍。
2.1.8 小结
. Intset 用于有序、无重复地保存多个整数值，它会根据元素的值，自动选择该用什么长度
的整数类型来保存元素。
. 当一个位长度更长的整数值添加到intset 时，需要对intset 进行升级，新intset 中每个
元素的位长度都等于新添加值的位长度，但原有元素的值不变。
. 升级会引起整个intset 进行内存重分配，并移动集合中的所有元素，这个操作的复杂度
为O(N) 。
. Intset 只支持升级，不支持降级。
. Intset 是有序的，程序使用二分查找算法来实现查找操作，复杂度为O(lgN) 。
2.2 压缩列表
Ziplist 是由一系列特殊编码的内存块构成的列表，一个ziplist 可以包含多个节点（entry），每
个节点可以保存一个长度受限的字符数组（不以\0 结尾的char 数组）或者整数，包括：
. 字符数组
– 长度小于等于63 （26 .. 1）字节的字符数组
– 长度小于等于16383 （214 .. 1）字节的字符数组
– 长度小于等于4294967295 （232 .. 1）字节的字符数组
. 整数
– 4 位长，介于0 至12 之间的无符号整数
– 1 字节长，有符号整数
– 3 字节长，有符号整数
– int16_t 类型整数
– int32_t 类型整数
– int64_t 类型整数
因为ziplist 节约内存的性质，它被哈希键、列表键和有序集合键作为初始化的底层实现来使
用，更多信息请参考《哈希表》、《列表》和《有序集》章节。
本章先介绍ziplist 的组成结构，以及ziplist 节点的编码方式，然后介绍ziplist 的添加操作和
删除操作的执行过程，以及这两种操作可能引起的连锁更新现象，最后介绍ziplist 的遍历方法
和节点查找方式。
2.2. 压缩列表45
Redis 设计与实现, 第一版
2.2.1 ziplist 的构成
下图展示了一个ziplist 的典型分布结构：
area |<---- ziplist header ---->|<----------- entries ------------->|<-end->|
size 4 bytes 4 bytes 2 bytes ? ? ? ? 1 byte
+---------+--------+-------+--------+--------+--------+--------+-------+
component | zlbytes | zltail | zllen | entry1 | entry2 | ... | entryN | zlend |
+---------+--------+-------+--------+--------+--------+--------+-------+
^ ^ ^
address | | |
ZIPLIST_ENTRY_HEAD | ZIPLIST_ENTRY_END
|
ZIPLIST_ENTRY_TAIL
图中各个域的作用如下：
域长度/类型域的值
zlbytes uint32_t 整个ziplist 占用的内存字节数，对ziplist 进行内存重分配，或
者计算末端时使用。
zltail uint32_t 到达ziplist 表尾节点的偏移量。通过这个偏移量，可以在不遍
历整个ziplist 的前提下，弹出表尾节点。
zllen uint16_t ziplist 中节点的数量。当这个值小于UINT16_MAX（65535）时，
这个值就是ziplist 中节点的数量；当这个值等于UINT16_MAX
时，节点的数量需要遍历整个ziplist 才能计算得出。
entryX ? ziplist 所保存的节点，各个节点的长度根据内容而定。
zlend uint8_t 255 的二进制值1111 1111 （UINT8_MAX），用于标记ziplist
的末端。
为了方便地取出ziplist 的各个域以及一些指针地址，ziplist 模块定义了以下宏：
宏作用算法复杂度
ZIPLIST_BYTES(ziplist) 取出zlbytes 的值(1)
ZIPLIST_TAIL_OFFSET(ziplist) 取出zltail 的值(1)
ZIPLIST_LENGTH(ziplist) 取出zllen 的值(1)
ZIPLIST_HEADER_SIZE 返回ziplist header 部分的长度，总是固
定的10 字节
(1)
ZIPLIST_ENTRY_HEAD(ziplist) 返回到达ziplist 第一个节点（表头）的地
址
(1)
ZIPLIST_ENTRY_TAIL(ziplist) 返回到达ziplist 最后一个节点（表尾）的
地址
(1)
ZIPLIST_ENTRY_END(ziplist) 返回ziplist 的末端，也即是zlend 之前
的地址
(1)
因为ziplist header 部分的长度总是固定的（4 字节+ 4 字节+ 2 字节），因此将指针移动到表
头节点的复杂度为常数时间；除此之外，因为表尾节点的地址可以通过zltail 计算得出，因
此将指针移动到表尾节点的复杂度也为常数时间。
以下是用于操作ziplist 的函数：
46 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
函数名作用算法复杂度
ziplistNew 创建一个新的ziplist (1)
ziplistResize 重新调整ziplist 的内存大小O(N)
ziplistPush 将一个包含给定值的新节点推入ziplist 的表头或
者表尾
O(N2)
zipEntry 取出给定地址上的节点， 并将它的属性保存到
zlentry 结构然后返回
(1)
ziplistInsert 将一个包含给定值的新节点插入到给定地址O(N2)
ziplistDelete 删除给定地址上的节点O(N2)
ziplistDeleteRange 在给定索引上，连续进行多次删除O(N2)
ziplistFind 在ziplist 中查找并返回包含给定值的节点O(N)
ziplistLen 返回ziplist 保存的节点数量O(N)
ziplistBlobLen 以字节为单位，返回ziplist 占用的内存大小(1)
因为ziplist 由连续的内存块构成，在最坏情况下，当ziplistPush 、ziplistDelete 这类对
节点进行增加或删除的函数之后，程序需要执行一种称为连锁更新的动作来维持ziplist 结构本
身的性质，所以这些函数的最坏复杂度都为O(N2) 。不过，因为这种最坏情况出现的概率并不
高，所以大可以放心使用ziplist ，而不必太担心出现最坏情况。
2.2.2 节点的构成
一个ziplist 可以包含多个节点，每个节点可以划分为以下几个部分：
area |<------------------- entry -------------------->|
+------------------+----------+--------+---------+
component | pre_entry_length | encoding | length | content |
+------------------+----------+--------+---------+
以下几个小节将分别对这个四个部分进行介绍。
pre_entry_length
pre_entry_length 记录了前一个节点的长度，通过这个值，可以进行指针计算，从而跳转到
上一个节点。
area |<---- previous entry --->|<--------------- current entry ---------------->|
size 5 bytes 1 byte ? ? ?
+-------------------------+-----------------------------+--------+---------+
component | ... | pre_entry_length | encoding | length | content |
| | | | | |
value | | 0000 0101 | ? | ? | ? |
+-------------------------+-----------------------------+--------+---------+
^ ^
address | |
p = e - 5 e
上图展示了如何通过一个节点向前跳转到另一个节点：用指向当前节点的指针e ，减去
pre_entry_length 的值（0000 0101 的十进制值，5），得出的结果就是指向前一个节点的地
址p 。
根据编码方式的不同，pre_entry_length 域可能占用1 字节或者5 字节：
2.2. 压缩列表47
Redis 设计与实现, 第一版
. 1 字节：如果前一节点的长度小于254 字节，那么只使用一个字节保存它的值。
. 5 字节：如果前一节点的长度大于等于254 字节，那么将第1 个字节的值设为254 ，然
后用接下来的4 个字节保存实际长度。
作为例子，以下是一个长度为1 字节的pre_entry_length 域，域的值为128 （二进制为1000
0000 ）：
area |<------------------- entry -------------------->|
size 1 byte ? ? ?
+------------------+----------+--------+---------+
component | pre_entry_length | encoding | length | content |
| | | | |
value | 1000 0000 | | | |
+------------------+----------+--------+---------+
而以下则是一个长度为5 字节的pre_entry_length 域，域的第一个字节被设为254 的二进制
1111 1110 ，而之后的四个字节则被设置为10086 的二进制10 0111 0110 0110 （多余的高
位用0 补完）：
area |<------------------------------ entry ---------------------------------->|
size 5 bytes ? ? ?
+-------------------------------------------+----------+--------+---------+
component | pre_entry_length | encoding | length | content |
| | | | |
| 11111110 00000000000000000010011101100110 | ? | ? | ? |
+-------------------------------------------+----------+--------+---------+
|<------->|<------------------------------->|
1 byte 4 bytes
encoding 和length
encoding 和length 两部分一起决定了content 部分所保存的数据的类型（以及长度）。
其中，encoding 域的长度为两个bit ，它的值可以是00 、01 、10 和11 ：
. 00 、01 和10 表示content 部分保存着字符数组。
. 11 表示content 部分保存着整数。
以00 、01 和10 开头的字符数组的编码方式如下：
编码编码长度content 部分保存的值
00bbbbbb 1 byte 长度小于等于63 字节的
字符数组。
01bbbbbb xxxxxxxx 2 byte 长度小于等于16383 字
节的字符数组。
10____ aaaaaaaa bbbbbbbb cccccccc dddddddd 5 byte 长度小于等于
4294967295 的字符
数组。
表格中的下划线_ 表示留空，而变量b 、x 等则代表实际的二进制数据。为了方便阅读，多个
字节之间用空格隔开。
11 开头的整数编码如下：
48 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
编码编码长度content 部分保存的值
11000000 1 byte int16_t 类型的整数
11010000 1 byte int32_t 类型的整数
11100000 1 byte int64_t 类型的整数
11110000 1 byte 24 bit 有符号整数
11111110 1 byte 8 bit 有符号整数
1111xxxx 1 byte 4 bit 无符号整数，介于0 至12 之间
content
content 部分保存着节点的内容，它的类型和长度由encoding 和length 决定。
以下是一个保存着字符数组hello world 的节点的例子：
area |<---------------------- entry ----------------------->|
size ? 2 bit 6 bit 11 byte
+------------------+----------+--------+---------------+
component | pre_entry_length | encoding | length | content |
| | | | |
value | ? | 00 | 001011 | hello world |
+------------------+----------+--------+---------------+
encoding 域的值00 表示节点保存着一个长度小于等于63 字节的字符数组，length 域给出
了这个字符数组的准确长度——11 字节（的二进制001011），content 则保存着字符数组值
hello world 本身（为了表示的简单，content 部分使用字符而不是二进制表示）。
以下是另一个节点，它保存着整数10086 ：
area |<---------------------- entry ----------------------->|
size ? 2 bit 6 bit 2 bytes
+------------------+----------+--------+---------------+
component | pre_entry_length | encoding | length | content |
| | | | |
value | ? | 11 | 000000 | 10086 |
+------------------+----------+--------+---------------+
encoding 域的值11 表示节点保存的是一个整数；而length 域的值000000 表示这个节点的
值的类型为int16_t ；最后，content 保存着整数值10086 本身（为了表示的简单，content
部分用十进制而不是二进制表示）。
2.2.3 创建新ziplist
函数ziplistNew 用于创建一个新的空白ziplist ，这个ziplist 可以表示为下图：
area |<---- ziplist header ---->|<-- end -->|
size 4 bytes 4 bytes 2 bytes 1 byte
+---------+--------+-------+-----------+
component | zlbytes | zltail | zllen | zlend |
| | | | |
value | 1011 | 1010 | 0 | 1111 1111 |
+---------+--------+-------+-----------+
^
2.2. 压缩列表49
Redis 设计与实现, 第一版
|
ZIPLIST_ENTRY_HEAD
&
address ZIPLIST_ENTRY_TAIL
&
ZIPLIST_ENTRY_END
空白ziplist 的表头、表尾和末端处于同一地址。
创建了ziplist 之后，就可以往里面添加新节点了，根据新节点添加位置的不同，这个工作可以
分为两类来进行：
1. 将节点添加到ziplist 末端：在这种情况下，新节点的后面没有任何节点。
2. 将节点添加到某个/某些节点的前面：在这种情况下，新节点的后面有至少一个节点。
以下两个小节分别讨论这两种情况。
2.2.4 将节点添加到末端
将新节点添加到ziplist 的末端需要执行以下三个步骤：
1. 记录到达ziplist 末端所需的偏移量（因为之后的内存重分配可能会改变ziplist 的地址，
因此记录偏移量而不是保存指针）
2. 根据新节点要保存的值，计算出编码这个值所需的空间大小，以及编码它前一个节点的
长度所需的空间大小，然后对ziplist 进行内存重分配。
3. 设置新节点的各项属性：pre_entry_length 、encoding 、length 和content 。
4. 更新ziplist 的各项属性，比如记录空间占用的zlbytes ，到达表尾节点的偏移量zltail
，以及记录节点数量的zllen 。
举个例子，假设现在要将一个新节点添加到只含有一个节点的ziplist 上，程序首先要执行步骤
1 ，定位ziplist 的末端：
area |<---- ziplist header ---->|<--- entries -->|<-- end -->|
size 4 bytes 4 bytes 2 bytes 5 bytes 1 bytes
+---------+--------+-------+----------------+-----------+
component | zlbytes | zltail | zllen | entry 1 | zlend |
| | | | | |
value | 10000 | 1010 | 1 | ? | 1111 1111 |
+---------+--------+-------+----------------+-----------+
^ ^
| |
address ZIPLIST_ENTRY_HEAD ZIPLIST_ENTRY_END
&
ZIPLIST_ENTRY_TAIL
然后执行步骤2 ，程序需要计算新节点所需的空间：
假设我们要添加到节点里的值为字符数组hello world ，那么保存这个值共需要12 字节的空
间：
. 11 字节用于保存字符数组本身；
. 另外1 字节中的2 bit 用于保存类型编码00 ，而其余6 bit 则保存字符数组长度11 的二
进制001011 。
50 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
另外，节点还需要1 字节，用于保存前一个节点的长度5 （二进制101 ）。
合算起来，为了添加新节点，ziplist 总共需要多分配13 字节空间。以下是分配完成之后，
ziplist 的样子：
area |<---- ziplist header ---->|<------------ entries ------------>|<-- end -->|
size 4 bytes 4 bytes 2 bytes 5 bytes 13 bytes 1 bytes
+---------+--------+-------+----------------+------------------+-----------+
component | zlbytes | zltail | zllen | entry 1 | entry 2 | zlend |
| | | | | | |
value | 10000 | 1010 | 1 | ? | pre_entry_length | 1111 1111 |
| | | | | ? | |
| | | | | | |
| | | | | encoding | |
| | | | | ? | |
| | | | | | |
| | | | | length | |
| | | | | ? | |
| | | | | | |
| | | | | content | |
| | | | | ? | |
| | | | | | |
+---------+--------+-------+----------------+------------------+-----------+
^ ^
| |
address ZIPLIST_ENTRY_HEAD ZIPLIST_ENTRY_END
&
ZIPLIST_ENTRY_TAIL
步骤三，更新新节点的各项属性（为了表示的简单，content 的内容使用字符而不是二进制来
表示）：
area |<---- ziplist header ---->|<------------ entries ------------>|<-- end -->|
size 4 bytes 4 bytes 2 bytes 5 bytes 13 bytes 1 bytes
+---------+--------+-------+----------------+------------------+-----------+
component | zlbytes | zltail | zllen | entry 1 | entry 2 | zlend |
| | | | | | |
value | 10000 | 1010 | 1 | ? | pre_entry_length | 1111 1111 |
| | | | | 101 | |
| | | | | | |
| | | | | encoding | |
| | | | | 00 | |
| | | | | | |
| | | | | length | |
| | | | | 001011 | |
| | | | | | |
| | | | | content | |
| | | | | hello world | |
| | | | | | |
+---------+--------+-------+----------------+------------------+-----------+
^ ^
| |
address ZIPLIST_ENTRY_HEAD ZIPLIST_ENTRY_END
&
ZIPLIST_ENTRY_TAIL
2.2. 压缩列表51
Redis 设计与实现, 第一版
最后一步，更新ziplist 的zlbytes 、zltail 和zllen 属性：
area |<---- ziplist header ---->|<------------ entries ------------>|<-- end -->|
size 4 bytes 4 bytes 2 bytes 5 bytes 13 bytes 1 bytes
+---------+--------+-------+----------------+------------------+-----------+
component | zlbytes | zltail | zllen | entry 1 | entry 2 | zlend |
| | | | | | |
value | 11101 | 1111 | 10 | ? | pre_entry_length | 1111 1111 |
| | | | | 101 | |
| | | | | | |
| | | | | encoding | |
| | | | | 00 | |
| | | | | | |
| | | | | length | |
| | | | | 001011 | |
| | | | | | |
| | | | | content | |
| | | | | hello world | |
| | | | | | |
+---------+--------+-------+----------------+------------------+-----------+
^ ^ ^
| | |
address | ZIPLIST_ENTRY_TAIL ZIPLIST_ENTRY_END
|
ZIPLIST_ENTRY_HEAD
到这一步，添加新节点到表尾的工作正式完成。
Note: 这里没有演示往空ziplist 添加第一个节点的过程，因为这个过程和上面演示的添加第
二个节点的过程类似；而且因为第一个节点的存在，添加第二个节点的过程可以更好地展示“将
节点添加到表尾”这一操作的一般性。
2.2.5 将节点添加到某个/某些节点的前面
比起将新节点添加到ziplist 的末端，将一个新节点添加到某个/某些节点的前面要复杂得多，
因为这种操作除了将新节点添加到ziplist 以外，还可能引起后续一系列节点的改变。
举个例子，假设我们要将一个新节点new 添加到节点prev 和next 之间：
add new entry here
|V
+----------+----------+----------+----------+----------+
| | | | | |
| prev | next | next + 1 | next + 2 | ... |
| | | | | |
+----------+----------+----------+----------+----------+
程序首先为新节点扩大ziplist 的空间：
+----------+----------+----------+----------+----------+----------+
| | | | | | |
| prev | ??? | next | next + 1 | next + 2 | ... |
| | | | | | |
+----------+----------+----------+----------+----------+----------+
52 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
|<-------->|
expand
space
然后设置new 节点的各项值——到目前为止，一切都和前面介绍的添加操作一样：
set value,
property,
length,
etc.
|v
+----------+----------+----------+----------+----------+----------+
| | | | | | |
| prev | new | next | next + 1 | next + 2 | ... |
| | | | | | |
+----------+----------+----------+----------+----------+----------+
现在，新的new 节点取代原来的prev 节点，成为了next 节点的新前驱节点，不过，因为这时
next 节点的pre_entry_length 域编码的仍然是prev 节点的长度，所以程序需要将new 节点
的长度编码进next 节点的pre_entry_length 域里，这里会出现三种可能：
1. next 的pre_entry_length 域的长度正好能够编码new 的长度（都是1 字节或者都是5
字节）
2. next 的pre_entry_length 只有1 字节长，但编码new 的长度需要5 字节
3. next 的pre_entry_length 有5 字节长，但编码new 的长度只需要1 字节
对于情况1 和3 ，程序直接更新next 的pre_entry_length 域。
如果是第二种情况，那么程序必须对ziplist 进行内存重分配，从而扩展next 的空间。然而，
因为next 的空间长度改变了，所以程序又必须检查next 的后继节点——next+1 ，看它的
pre_entry_length 能否编码next 的新长度，如果不能的话，程序又需要继续对next+1 进行
扩容。。。
这就是说，在某个/某些节点的前面添加新节点之后，程序必须沿着路径一个个检查后续的节
点是否满足新长度的编码要求，直到遇到一个能满足要求的节点（如果有一个能满足，那么这
个节点之后的其他节点也满足），或者到达ziplist 的末端zlend 为止，这种检查操作的复杂度
为O(N2) 。
不过，因为只有在新添加节点的后面有连续多个长度接近254 的节点时，这种连锁更新才会发
生，所以可以普遍地认为，这种连锁更新发生的概率非常小，在一般情况下，将添加操作看成
是O(N) 复杂度也是可以的。
执行完这三种情况的其中一种后，程序更新ziplist 的各项属性，至此，添加操作完成。
Note: 在第三种情况中，程序实际上是可以执行类似于情况二的动作的：它可以一个个地检
查新节点之后的节点，尝试收缩它们的空间长度，不过Redis 决定不这么做，因为在一些情况
下，比如前面提到的，有连续多个长度接近254 的节点时，可能会出现重复的扩展——收缩
——再扩展——再收缩的抖动（flapping）效果，这会让操作的性能变得非常差。
2.2.6 删除节点
删除节点和添加操作的步骤类似。
2.2. 压缩列表53
Redis 设计与实现, 第一版
1) 定位目标节点，并计算节点的空间长度target-size ：
target start here
|V
+----------+----------+----------+----------+----------+----------+
| | | | | | |
| prev | target | next | next + 1 | next + 2 | ... |
| | | | | | |
+----------+----------+----------+----------+----------+----------+
|<-------->|
target-size
2) 进行内存移位，覆盖target 原本的数据，然后通过内存重分配，收缩多余空间：
target start here
|V
+----------+----------+----------+----------+----------+
| | | | | |
| prev | next | next + 1 | next + 2 | ... |
| | | | | |
+----------+----------+----------+----------+----------+
| <------------------------------------------ memmove
3) 检查next 、next+1 等后续节点能否满足新前驱节点的编码。和添加操作一样，删除操作也
可能会引起连锁更新。
2.2.7 遍历
可以对ziplist 进行从前向后的遍历，或者从后先前的遍历。
当进行从前向后的遍历时，程序从指向节点e1 的指针p 开始，计算节点e1 的长度（e1-size），
然后将p 加上e1-size ，就将指针后移到了下一个节点e2 。。。一直这样做下去，直到p 遇到
ZIPLIST_ENTRY_END 为止，这样整个ziplist 就遍历完了：
p + e1-size + e2-size
p + e1-size |
p | |
| | |
V V V
+----------+----------+----------+----------+----------+----------+----------+
| ZIPLIST | | | | | | ZIPLIST |
| ENTRY | e1 | e2 | e3 | e4 | ... | ENTRY |
| HEAD | | | | | | END |
+----------+----------+----------+----------+----------+----------+----------+
|<-------->|<-------->|
e1-size e2-size
当进行从后往前遍历的时候，程序从指向节点eN 的指针p 出发，取出eN 的pre_entry_length
值，然后用p 减去pre_entry_length ，这就将指针移动到了前一个节点eN-1 。。。一直这样
做下去，直到p 遇到ZIPLIST_ENTRY_HEAD 为止，这样整个ziplist 就遍历完了。
54 Chapter 2. 内存映射数据结构
Redis 设计与实现, 第一版
p - eN.pre_entry_length
||
p
| |
V V
+----------+----------+----------+----------+----------+----------+----------+
| ZIPLIST | | | | | | ZIPLIST |
| ENTRY | e1 | e2 | ... | eN-1 | eN | ENTRY |
| HEAD | | | | | | END |
+----------+----------+----------+----------+----------+----------+----------+
2.2.8 查找元素、根据值定位节点
这两个操作和遍历的原理基本相同，不再赘述。
2.2.9 小结
. ziplist 是由一系列特殊编码的内存块构成的列表，它可以保存字符数组或整数值，它还是
哈希键、列表键和有序集合键的底层实现之一。
. ziplist 典型分布结构如下：
area |<---- ziplist header ---->|<----------- entries ------------->|<-end->|
size 4 bytes 4 bytes 2 bytes ? ? ? ? 1 byte
+---------+--------+-------+--------+--------+--------+--------+-------+
component | zlbytes | zltail | zllen | entry1 | entry2 | ... | entryN | zlend |
+---------+--------+-------+--------+--------+--------+--------+-------+
^ ^ ^
address | | |
ZIPLIST_ENTRY_HEAD | ZIPLIST_ENTRY_END
|
ZIPLIST_ENTRY_TAIL
. ziplist 节点的分布结构如下：
area |<------------------- entry -------------------->|
+------------------+----------+--------+---------+
component | pre_entry_length | encoding | length | content |
+------------------+----------+--------+---------+
. 添加和删除ziplist 节点有可能会引起连锁更新，因此，添加和删除操作的最坏复杂度为
O(N2) ，不过，因为连锁更新的出现概率并不高，所以一般可以将添加和删除操作的复
杂度视为O(N) 。
2.2. 压缩列表55
Redis 设计与实现, 第一版
56 Chapter 2. 内存映射数据结构
第3 章
Redis 数据类型
既然Redis 的键值对可以保存不同类型的值，那么很自然就需要对键值的类型进行检查以及多
态处理。
为了让基于类型的操作更加方便地执行，Redis 创建了自己的类型系统。
在这一部分，我们将对Redis 所使用的对象系统进行了解，并分别观察字符串、哈希表、列表、
集合和有序集类型的底层实现。
3.1 对象处理机制
在Redis 的命令中，用于对键（key）进行处理的命令占了很大一部分，而对于键所保存的值的
类型（后简称“键的类型” ），键能执行的命令又各不相同。
比如说，LPUSH 和LLEN 只能用于列表键，而SADD 和SRANDMEMBER 只能用于集合
键，等等。
另外一些命令，比如DEL 、TTL 和TYPE ，可以用于任何类型的键，但是，要正确实现这些
命令，必须为不同类型的键设置不同的处理方式：比如说，删除一个列表键和删除一个字符串
键的操作过程就不太一样。
以上的描述说明，Redis 必须让每个键都带有类型信息，使得程序可以检查键的类型，并为它
选择合适的处理方式。
另外，在前面介绍各个底层数据结构时有提到，Redis 的每一种数据类型，比如字符串、列表、
有序集，它们都拥有不只一种底层实现（Redis 内部称之为编码，encoding），这说明，每当对
某种数据类型的键进行操作时，程序都必须根据键所采取的编码，进行不同的操作。
比如说，集合类型就可以由字典和整数集合两种不同的数据结构实现，但是，当用户执行
ZADD 命令时，他/她应该不必关心集合使用的是什么编码，只要Redis 能按照ZADD 命令的
指示，将新元素添加到集合就可以了。
这说明，操作数据类型的命令除了要对键的类型进行检查之外，还需要根据数据类型的不同编
码进行多态处理。
57
Redis 设计与实现, 第一版
为了解决以上问题，Redis 构建了自己的类型系统，这个系统的主要功能包括：
. redisObject 对象。
. 基于redisObject 对象的类型检查。
. 基于redisObject 对象的显式多态函数。
. 对redisObject 进行分配、共享和销毁的机制。
以下小节将分别介绍类型系统的这几个方面。
Note: 因为C 并不是面向对象语言，这里将redisObject 称呼为对象一是为了讲述的方便，
二是希望通过模仿OOP 的常用术语，让这里的内容更容易被理解，redisObject 实际上是只
是一个结构类型。
3.1.1 redisObject 数据结构，以及Redis 的数据类型
redisObject 是Redis 类型系统的核心，数据库中的每个键、值，以及Redis 本身处理的参数，
都表示为这种数据类型。
redisObject 的定义位于redis.h ：
/*
* Redis 对象
*/
typedef struct redisObject {
// 类型
unsigned type:4;
// 对齐位
unsigned notused:2;
// 编码方式
unsigned encoding:4;
// LRU 时间（相对于server.lruclock）
unsigned lru:22;
// 引用计数
int refcount;
// 指向对象的值
void *ptr;
} robj;
type 、encoding 和ptr 是最重要的三个属性。
type 记录了对象所保存的值的类型，它的值可能是以下常量的其中一个（定义位于redis.h）：
/*
* 对象类型
*/
#define REDIS_STRING 0 // 字符串
#define REDIS_LIST 1 // 列表
58 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
#define REDIS_SET 2 // 集合
#define REDIS_ZSET 3 // 有序集
#define REDIS_HASH 4 // 哈希表
encoding 记录了对象所保存的值的编码，它的值可能是以下常量的其中一个（定义位于
redis.h）：
/*
* 对象编码
*/
#define REDIS_ENCODING_RAW 0 // 编码为字符串
#define REDIS_ENCODING_INT 1 // 编码为整数
#define REDIS_ENCODING_HT 2 // 编码为哈希表
#define REDIS_ENCODING_ZIPMAP 3 // 编码为zipmap
#define REDIS_ENCODING_LINKEDLIST 4 // 编码为双端链表
#define REDIS_ENCODING_ZIPLIST 5 // 编码为压缩列表
#define REDIS_ENCODING_INTSET 6 // 编码为整数集合
#define REDIS_ENCODING_SKIPLIST 7 // 编码为跳跃表
ptr 是一个指针，指向实际保存值的数据结构，这个数据结构由type 属性和encoding 属性决
定。
举个例子， 如果一个redisObject 的type 属性为REDIS_LIST ， encoding 属性为
REDIS_ENCODING_LINKEDLIST ，那么这个对象就是一个Redis 列表，它的值保存在一个双
端链表内，而ptr 指针就指向这个双端链表；
另一方面， 如果一个redisObject 的type 属性为REDIS_HASH ， encoding 属性为
REDIS_ENCODING_ZIPMAP ，那么这个对象就是一个Redis 哈希表，它的值保存在一个zipmap
里，而ptr 指针就指向这个zipmap ；诸如此类。
下图展示了redisObject 、Redis 所有数据类型、以及Redis 所有编码方式（底层实现）三者
之间的关系：
3.1. 对象处理机制59
Redis 设计与实现, 第一版
redisObject
字符串
REDIS_STRING
列表
REDIS_LIST
集合
REDIS_SET
有序集合
REDIS_ZSET
哈希表
REDIS_HASH
字符串
REDIS_ENCODING_RAW
整数
REDIS_ENCODING_INT
双端链表
REDIS_ENCODING_LINKEDLIST
压缩列表
REDIS_ENCODING_ZIPLIST
字典
REDIS_ENCODING_HT
整数集合
REDIS_ENCODING_INTSET
跳跃表
REDIS_ENCODING_SKIPLIST
这个图展示了Redis 各种数据类型，以及它们的编码方式。
Note: REDIS_ENCODING_ZIPMAP 没有出现在图中，因为从Redis 2.6 开始，它不再是任何数
据类型的底层结构。
3.1.2 命令的类型检查和多态
有了redisObject 结构的存在，在执行处理数据类型的命令时，进行类型检查和对编码进行多
态操作就简单得多了。
当执行一个处理数据类型的命令时，Redis 执行以下步骤：
1. 根据给定key ，在数据库字典中查找和它像对应的redisObject ，如果没找到，就返回
NULL 。
2. 检查redisObject 的type 属性和执行命令所需的类型是否相符，如果不相符，返回类
型错误。
3. 根据redisObject 的encoding 属性所指定的编码，选择合适的操作函数来处理底层的
数据结构。
4. 返回数据结构的操作结果作为命令的返回值。
作为例子，以下展示了对键key 执行LPOP 命令的完整过程：
60 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
LPOP key
Redis 从数据库中查找 key
对应的 redisObject 结构
数据库返回 NULL ？
key 不存在
返回空回复
是
redisObject 的类型为
REDIS_LIST ？
否
调用多态pop 函数
是
key 不是列表
返回类型错误
否
从 ziplist 中弹出最左节点
对象的编码为
ZIPLIST
从双端链表中弹出最左节点
对象的编码为
LINKEDLIST
返回被弹出的元素
3.1.3 对象共享
有一些对象在Redis 中非常常见，比如命令的返回值OK 、ERROR 、WRONGTYPE 等字符，另外，
一些小范围的整数，比如个位、十位、百位的整数都非常常见。
3.1. 对象处理机制61
Redis 设计与实现, 第一版
为了利用这种常见情况，Redis 在内部使用了一个Flyweight 模式：通过预分配一些常见的值
对象，并在多个数据结构之间共享这些对象，程序避免了重复分配的麻烦，也节约了一些CPU
时间。
Redis 预分配的值对象有如下这些：
. 各种命令的返回值，比如执行成功时返回的OK ，执行错误时返回的ERROR ，类型错误时
返回的WRONGTYPE ，命令入队事务时返回的QUEUED ，等等。
. 包括0 在内， 小于redis.h/REDIS_SHARED_INTEGERS 的所有整数
（REDIS_SHARED_INTEGERS 的默认值为10000）
因为命令的回复值直接返回给客户端，所以它们的值无须进行共享；另一方面，如果某个命令
的输入值是一个小于REDIS_SHARED_INTEGERS 的整数对象，那么当这个对象要被保存进数据
库时，Redis 就会释放原来的值，并将值的指针指向共享对象。
作为例子，下图展示了三个列表，它们都带有指向共享对象数组中某个值对象的指针：
列表A 20130101 * 10086
共享整数对象数组0 ... 81 ... 100 123 ... 300 ... 999 ... 10000
列表C * * -25 * 列表B * 12345678910 *
三个列表的值分别为：
. 列表A ：[20130101, 300, 10086] ，
. 列表B ：[81, 12345678910, 999] ，
. 列表C ：[100, 0, -25, 123] 。
Note: 共享对象只能被带指针的数据结构使用。
需要提醒的一点是，共享对象只能被字典和双端链表这类能带有指针的数据结构使用。
像整数集合和压缩列表这些只能保存字符串、整数等字面值的内存数据结构，就不能使用共享
对象。
3.1.4 引用计数以及对象的销毁
当将redisObject 用作数据库的键或者值，而不是用来储存参数时，对象的生命期是非常长
的，因为C 语言本身没有自动释放内存的相关机制，如果只依靠程序员的记忆来对对象进行追
踪和销毁，基本是不太可能的。
另一方面，正如前面提到的，一个共享对象可能被多个数据结构所引用，这时像是“这个对象被
引用了多少次？ ”之类的问题就会出现。
为了解决以上两个问题，Redis 的对象系统使用了引用计数技术来负责维持和销毁对象，它的
运作机制如下：
. 每个redisObject 结构都带有一个refcount 属性，指示这个对象被引用了多少次。
. 当新创建一个对象时，它的refcount 属性被设置为1 。
62 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
. 当对一个对象进行共享时，Redis 将这个对象的refcount 增一。
. 当使用完一个对象之后，或者取消对共享对象的引用之后，程序将对象的refcount 减
一。
. 当对象的refcount 降至0 时，这个redisObject 结构，以及它所引用的数据结构的内
存，都会被释放。
3.1.5 小结
. Redis 使用自己实现的对象机制来实现类型判断、命令多态和基于引用计数的垃圾回收。
. 一种Redis 类型的键可以有多种底层实现。
. Redis 会预分配一些常用的数据对象，并通过共享这些对象来减少内存占用，和避免频繁
地为小对象分配内存。
3.2 字符串
REDIS_STRING （字符串）是Redis 使用得最为广泛的数据类型，它除了是SET 、GET 等命令
的操作对象之外，数据库中的所有键，以及执行命令时提供给Redis 的参数，都是用这种类型
保存的。
3.2.1 字符串编码
字符串类型分别使用REDIS_ENCODING_INT 和REDIS_ENCODING_RAW 两种编码：
. REDIS_ENCODING_INT 使用long 类型来保存long 类型值。
. REDIS_ENCODING_RAW 则使用sdshdr 结构来保存sds （也即是char* )、long long 、
double 和long double 类型值。
换句话来说，在Redis 中，只有能表示为long 类型的值，才会以整数的形式保存，其他类型
的整数、小数和字符串，都是用sdshdr 结构来保存。
3.2. 字符串63
Redis 设计与实现, 第一版
字符串
REDIS_STRING
字符串
REDIS_ENCODING_RAW
整数
REDIS_ENCODING_INT
sdshdr long
sds/char* long long double long double long
3.2.2 编码的选择
新创建的字符串默认使用REDIS_ENCODING_RAW 编码，在将字符串作为键或者值保存进数据库
时，程序会尝试将字符串转为REDIS_ENCODING_INT 编码。
3.2.3 字符串命令的实现
Redis 的字符串类型命令，基本上是通过包装sds 数据结构的操作函数来实现的，没有什么需
要说明的地方。
3.3 哈希表
REDIS_HASH （哈希表） 是HSET 、HLEN 等命令的操作对象， 它使用
REDIS_ENCODING_ZIPLIST 和REDIS_ENCODING_HT 两种编码方式：
64 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
哈希表
REDIS_HASH
压缩列表
REDIS_ENCODING_ZIPLIST
字典
REDIS_ENCODING_HT
ziplist dict.h/dict
3.3.1 字典编码的哈希表
当哈希表使用字典编码时，程序将哈希表的键（key）保存为字典的键，将哈希表的值（value）
保存为字典的值。
哈希表所使用的字典的键和值都是字符串对象。
下图展示了一个包含三个键值对的哈希表：
dict
StringObject
"number"
NULL
StringObject
"book"
StringObject
"message"
StringObject
10086
StringObject
"Mastering C++ in 21 days"
StringObject
"hello moto"
3.3. 哈希表65
Redis 设计与实现, 第一版
3.3.2 压缩列表编码的哈希表
当使用REDIS_ENCODING_ZIPLIST 编码哈希表时，程序通过将键和值一同推入压缩列表，从而
形成保存哈希表所需的键-值对结构：
+---------+------+------+------+------+------+------+------+------+---------+
| ZIPLIST | | | | | | | | | ZIPLIST |
| ENTRY | key1 | val1 | key2 | val2 | ... | ... | keyN | valN | ENTRY |
| HEAD | | | | | | | | | END |
+---------+------+------+------+------+------+------+------+------+---------+
新添加的key-value 对会被添加到压缩列表的表尾。
当进行查找/删除或更新操作时，程序先定位到键的位置，然后再通过对键的位置来定位值的
位置。
3.3.3 编码的选择
创建空白哈希表时，程序默认使用REDIS_ENCODING_ZIPLIST 编码，当以下任何一个条件被满
足时，程序将编码从切换为REDIS_ENCODING_HT ：
. 哈希表中某个键或某个值的长度大于server.hash_max_ziplist_value （默认值为64
）。
. 压缩列表中的节点数量大于server.hash_max_ziplist_entries （默认值为512 ）。
3.3.4 哈希命令的实现
哈希类型命令的实现全都是对字典和压缩列表操作函数的包装，以及几个在两种编码之间进行
转换的函数，没有特别要讲解的地方。
3.4 列表
REDIS_LIST （列表） 是LPUSH 、LRANGE 等命令的操作对象， 它使用
REDIS_ENCODING_ZIPLIST 和REDIS_ENCODING_LINKEDLIST 这两种方式编码：
66 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
列表
REDIS_LIST
压缩列表
REDIS_ENCODING_ZIPLIST
双端链表
REDIS_ENCODING_LINKEDLIST
ziplist adlist.h/list
3.4.1 编码的选择
创建新列表时Redis 默认使用REDIS_ENCODING_ZIPLIST 编码，当以下任意一个条件被满足
时，列表会被转换成REDIS_ENCODING_LINKEDLIST 编码：
. 试图往列表新添加一个字符串值， 且这个字符串的长度超过
server.list_max_ziplist_value （默认值为64 ）。
. ziplist 包含的节点超过server.list_max_ziplist_entries （默认值为512 ）。
3.4.2 列表命令的实现
因为两种底层实现的抽象方式和列表的抽象方式非常接近，所以列表命令几乎就是通过一对一
地映射到底层数据结构的操作来实现的。
既然这些映射都非常直观，这里就不做赘述了，在以下的内容中，我们将焦点放在BLPOP 、
BRPOP 和BRPOPLPUSH 这个几个阻塞命令的实现原理上。
3.4.3 阻塞的条件
BLPOP 、BRPOP 和BRPOPLPUSH 三个命令都可能造成客户端被阻塞，以下将这些命令统
称为列表的阻塞原语。
阻塞原语并不是一定会造成客户端阻塞：
. 只有当这些命令被用于空列表时，它们才会阻塞客户端。
. 如果被处理的列表不为空的话，它们就执行无阻塞版本的LPOP 、RPOP 或RPOPLPUSH
命令。
作为例子，以下流程图展示了BLPOP 决定是否对客户端进行阻塞过程：
3.4. 列表67
Redis 设计与实现, 第一版
BLPOP key
key 非空且不是列表？
返回类型错误
是
key 是否为空?
否
阻塞客户端
是
执行LPOP key 命令
否
3.4.4 阻塞
当一个阻塞原语的处理目标为空键时，执行该阻塞原语的客户端就会被阻塞。
阻塞一个客户端需要执行以下步骤：
1. 将客户端的状态设为“正在阻塞” ，并记录阻塞这个客户端的各个键，以及阻塞的最长时限
（timeout）等数据。
2. 将客户端的信息记录到server.db[i]->blocking_keys 中（其中i 为客户端所使用的数
据库号码）。
3. 继续维持客户端和服务器之间的网络连接，但不再向客户端传送任何信息，造成客户端
阻塞。
步骤2 是将来解除阻塞的关键，server.db[i]->blocking_keys 是一个字典，字典的键是那
些造成客户端阻塞的键，而字典的值是一个链表，链表里保存了所有因为这个键而被阻塞的客
户端（被同一个键所阻塞的客户端可能不止一个）：
68 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
blocking_keys
key1
key2
key3
...
keyN
client2
client7
client3
client5 client1 NULL
NULL
client4 client6 NULL
在上图展示的blocking_keys 例子中，client2 、client5 和client1 三个客户端就正被
key1 阻塞，而其他几个客户端也正在被别的两个key 阻塞。
当客户端被阻塞之后，脱离阻塞状态有以下三种方法：
1. 被动脱离：有其他客户端为造成阻塞的键推入了新元素。
2. 主动脱离：到达执行阻塞原语时设定的最大阻塞时间。
3. 强制脱离：客户端强制终止和服务器的连接，或者服务器停机。
以下内容将分别介绍被动脱离和主动脱离的实现方式。
3.4.5 阻塞因LPUSH 、RPUSH 、LINSERT 等添加命令而被取消
通过将新元素推入造成客户端阻塞的某个键中，可以让相应的客户端从阻塞状态中脱离出来
（取消阻塞的客户端数量取决于推入元素的数量）。
LPUSH 、RPUSH 和LINSERT 这三个添加新元素到列表的命令， 在底层都由一个
pushGenericCommand 的函数实现，这个函数的运作流程如下图：
3.4. 列表69
Redis 设计与实现, 第一版
pushGenericCommand
key 非空且不是列表？
返回类型错误
是
key 为空？
否
如果 key 存在于 server.db[i]->blocking_keys
那么为 key 创建一个 readyList 结构
并将它添加到 server.ready_keys 链表中
是
将输入值推入列表
否
当向一个空键推入新元素时，pushGenericCommand 函数执行以下两件事：
1. 检查这个键是否存在于前面提到的server.db[i]->blocking_keys 字典里，如果是的
话，那么说明有至少一个客户端因为这个key 而被阻塞，程序会为这个键创建一个
redis.h/readyList 结构，并将它添加到server.ready_keys 链表中。
2. 将给定的值添加到列表键中。
readyList 结构的定义如下：
typedef struct readyList {
redisDb *db;
robj *key;
} readyList;
readyList 结构的key 属性指向造成阻塞的键，而db 则指向该键所在的数据库。
70 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
举个例子，假设某个非阻塞客户端正在使用0 号数据库，而这个数据库当前的blocking_keys
属性的值如下：
blocking_keys
key1
key2
key3
...
keyN
client2
client7
client3
client5 client1 NULL
NULL
client4 client6 NULL
如果这时客户端对该数据库执行PUSH key3 value ，那么pushGenericCommand 将创建一个
db 属性指向0 号数据库、key 属性指向key3 键对象的readyList 结构，并将它添加到服务器
server.ready_keys 属性的链表中：
redisServer
...
ready_keys
...
listNode
prev next value
list
readyList
db
key
redisDb
...
dict
...
dict
(key space of DB)
...
key3
...
NULL
在我们这个例子中，到目前为止，pushGenericCommand 函数完成了以下两件事：
1. 将readyList 添加到服务器。
2. 将新元素value 添加到键key3 。
虽然key3 已经不再是空键，但到目前为止，被key3 阻塞的客户端还没有任何一个被解除阻塞
状态。
为了做到这一点，Redis 的主进程在执行完pushGenericCommand 函数之后，会继续调用
handleClientsBlockedOnLists 函数，这个函数执行以下操作：
1. 如果server.ready_keys 不为空， 那么弹出该链表的表头元素， 并取出元素中的
readyList 值。
2. 根据readyList 值所保存的key 和db ，在server.blocking_keys 中查找所有因为key
而被阻塞的客户端（以链表的形式保存）。
3. 如果key 不为空，那么从key 中弹出一个元素，并弹出客户端链表的第一个客户端，然
后将被弹出元素返回给被弹出客户端作为阻塞原语的返回值。
4. 根据readyList 结构的属性，删除server.blocking_keys 中相应的客户端数据，取消
客户端的阻塞状态。
3.4. 列表71
Redis 设计与实现, 第一版
5. 继续执行步骤3 和4 ，直到key 没有元素可弹出，或者所有因为key 而阻塞的客户端都
取消阻塞为止。
6. 继续执行步骤1 ，直到ready_keys 链表里的所有readyList 结构都被处理完为止。
用一段伪代码描述以上操作可能会更直观一些：
def handleClientsBlockedOnLists():
# 执行直到ready_keys 为空
while server.ready_keys != NULL:
# 弹出链表中的第一个readyList
rl = server.ready_keys.pop_first_node()
# 遍历所有因为这个键而被阻塞的客户端
for client in all_client_blocking_by_key(rl.key, rl.db):
# 只要还有客户端被这个键阻塞，就一直从键中弹出元素
# 如果被阻塞客户端执行的是BLPOP ，那么对键执行LPOP
# 如果执行的是BRPOP ，那么对键执行RPOP
element = rl.key.pop_element()
if element == NULL:
# 键为空，跳出for 循环
# 余下的未解除阻塞的客户端只能等待下次新元素的进入了
break
else:#
清除客户端的阻塞信息
server.blocking_keys.remove_blocking_info(client)
# 将元素返回给客户端，脱离阻塞状态
client.reply_list_item(element)
3.4.6 先阻塞先服务（FBFS）策略
值得一提的是，当程序添加一个新的被阻塞客户端到server.blocking_keys 字典的链表中
时，它将该客户端放在链表的最后，而当handleClientsBlockedOnLists 取消客户端的阻塞
时，它从链表的最前面开始取消阻塞：这个链表形成了一个FIFO 队列，最先被阻塞的客户端
总值最先脱离阻塞状态，Redis 文档称这种模式为先阻塞先服务（FBFS，first-block-first-serve）。
举个例子，在下图所示的阻塞状况中，如果客户端对数据库执行PUSH key3 value ，那么只有
client3 会被取消阻塞，client6 和client4 仍然阻塞；如果客户端对数据库执行PUSH key3
value1 value2 ，那么client3 和client4 的阻塞都会被取消，而客户端client6 依然处于
阻塞状态：
72 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
blocking_keys
key1
key2
key3
...
keyN
client2
client7
client3
client5 client1 NULL
NULL
client4 client6 NULL
3.4.7 阻塞因超过最大等待时间而被取消
前面提到过，当客户端被阻塞时，所有造成它阻塞的键，以及阻塞的最长时限会被记录在客户
端里面，并且该客户端的状态会被设置为“正在阻塞” 。
每次Redis 服务器常规操作函数（server cron job）执行时，程序都会检查所有连接到服务器
的客户端，查看那些处于“正在阻塞”状态的客户端的最大阻塞时限是否已经过期，如果是的话，
就给客户端返回一个空白回复，然后撤销对客户端的阻塞。
可以用一段伪代码来描述这个过程：
def server_cron_job():
# 其他操作...
# 遍历所有已连接客户端
for client in server.all_connected_client:
# 如果客户端状态为“正在阻塞”，并且最大阻塞时限已到达
if client.state == BLOCKING and \
client.max_blocking_timestamp < current_timestamp():
# 那么给客户端发送空回复, 脱离阻塞状态
client.send_empty_reply()
# 并清除客户端在服务器上的阻塞信息
server.blocking_keys.remove_blocking_info(client)
# 其他操作...
3.5 集合
REDIS_SET （集合） 是SADD 、SRANDMEMBER 等命令的操作对象， 它使用
REDIS_ENCODING_INTSET 和REDIS_ENCODING_HT 两种方式编码：
3.5. 集合73
Redis 设计与实现, 第一版
集合
REDIS_SET
intset
REDIS_ENCODING_INTSET
字典
REDIS_ENCODING_HT
intset.h/intset dict.h/dict
3.5.1 编码的选择
第一个添加到集合的元素，决定了创建集合时所使用的编码：
. 如果第一个元素可以表示为long long 类型值（也即是，它是一个整数），那么集合的初
始编码为REDIS_ENCODING_INTSET 。
. 否则，集合的初始编码为REDIS_ENCODING_HT 。
3.5.2 编码的切换
如果一个集合使用REDIS_ENCODING_INTSET 编码，那么当以下任何一个条件被满足时，这个
集合会被转换成REDIS_ENCODING_HT 编码：
. intset 保存的整数值个数超过server.set_max_intset_entries （默认值为512 ）。
. 试图往集合里添加一个新元素，并且这个元素不能被表示为long long 类型（也即是，
它不是一个整数）。
3.5.3 字典编码的集合
当使用REDIS_ENCODING_HT 编码时，集合将元素保存到字典的键里面，而字典的值则统一设
为NULL 。
作为例子，以下图片展示了一个以REDIS_ENCODING_HT 编码表示的集合，集合的成员为elem1
、elem2 和elem3 ：
74 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
dictht
table
size:4
sizemask:3
used:3
dictEntry**
(bucket)
0
1
2
3
dictEntry
key
elem1
value
NULL
next
NULL
dictEntry
key
elem2
value
NULL
next
NULL
dictEntry
key
elem3
value
NULL
next
NULL
NULL
3.5.4 集合命令的实现
Redis 集合类型命令的实现，主要是对intset 和dict 两个数据结构的操作函数的包装，以及
一些在两种编码之间进行转换的函数，大部分都没有什么需要解释的地方，唯一比较有趣的是
SINTER 、SUNION 等命令之下的算法实现，以下三个小节就分别讨论它们所使用的算法。
3.5.5 求交集算法
SINTER 和SINTERSTORE 两个命令所使用的求并交集算法可以用Python 表示如下：
# coding: utf-8
def sinter(*multi_set):
# 根据集合的基数进行排序
sorted_multi_set = sorted(multi_set, lambda x, y: len(x) - len(y))
# 使用基数最小的集合作为基础结果集，有助于降低常数项
result = sorted_multi_set[0].copy()
# 剔除所有在sorted_multi_set[0] 中存在
# 但在其他某个集合中不存在的元素
for elem in sorted_multi_set[0]:
for s in sorted_multi_set[1:]:
3.5. 集合75
Redis 设计与实现, 第一版
if (not elem in s):
result.remove(elem)
break
return result
算法的复杂度为O(N2) ，执行步数为S  T ，其中S 为输入集合中基数最小的集合，而T 则
为输入集合的数量。
3.5.6 求并集算法
SUNION 和SUNIONSTORE 两个命令所使用的求并集算法可以用Python 表示如下：
# coding: utf-8
def sunion(*multi_set):
result = set()
for s in multi_set:
for elem in s:
# 重复的元素会被自动忽略
result.add(elem)
return result
算法的复杂度为O(N) 。
3.5.7 求差集算法
Redis 为SDIFF 和SDIFFSTORE 两个命令准备了两种求集合差的算法。
以Python 代码表示的算法一定义如下：
# coding: utf-8
def sdiff_1(*multi_set):
result = multi_set[0].copy()
sorted_multi_set = sorted(multi_set[1:], lambda x, y: len(x) - len(y))
# 当elem 存在于除multi_set[0] 之外的集合时
# 将elem 从result 中删除
for elem in multi_set[0]:
for s in sorted_multi_set:
if elem in s:
result.remove(elem)
break
return result
这个算法的复杂度为O(N2) ，执行步数为S  T ，其中S 为输入集合中基数最小的集合，而
T 则为除第一个集合之外，其他集合的数量。
76 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
以Python 代码表示的算法二定于如下：
# coding: utf-8
def sdiff_2(*multi_set):
# 用第一个集合作为结果集的起始值
result = multi_set[0].copy()
for s in multi_set[1:]:
for elem in s:
# 从结果集中删去其他集合中包含的元素
if elem in result:
result.remove(elem)
return result
这个算法的复杂度同样为O(N2) ，执行步数为S ，其中S 为所有集合的基数总和。
Redis 使用一个程序决定该使用那个求差集算法，程序用Python 表示如下：
# coding: utf-8
from sdiff_1 import sdiff_1
from sdiff_2 import sdiff_2
def sdiff(*multi_set):
# 算法一的常数项较低，给它一点额外的优先级
algo_one_advantage = 2
algo_one_weight = len(multi_set[0]) * len(multi_set[1:]) / algo_one_advantage
algo_two_weight = sum(map(len, multi_set))
if algo_one_weight <= algo_two_weight:
return sdiff_1(*multi_set)
else:
return sdiff_2(*multi_set)
3.6 有序集
REDIS_ZSET （有序集） 是ZADD 、ZCOUNT 等命令的操作对象， 它使用
REDIS_ENCODING_ZIPLIST 和REDIS_ENCODING_SKIPLIST 两种方式编码：
3.6. 有序集77
Redis 设计与实现, 第一版
有序集合
REDIS_ZSET
ziplist
REDIS_ENCODING_ZIPLIST
跳跃表
REDIS_ENCODING_SKIPLIST
ziplist redis.h/zset
dict.h/dict redis.h/zskiplist
3.6.1 编码的选择
在通过ZADD 命令添加第一个元素到空key 时，程序通过检查输入的第一个元素来决定该创
建什么编码的有序集。
如果第一个元素符合以下条件的话，就创建一个REDIS_ENCODING_ZIPLIST 编码的有序集：
. 服务器属性server.zset_max_ziplist_entries 的值大于0 （默认为128 ）。
. 元素的member 长度小于服务器属性server.zset_max_ziplist_value 的值（默认为64
）。
否则，程序就创建一个REDIS_ENCODING_SKIPLIST 编码的有序集。
3.6.2 编码的转换
对于一个REDIS_ENCODING_ZIPLIST 编码的有序集，只要满足以下任一条件，就将它转换为
REDIS_ENCODING_SKIPLIST 编码：
. ziplist 所保存的元素数量超过服务器属性server.zset_max_ziplist_entries 的值
（默认值为128 ）
. 新添加元素的member 的长度大于服务器属性server.zset_max_ziplist_value 的值
（默认值为64 ）
3.6.3 ZIPLIST 编码的有序集
当使用REDIS_ENCODING_ZIPLIST 编码时，有序集将元素保存到ziplist 数据结构里面。
78 Chapter 3. Redis 数据类型
Redis 设计与实现, 第一版
其中，每个有序集元素以两个相邻的ziplist 节点表示，第一个节点保存元素的member 域，
第二个元素保存元素的score 域。
多个元素之间按score 值从小到大排序，如果两个元素的score 相同，那么按字典序对member
进行对比，决定那个元素排在前面，那个元素排在后面。
|<-- element 1 -->|<-- element 2 -->|<-- ....... -->|
+---------+---------+--------+---------+--------+---------+---------+---------+
| ZIPLIST | | | | | | | ZIPLIST |
| ENTRY | member1 | score1 | member2 | score2 | ... | ... | ENTRY |
| HEAD | | | | | | | END |
+---------+---------+--------+---------+--------+---------+---------+---------+
score1 <= score2 <= ...
虽然元素是按score 域有序排序的，但对ziplist 的节点指针只能线性地移动，所以在
REDIS_ENCODING_ZIPLIST 编码的有序集中，查找某个给定元素的复杂度为O(N) 。
每次执行添加/删除/更新操作都需要执行一次查找元素的操作，因此这些函数的复杂度都不低
于O(N) ，至于这些操作的实际复杂度，取决于它们底层所执行的ziplist 操作。
3.6.4 SKIPLIST 编码的有序集
当使用REDIS_ENCODING_SKIPLIST 编码时，有序集元素由redis.h/zset 结构来保存：
/*
* 有序集
*/
typedef struct zset {
// 字典
dict *dict;
// 跳跃表
zskiplist *zsl;
} zset;
zset 同时使用字典和跳跃表两个数据结构来保存有序集元素。
其中，元素的成员由一个redisObject 结构表示，而元素的score 则是一个double 类型的浮
点数，字典和跳跃表两个结构通过将指针共同指向这两个值来节约空间（不用每个元素都复制
两份）。
下图展示了一个REDIS_ENCODING_SKIPLIST 编码的有序集：
3.6. 有序集79
Redis 设计与实现, 第一版
在实际中，字典和跳跃表通过指针来共享元素的 member 和 score
图中对每个元素都重复显示了一遍，只是为了展示的方便
zset
dict
zskiplist
zskipNode
score
NULL
robj
NULL
dict
...
table
...
zskipNode
score
6
robj
x
zskipNode
score
10
robj
y
zskipNode
score
15
robj
z
dictEntry**
(bucket)
0
1
2
dictEntry
key
x
value
6
dictEntry
key
y
value
10
dictEntry
key
z
value
15
通过使用字典结构，并将member 作为键，score 作为值，有序集可以在O(1) 复杂度内：
. 检查给定member 是否存在于有序集（被很多底层函数使用）；
. 取出member 对应的score 值（实现ZSCORE 命令）。
另一方面，通过使用跳跃表，可以让有序集支持以下两种操作：
. 在O(logN) 期望时间、O(N) 最坏时间内根据score 对member 进行定位（被很多底层
函数使用）；
. 范围性查找和处理操作，这是（高效地）实现ZRANGE 、ZRANK 和ZINTERSTORE
等命令的关键。
通过同时使用字典和跳跃表，有序集可以高效地实现按成员查找和按顺序查找两种操作。
80 Chapter 3. Redis 数据类型
第4 章
功能的实现
除了针对单个键值对的操作外，Redis 还提供了一些同时对多个键值对进行处理的功能，比如
事务和Lua 脚本。
另外，一些辅助性的功能，比如慢查询，以及一些和数据库无关的功能，比如订阅与发布，我
们也会经常用到。
通过理解这些功能的底层实现，我们可以更有效地使用它们。
这一部分将对这些功能进行介绍。
4.1 事务
Redis 通过MULTI 、DISCARD 、EXEC 和WATCH 四个命令来实现事务功能，本章首先讨
论使用MULTI 、DISCARD 和EXEC 三个命令实现的一般事务，然后再来讨论带有WATCH
的事务的实现。
因为事务的安全性也非常重要，所以本章最后通过常见的ACID 性质对Redis 事务的安全性进
行了说明。
4.1.1 事务
事务提供了一种“将多个命令打包，然后一次性、按顺序地执行”的机制，并且事务在执行的期
间不会主动中断——服务器在执行完事务中的所有命令之后，才会继续处理其他客户端的其他
命令。
以下是一个事务的例子，它先以MULTI 开始一个事务，然后将多个命令入队到事务中，最后
由EXEC 命令触发事务，一并执行事务中的所有命令：
redis> MULTI
OK
redis> SET book-name "Mastering C++ in 21 days"
81
Redis 设计与实现, 第一版
QUEUED
redis> GET book-name
QUEUED
redis> SADD tag "C++" "Programming" "Mastering Series"
QUEUED
redis> SMEMBERS tag
QUEUED
redis> EXEC
1) OK
2) "Mastering C++ in 21 days"
3) (integer) 3
4) 1) "Mastering Series"
2) "C++"
3) "Programming"
一个事务从开始到执行会经历以下三个阶段：
1. 开始事务。
2. 命令入队。
3. 执行事务。
下文将分别介绍事务的这三个阶段。
4.1.2 开始事务
MULTI 命令的执行标记着事务的开始：
redis> MULTI
OK
这个命令唯一做的就是，将客户端的REDIS_MULTI 选项打开，让客户端从非事务状态切换到事
务状态。
客户端状态的切换
非事务状态事务状态
打开选项
REDIS_MULTI
82 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
4.1.3 命令入队
当客户端处于非事务状态下时，所有发送给服务器端的命令都会立即被服务器执行：
redis> SET msg "hello moto"
OK
redis> GET msg
"hello moto"
但是，当客户端进入事务状态之后，服务器在收到来自客户端的命令时，不会立即执行命令，
而是将这些命令全部放进一个事务队列里，然后返回QUEUED ，表示命令已入队：
redis> MULTI
OK
redis> SET msg "hello moto"
QUEUED
redis> GET msg
QUEUED
以下流程图展示了这一行为：
服务器接到来自客户端的命令
客户端是否正处于事务状态？
将命令放进事务队列里
是
执行命令
否
向客户端返回QUEUED 字符串
表示命令已入队
向客户端返回命令的执行结果
事务队列是一个数组，每个数组项是都包含三个属性：
1. 要执行的命令（cmd）。
2. 命令的参数（argv）。
3. 参数的个数（argc）。
4.1. 事务83
Redis 设计与实现, 第一版
举个例子，如果客户端执行以下命令：
redis> MULTI
OK
redis> SET book-name "Mastering C++ in 21 days"
QUEUED
redis> GET book-name
QUEUED
redis> SADD tag "C++" "Programming" "Mastering Series"
QUEUED
redis> SMEMBERS tag
QUEUED
那么程序将为客户端创建以下事务队列：
数组索引cmd argv argc
0 SET ["book-name", "Mastering C++ in 21 days"] 2
1 GET ["book-name"] 1
2 SADD ["tag", "C++", "Programming", "Mastering Series"] 4
3 SMEMBERS ["tag"] 1
4.1.4 执行事务
前面说到，当客户端进入事务状态之后，客户端发送的命令就会被放进事务队列里。
但其实并不是所有的命令都会被放进事务队列，其中的例外就是EXEC 、DISCARD 、MULTI
和WATCH 这四个命令——当这四个命令从客户端发送到服务器时，它们会像客户端处于非
事务状态一样，直接被服务器执行：
84 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
服务器接到来自客户端的命令
客户端是否正处于事务状态？
命令是否
EXEC 、 DISCARD 、
MULTI 或 WATCH ？
是
执行命令
否
将命令放进事务队列里
否是
向客户端返回QUEUED 字符串
表示命令已入队
向客户端返回命令的执行结果
如果客户端正处于事务状态，那么当EXEC 命令执行时，服务器根据客户端所保存的事务队
列，以先进先出（FIFO）的方式执行事务队列中的命令：最先入队的命令最先执行，而最后入
队的命令最后执行。
比如说，对于以下事务队列：
数组索引cmd argv argc
0 SET ["book-name", "Mastering C++ in 21 days"] 2
1 GET ["book-name"] 1
2 SADD ["tag", "C++", "Programming", "Mastering Series"] 4
3 SMEMBERS ["tag"] 1
程序会首先执行SET 命令，然后执行GET 命令，再然后执行SADD 命令，最后执行SMEMBERS
命令。
执行事务中的命令所得的结果会以FIFO 的顺序保存到一个回复队列中。
比如说，对于上面给出的事务队列，程序将为队列中的命令创建如下回复队列：
4.1. 事务85
Redis 设计与实现, 第一版
数组索引回复类型回复内容
0 status code reply OK
1 bulk reply "Mastering C++ in 21 days"
2 integer reply 3
3 multi-bulk reply ["Mastering Series", "C++", "Programming"]
当事务队列里的所有命令被执行完之后，EXEC 命令会将回复队列作为自己的执行结果返回给
客户端，客户端从事务状态返回到非事务状态，至此，事务执行完毕。
事务的整个执行过程可以用以下伪代码表示：
def execute_transaction():
# 创建空白的回复队列
reply_queue = []
# 取出事务队列里的所有命令、参数和参数数量
for cmd, argv, argc in client.transaction_queue:
# 执行命令，并取得命令的返回值
reply = execute_redis_command(cmd, argv, argc)
# 将返回值追加到回复队列末尾
reply_queue.append(reply)
# 清除客户端的事务状态
clear_transaction_state(client)
# 清空事务队列
clear_transaction_queue(client)
# 将事务的执行结果返回给客户端
send_reply_to_client(client, reply_queue)
4.1.5 在事务和非事务状态下执行命令
无论在事务状态下，还是在非事务状态下，Redis 命令都由同一个函数执行，所以它们共享很
多服务器的一般设置，比如AOF 的配置、RDB 的配置，以及内存限制，等等。
不过事务中的命令和普通命令在执行上还是有一点区别的，其中最重要的两点是：
1. 非事务状态下的命令以单个命令为单位执行，前一个命令和后一个命令的客户端不一定
是同一个；
而事务状态则是以一个事务为单位，执行事务队列中的所有命令：除非当前事务执行完
毕，否则服务器不会中断事务，也不会执行其他客户端的其他命令。
2. 在非事务状态下，执行命令所得的结果会立即被返回给客户端；
而事务则是将所有命令的结果集合到回复队列，再作为EXEC 命令的结果返回给客户端。
4.1.6 事务状态下的DISCARD 、MULTI 和WATCH 命令
除了EXEC 之外，服务器在客户端处于事务状态时，不加入到事务队列而直接执行的另外三
个命令是DISCARD 、MULTI 和WATCH 。
86 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
DISCARD 命令用于取消一个事务，它清空客户端的整个事务队列，然后将客户端从事务状态
调整回非事务状态，最后返回字符串OK 给客户端，说明事务已被取消。
Redis 的事务是不可嵌套的，当客户端已经处于事务状态，而客户端又再向服务器发送MULTI
时，服务器只是简单地向客户端发送一个错误，然后继续等待其他命令的入队。MULTI 命令
的发送不会造成整个事务失败，也不会修改事务队列中已有的数据。
WATCH 只能在客户端进入事务状态之前执行，在事务状态下发送WATCH 命令会引发一个
错误，但它不会造成整个事务失败，也不会修改事务队列中已有的数据（和前面处理MULTI
的情况一样）。
4.1.7 带WATCH 的事务
WATCH 命令用于在事务开始之前监视任意数量的键：当调用EXEC 命令执行事务时，如果
任意一个被监视的键已经被其他客户端修改了，那么整个事务不再执行，直接返回失败。
以下示例展示了一个执行失败的事务例子：
redis> WATCH name
OK
redis> MULTI
OK
redis> SET name peter
QUEUED
redis> EXEC
(nil)
以下执行序列展示了上面的例子是如何失败的：
时间客户端A 客户端B
T1 WATCH name
T2 MULTI
T3 SET name peter
T4 SET name john
T5 EXEC
在时间T4 ，客户端B 修改了name 键的值，当客户端A 在T5 执行EXEC 时，Redis 会发现
name 这个被监视的键已经被修改，因此客户端A 的事务不会被执行，而是直接返回失败。
下文就来介绍WATCH 的实现机制，并且看看事务系统是如何检查某个被监视的键是否被修
改，从而保证事务的安全性的。
4.1.8 WATCH 命令的实现
在每个代表数据库的redis.h/redisDb 结构类型中，都保存了一个watched_keys 字典，字典
的键是这个数据库被监视的键，而字典的值则是一个链表，链表中保存了所有监视这个键的客
户端。
比如说，以下字典就展示了一个watched_keys 字典的例子：
4.1. 事务87
Redis 设计与实现, 第一版
watched_keys
key1
key2
key3
...
keyN
client2
client7
client3
client5 client1 NULL
NULL
client4 client6 NULL
其中，键key1 正在被client2 、client5 和client1 三个客户端监视，其他一些键也分别被
其他别的客户端监视着。
WATCH 命令的作用，就是将当前客户端和要监视的键在watched_keys 中进行关联。
举个例子，如果当前客户端为client10086 ，那么当客户端执行WATCH key1 key2 时，前面
展示的watched_keys 将被修改成这个样子：
watched_keys
key1
key2
key3
...
keyN
client2
client7
client3
client5 client1 client10086 NULL
client10086 NULL
client4 client6 NULL
通过watched_keys 字典，如果程序想检查某个键是否被监视，那么它只要检查字典中是否存
在这个键即可；如果程序要获取监视某个键的所有客户端，那么只要取出键的值（一个链表），
然后对链表进行遍历即可。
4.1.9 WATCH 的触发
在任何对数据库键空间（key space）进行修改的命令成功执行之后（比如FLUSHDB 、SET
、DEL 、LPUSH 、SADD 、ZREM ，诸如此类），multi.c/touchWatchKey 函数都会被调用
——它检查数据库的watched_keys 字典，看是否有客户端在监视已经被命令修改的键，如果
有的话，程序将所有监视这个/这些被修改键的客户端的REDIS_DIRTY_CAS 选项打开：
88 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
客户端状态的切换
非事务状态事务状态
打开选项
REDIS_MULTI 事务安全性
已被破坏
打开选项
REDIS_DIRTY_CAS
当客户端发送EXEC 命令、触发事务执行时，服务器会对客户端的状态进行检查：
. 如果客户端的REDIS_DIRTY_CAS 选项已经被打开，那么说明被客户端监视的键至少有一
个已经被修改了，事务的安全性已经被破坏。服务器会放弃执行这个事务，直接向客户端
返回空回复，表示事务执行失败。
. 如果REDIS_DIRTY_CAS 选项没有被打开，那么说明所有监视键都安全，服务器正式执行
事务。
可以用一段伪代码来表示这个检查：
def check_safety_before_execute_trasaction():
if client.state && REDIS_DIRTY_CAS:
# 安全性已破坏，清除事务状态
clear_transaction_state(client)
# 清空事务队列
clear_transaction_queue(client)
# 返回空回复给客户端
send_empty_reply(client)
else
# 安全性完好，执行事务
execute_transaction()
举个例子，假设数据库的watched_keys 字典如下图所示：
watched_keys
key1
key2
key3
...
keyN
client2
client7
client3
client5 client1 NULL
NULL
client4 client6 NULL
如果某个客户端对key1 进行了修改（比如执行DEL key1 ），那么所有监视key1 的客户端，包
括client2 、client5 和client1 的REDIS_DIRTY_CAS 选项都会被打开，当客户端client2
、client5 和client1 执行EXEC 的时候，它们的事务都会以失败告终。
4.1. 事务89
Redis 设计与实现, 第一版
最后，当一个客户端结束它的事务时，无论事务是成功执行，还是失败，watched_keys 字典
中和这个客户端相关的资料都会被清除。
4.1.10 事务的ACID 性质
在传统的关系式数据库中，常常用ACID 性质来检验事务功能的安全性。
Redis 事务保证了其中的一致性（C）和隔离性（I），但并不保证原子性（A）和持久性（D）。
以下四小节是关于这四个性质的详细讨论。
原子性（Atomicity）
单个Redis 命令的执行是原子性的，但Redis 没有在事务上增加任何维持原子性的机制，所以
Redis 事务的执行并不是原子性的。
如果一个事务队列中的所有命令都被成功地执行，那么称这个事务执行成功。
另一方面，如果Redis 服务器进程在执行事务的过程中被停止——比如接到KILL 信号、宿主
机器停机，等等，那么事务执行失败。
当事务失败时，Redis 也不会进行任何的重试或者回滚动作。
一致性（Consistency）
Redis 的一致性问题可以分为三部分来讨论：入队错误、执行错误、Redis 进程被终结。
入队错误
在命令入队的过程中， 如果客户端向服务器发送了错误的命令， 比如命令的参数数量
不对，等等，那么服务器将向客户端返回一个出错信息，并且将客户端的事务状态设为
REDIS_DIRTY_EXEC 。
当客户端执行EXEC 命令时，Redis 会拒绝执行状态为REDIS_DIRTY_EXEC 的事务，并返回失
败信息。
redis 127.0.0.1:6379> MULTI
OK
redis 127.0.0.1:6379> set key
(error) ERR wrong number of arguments for 'set' command
redis 127.0.0.1:6379> EXISTS key
QUEUED
redis 127.0.0.1:6379> EXEC
(error) EXECABORT Transaction discarded because of previous errors.
因此，带有不正确入队命令的事务不会被执行，也不会影响数据库的一致性。
90 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
执行错误
如果命令在事务执行的过程中发生错误，比如说，对一个不同类型的key 执行了错误的操作，
那么Redis 只会将错误包含在事务的结果中，这不会引起事务中断或整个失败，不会影响已执
行事务命令的结果，也不会影响后面要执行的事务命令，所以它对事务的一致性也没有影响。
Redis 进程被终结
如果Redis 服务器进程在执行事务的过程中被其他进程终结，或者被管理员强制杀死，那么根
据Redis 所使用的持久化模式，可能有以下情况出现：
. 内存模式：如果Redis 没有采取任何持久化机制，那么重启之后的数据库总是空白的，所
以数据总是一致的。
. RDB 模式：在执行事务时，Redis 不会中断事务去执行保存RDB 的工作，只有在事务执
行之后，保存RDB 的工作才有可能开始。所以当RDB 模式下的Redis 服务器进程在事
务中途被杀死时，事务内执行的命令，不管成功了多少，都不会被保存到RDB 文件里。
恢复数据库需要使用现有的RDB 文件，而这个RDB 文件的数据保存的是最近一次的数
据库快照（snapshot），所以它的数据可能不是最新的，但只要RDB 文件本身没有因为
其他问题而出错，那么还原后的数据库就是一致的。
. AOF 模式：因为保存AOF 文件的工作在后台线程进行，所以即使是在事务执行的中途，
保存AOF 文件的工作也可以继续进行，因此，根据事务语句是否被写入并保存到AOF
文件，有以下两种情况发生：
1）如果事务语句未写入到AOF 文件，或AOF 未被SYNC 调用保存到磁盘，那么当进
程被杀死之后，Redis 可以根据最近一次成功保存到磁盘的AOF 文件来还原数据库，只
要AOF 文件本身没有因为其他问题而出错，那么还原后的数据库总是一致的，但其中的
数据不一定是最新的。
2）如果事务的部分语句被写入到AOF 文件，并且AOF 文件被成功保存，那么不完整的
事务执行信息就会遗留在AOF 文件里，当重启Redis 时，程序会检测到AOF 文件并不
完整，Redis 会退出，并报告错误。需要使用redis-check-aof 工具将部分成功的事务命令
移除之后，才能再次启动服务器。还原之后的数据总是一致的，而且数据也是最新的（直
到事务执行之前为止）。
隔离性（Isolation）
Redis 是单进程程序，并且它保证在执行事务时，不会对事务进行中断，事务可以运行直到执
行完所有事务队列中的命令为止。因此，Redis 的事务是总是带有隔离性的。
持久性（Durability）
因为事务不过是用队列包裹起了一组Redis 命令，并没有提供任何额外的持久性功能，所以事
务的持久性由Redis 所使用的持久化模式决定：
. 在单纯的内存模式下，事务肯定是不持久的。
. 在RDB 模式下，服务器可能在事务执行之后、RDB 文件更新之前的这段时间失败，所
以RDB 模式下的Redis 事务也是不持久的。
. 在AOF 的“总是SYNC ”模式下，事务的每条命令在执行成功之后，都会立即调用fsync
或fdatasync 将事务数据写入到AOF 文件。但是，这种保存是由后台线程进行的，主
4.1. 事务91
Redis 设计与实现, 第一版
线程不会阻塞直到保存成功，所以从命令执行成功到数据保存到硬盘之间，还是有一段
非常小的间隔，所以这种模式下的事务也是不持久的。
其他AOF 模式也和“总是SYNC ”模式类似，所以它们都是不持久的。
4.1.11 小结
. 事务提供了一种将多个命令打包，然后一次性、有序地执行的机制。
. 事务在执行过程中不会被中断，所有事务命令执行完之后，事务才能结束。
. 多个命令会被入队到事务队列中，然后按先进先出（FIFO）的顺序执行。
. 带WATCH 命令的事务会将客户端和被监视的键在数据库的watched_keys 字典中进行关
联，当键被修改时，程序会将所有监视被修改键的客户端的REDIS_DIRTY_CAS 选项打开。
. 只有在客户端的REDIS_DIRTY_CAS 选项未被打开时，才能执行事务，否则事务直接返回
失败。
. Redis 的事务保证了ACID 中的一致性（C）和隔离性（I），但并不保证原子性（A）和
持久性（D）。
4.2 订阅与发布
Redis 通过PUBLISH 、SUBSCRIBE 等命令实现了订阅与发布模式，这个功能提供两种信息
机制，分别是订阅/发布到频道和订阅/发布到模式，下文先讨论订阅/发布到频道的实现，再讨
论订阅/发布到模式的实现。
4.2.1 频道的订阅与信息发送
Redis 的SUBSCRIBE 命令可以让客户端订阅任意数量的频道，每当有新信息发送到被订阅的
频道时，信息就会被发送给所有订阅指定频道的客户端。
作为例子，下图展示了频道channel1 ，以及订阅这个频道的三个客户端——client2 、
client5 和client1 之间的关系：
channel1
client2
subscribe
client5
subscribe
client1
subscribe
92 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
当有新消息通过PUBLISH 命令发送给频道channel1 时，这个消息就会被发送给订阅它的三
个客户端：
PUBLISH channel1 message
channel1
client2
message
client5
message
client1
message
在后面的内容中，我们将探讨SUBSCRIBE 和PUBLISH 命令的实现，以及这套订阅与发布
机制的运作原理。
4.2.2 订阅频道
每个Redis 服务器进程都维持着一个表示服务器状态的redis.h/redisServer 结构，结构的
pubsub_channels 属性是一个字典，这个字典就用于保存订阅频道的信息：
struct redisServer {
// ...
dict *pubsub_channels;
// ...
};
其中，字典的键为正在被订阅的频道，而字典的值则是一个链表，链表中保存了所有订阅这个
频道的客户端。
比如说，在下图展示的这个pubsub_channels 示例中，client2 、client5 和client1 就订
阅了channel1 ，而其他频道也分别被别的客户端所订阅：
4.2. 订阅与发布93
Redis 设计与实现, 第一版
pubsub_channels
channel1
channel2
channel3
...
channelN
client2
client7
client3
client5 client1 NULL
NULL
client4 client6 NULL
当客户端调用SUBSCRIBE 命令时，程序就将客户端和要订阅的频道在pubsub_channels 字
典中关联起来。
举个例子，如果客户端client10086 执行命令SUBSCRIBE channel1 channel2 channel3 ，
那么前面展示的pubsub_channels 将变成下面这个样子：
pubsub_channels
channel1
channel2
channel3
...
channelN
client2
client7
client3
client5 client1 client10086 NULL
client10086 NULL
client4 client6 client10086 NULL
SUBSCRIBE 命令的行为可以用伪代码表示如下：
def SUBSCRIBE(client, channels):
# 遍历所有输入频道
for channel in channels:
# 将客户端添加到链表的末尾
redisServer.pubsub_channels[channel].append(client)
通过pubsub_channels 字典，程序只要检查某个频道是否字典的键，就可以知道该频道是否正
在被客户端订阅；只要取出某个键的值，就可以得到所有订阅该频道的客户端的信息。
4.2.3 发送信息到频道
了解了pubsub_channels 字典的结构之后，解释PUBLISH 命令的实现就非常简单了：当调
用PUBLISH channel message 命令，程序首先根据channel 定位到字典的键，然后将信息发
送给字典值链表中的所有客户端。
比如说，对于以下这个pubsub_channels 实例，如果某个客户端执行命令PUBLISH channel1
"hello moto" ，那么client2 、client5 和client1 三个客户端都将接收到"hello moto"
信息：
94 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
pubsub_channels
channel1
channel2
channel3
...
channelN
client2
client7
client3
client5 client1 NULL
NULL
client4 client6 NULL
PUBLISH 命令的实现可以用以下伪代码来描述：
def PUBLISH(channel, message):
# 遍历所有订阅频道channel 的客户端
for client in server.pubsub_channels[channel]:
# 将信息发送给它们
send_message(client, message)
4.2.4 退订频道
使用UNSUBSCRIBE 命令可以退订指定的频道，这个命令执行的是订阅的反操作：它从
pubsub_channels 字典的给定频道（键）中，删除关于当前客户端的信息，这样被退订频道的
信息就不会再发送给这个客户端。
4.2.5 模式的订阅与信息发送
当使用PUBLISH 命令发送信息到某个频道时，不仅所有订阅该频道的客户端会收到信息，如
果有某个/某些模式和这个频道匹配的话，那么所有订阅这个/这些频道的客户端也同样会收到
信息。
下图展示了一个带有频道和模式的例子，其中tweet.shop.* 模式匹配了tweet.shop.kindle
频道和tweet.shop.ipad 频道，并且有不同的客户端分别订阅它们三个：
tweet.shop.ipad tweet.shop.kindle
tweet.shop.*
match match
client123
subscribe
client256
subscribe
clientX
subscribe
clientY
subscribe
client3333
subscribe
client4444
subscribe
client5555
subscribe
4.2. 订阅与发布95
Redis 设计与实现, 第一版
当有信息发送到tweet.shop.kindle 频道时，信息除了发送给clientX 和clientY 之外，还
会发送给订阅tweet.shop.* 模式的client123 和client256 ：
tweet.shop.ipad
tweet.shop.*
match
client3333
subscribe
client4444
subscribe
client5555
subscribe
client123
message
client256
message
PUBLISH tweet.shop.kindle message
tweet.shop.kindle
clientX
message
clientY
message
另一方面，如果接收到信息的是频道tweet.shop.ipad ，那么client123 和client256 同样
会收到信息：
tweet.shop.ipad tweet.shop.kindle
PUBLISH tweet.shop.ipad message
tweet.shop.*
match
client123
message
client256
message
clientX
subscribe
clientY
subscribe
client3333
message
client4444
message
client5555
message
4.2.6 订阅模式
redisServer.pubsub_patterns 属性是一个链表，链表中保存着所有和模式相关的信息：
struct redisServer {
// ...
list *pubsub_patterns;
// ...
};
链表中的每个节点都包含一个redis.h/pubsubPattern 结构：
96 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
typedef struct pubsubPattern {
redisClient *client;
robj *pattern;
} pubsubPattern;
client 属性保存着订阅模式的客户端，而pattern 属性则保存着被订阅的模式。
每当调用PSUBSCRIBE 命令订阅一个模式时，程序就创建一个包含客户端信息和被订阅模式的
pubsubPattern 结构，并将该结构添加到redisServer.pubsub_patterns 链表中。
作为例子，下图展示了一个包含两个模式的pubsub_patterns 链表，其中client123 和
client256 都正在订阅tweet.shop.* 模式：
redisServer
...
pubsub_patterns
...
pubsubPattern
client
client123
pattern
tweet.shop.*
pubsubPattern
client
client256
pattern
tweet.shop.*
如果这时客户端client10086 执行PSUBSCRIBE broadcast.list.* ，那么pubsub_patterns
链表将被更新成这样：
redisServer
...
pubsub_patterns
...
pubsubPattern
client
client123
pattern
tweet.shop.*
pubsubPattern
client
client256
pattern
tweet.shop.*
pubsubPattern
client
client10086
pattern
broadcast.live.*
通过遍历整个pubsub_patterns 链表，程序可以检查所有正在被订阅的模式，以及订阅这些模
式的客户端。
4.2.7 发送信息到模式
发送信息到模式的工作也是由PUBLISH 命令进行的，在前面讲解频道的时候，我们给出了这
样一段伪代码，说它定义了PUBLISH 命令的行为：
def PUBLISH(channel, message):
# 遍历所有订阅频道channel 的客户端
for client in server.pubsub_channels[channel]:
# 将信息发送给它们
send_message(client, message)
4.2. 订阅与发布97
Redis 设计与实现, 第一版
但是，这段伪代码并没有完整描述PUBLISH 命令的行为，因为PUBLISH 除了将message 发
送到所有订阅channel 的客户端之外，它还会将channel 和pubsub_patterns 中的模式进行
对比，如果channel 和某个模式匹配的话，那么也将message 发送到订阅那个模式的客户端。
完整描述PUBLISH 功能的伪代码定于如下：
def PUBLISH(channel, message):
# 遍历所有订阅频道channel 的客户端
for client in server.pubsub_channels[channel]:
# 将信息发送给它们
send_message(client, message)
# 取出所有模式，以及订阅模式的客户端
for pattern, client in server.pubsub_patterns:
# 如果channel 和模式匹配
if match(channel, pattern):
# 那么也将信息发给订阅这个模式的客户端
send_message(client, message)
举个例子，如果Redis 服务器的pubsub_patterns 状态如下：
redisServer
...
pubsub_patterns
...
pubsubPattern
client
client123
pattern
tweet.shop.*
pubsubPattern
client
client256
pattern
tweet.shop.*
pubsubPattern
client
client10086
pattern
broadcast.live.*
那么当某个客户端发送信息"Amazon Kindle, $69." 到tweet.shop.kindle 频道时，除了所
有订阅了tweet.shop.kindle 频道的客户端会收到信息之外，客户端client123 和client256
也同样会收到信息，因为这两个客户端订阅的tweet.shop.* 模式和tweet.shop.kindle 频道
匹配。
4.2.8 退订模式
使用PUNSUBSCRIBE 命令可以退订指定的模式，这个命令执行的是订阅模式的反操作：程序
会删除redisServer.pubsub_patterns 链表中，所有和被退订模式相关联的pubsubPattern
结构，这样客户端就不会再收到和模式相匹配的频道发来的信息。
4.2.9 小结
. 订阅信息由服务器进程维持的redisServer.pubsub_channels 字典保存，字典的键为被
订阅的频道，字典的值为订阅频道的所有客户端。
. 当有新消息发送到频道时，程序遍历频道（键）所对应的（值）所有客户端，然后将消息
发送到所有订阅频道的客户端上。
98 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
. 订阅模式的信息由服务器进程维持的redisServer.pubsub_patterns 链表保存，链表的
每个节点都保存着一个pubsubPattern 结构，结构中保存着被订阅的模式，以及订阅该
模式的客户端。程序通过遍历链表来查找某个频道是否和某个模式匹配。
. 当有新消息发送到频道时，除了订阅频道的客户端会收到消息之外，所有订阅了匹配频
道的模式的客户端，也同样会收到消息。
. 退订频道和退订模式分别是订阅频道和订阅模式的反操作。
4.3 Lua 脚本
Lua 脚本功能是Reids 2.6 版本的最大亮点，通过内嵌对Lua 环境的支持，Redis 解决了长久
以来不能高效地处理CAS （check-and-set）命令的缺点，并且可以通过组合使用多个命令，轻
松实现以前很难实现或者不能高效实现的模式。
本章先介绍Lua 环境的初始化步骤，然后对Lua 脚本的安全性问题、以及解决这些问题的方
法进行说明，最后对执行Lua 脚本的两个命令——EVAL 和EVALSHA 的实现原理进行介绍。
4.3.1 初始化Lua 环境
在初始化Redis 服务器时，对Lua 环境的初始化也会一并进行。
为了让Lua 环境符合Redis 脚本功能的需求，Redis 对Lua 环境进行了一系列的修改，包括添
加函数库、更换随机函数、保护全局变量，等等。
整个初始化Lua 环境的步骤如下：
1. 调用lua_open 函数，创建一个新的Lua 环境。
2. 载入指定的Lua 函数库，包括：
. 基础库（base lib）。
. 表格库（table lib）。
. 字符串库（string lib）。
. 数学库（math lib）。
. 调试库（debug lib）。
. 用于处理JSON 对象的cjson 库。
. 在Lua 值和C 结构（struct） 之间进行转换的struct 库（www.inf.pucrio.
br/ roberto/struct/）
. 处理MessagePack 数据的cmsgpack 库（github.com/antirez/lua-cmsgpack）。
3. 屏蔽一些可能对Lua 环境产生安全问题的函数，比如loadfile 。
4. 创建一个Redis 字典，保存Lua 脚本，并在复制（replication）脚本时使用。字典的键为
SHA1 校验和，字典的值为Lua 脚本。
5. 创建一个redis 全局表格到Lua 环境，表格中包含了各种对Redis 进行操作的函数，包
括：
. 用于执行Redis 命令的redis.call 和redis.pcall 函数。
. 用于发送日志（log）的redis.log 函数，以及相应的日志级别（level）：
4.3. Lua 脚本99
Redis 设计与实现, 第一版
– redis.LOG_DEBUG
– redis.LOG_VERBOSE
– redis.LOG_NOTICE
– redis.LOG_WARNING
. 用于计算SHA1 校验和的redis.sha1hex 函数。
. 用于返回错误信息的redis.error_reply 函数和redis.status_reply 函数。
6. 用Redis 自己定义的随机生成函数， 替换math 表原有的math.random 函数和
math.randomseed 函数，新的函数具有这样的性质：每次执行Lua 脚本时，除非显
式地调用math.randomseed ，否则math.random 生成的伪随机数序列总是相同的。
7. 创建一个对Redis 多批量回复（multi bulk reply）进行排序的辅助函数。
8. 对Lua 环境中的全局变量进行保护，以免被传入的脚本修改。
9. 因为Redis 命令必须通过客户端来执行，所以需要在服务器状态中创建一个无网络连接
的伪客户端（fake client），专门用于执行Lua 脚本中包含的Redis 命令：当Lua 脚本需
要执行Redis 命令时，它通过伪客户端来向服务器发送命令请求，服务器在执行完命令
之后，将结果返回给伪客户端，而伪客户端又转而将命令结果返回给Lua 脚本。
10. 将Lua 环境的指针记录到Redis 服务器的全局状态中，等候Redis 的调用。
以上就是Redis 初始化Lua 环境的整个过程，当这些步骤都执行完之后，Redis 就可以使用
Lua 环境来处理脚本了。
严格来说，步骤1 至8 才是初始化Lua 环境的操作，而步骤9 和10 则是将Lua 环境关联到
服务器的操作，为了按顺序观察整个初始化过程，我们将两种操作放在了一起。
另外，步骤6 用于创建无副作用的脚本，而步骤7 则用于去除部分Redis 命令中的不确定性
（non deterministic），关于这两点，请看下面一节关于脚本安全性的讨论。
4.3.2 脚本的安全性
当将Lua 脚本复制到附属节点，或者将Lua 脚本写入AOF 文件时，Redis 需要解决这样一个
问题：如果一段Lua 脚本带有随机性质或副作用，那么当这段脚本在附属节点运行时，或者从
AOF 文件载入重新运行时，它得到的结果可能和之前运行的结果完全不同。
考虑以下一段代码，其中的get_random_number() 带有随机性质，我们在服务器SERVER 中
执行这段代码，并将随机数的结果保存到键number 上：
# 虚构例子，不会真的出现在脚本环境中
redis> EVAL "return redis.call('set', KEYS[1], get_random_number())" 1 number
OK
redis> GET number
"10086"
现在，假如EVAL 的代码被复制到了附属节点SLAVE ，因为get_random_number() 的随机
性质，它有很大可能会生成一个和10086 完全不同的值，比如65535 ：
# 虚构例子，不会真的出现在脚本环境中
redis> EVAL "return redis.call('set', KEYS[1], get_random_number())" 1 number
100 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
OK
redis> GET number
"65535"
可以看到，带有随机性的写入脚本产生了一个严重的问题：它破坏了服务器和附属节点数据之
间的一致性。
当从AOF 文件中载入带有随机性质的写入脚本时，也会发生同样的问题。
Note: 只有在带有随机性的脚本进行写入时，随机性才是有害的。
如果一个脚本只是执行只读操作，那么随机性是无害的。
比如说， 如果脚本只是单纯地执行RANDOMKEY 命令， 那么它是无害的； 但如果在执行
RANDOMKEY 之后，基于RANDOMKEY 的结果进行写入操作，那么这个脚本就是有害的。
和随机性质类似，如果一个脚本的执行对任何副作用产生了依赖，那么这个脚本每次执行所产
生的结果都可能会不一样。
为了解决这个问题，Redis 对Lua 环境所能执行的脚本做了一个严格的限制——所有脚本都必
须是无副作用的纯函数（pure function）。
为此，Redis 对Lua 环境做了一些列相应的措施：
. 不提供访问系统状态状态的库（比如系统时间库）。
. 禁止使用loadfile 函数。
. 如果脚本在执行带有随机性质的命令（比如RANDOMKEY ），或者带有副作用的命令
（比如TIME ）之后，试图执行一个写入命令（比如SET ），那么Redis 将阻止这个脚本
继续运行，并返回一个错误。
. 如果脚本执行了带有随机性质的读命令（比如SMEMBERS ），那么在脚本的输出返回给
Redis 之前，会先被执行一个自动的字典序排序，从而确保输出结果是有序的。
. 用Redis 自己定义的随机生成函数，替换Lua 环境中math 表原有的math.random 函数
和math.randomseed 函数，新的函数具有这样的性质：每次执行Lua 脚本时，除非显式
地调用math.randomseed ，否则math.random 生成的伪随机数序列总是相同的。
经过这一系列的调整之后，Redis 可以保证被执行的脚本：
1. 无副作用。
2. 没有有害的随机性。
3. 对于同样的输入参数和数据集，总是产生相同的写入命令。
4.3.3 脚本的执行
在脚本环境的初始化工作完成以后，Redis 就可以通过EVAL 命令或EVALSHA 命令执行Lua
脚本了。
其中，EVAL 直接对输入的脚本代码体（body）进行求值：
redis> EVAL "return 'hello world'" 0
"hello world"
4.3. Lua 脚本101
Redis 设计与实现, 第一版
而EVALSHA 则要求输入某个脚本的SHA1 校验和，这个校验和所对应的脚本必须至少被
EVAL 执行过一次：
redis> EVAL "return 'hello world'" 0
"hello world"
redis> EVALSHA 5332031c6b470dc5a0dd9b4bf2030dea6d65de91 0 // 上一个脚本的校验和
"hello world"
或者曾经使用SCRIPT LOAD 载入过这个脚本：
redis> SCRIPT LOAD "return 'dlrow olleh'"
"d569c48906b1f4fca0469ba4eee89149b5148092"
redis> EVALSHA d569c48906b1f4fca0469ba4eee89149b5148092 0
"dlrow olleh"
因为EVALSHA 是基于EVAL 构建的，所以下文先用一节讲解EVAL 的实现，之后再讲解
EVALSHA 的实现。
4.3.4 EVAL 命令的实现
EVAL 命令的执行可以分为以下步骤：
1. 为输入脚本定义一个Lua 函数。
2. 执行这个Lua 函数。
以下两个小节分别介绍这两个步骤。
定义Lua 函数
所有被Redis 执行的Lua 脚本，在Lua 环境中都会有一个和该脚本相对应的无参数函数：当
调用EVAL 命令执行脚本时，程序第一步要完成的工作就是为传入的脚本创建一个相应的Lua
函数。
举个例子，当执行命令EVAL "return 'hello world'" 0 时，Lua 会为脚本"return 'hello
world'" 创建以下函数：
function f_5332031c6b470dc5a0dd9b4bf2030dea6d65de91()
return 'hello world'
end
其中，函数名以f_ 为前缀，后跟脚本的SHA1 校验和（一个40 个字符长的字符串）拼接而
成。而函数体（body）则是用户输入的脚本。
以函数为单位保存Lua 脚本有以下好处：
. 执行脚本的步骤非常简单，只要调用和脚本相对应的函数即可。
. Lua 环境可以保持清洁，已有的脚本和新加入的脚本不会互相干扰，也可以将重置Lua
环境和调用Lua GC 的次数降到最低。
. 如果某个脚本所对应的函数在Lua 环境中被定义过至少一次，那么只要记得这个脚本的
SHA1 校验和，就可以直接执行该脚本——这是实现EVALSHA 命令的基础，稍后在介
绍EVALSHA 的时候就会说到这一点。
102 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
在为脚本创建函数前，程序会先用函数名检查Lua 环境，只有在函数定义未存在时，程序才创
建函数。重复定义函数一般并没有什么副作用，这算是一个小优化。
另外，如果定义的函数在编译过程中出错（比如，脚本的代码语法有错），那么程序向用户返回
一个脚本错误，不再执行后面的步骤。
执行Lua 函数
在定义好Lua 函数之后，程序就可以通过运行这个函数来达到运行输入脚本的目的了。
不过，在此之前，为了确保脚本的正确和安全执行，还需要执行一些设置钩子、传入参数之类
的操作，整个执行函数的过程如下：
1. 将EVAL 命令中输入的KEYS 参数和ARGV 参数以全局数组的方式传入到Lua 环境中。
2. 设置伪客户端的目标数据库为调用者客户端的目标数据库： fake_client->db =
caller_client->db ，确保脚本中执行的Redis 命令访问的是正确的数据库。
3. 为Lua 环境装载超时钩子，保证在脚本执行出现超时时可以杀死脚本，或者停止Redis
服务器。
4. 执行脚本对应的Lua 函数。
5. 如果被执行的Lua 脚本中带有SELECT 命令，那么在脚本执行完毕之后，伪客户端
中的数据库可能已经有所改变，所以需要对调用者客户端的目标数据库进行更新：
caller_client->db = fake_client->db 。
6. 执行清理操作：清除钩子；清除指向调用者客户端的指针；等等。
7. 将Lua 函数执行所得的结果转换成Redis 回复，然后传给调用者客户端。
8. 对Lua 环境进行一次单步的渐进式GC 。
以下是执行EVAL "return 'hello world'" 0 的过程中，调用者客户端（caller）、Redis 服务
器和Lua 环境之间的数据流表示图：
发送命令请求
EVAL "return 'hello world'" 0
Caller ----------------------------------------> Redis
为脚本"return 'hello world'"
创建Lua 函数
Redis ----------------------------------------> Lua
绑定超时处理钩子
Redis ----------------------------------------> Lua
执行脚本函数
Redis ----------------------------------------> Lua
返回函数执行结果（一个Lua 值）
Redis <---------------------------------------- Lua
将Lua 值转换为Redis 回复
并将结果返回给客户端
Caller <---------------------------------------- Redis
4.3. Lua 脚本103
Redis 设计与实现, 第一版
上面这个图可以作为所有Lua 脚本的基本执行流程图，不过它展示的Lua 脚本中不带有Redis
命令调用：当Lua 脚本里本身有调用Redis 命令时（执行redis.call 或者redis.pcall ），
Redis 和Lua 脚本之间的数据交互会更复杂一些。
举个例子，以下是执行命令EVAL "return redis.call('DBSIZE')" 0 时，调用者客户端
（caller）、伪客户端（fake client）、Redis 服务器和Lua 环境之间的数据流表示图：
发送命令请求
EVAL "return redis.call('DBSIZE')" 0
Caller ------------------------------------------> Redis
为脚本"return redis.call('DBSIZE')"
创建Lua 函数
Redis ------------------------------------------> Lua
绑定超时处理钩子
Redis ------------------------------------------> Lua
执行脚本函数
Redis ------------------------------------------> Lua
执行redis.call('DBSIZE')
Fake Client <------------------------------------- Lua
伪客户端向服务器发送
DBSIZE 命令请求
Fake Client -------------------------------------> Redis
服务器将DBSIZE 的结果
（Redis 回复）返回给伪客户端
Fake Client <------------------------------------- Redis
将命令回复转换为Lua 值
并返回给Lua 环境
Fake Client -------------------------------------> Lua
返回函数执行结果（一个Lua 值）
Redis <------------------------------------------ Lua
将Lua 值转换为Redis 回复
并将该回复返回给客户端
Caller <------------------------------------------ Redis
因为EVAL "return redis.call('DBSIZE')" 只是简单地调用了一次DBSIZE 命令，所以Lua
和伪客户端只进行了一趟交互，当脚本中的redis.call 或者redis.pcall 次数增多时，Lua
和伪客户端的交互趟数也会相应地增多，不过总体的交互方法和上图展示的一样。
4.3.5 EVALSHA 命令的实现
前面介绍EVAL 命令的实现时说过，每个被执行过的Lua 脚本，在Lua 环境中都有一个
和它相对应的函数，函数的名字由f_ 前缀加上40 个字符长的SHA1 校验和构成：比如
f_5332031c6b470dc5a0dd9b4bf2030dea6d65de91 。
只要脚本所对应的函数曾经在Lua 里面定义过，那么即使用户不知道脚本的内容本身，也可以
直接通过脚本的SHA1 校验和来调用脚本所对应的函数，从而达到执行脚本的目的——这就是
EVALSHA 命令的实现原理。
104 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
可以用伪代码来描述这一原理：
def EVALSHA(sha1):
# 拼接出Lua 函数名字
func_name = "f_" + sha1
# 查看该函数是否已经在Lua 中定义
if function_defined_in_lua(func_name):
# 如果已经定义过的话，执行函数
return exec_lua_function(func_name)
else:
# 没有找到和输入SHA1 值相对应的函数则返回一个脚本未找到错误
return script_error("SCRIPT NOT FOUND")
除了执行EVAL 命令之外，SCRIPT LOAD 命令也可以为脚本在Lua 环境中创建函数：
redis> SCRIPT LOAD "return 'hello world'"
"5332031c6b470dc5a0dd9b4bf2030dea6d65de91"
redis> EVALSHA 5332031c6b470dc5a0dd9b4bf2030dea6d65de91 0
"hello world"
SCRIPT LOAD 执行的操作和前面《定义Lua 函数》小节描述的一样。
4.3.6 小结
. 初始化Lua 脚本环境需要一系列步骤，其中最重要的包括：
– 创建Lua 环境。
– 载入Lua 库，比如字符串库、数学库、表格库，等等。
– 创建redis 全局表格，包含各种对Redis 进行操作的函数，比如redis.call 和
redis.log ，等等。
– 创建一个无网络连接的伪客户端，专门用于执行Lua 脚本中的Redis 命令。
. Reids 通过一系列措施保证被执行的Lua 脚本无副作用，也没有有害的写随机性：对于
同样的输入参数和数据集，总是产生相同的写入命令。
. EVAL 命令为输入脚本定义一个Lua 函数，然后通过执行这个函数来执行脚本。
. EVALSHA 通过构建函数名，直接调用Lua 中已定义的函数，从而执行相应的脚本。
4.4 慢查询日志
慢查询日志是Redis 提供的一个用于观察系统性能的功能，这个功能的实现非常简单，这里我
们也简单地讲解一下。
本章先介绍和慢查询功能相关的数据结构和变量，然后介绍Redis 是如何记录命令的执行时
间，以及如何为执行超过限制事件的命令记录慢查询日志的。
4.4. 慢查询日志105
Redis 设计与实现, 第一版
4.4.1 相关数据结构
每条慢查询日志都以一个slowlog.h/slowlogEntry 结构定义：
typedef struct slowlogEntry {
// 命令参数
robj **argv;
// 命令参数数量
int argc;
// 唯一标识符
long long id; /* Unique entry identifier. */
// 执行命令消耗的时间，以纳秒（1 / 1,000,000,000 秒）为单位
long long duration; /* Time spent by the query, in nanoseconds. */
// 命令执行时的时间
time_t time; /* Unix time at which the query was executed. */
} slowlogEntry;
记录服务器状态的redis.h/redisServer 结构里保存了几个和慢查询有关的属性：
struct redisServer {
// ... other fields
// 保存慢查询日志的链表
list *slowlog; /* SLOWLOG list of commands */
// 慢查询日志的当前id 值
long long slowlog_entry_id; /* SLOWLOG current entry ID */
// 慢查询时间限制
long long slowlog_log_slower_than; /* SLOWLOG time limit (to get logged) */
// 慢查询日志的最大条目数量
unsigned long slowlog_max_len; /* SLOWLOG max number of items logged */
// ... other fields
};
slowlog 属性是一个链表，链表里的每个节点保存了一个慢查询日志结构，所有日志按添加时
间从新到旧排序，新的日志在链表的左端，旧的日志在链表的右端。
slowlog_entry_id 在创建每条新的慢查询日志时增一，用于产生慢查询日志的ID （这个ID
在执行SLOWLOG RESET 之后会被重置）。
slowlog_log_slower_than 是用户指定的命令执行时间上限，执行时间大于等于这个值的命令
会被慢查询日志记录。
slowlog_max_len 慢查询日志的最大数量，当日志数量等于这个值时，添加一条新日志会造成
最旧的一条日志被删除。
下图展示了一个slowlog 属性的实例：
106 Chapter 4. 功能的实现
Redis 设计与实现, 第一版
redisServer
...
slowlog
...
slowlogEntry
argv
argc
id
duration
time
slowlogEntry
argv
argc
id
duration
time
slowlogEntry
argv
argc
id
duration
time
4.4.2 慢查询日志的记录
在每次执行命令之前，Redis 都会用一个参数记录命令执行前的时间，在命令执行完之后，再
计算一次当前时间，然后将两个时间值相减，得出执行命令所耗费的时间值duration ，并将
duration 传给slowlogPushEntryIfNeed 函数。
如果duration 超过服务器设置的执行时间上限server.slowlog_log_slower_than 的话，
slowlogPushEntryIfNeed 就会创建一条新的慢查询日志，并将它加入到慢查询日志链表里。
可以用一段伪代码来表示这个过程：
def execute_redis_command_with_slowlog():
# 命令执行前的时间
start = ustime()
# 执行命令
execute_command(argv, argc)
# 计算命令执行所耗费的时间
duration = ustime() - start
if slowlog_is_enabled:
slowlogPushEntryIfNeed(argv, argc, duration)
def slowlogPushEntryIfNeed(argv, argc, duration)
# 如果执行命令耗费的时间超过服务器设置命令执行时间上限
# 那么创建一条新的slowlog
if duration > server.slowlog_log_slower_than:
# 创建新slowlog
log = new slowlogEntry()
# 设置各个域
log.argv = argv
log.argc = argc
log.duration = duration
log.id = server.slowlog_entry_id
log.time = now()
4.4. 慢查询日志107
Redis 设计与实现, 第一版
# 将新slowlog 追加到日志链表末尾
server.slowlog.append(log)
# 更新服务器slowlog
server.slowlog_entry_id += 1
4.4.3 慢查询日志的操作
针对慢查询日志有三种操作，分别是查看、清空和获取日志数量：
. 查看日志：在日志链表中遍历指定数量的日志节点，复杂度为O(N) 。
. 清空日志：释放日志链表中的所有日志节点，复杂度为O(N) 。
. 获取日志数量：获取日志的数量等同于获取server.slowlog 链表的数量，复杂度为
O(1) 。
4.4.4 小结
. Redis 用一个链表以FIFO 的顺序保存着所有慢查询日志。
. 每条慢查询日志以一个慢查询节点表示，节点中记录着执行超时的命令、命令的参数、命
令执行时的时间，以及执行命令所消耗的时间等信息。
108 Chapter 4. 功能的实现
第5 章
内部运作机制
以下章节将对Redis 最底层也最隐蔽的模块进行探讨：
. Redis 如何表示一个数据库？数据库操作是如何实现的？
. Redis 如何进行持久化？RDB 模式和AOF 模式有什么区别？
. Redis 如何处理输入命令？它又是如何将输出返回给客户端的？
. Redis 服务器如何初始化？传入服务器的命令又是以什么方法执行的？
以上的这些问题，都是这一部分要解答的。
5.1 数据库
本章将对Redis 数据库的构造和实现进行讨论。
除了说明数据库是如何储存数据对象之外，本章还会讨论键的过期信息是如何保存，而Redis
又是如何删除过期键的。
5.1.1 数据库的结构
Redis 中的每个数据库，都由一个redis.h/redisDb 结构表示：
typedef struct redisDb {
// 保存着数据库以整数表示的号码
int id;
// 保存着数据库中的所有键值对数据
// 这个属性也被称为键空间（key space）
dict *dict;
// 保存着键的过期信息
109
Redis 设计与实现, 第一版
dict *expires;
// 实现列表阻塞原语，如BLPOP
// 在列表类型一章有详细的讨论
dict *blocking_keys;
dict *ready_keys;
// 用于实现WATCH 命令
// 在事务章节有详细的讨论
dict *watched_keys;
} redisDb;
下文将详细讨论id 、dict 和expires 三个属性，以及针对这三个属性所执行的数据库操作。
5.1.2 数据库的切换
redisDb 结构的id 域保存着数据库的号码。
这个号码很容易让人将它和切换数据库的SELECT 命令联系在一起，但是，实际上，id 属性
并不是用来实现SELECT 命令，而是给Redis 内部程序使用的。
当Redis 服务器初始化时， 它会创建出redis.h/REDIS_DEFAULT_DBNUM 个数据库， 并
将所有数据库保存到redis.h/redisServer.db 数组中， 每个数据库的id 为从0 到
REDIS_DEFAULT_DBNUM - 1 的值。
当执行SELECT number 命令时，程序直接使用redisServer.db[number] 来切换数据库。
但是，一些内部程序，比如AOF 程序、复制程序和RDB 程序，需要知道当前数据库的号码，
如果没有id 域的话，程序就只能在当前使用的数据库的指针，和redisServer.db 数组中所
有数据库的指针进行对比，以此来弄清楚自己正在使用的是那个数据库。
以下伪代码描述了这个对比过程：
def PSEUDO_GET_CURRENT_DB_NUMBER(current_db_pointer):
i = 0
for db_pointer in redisServer.db:
if db_pointer == current_db_pointer:
break
i += 1
return i
有了id 域的话，程序就可以通过读取id 域来了解自己正在使用的是哪个数据库，这样就不用
对比指针那么麻烦了。
5.1.3 数据库键空间
因为Redis 是一个键值对数据库（key-value pairs database），所以它的数据库本身也是一个字
典（俗称key space）：
. 字典的键是一个字符串对象。
. 字典的值则可以是包括字符串、列表、哈希表、集合或有序集在内的任意一种Redis 类型
对象。
在redisDb 结构的dict 属性中，保存着数据库的所有键值对数据。
110 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
下图展示了一个包含number 、book 、message 三个键的数据库——其中number 键是一个列
表，列表中包含三个整数值；book 键是一个哈希表，表中包含三个键值对；而message 键则
指向另一个字符串：
redisDb
id
dict
...
dict
(key space)
StringObject
"number"
NULL
StringObject
"book"
StringObject
"message"
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"hello moto"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
5.1.4 键空间的操作
因为数据库本身是一个字典，所以对数据库的操作基本上都是对字典的操作，加上以下一些维
护操作：
. 更新键的命中率和不命中率，这个值可以用INFO 命令查看；
. 更新键的LRU 时间，这个值可以用OBJECT 命令来查看；
. 删除过期键（稍后会详细说明）；
. 如果键被修改了的话，那么将键设为脏（用于事务监视），并将服务器设为脏（等待RDB
保存）；
. 将对键的修改发送到AOF 文件和附属节点，保持数据库状态的一致；
作为例子，以下几个小节会展示键的添加、删除、更新、取值等几个主要操作。
添加新键
添加一个新键对到数据库，实际上就是将一个新的键值对添加到键空间字典中，其中键为字符
串对象，而值则是任意一种Redis 类型值对象。
举个例子，如果数据库的目前状态如下图所示（和前面展示的数据库状态图一样）：
5.1. 数据库111
Redis 设计与实现, 第一版
redisDb
id
dict
...
dict
(key space)
StringObject
"number"
NULL
StringObject
"book"
StringObject
"message"
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"hello moto"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
那么在客户端执行SET date 2013.2.1 命令之后，数据库更新为下图状态：
redisDb
id
dict
...
dict
(key space)
StringObject
"number"
StringObject
"date"
StringObject
"book"
StringObject
"message"
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"hello moto"
StringObject
"2013.2.1"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
删除键
删除数据库中的一个键，实际上就是删除字典空间中对应的键对象和值对象。
112 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
举个例子，如果数据库的目前状态如下图所示（和前面展示的数据库状态图一样）：
redisDb
id
dict
...
dict
(key space)
StringObject
"number"
NULL
StringObject
"book"
StringObject
"message"
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"hello moto"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
那么在客户端执行DEL message 命令之后，数据库更新为下图状态：
redisDb
id
dict
...
dict
(key space)
StringObject
"number"
NULL
StringObject
"book"
NULL
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
更新键
当对一个已存在于数据库的键执行更新操作时，数据库释放键原来的值对象，然后将指针指向
新的值对象。
5.1. 数据库113
Redis 设计与实现, 第一版
举个例子，如果数据库的目前状态如下图所示（和前面展示的数据库状态图一样）：
redisDb
id
dict
...
dict
(key space)
StringObject
"number"
NULL
StringObject
"book"
StringObject
"message"
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"hello moto"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
那么在客户端执行SET message "blah blah" 命令之后，数据库更新为下图状态：
redisDb
id
dict
...
dict
(key space)
StringObject
"number"
NULL
StringObject
"book"
StringObject
"message"
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"blah blah"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
取值
在数据库中取值实际上就是在字典空间中取值，再加上一些额外的类型检查：
114 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
. 键不存在，返回空回复；
. 键存在，且类型正确，按照通讯协议返回值对象；
. 键存在，但类型不正确，返回类型错误。
举个例子，如果数据库的目前状态如下图所示（和前面展示的数据库状态图一样）：
redisDb
id
dict
...
dict
(key space)
StringObject
"number"
NULL
StringObject
"book"
StringObject
"message"
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"hello moto"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
当客户端执行GET message 时，服务器返回"hello moto" 。
当客户端执行GET not-exists-key 时，服务器返回空回复。
当服务器执行GET book 时，服务器返回类型错误。
其他操作
除了上面展示的键值操作之外，还有很多针对数据库本身的命令，也是通过对键空间进行处理
来完成的：
. FLUSHDB 命令：删除键空间中的所有键值对。
. RANDOMKEY 命令：从键空间中随机返回一个键。
. DBSIZE 命令：返回键空间中键值对的数量。
. EXISTS 命令：检查给定键是否存在于键空间中。
. RENAME 命令：在键空间中，对给定键进行改名。
等等。
5.1. 数据库115
Redis 设计与实现, 第一版
5.1.5 键的过期时间
在前面的内容中，我们讨论了很多涉及数据库本身、以及对数据库中的键值对进行处理的操作，
但是，关于数据库如何保存键的过期时间，以及如何处理过期键这一问题，我们还没有讨论到。
通过EXPIRE 、PEXPIRE 、EXPIREAT 和PEXPIREAT 四个命令，客户端可以给某个存
在的键设置过期时间，当键的过期时间到达时，键就不再可用：
redis> SETEX key 5 value
OK
redis> GET key
"value"
redis> GET key // 5 秒过后
(nil)
命令TTL 和PTTL 则用于返回给定键距离过期还有多长时间：
redis> SETEX key 10086 value
OK
redis> TTL key
(integer) 10082
redis> PTTL key
(integer) 10068998
在接下来的内容中，我们将探讨和键的过期时间相关的问题：比如键的过期时间是如何保存
的，而过期键又是如何被删除的，等等。
5.1.6 过期时间的保存
在数据库中，所有键的过期时间都被保存在redisDb 结构的expires 字典里：
typedef struct redisDb {
// ...
dict *expires;
// ...
} redisDb;
expires 字典的键是一个指向dict 字典（键空间）里某个键的指针，而字典的值则是键所指
向的数据库键的到期时间，这个值以long long 类型表示。
下图展示了一个含有三个键的数据库，其中number 和book 两个键带有过期时间：
116 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
redisDb
id
dict
expires
...
dict
(key space)
StringObject
"number"
NULL
StringObject
"book"
StringObject
"message"
dict
StringObject
"number"
NULL
StringObject
"book"
NULL
ListObject
123 456 789
HashObject
StringObject
"name"
StringObject
"author"
StringObject
"publisher"
StringObject
"hello moto"
StringObject
"Mastering C++ in 21 days"
StringObject
"Someone"
StringObject
"Oh-Really? Publisher"
long long
1360454400000
long long
1360800000000
Note: 为了展示的方便，图中重复出现了两次number 键和book 键。在实际中，键空间字典
的键和过期时间字典的键都指向同一个字符串对象，所以不会浪费任何空间。
5.1.7 设置生存时间
Redis 有四个命令可以设置键的生存时间（可以存活多久）和过期时间（什么时候到期）：
. EXPIRE 以秒为单位设置键的生存时间；
. PEXPIRE 以毫秒为单位设置键的生存时间；
. EXPIREAT 以秒为单位，设置键的过期UNIX 时间戳；
. PEXPIREAT 以毫秒为单位，设置键的过期UNIX 时间戳。
虽然有那么多种不同单位和不同形式的设置方式，但是expires 字典的值只保存“以毫秒为单
位的过期UNIX 时间戳” ，这就是说，通过进行转换，所有命令的效果最后都和PEXPIREAT
命令的效果一样。
举个例子，从EXPIRE 命令到PEXPIREAT 命令的转换可以用伪代码表示如下：
def EXPIRE(key, sec):
# 将TTL 从秒转换为毫秒
ms = sec_to_ms(sec)
# 获取以毫秒计算的当前UNIX 时间戳
ts_in_ms = get_current_unix_timestamp_in_ms()
5.1. 数据库117
Redis 设计与实现, 第一版
# 毫秒TTL 加上毫秒时间戳，就是key 到期的时间戳
PEXPIREAT(ms + ts_in_ms, key)
其他函数的转换方式也是类似的。
作为例子，下图展示了一个expires 字典示例，字典中number 键的过期时间是2013 年2 月
10 日（农历新年），而book 键的过期时间则是2013 年2 月14 日（情人节）：
redisDb
id
...
expires
...
dict
StringObject
"number"
NULL
StringObject
"book"
NULL
long long
1360454400000
long long
1360800000000
这两个键的过期时间可能是用以上四个命令的任意一个设置的，但它们都以统一的格式被保存
在expires 字典中。
5.1.8 过期键的判定
通过expires 字典，可以用以下步骤检查某个键是否过期：
1. 检查键是否存在于expires 字典：如果存在，那么取出键的过期时间；
2. 检查当前UNIX 时间戳是否大于键的过期时间：如果是的话，那么键已经过期；否则，
键未过期。
可以用伪代码来描述这一过程：
def is_expired(key):
# 取出键的过期时间
key_expire_time = expires.get(key)
# 如果过期时间不为空，并且当前时间戳大于过期时间，那么键已经过期
if expire_time is not None and current_timestamp() > key_expire_time:
return True
118 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
# 否则，键未过期或没有设置过期时间
return False
5.1.9 过期键的清除
我们知道了过期时间保存在expires 字典里，又知道了该如何判定一个键是否过期，现在剩下
的问题是，如果一个键是过期的，那它什么时候会被删除？
这个问题有三种可能的答案：
1. 定时删除：在设置键的过期时间时，创建一个定时事件，当过期时间到达时，由事件处理
器自动执行键的删除操作。
2. 惰性删除：放任键过期不管，但是在每次从dict 字典中取出键值时，要检查键是否过
期，如果过期的话，就删除它，并返回空；如果没过期，就返回键值。
3. 定期删除：每隔一段时间，对expires 字典进行检查，删除里面的过期键。
定时删除
定时删除策略对内存是最友好的：因为它保证过期键会在第一时间被删除，过期键所消耗的内
存会立即被释放。
这种策略的缺点是，它对CPU 时间是最不友好的：因为删除操作可能会占用大量的CPU 时间
——在内存不紧张、但是CPU 时间非常紧张的时候（比如说，进行交集计算或排序的时候），
将CPU 时间花在删除那些和当前任务无关的过期键上，这种做法毫无疑问会是低效的。
除此之外，目前Redis 事件处理器对时间事件的实现方式——无序链表，查找一个时间复杂度
为O(N) ——并不适合用来处理大量时间事件。
惰性删除
惰性删除对CPU 时间来说是最友好的：它只会在取出键时进行检查，这可以保证删除操作只
会在非做不可的情况下进行——并且删除的目标仅限于当前处理的键，这个策略不会在删除其
他无关的过期键上花费任何CPU 时间。
惰性删除的缺点是，它对内存是最不友好的：如果一个键已经过期，而这个键又仍然保留在数
据库中，那么dict 字典和expires 字典都需要继续保存这个键的信息，只要这个过期键不被
删除，它占用的内存就不会被释放。
在使用惰性删除策略时，如果数据库中有非常多的过期键，但这些过期键又正好没有被访问的
话，那么它们就永远也不会被删除（除非用户手动执行），这对于性能非常依赖于内存大小的
Redis 来说，肯定不是一个好消息。
举个例子，对于一些按时间点来更新的数据，比如日志（log），在某个时间点之后，对它们的访
问就会大大减少，如果大量的这些过期数据积压在数据库里面，用户以为它们已经过期了（已
经被删除了），但实际上这些键却没有真正的被删除（内存也没有被释放），那结果肯定是非常
糟糕。
定期删除
从上面对定时删除和惰性删除的讨论来看，这两种删除方式在单一使用时都有明显的缺陷：定
时删除占用太多CPU 时间，惰性删除浪费太多内存。
5.1. 数据库119
Redis 设计与实现, 第一版
定期删除是这两种策略的一种折中：
. 它每隔一段时间执行一次删除操作，并通过限制删除操作执行的时长和频率，籍此来减
少删除操作对CPU 时间的影响。
. 另一方面，通过定期删除过期键，它有效地减少了因惰性删除而带来的内存浪费。
Redis 使用的策略
Redis 使用的过期键删除策略是惰性删除加上定期删除，这两个策略相互配合，可以很好地在
合理利用CPU 时间和节约内存空间之间取得平衡。
因为前面已经说了这两个策略的概念了，下面两节就来探讨这两个策略在Redis 中的具体实现。
5.1.10 过期键的惰性删除策略
实现过期键惰性删除策略的核心是db.c/expireIfNeeded 函数——所有命令在读取或写入数
据库之前，程序都会调用expireIfNeeded 对输入键进行检查，并将过期键删除：
SET 、
LPUSH 、
SADD 、
等等
调用expire_if_needed()
删除过期键
写请求
GET 、
LRANGE 、
SMEMBERS 、
等等
读请求
执行实际的命令流程
比如说，GET 命令的执行流程可以用下图来表示：
120 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
GET key
调用
expire_if_needed()
如果键已经过期
那么将它删除
key 不存在
向客户端返回NIL
已过期
向客户端返回 key 的值
未过期
expireIfNeeded 的作用是，如果输入键已经过期的话，那么将键、键的值、键保存在expires
字典中的过期时间都删除掉。
用伪代码描述的expireIfNeeded 定义如下：
def expireIfNeeded(key):
# 对过期键执行以下操作。。。
if key.is_expired():
# 从键空间中删除键值对
db.dict.remove(key)
# 删除键的过期时间
db.expires.remove(key)
# 将删除命令传播到AOF 文件和附属节点
propagateDelKeyToAofAndReplication(key)
5.1. 数据库121
Redis 设计与实现, 第一版
5.1.11 过期键的定期删除策略
对过期键的定期删除由redis.c/activeExpireCycle 函执行：每当Redis 的例行处理程序
serverCron 执行时，activeExpireCycle 都会被调用——这个函数在规定的时间限制内，尽
可能地遍历各个数据库的expires 字典，随机地检查一部分键的过期时间，并删除其中的过期
键。
整个过程可以用伪代码描述如下：
def activeExpireCycle():
# 遍历数据库（不一定能全部都遍历完，看时间是否足够）
for db in server.db:
# MAX_KEY_PER_DB 是一个DB 最大能处理的key 个数
# 它保证时间不会全部用在个别的DB 上（避免饥饿）
i = 0
while (i < MAX_KEY_PER_DB):
# 数据库为空，跳出while ，处理下个DB
if db.is_empty(): break
# 随机取出一个带TTL 的键
key_with_ttl = db.expires.get_random_key()
# 检查键是否过期，如果是的话，将它删除
if is_expired(key_with_ttl):
db.deleteExpiredKey(key_with_ttl)
# 当执行时间到达上限，函数就返回，不再继续
# 这确保删除操作不会占用太多的CPU 时间
if reach_time_limit(): return
i += 1
5.1.12 过期键对AOF 、RDB 和复制的影响
前面的内容讨论了过期键对CPU 时间和内存的影响，现在，是时候说说过期键在RDB 文件、
AOF 文件、AOF 重写以及复制中的影响了：
过期键会被保存在更新后的RDB 文件、AOF 文件或者重写后的AOF 文件里面吗？
附属节点会会如何处理过期键？处理的方式和主节点一样吗？
以上这些问题就是本节要解答的。
更新后的RDB 文件
在创建新的RDB 文件时，程序会对键进行检查，过期的键不会被写入到更新后的RDB 文件
中。
因此，过期键对更新后的RDB 文件没有影响。
122 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
AOF 文件
在键已经过期，但是还没有被惰性删除或者定期删除之前，这个键不会产生任何影响，AOF 文
件也不会因为这个键而被修改。
当过期键被惰性删除、或者定期删除之后，程序会向AOF 文件追加一条DEL 命令，来显式地
记录该键已被删除。
举个例子，如果客户端使用GET message 试图访问message 键的值，但message 已经过期了，
那么服务器执行以下三个动作：
1. 从数据库中删除message ；
2. 追加一条DEL message 命令到AOF 文件；
3. 向客户端返回NIL 。
AOF 重写
和RDB 文件类似，当进行AOF 重写时，程序会对键进行检查，过期的键不会被保存到重写
后的AOF 文件。
因此，过期键对重写后的AOF 文件没有影响。
复制
当服务器带有附属节点时，过期键的删除由主节点统一控制：
. 如果服务器是主节点，那么它在删除一个过期键之后，会显式地向所有附属节点发送一
个DEL 命令。
. 如果服务器是附属节点，那么当它碰到一个过期键的时候，它会向程序返回键已过期的
回复，但并不真正的删除过期键。因为程序只根据键是否已经过期、而不是键是否已经被
删除来决定执行流程，所以这种处理并不影响命令的正确执行结果。当接到从主节点发来
的DEL 命令之后，附属节点才会真正的将过期键删除掉。
附属节点不自主对键进行删除是为了和主节点的数据保持绝对一致，因为这个原因，当一个过
期键还存在于主节点时，这个键在所有附属节点的副本也不会被删除。
这种处理机制对那些使用大量附属节点，并且带有大量过期键的应用来说，可能会造成一部分
内存不能立即被释放，但是，因为过期键通常很快会被主节点发现并删除，所以这实际上也算
不上什么大问题。
5.1.13 数据库空间的收缩和扩展
因为数据库空间是由字典来实现的，所以数据库空间的扩展/收缩规则和字典的扩展/收缩规则
完全一样，具体的信息可以参考《字典》章节。
因为对字典进行收缩的时机是由使用字典的程序决定的， 所以Redis 使用
redis.c/tryResizeHashTables 函数来检查数据库所使用的字典是否需要进行收缩：每次
redis.c/serverCron 函数运行的时候，这个函数都会被调用。
tryResizeHashTables 函数的完整定义如下：
5.1. 数据库123
Redis 设计与实现, 第一版
/*
* 对服务器中的所有数据库键空间字典、以及过期时间字典进行检查，
* 看是否需要对这些字典进行收缩。
**
如果字典的使用空间比率低于REDIS_HT_MINFILL
* 那么将字典的大小缩小，让USED/BUCKETS 的比率<= 1
*/
void tryResizeHashTables(void) {
int j;
for (j = 0; j < server.dbnum; j++) {
// 缩小键空间字典
if (htNeedsResize(server.db[j].dict))
dictResize(server.db[j].dict);
// 缩小过期时间字典
if (htNeedsResize(server.db[j].expires))
dictResize(server.db[j].expires);
}
}
5.1.14 小结
. 数据库主要由dict 和expires 两个字典构成，其中dict 保存键值对，而expires 则
保存键的过期时间。
. 数据库的键总是一个字符串对象，而值可以是任意一种Redis 数据类型，包括字符串、哈
希、集合、列表和有序集。
. expires 的某个键和dict 的某个键共同指向同一个字符串对象，而expires 键的值则
是该键以毫秒计算的UNIX 过期时间戳。
. Redis 使用惰性删除和定期删除两种策略来删除过期的键。
. 更新后的RDB 文件和重写后的AOF 文件都不会保留已经过期的键。
. 当一个过期键被删除之后，程序会追加一条新的DEL 命令到现有AOF 文件末尾。
. 当主节点删除一个过期键之后，它会显式地发送一条DEL 命令到所有附属节点。
. 附属节点即使发现过期键，也不会自作主张地删除它，而是等待主节点发来DEL 命令，
这样可以保证主节点和附属节点的数据总是一致的。
. 数据库的dict 字典和expires 字典的扩展策略和普通字典一样。它们的收缩策略是：当
节点的填充百分比不足10% 时，将可用节点数量减少至大于等于当前已用节点数量。
5.2 RDB
在运行情况下，Redis 以数据结构的形式将数据维持在内存中，为了让这些数据在Redis 重启
之后仍然可用，Redis 分别提供了RDB 和AOF 两种持久化模式。
在Redis 运行时，RDB 程序将当前内存中的数据库快照保存到磁盘文件中，在Redis 重启动
时，RDB 程序可以通过载入RDB 文件来还原数据库的状态。
124 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
RDB 功能最核心的是rdbSave 和rdbLoad 两个函数，前者用于生成RDB 文件到磁盘，而后
者则用于将RDB 文件中的数据重新载入到内存中：
内存中的
数据对象
磁盘中的
RDB文件
rdbSave
rdbLoad
本章先介绍SAVE 和BGSAVE 命令的实现，以及rdbSave 和rdbLoad 两个函数的运行机制，
然后以图表的方式，分部分来介绍RDB 文件的组织形式。
因为本章涉及RDB 运行的相关机制，如果还没了解过RDB 功能的话，请先阅读Redis 官网
上的persistence 手册。
5.2.1 保存
rdbSave 函数负责将内存中的数据库数据以RDB 格式保存到磁盘中，如果RDB 文件已存在，
那么新的RDB 文件将替换已有的RDB 文件。
在保存RDB 文件期间，主进程会被阻塞，直到保存完成为止。
SAVE 和BGSAVE 两个命令都会调用rdbSave 函数，但它们调用的方式各有不同：
. SAVE 直接调用rdbSave ，阻塞Redis 主进程，直到保存完成为止。在主进程阻塞期间，
服务器不能处理客户端的任何请求。
. BGSAVE 则fork 出一个子进程，子进程负责调用rdbSave ，并在保存完成之后向主
进程发送信号，通知保存已完成。因为rdbSave 在子进程被调用，所以Redis 服务器在
BGSAVE 执行期间仍然可以继续处理客户端的请求。
通过伪代码来描述这两个命令，可以很容易地看出它们之间的区别：
def SAVE():
rdbSave()
def BGSAVE():
pid = fork()
if pid == 0:
# 子进程保存RDB
rdbSave()
elif pid > 0:
5.2. RDB 125
Redis 设计与实现, 第一版
# 父进程继续处理请求，并等待子进程的完成信号
handle_request()
else:
# pid == -1
# 处理fork 错误
handle_fork_error()
5.2.2 SAVE 、BGSAVE 、AOF 写入和BGREWRITEAOF
除了了解RDB 文件的保存方式之外，我们可能还想知道，两个RDB 保存命令能否同时使用？
它们和AOF 保存工作是否冲突？
本节就来解答这些问题。
SAVE
前面提到过，当SAVE 执行时，Redis 服务器是阻塞的，所以当SAVE 正在执行时，新的
SAVE 、BGSAVE 或BGREWRITEAOF 调用都不会产生任何作用。
只有在上一个SAVE 执行完毕、Redis 重新开始接受请求之后，新的SAVE 、BGSAVE 或
BGREWRITEAOF 命令才会被处理。
另外，因为AOF 写入由后台线程完成，而BGREWRITEAOF 则由子进程完成，所以在SAVE
执行的过程中，AOF 写入和BGREWRITEAOF 可以同时进行。
BGSAVE
在执行SAVE 命令之前，服务器会检查BGSAVE 是否正在执行当中，如果是的话，服务器就
不调用rdbSave ，而是向客户端返回一个出错信息，告知在BGSAVE 执行期间，不能执行
SAVE 。
这样做可以避免SAVE 和BGSAVE 调用的两个rdbSave 交叉执行，造成竞争条件。
另一方面，当BGSAVE 正在执行时，调用新BGSAVE 命令的客户端会收到一个出错信息，告
知BGSAVE 已经在执行当中。
BGREWRITEAOF 和BGSAVE 不能同时执行：
. 如果BGSAVE 正在执行，那么BGREWRITEAOF 的重写请求会被延迟到BGSAVE 执
行完毕之后进行，执行BGREWRITEAOF 命令的客户端会收到请求被延迟的回复。
. 如果BGREWRITEAOF 正在执行，那么调用BGSAVE 的客户端将收到出错信息，表示
这两个命令不能同时执行。
BGREWRITEAOF 和BGSAVE 两个命令在操作方面并没有什么冲突的地方，不能同时执行
它们只是一个性能方面的考虑：并发出两个子进程，并且两个子进程都同时进行大量的磁盘写
入操作，这怎么想都不会是一个好主意。
126 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
5.2.3 载入
当Redis 服务器启动时，rdbLoad 函数就会被执行，它读取RDB 文件，并将文件中的数据库
数据载入到内存中。
在载入期间，服务器每载入1000 个键就处理一次所有已到达的请求，不过只有PUBLISH 、
SUBSCRIBE 、PSUBSCRIBE 、UNSUBSCRIBE 、PUNSUBSCRIBE 五个命令的请求会被正确地处理，
其他命令一律返回错误。等到载入完成之后，服务器才会开始正常处理所有命令。
Note: 发布与订阅功能和其他数据库功能是完全隔离的，前者不写入也不读取数据库，所以
在服务器载入期间，订阅与发布功能仍然可以正常使用，而不必担心对载入数据的完整性产生
影响。
另外，因为AOF 文件的保存频率通常要高于RDB 文件保存的频率，所以一般来说，AOF 文
件中的数据会比RDB 文件中的数据要新。
因此，如果服务器在启动时，打开了AOF 功能，那么程序优先使用AOF 文件来还原数据。只
有在AOF 功能未打开的情况下，Redis 才会使用RDB 文件来还原数据。
5.2.4 RDB 文件结构
前面介绍了保存和读取RDB 文件的两个函数，现在，是时候介绍RDB 文件本身了。
一个RDB 文件可以分为以下几个部分：
+-------+-------------+-----------+-----------------+-----+-----------+
| REDIS | RDB-VERSION | SELECT-DB | KEY-VALUE-PAIRS | EOF | CHECK-SUM |
+-------+-------------+-----------+-----------------+-----+-----------+
|<-------- DB-DATA ---------->|
以下的几个小节将分别对这几个部分的保存和读入规则进行介绍。
REDIS
文件的最开头保存着REDIS 五个字符，标识着一个RDB 文件的开始。
在读入文件的时候，程序可以通过检查一个文件的前五个字节，来快速地判断该文件是否有可
能是RDB 文件。
RDB-VERSION
一个四字节长的以字符表示的整数，记录了该文件所使用的RDB 版本号。
目前的RDB 文件版本为0006 。
因为不同版本的RDB 文件互不兼容，所以在读入程序时，需要根据版本来选择不同的读入方
式。
DB-DATA
这个部分在一个RDB 文件中会出现任意多次，每个DB-DATA 部分保存着服务器上一个非空数
据库的所有数据。
5.2. RDB 127
Redis 设计与实现, 第一版
SELECT-DB
这域保存着跟在后面的键值对所属的数据库号码。
在读入RDB 文件时，程序会根据这个域的值来切换数据库，确保数据被还原到正确的数据库
上。
KEY-VALUE-PAIRS
因为空的数据库不会被保存到RDB 文件，所以这个部分至少会包含一个键值对的数据。
每个键值对的数据使用以下结构来保存：
+----------------------+---------------+-----+-------+
| OPTIONAL-EXPIRE-TIME | TYPE-OF-VALUE | KEY | VALUE |
+----------------------+---------------+-----+-------+
OPTIONAL-EXPIRE-TIME 域是可选的，如果键没有设置过期时间，那么这个域就不会出现；反
之，如果这个域出现的话，那么它记录着键的过期时间，在当前版本的RDB 中，过期时间是
一个以毫秒为单位的UNIX 时间戳。
KEY 域保存着键，格式和REDIS_ENCODING_RAW 编码的字符串对象一样（见下文）。
TYPE-OF-VALUE 域记录着VALUE 域的值所使用的编码，根据这个域的指示，程序会使用不同的
方式来保存和读取VALUE 的值。
Note: 下文提到的编码在《对象处理机制》章节介绍过，如果忘记了可以回去重温下。
保存VALUE 的详细格式如下：
. REDIS_ENCODING_INT 编码的REDIS_STRING 类型对象：
如果值可以表示为8 位、16 位或32 位有符号整数，那么直接以整数类型的形式来保存
它们：
+---------+
| integer |
+---------+
比如说，整数8 可以用8 位序列00001000 保存。
当读入这类值时，程序按指定的长度读入字节数据，然后将数据转换回整数类型。
另一方面，如果值不能被表示为最高32 位的有符号整数，那么说明这是一个long long
类型的值，在RDB 文件中，这种类型的值以字符序列的形式保存。
一个字符序列由两部分组成：
+-----+---------+
| LEN | CONTENT |
+-----+---------+
其中，CONTENT 域保存了字符内容，而LEN 则保存了以字节为单位的字符长度。
当进行载入时，读入器先读入LEN ，创建一个长度等于LEN 的字符串对象，然后再从文
件中读取LEN 字节数据，并将这些数据设置为字符串对象的值。
. REDIS_ENCODING_RAW 编码的REDIS_STRING 类型值有三种保存方式：
128 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
1. 如果值可以表示为8 位、16 位或32 位长的有符号整数，那么用整数类型的形式来
保存它们。
2. 如果字符串长度大于20 ，并且服务器开启了LZF 压缩功能，那么对字符串进行压
缩，并保存压缩之后的数据。
经过LZF 压缩的字符串会被保存为以下结构：
+----------+----------------+--------------------+
| LZF-FLAG | COMPRESSED-LEN | COMPRESSED-CONTENT |
+----------+----------------+--------------------+
LZF-FLAG 告知读入器，后面跟着的是被LZF 算法压缩过的数据。
COMPRESSED-CONTENT 是被压缩后的数据，COMPRESSED-LEN 则是该数据的字节长度。
3. 在其他情况下，程序直接以普通字节序列的方式来保存字符串。比如说，对于一个
长度为20 字节的字符串，需要使用20 字节的空间来保存它。
这种字符串被保存为以下结构：
+-----+---------+
| LEN | CONTENT |
+-----+---------+
LEN 为字符串的字节长度，CONTENT 为字符串。
当进行载入时，读入器先检测字符串保存的方式，再根据不同的保存方式，用不同的方法
取出内容，并将内容保存到新建的字符串对象当中。
. REDIS_ENCODING_LINKEDLIST 编码的REDIS_LIST 类型值保存为以下结构：
+-----------+--------------+--------------+-----+--------------+
| NODE-SIZE | NODE-VALUE-1 | NODE-VALUE-2 | ... | NODE-VALUE-N |
+-----------+--------------+--------------+-----+--------------+
其中NODE-SIZE 保存链表节点数量，后面跟着任意多个节点值。节点值的保存方式和字
符串的保存方式一样。
当进行载入时，读入器读取节点的数量，创建一个新的链表，然后一直执行以下步骤，直
到指定节点数量满足为止：
1. 读取字符串表示的节点值
2. 将包含节点值的新节点添加到链表中
. REDIS_ENCODING_HT 编码的REDIS_SET 类型值保存为以下结构：
+----------+-----------+-----------+-----+-----------+
| SET-SIZE | ELEMENT-1 | ELEMENT-2 | ... | ELEMENT-N |
+----------+-----------+-----------+-----+-----------+
SET-SIZE 记录了集合元素的数量，后面跟着多个元素值。元素值的保存方式和字符串的
保存方式一样。
载入时，读入器先读入集合元素的数量SET-SIZE ，再连续读入SET-SIZE 个字符串，并
将这些字符串作为新元素添加至新创建的集合。
. REDIS_ENCODING_SKIPLIST 编码的REDIS_ZSET 类型值保存为以下结构：
+--------------+-------+---------+-------+---------+-----+-------+---------+
| ELEMENT-SIZE | MEB-1 | SCORE-1 | MEB-2 | SCORE-2 | ... | MEB-N | SCORE-N |
+--------------+-------+---------+-------+---------+-----+-------+---------+
5.2. RDB 129
Redis 设计与实现, 第一版
其中ELEMENT-SIZE 为有序集元素的数量，MEMBER-i 为第i 个有序集元素的成员，
SCORE-i 为第i 个有序集元素的分值。
当进行载入时，读入器读取有序集元素数量，创建一个新的有序集，然后一直执行以下步
骤，直到指定元素数量满足为止：
1. 读入字符串形式保存的member
2. 读入字符串形式保存的score ，并将它转换为浮点数
3. 添加member 为成员、score 为分值的新元素到有序集
. REDIS_ENCODING_HT 编码的REDIS_HASH 类型值保存为以下结构：
+-----------+-------+---------+-------+---------+-----+-------+---------+
| HASH-SIZE | KEY-1 | VALUE-1 | KEY-2 | VALUE-2 | ... | KEY-N | VALUE-N |
+-----------+-------+---------+-------+---------+-----+-------+---------+
HASH-SIZE 是哈希表包含的键值对的数量，KEY-i 和VALUE-i 分别是哈希表的键和值。
载入时，程序先创建一个新的哈希表，然后读入HASH-SIZE ，再执行以下步骤HASH-SIZE
次：
1. 读入一个字符串
2. 再读入另一个字符串
3. 将第一个读入的字符串作为键，第二个读入的字符串作为值，插入到新建立的哈希
中。
. REDIS_LIST 类型、REDIS_HASH 类型和REDIS_ZSET 类型都使用了
REDIS_ENCODING_ZIPLIST 编码，ziplist 在RDB 中的保存方式如下：
+-----+---------+
| LEN | ZIPLIST |
+-----+---------+
载入时，读入器先读入ziplist 的字节长，再根据该字节长读入数据，最后将数据还原
成一个ziplist 。
. REDIS_ENCODING_INTSET 编码的REDIS_SET 类型值保存为以下结构：
+-----+--------+
| LEN | INTSET |
+-----+--------+
载入时，读入器先读入intset 的字节长度，再根据长度读入数据，最后将数据还原成
intset 。
EOF
标志着数据库内容的结尾（不是文件的结尾），值为rdb.h/EDIS_RDB_OPCODE_EOF （255）。
CHECK-SUM
RDB 文件所有内容的校验和，一个uint_64t 类型值。
REDIS 在写入RDB 文件时将校验和保存在RDB 文件的末尾，当读取时，根据它的值对内容
进行校验。
130 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
如果这个域的值为0 ，那么表示Redis 关闭了校验和功能。
5.2.5 小结
. rdbSave 会将数据库数据保存到RDB 文件，并在保存完成之前阻塞调用者。
. SAVE 命令直接调用rdbSave ，阻塞Redis 主进程；BGSAVE 用子进程调用rdbSave ，
主进程仍可继续处理命令请求。
. SAVE 执行期间，AOF 写入可以在后台线程进行，BGREWRITEAOF 可以在子进程进
行，所以这三种操作可以同时进行。
. 为了避免产生竞争条件，BGSAVE 执行时，SAVE 命令不能执行。
. 为了避免性能问题，BGSAVE 和BGREWRITEAOF 不能同时执行。
. 调用rdbLoad 函数载入RDB 文件时，不能进行任何和数据库相关的操作，不过订阅与
发布方面的命令可以正常执行，因为它们和数据库不相关联。
. RDB 文件的组织方式如下：
+-------+-------------+-----------+-----------------+-----+-----------+
| REDIS | RDB-VERSION | SELECT-DB | KEY-VALUE-PAIRS | EOF | CHECK-SUM |
+-------+-------------+-----------+-----------------+-----+-----------+
|<-------- DB-DATA ---------->|
. 键值对在RDB 文件中的组织方式如下：
+----------------------+---------------+-----+-------+
| OPTIONAL-EXPIRE-TIME | TYPE-OF-VALUE | KEY | VALUE |
+----------------------+---------------+-----+-------+
RDB 文件使用不同的格式来保存不同类型的值。
5.3 AOF
Redis 分别提供了RDB 和AOF 两种持久化机制：
. RDB 将数据库的快照（snapshot）以二进制的方式保存到磁盘中。
. AOF 则以协议文本的方式，将所有对数据库进行过写入的命令（及其参数）记录到AOF
文件，以此达到记录数据库状态的目的。
客户端服务器
命令请求AOF
文件
网络协议格式的
命令内容
本章首先介绍AOF 功能的运作机制，了解命令是如何被保存到AOF 文件里的，观察不同的
AOF 保存模式对数据的安全性、以及Redis 性能的影响。
5.3. AOF 131
Redis 设计与实现, 第一版
之后会介绍从AOF 文件中恢复数据库状态的方法，以及该方法背后的实现机制。
最后还会介绍对AOF 进行重写以调整文件体积的方法，并研究这种方法是如何在不改变数据
库状态的前提下进行的。
因为本章涉及AOF 运行的相关机制，如果还没了解过AOF 功能的话，请先阅读Redis 持久
化手册中关于AOF 的部分。
5.3.1 AOF 命令同步
Redis 将所有对数据库进行过写入的命令（及其参数）记录到AOF 文件，以此达到记录数据库
状态的目的，为了方便起见，我们称呼这种记录过程为同步。
举个例子，如果执行以下命令：
redis> RPUSH list 1 2 3 4
(integer) 4
redis> LRANGE list 0 -1
1) "1"
2) "2"
3) "3"
4) "4"
redis> KEYS *
1) "list"
redis> RPOP list
"4"
redis> LPOP list
"1"
redis> LPUSH list 1
(integer) 3
redis> LRANGE list 0 -1
1) "1"
2) "2"
3) "3"
那么其中四条对数据库有修改的写入命令就会被同步到AOF 文件中：
RPUSH list 1 2 3 4
RPOP list
LPOP list
LPUSH list 1
为了处理的方便，AOF 文件使用网络通讯协议的格式来保存这些命令。
比如说，上面列举的四个命令在AOF 文件中就实际保存如下：
*2
$6
SELECT
132 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
$1
0
*6
$5
RPUSH
$4
list
$1
1
$1
2
$1
3
$1
4
*2
$4
RPOP
$4
list
*2
$4
LPOP
$4
list
*3
$5
LPUSH
$4
list
$1
1
除了SELECT 命令是AOF 程序自己加上去的之外，其他命令都是之前我们在终端里执行的
命令。
同步命令到AOF 文件的整个过程可以分为三个阶段：
1. 命令传播：Redis 将执行完的命令、命令的参数、命令的参数个数等信息发送到AOF 程
序中。
2. 缓存追加：AOF 程序根据接收到的命令数据，将命令转换为网络通讯协议的格式，然后
将协议内容追加到服务器的AOF 缓存中。
3. 文件写入和保存：AOF 缓存中的内容被写入到AOF 文件末尾，如果设定的AOF 保存
条件被满足的话，fsync 函数或者fdatasync 函数会被调用，将写入的内容真正地保存
到磁盘中。
以下几个小节将详细地介绍这三个步骤。
5.3.2 命令传播
当一个Redis 客户端需要执行命令时，它通过网络连接，将协议文本发送给Redis 服务器。
比如说， 要执行命令SET KEY VALUE ， 客户端将向服务器发送文本
"*3\r\n$3\r\nSET\r\n$3\r\nKEY\r\n$5\r\nVALUE\r\n" 。
5.3. AOF 133
Redis 设计与实现, 第一版
服务器在接到客户端的请求之后，它会根据协议文本的内容，选择适当的命令函数，并将各个
参数从字符串文本转换为Redis 字符串对象（StringObject）。
比如说，针对上面的SET 命令例子，Redis 将客户端的命令指针指向实现SET 命令的
setCommand 函数，并创建三个Redis 字符串对象，分别保存SET 、KEY 和VALUE 三个参数（命
令也算作参数）。
每当命令函数成功执行之后，命令参数都会被传播到AOF 程序，以及REPLICATION 程序
（本节不讨论这个，列在这里只是为了完整性的考虑）。
这个执行并传播命令的过程可以用以下伪代码表示：
if (execRedisCommand(cmd, argv, argc) == EXEC_SUCCESS):
if aof_is_turn_on():
# 传播命令到AOF 程序
propagate_aof(cmd, argv, argc)
if replication_is_turn_on():
# 传播命令到REPLICATION 程序
propagate_replication(cmd, argv, argc)
以下是该过程的流程图：
134 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
命令执行成功
AOF
功能已打开？
传播命令到 AOF 程序
是
REPLICATION
功能已打开？
否
传播命令到 REPLICATION 程序
是
处理后续步骤：
清理资源、
等等
否
5.3. AOF 135
Redis 设计与实现, 第一版
5.3.3 缓存追加
当命令被传播到AOF 程序之后，程序会根据命令以及命令的参数，将命令从字符串对象转换
回原来的协议文本。
比如说，如果AOF 程序接受到的三个参数分别保存着SET 、KEY 和VALUE 三个字符串，那么
它将生成协议文本"*3\r\n$3\r\nSET\r\n$3\r\nKEY\r\n$5\r\nVALUE\r\n" 。
协议文本生成之后，它会被追加到redis.h/redisServer 结构的aof_buf 末尾。
redisServer 结构维持着Redis 服务器的状态，aof_buf 域则保存着所有等待写入到AOF 文
件的协议文本：
struct redisServer {
// 其他域...
sds aof_buf;
// 其他域...
};
至此，追加命令到缓存的步骤执行完毕。
综合起来，整个缓存追加过程可以分为以下三步：
1. 接受命令、命令的参数、以及参数的个数、所使用的数据库等信息。
2. 将命令还原成Redis 网络通讯协议。
3. 将协议文本追加到aof_buf 末尾。
5.3.4 文件写入和保存
每当服务器常规任务函数被执行、或者事件处理器被执行时，aof.c/flushAppendOnlyFile 函
数都会被调用，这个函数执行以下两个工作：
WRITE：根据条件，将aof_buf 中的缓存写入到AOF 文件。
SAVE：根据条件，调用fsync 或fdatasync 函数，将AOF 文件保存到磁盘中。
两个步骤都需要根据一定的条件来执行，而这些条件由AOF 所使用的保存模式来决定，以下
小节就来介绍AOF 所使用的三种保存模式，以及在这些模式下，步骤WRITE 和SAVE 的调
用条件。
5.3.5 AOF 保存模式
Redis 目前支持三种AOF 保存模式，它们分别是：
1. AOF_FSYNC_NO ：不保存。
2. AOF_FSYNC_EVERYSEC ：每一秒钟保存一次。
3. AOF_FSYNC_ALWAYS ：每执行一个命令保存一次。
以下三个小节将分别讨论这三种保存模式。
136 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
不保存
在这种模式下，每次调用flushAppendOnlyFile 函数，WRITE 都会被执行，但SAVE 会被
略过。
在这种模式下，SAVE 只会在以下任意一种情况中被执行：
. Redis 被关闭
. AOF 功能被关闭
. 系统的写缓存被刷新（可能是缓存已经被写满，或者定期保存操作被执行）
这三种情况下的SAVE 操作都会引起Redis 主进程阻塞。
每一秒钟保存一次
在这种模式中，SAVE 原则上每隔一秒钟就会执行一次，因为SAVE 操作是由后台子线程调用
的，所以它不会引起服务器主进程阻塞。
注意，在上一句的说明里面使用了词语“原则上” ，在实际运行中，程序在这种模式下对fsync
或fdatasync 的调用并不是每秒一次，它和调用flushAppendOnlyFile 函数时Redis 所处的
状态有关。
每当flushAppendOnlyFile 函数被调用时，可能会出现以下四种情况：
. 子线程正在执行SAVE ，并且：
1. 这个SAVE 的执行时间未超过2 秒，那么程序直接返回，并不执行WRITE 或新的
SAVE 。
2. 这个SAVE 已经执行超过2 秒，那么程序执行WRITE ，但不执行新的SAVE 。
注意，因为这时WRITE 的写入必须等待子线程先完成（旧的）SAVE ，因此这里
WRITE 会比平时阻塞更长时间。
. 子线程没有在执行SAVE ，并且：
3. 上次成功执行SAVE 距今不超过1 秒，那么程序执行WRITE ，但不执行SAVE 。
4. 上次成功执行SAVE 距今已经超过1 秒，那么程序执行WRITE 和SAVE 。
可以用流程图表示这四种情况：
5.3. AOF 137
Redis 设计与实现, 第一版
SAVE 正在执行？
运行时间
超过 2 秒？
是
距离上次 SAVE
执行成功
超过 1 秒？
否
情况 1 ：
函数直接返回
不执行 WRITE 或
新的 SAVE
否
情况 2 ：
执行WRITE
但不执行新的SAVE
是
情况 3 ：
执行WRITE
但不执行新的SAVE
否
情况 4 ：
执行WRITE 和
新的 SAVE
是
根据以上说明可以知道，在“每一秒钟保存一次”模式下，如果在情况1 中发生故障停机，那么
用户最多损失小于2 秒内所产生的所有数据。
如果在情况2 中发生故障停机，那么用户损失的数据是可以超过2 秒的。
Redis 官网上所说的，AOF 在“每一秒钟保存一次”时发生故障，只丢失1 秒钟数据的说法，实
际上并不准确。
每执行一个命令保存一次
在这种模式下，每次执行完一个命令之后，WRITE 和SAVE 都会被执行。
另外，因为SAVE 是由Redis 主进程执行的，所以在SAVE 执行期间，主进程会被阻塞，不能
接受命令请求。
5.3.6 AOF 保存模式对性能和安全性的影响
在上一个小节，我们简短地描述了三种AOF 保存模式的工作方式，现在，是时候研究一下这
三个模式在安全性和性能方面的区别了。
对于三种AOF 保存模式，它们对服务器主进程的阻塞情况如下：
1. 不保存（AOF_FSYNC_NO）：写入和保存都由主进程执行，两个操作都会阻塞主进程。
2. 每一秒钟保存一次（AOF_FSYNC_EVERYSEC）：写入操作由主进程执行，阻塞主进程。保存
操作由子线程执行，不直接阻塞主进程，但保存操作完成的快慢会影响写入操作的阻塞
时长。
3. 每执行一个命令保存一次（AOF_FSYNC_ALWAYS）：和模式1 一样。
因为阻塞操作会让Redis 主进程无法持续处理请求，所以一般说来，阻塞操作执行得越少、完
成得越快，Redis 的性能就越好。
138 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
模式1 的保存操作只会在AOF 关闭或Redis 关闭时执行，或者由操作系统触发，在一般情况
下，这种模式只需要为写入阻塞，因此它的写入性能要比后面两种模式要高，当然，这种性能
的提高是以降低安全性为代价的：在这种模式下，如果运行的中途发生停机，那么丢失数据的
数量由操作系统的缓存冲洗策略决定。
模式2 在性能方面要优于模式3 ，并且在通常情况下，这种模式最多丢失不多于2 秒的数据，
所以它的安全性要高于模式1 ，这是一种兼顾性能和安全性的保存方案。
模式3 的安全性是最高的，但性能也是最差的，因为服务器必须阻塞直到命令信息被写入并保
存到磁盘之后，才能继续处理请求。
综合起来，三种AOF 模式的操作特性可以总结如下：
模式WRITE
是否阻塞？
SAVE 是
否阻塞？
停机时丢失的数据量
AOF_FSYNC_NO 阻塞阻塞操作系统最后一次对AOF 文件触
发SAVE 操作之后的数据。
AOF_FSYNC_EVERYSEC 阻塞不阻塞一般情况下不超过2 秒钟的数据。
AOF_FSYNC_ALWAYS 阻塞阻塞最多只丢失一个命令的数据。
5.3.7 AOF 文件的读取和数据还原
AOF 文件保存了Redis 的数据库状态，而文件里面包含的都是符合Redis 通讯协议格式的命
令文本。
这也就是说，只要根据AOF 文件里的协议，重新执行一遍里面指示的所有命令，就可以还原
Redis 的数据库状态了。
Redis 读取AOF 文件并还原数据库的详细步骤如下：
1. 创建一个不带网络连接的伪客户端（fake client）。
2. 读取AOF 所保存的文本，并根据内容还原出命令、命令的参数以及命令的个数。
3. 根据命令、命令的参数和命令的个数，使用伪客户端执行该命令。
4. 执行2 和3 ，直到AOF 文件中的所有命令执行完毕。
完成第4 步之后，AOF 文件所保存的数据库就会被完整地还原出来。
注意，因为Redis 的命令只能在客户端的上下文中被执行，而AOF 还原时所使用的命令来自
于AOF 文件，而不是网络，所以程序使用了一个没有网络连接的伪客户端来执行命令。伪客
户端执行命令的效果，和带网络连接的客户端执行命令的效果，完全一样。
整个读取和还原过程可以用以下伪代码表示：
def READ_AND_LOAD_AOF():
# 打开并读取AOF 文件
file = open(aof_file_name)
while file.is_not_reach_eof():
# 读入一条协议文本格式的Redis 命令
cmd_in_text = file.read_next_command_in_protocol_format()
# 根据文本命令，查找命令函数，并创建参数和参数个数等对象
cmd, argv, argc = text_to_command(cmd_in_text)
# 执行命令
5.3. AOF 139
Redis 设计与实现, 第一版
execRedisCommand(cmd, argv, argc)
# 关闭文件
file.close()
作为例子，以下是一个简短的AOF 文件的内容：
*2
$6
SELECT
$1
0
*3
$3
SET
$3
key
$5
value
*8
$5
RPUSH
$4
list
$1
1
$1
2
$1
3
$1
4
$1
5
$1
6
当程序读入这个AOF 文件时，它首先执行SELECT 0 命令——这个SELECT 命令是由AOF 写
入程序自动生成的，它确保程序可以将数据还原到正确的数据库上。
然后执行后面的SET key value 和RPUSH 1 2 3 4 命令，还原key 和list 两个键的数据。
Note: 为了避免对数据的完整性产生影响，在服务器载入数据的过程中，只有和数据库无关
的订阅与发布功能可以正常使用，其他命令一律返回错误。
5.3.8 AOF 重写
AOF 文件通过同步Redis 服务器所执行的命令，从而实现了数据库状态的记录，但是，这种同
步方式会造成一个问题：随着运行时间的流逝，AOF 文件会变得越来越大。
举个例子，如果服务器执行了以下命令：
RPUSH list 1 2 3 4 // [1, 2, 3, 4]
RPOP list // [1, 2, 3]
140 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
LPOP list // [2, 3]
LPUSH list 1 // [1, 2, 3]
那么光是记录list 键的状态，AOF 文件就需要保存四条命令。
另一方面，有些被频繁操作的键，对它们所调用的命令可能有成百上千、甚至上万条，如果这
样被频繁操作的键有很多的话，AOF 文件的体积就会急速膨胀，对Redis 、甚至整个系统的造
成影响。
为了解决以上的问题，Redis 需要对AOF 文件进行重写（rewrite）：创建一个新的AOF 文件
来代替原有的AOF 文件，新AOF 文件和原有AOF 文件保存的数据库状态完全一样，但新
AOF 文件的体积小于等于原有AOF 文件的体积。
以下就来介绍AOF 重写的实现方式。
5.3.9 AOF 重写的实现
所谓的“重写”其实是一个有歧义的词语，实际上，AOF 重写并不需要对原有的AOF 文件进行
任何写入和读取，它针对的是数据库中键的当前值。
考虑这样一个情况，如果服务器对键list 执行了以下四条命令：
RPUSH list 1 2 3 4 // [1, 2, 3, 4]
RPOP list // [1, 2, 3]
LPOP list // [2, 3]
LPUSH list 1 // [1, 2, 3]
那么当前列表键list 在数据库中的值就为[1, 2, 3] 。
如果我们要保存这个列表的当前状态，并且尽量减少所使用的命令数，那么最简单的方式不是
去AOF 文件上分析前面执行的四条命令，而是直接读取list 键在数据库的当前值，然后用一
条RPUSH 1 2 3 命令来代替前面的四条命令。
再考虑这样一个例子，如果服务器对集合键animal 执行了以下命令：
SADD animal cat // {cat}
SADD animal dog panda tiger // {cat, dog, panda, tiger}
SREM animal cat // {dog, panda, tiger}
SADD animal cat lion // {cat, lion, dog, panda, tiger}
那么使用一条SADD animal cat lion dog panda tiger 命令，就可以还原animal 集合的状
态，这比之前的四条命令调用要大大减少。
除了列表和集合之外，字符串、有序集、哈希表等键也可以用类似的方法来保存状态，并且保
存这些状态所使用的命令数量，比起之前建立这些键的状态所使用命令的数量要大大减少。
根据键的类型，使用适当的写入命令来重现键的当前值，这就是AOF 重写的实现原理。整个
重写过程可以用伪代码表示如下：
5.3. AOF 141
Redis 设计与实现, 第一版
def AOF_REWRITE(tmp_tile_name):
f = create(tmp_tile_name)
# 遍历所有数据库
for db in redisServer.db:
# 如果数据库为空，那么跳过这个数据库
if db.is_empty(): continue
# 写入SELECT 命令，用于切换数据库
f.write_command("SELECT " + db.number)
# 遍历所有键
for key in db:
# 如果键带有过期时间，并且已经过期，那么跳过这个键
if key.have_expire_time() and key.is_expired(): continue
if key.type == String:
# 用SET key value 命令来保存字符串键
value = get_value_from_string(key)
f.write_command("SET " + key + value)
elif key.type == List:
# 用RPUSH key item1 item2 ... itemN 命令来保存列表键
item1, item2, ..., itemN = get_item_from_list(key)
f.write_command("RPUSH " + key + item1 + item2 + ... + itemN)
elif key.type == Set:
# 用SADD key member1 member2 ... memberN 命令来保存集合键
member1, member2, ..., memberN = get_member_from_set(key)
f.write_command("SADD " + key + member1 + member2 + ... + memberN)
elif key.type == Hash:
# 用HMSET key field1 value1 field2 value2 ... fieldN valueN 命令来保存哈希键
field1, value1, field2, value2, ..., fieldN, valueN =\
get_field_and_value_from_hash(key)
f.write_command("HMSET " + key + field1 + value1 + field2 + value2 +\
... + fieldN + valueN)
elif key.type == SortedSet:
# 用ZADD key score1 member1 score2 member2 ... scoreN memberN
# 命令来保存有序集键
142 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
score1, member1, score2, member2, ..., scoreN, memberN = \
get_score_and_member_from_sorted_set(key)
f.write_command("ZADD " + key + score1 + member1 + score2 + member2 +\
... + scoreN + memberN)
else:
raise_type_error()
# 如果键带有过期时间，那么用EXPIREAT key time 命令来保存键的过期时间
if key.have_expire_time():
f.write_command("EXPIREAT " + key + key.expire_time_in_unix_timestamp())
# 关闭文件
f.close()
5.3.10 AOF 后台重写
上一节展示的AOF 重写程序可以很好地完成创建一个新AOF 文件的任务，但是，在执行这
个程序的时候，调用者线程会被阻塞。
很明显，作为一种辅佐性的维护手段，Redis 不希望AOF 重写造成服务器无法处理请求，所以
Redis 决定将AOF 重写程序放到（后台）子进程里执行，这样处理的最大好处是：
1. 子进程进行AOF 重写期间，主进程可以继续处理命令请求。
2. 子进程带有主进程的数据副本，使用子进程而不是线程，可以在避免锁的情况下，保证数
据的安全性。
不过，使用子进程也有一个问题需要解决：因为子进程在进行AOF 重写期间，主进程还需要
继续处理命令，而新的命令可能对现有的数据进行修改，这会让当前数据库的数据和重写后的
AOF 文件中的数据不一致。
为了解决这个问题，Redis 增加了一个AOF 重写缓存，这个缓存在fork 出子进程之后开始启
用，Redis 主进程在接到新的写命令之后，除了会将这个写命令的协议内容追加到现有的AOF
文件之外，还会追加到这个缓存中：
5.3. AOF 143
Redis 设计与实现, 第一版
客户端
服务器
命令请求
现有 AOF 文件
命令协议内容
AOF 重写缓存
命令协议内容
换言之，当子进程在执行AOF 重写时，主进程需要执行以下三个工作：
1. 处理命令请求。
2. 将写命令追加到现有的AOF 文件中。
3. 将写命令追加到AOF 重写缓存中。
这样一来可以保证：
1. 现有的AOF 功能会继续执行，即使在AOF 重写期间发生停机，也不会有任何数据丢失。
2. 所有对数据库进行修改的命令都会被记录到AOF 重写缓存中。
当子进程完成AOF 重写之后，它会向父进程发送一个完成信号，父进程在接到完成信号之后，
会调用一个信号处理函数，并完成以下工作：
1. 将AOF 重写缓存中的内容全部写入到新AOF 文件中。
2. 对新的AOF 文件进行改名，覆盖原有的AOF 文件。
当步骤1 执行完毕之后，现有AOF 文件、新AOF 文件和数据库三者的状态就完全一致了。
当步骤2 执行完毕之后，程序就完成了新旧两个AOF 文件的交替。
这个信号处理函数执行完毕之后，主进程就可以继续像往常一样接受命令请求了。在整个AOF
后台重写过程中，只有最后的写入缓存和改名操作会造成主进程阻塞，在其他时候，AOF 后台
重写都不会对主进程造成阻塞，这将AOF 重写对性能造成的影响降到了最低。
以上就是AOF 后台重写，也即是BGREWRITEAOF 命令的工作原理。
5.3.11 AOF 后台重写的触发条件
AOF 重写可以由用户通过调用BGREWRITEAOF 手动触发。
144 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
另外，服务器在AOF 功能开启的情况下，会维持以下三个变量：
. 记录当前AOF 文件大小的变量aof_current_size 。
. 记录最后一次AOF 重写之后，AOF 文件大小的变量aof_rewirte_base_size 。
. 增长百分比变量aof_rewirte_perc 。
每次当serverCron 函数执行时，它都会检查以下条件是否全部满足，如果是的话，就会触发
自动的AOF 重写：
1. 没有BGSAVE 命令在进行。
2. 没有BGREWRITEAOF 在进行。
3. 当前AOF 文件大小大于server.aof_rewrite_min_size （默认值为1 MB）。
4. 当前AOF 文件大小和最后一次AOF 重写后的大小之间的比率大于等于指定的增长百分
比。
默认情况下，增长百分比为100% ，也即是说，如果前面三个条件都已经满足，并且当前AOF
文件大小比最后一次AOF 重写时的大小要大一倍的话，那么触发自动AOF 重写。
5.3.12 小结
. AOF 文件通过保存所有修改数据库的命令来记录数据库的状态。
. AOF 文件中的所有命令都以Redis 通讯协议的格式保存。
. 不同的AOF 保存模式对数据的安全性、以及Redis 的性能有很大的影响。
. AOF 重写的目的是用更小的体积来保存数据库状态，整个重写过程基本上不影响Redis
主进程处理命令请求。
. AOF 重写是一个有歧义的名字，实际的重写工作是针对数据库的当前值来进行的，程序
既不读写、也不使用原有的AOF 文件。
. AOF 可以由用户手动触发，也可以由服务器自动触发。
5.4 事件
事件是Redis 服务器的核心，它处理两项重要的任务：
1. 处理文件事件：在多个客户端中实现多路复用，接受它们发来的命令请求，并将命令的执
行结果返回给客户端。
2. 时间事件：实现服务器常规操作（server cron job）。
本文以下内容就来介绍这两种事件，以及它们背后的运作模式。
5.4.1 文件事件
Redis 服务器通过在多个客户端之间进行多路复用，从而实现高效的命令请求处理：多个客户
端通过套接字连接到Redis 服务器中，但只有在套接字可以无阻塞地进行读或者写时，服务器
才会和这些客户端进行交互。
Redis 将这类因为对套接字进行多路复用而产生的事件称为文件事件（file event），文件事件可
以分为读事件和写事件两类。
5.4. 事件145
Redis 设计与实现, 第一版
读事件
读事件标志着客户端命令请求的发送状态。
当一个新的客户端连接到服务器时，服务器会给为该客户端绑定读事件，直到客户端断开连接
之后，这个读事件才会被移除。
读事件在整个网络连接的生命期内，都会在等待和就绪两种状态之间切换：
. 当客户端只是连接到服务器，但并没有向服务器发送命令时，该客户端的读事件就处于
等待状态。
. 当客户端给服务器发送命令请求，并且请求已到达时（相应的套接字可以无阻塞地执行读
操作），该客户端的读事件处于就绪状态。
作为例子，下图展示了三个已连接到服务器、但并没有发送命令的客户端：
服务器
客户端 X
等待命令请求
客户端 Y
等待命令请求
客户端 Z
等待命令请求
这三个客户端的状态如下表：
客户端读事件状态命令发送状态
客户端X 等待未发送
客户端Y 等待未发送
客户端Z 等待未发送
之后，当客户端X 向服务器发送命令请求，并且命令请求已到达时，客户端X 的读事件状态
变为就绪：
146 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
服务器
客户端 X
发送命令请求
客户端 Y
等待命令请求
客户端 Z
等待命令请求
这时，三个客户端的状态如下表（只有客户端X 的状态被更新了）：
客户端读事件状态命令发送状态
客户端X 就绪已发送，并且已到达
客户端Y 等待未发送
客户端Z 等待未发送
当事件处理器被执行时，就绪的文件事件会被识别到，相应的命令请求会被发送到命令执行
器，并对命令进行求值。
写事件
写事件标志着客户端对命令结果的接收状态。
和客户端自始至终都关联着读事件不同，服务器只会在有命令结果要传回给客户端时，才会为
客户端关联写事件，并且在命令结果传送完毕之后，客户端和写事件的关联就会被移除。
一个写事件会在两种状态之间切换：
. 当服务器有命令结果需要返回给客户端，但客户端还未能执行无阻塞写，那么写事件处
于等待状态。
. 当服务器有命令结果需要返回给客户端，并且客户端可以进行无阻塞写，那么写事件处
于就绪状态。
当客户端向服务器发送命令请求，并且请求被接受并执行之后，服务器就需要将保存在缓存内
的命令执行结果返回给客户端，这时服务器就会为客户端关联写事件。
作为例子，下图展示了三个连接到服务器的客户端，其中服务器正等待客户端X 变得可写，从
而将命令的执行结果返回给它：
5.4. 事件147
Redis 设计与实现, 第一版
服务器
客户端 X
等待将命令结果返回
等待命令请求
客户端 Y
等待命令请求
客户端 Z
等待命令请求
此时三个客户端的事件状态分别如下表：
客户端读事件状态写事件状态
客户端X 等待等待
客户端Y 等待无
客户端Z 等待无
当客户端X 的套接字可以进行无阻塞写操作时，写事件就绪，服务器将保存在缓存内的命令执
行结果返回给客户端：
服务器
客户端 X
返回命令执行结果
等待命令请求
客户端 Y
等待命令请求
客户端 Z
等待命令请求
此时三个客户端的事件状态分别如下表（只有客户端X 的状态被更新了）：
148 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
客户端读事件状态写事件状态
客户端X 等待已就绪
客户端Y 等待无
客户端Z 等待无
当命令执行结果被传送回客户端之后，客户端和写事件之间的关联会被解除（只剩下读事件），
至此，返回命令执行结果的动作执行完毕：
服务器
客户端 X
等待命令请求
客户端 Y
等待命令请求
客户端 Z
等待命令请求
Note: 同时关联写事件和读事件
前面提到过，读事件只有在客户端断开和服务器的连接时，才会被移除。
这也就是说，当客户端关联写事件的时候，实际上它在同时关联读/写两种事件。
因为在同一次文件事件处理器的调用中，单个客户端只能执行其中一种事件（要么读，要么写，
但不能又读又写），当出现读事件和写事件同时就绪的情况时，事件处理器优先处理读事件。
这也就是说，当服务器有命令结果要返回客户端，而客户端又有新命令请求进入时，服务器先
处理新命令请求。
5.4.2 时间事件
时间事件记录着那些要在指定时间点运行的事件，多个时间事件以无序链表的形式保存在服务
器状态中。
每个时间事件主要由三个属性组成：
. when ：以毫秒格式的UNIX 时间戳为单位，记录了应该在什么时间点执行事件处理函数。
. timeProc ：事件处理函数。
. next 指向下一个时间事件，形成链表。
根据timeProc 函数的返回值，可以将时间事件划分为两类：
5.4. 事件149
Redis 设计与实现, 第一版
. 如果事件处理函数返回ae.h/AE_NOMORE ，那么这个事件为单次执行事件：该事件会在指
定的时间被处理一次，之后该事件就会被删除，不再执行。
. 如果事件处理函数返回一个非AE_NOMORE 的整数值，那么这个事件为循环执行事件：该
事件会在指定的时间被处理，之后它会按照事件处理函数的返回值，更新事件的when 属
性，让这个事件在之后的某个时间点再次运行，并以这种方式一直更新并运行下去。
可以用伪代码来表示这两种事件的处理方式：
def handle_time_event(server, time_event):
# 执行事件处理器，并获取返回值
# 返回值可以是AE_NOMORE ，或者一个表示毫秒数的非符整数值
retval = time_event.timeProc()
if retval == AE_NOMORE:
# 如果返回AE_NOMORE ，那么将事件从链表中删除，不再执行
server.time_event_linked_list.delete(time_event)
else:
# 否则，更新事件的when 属性
# 让它在当前时间之后的retval 毫秒之后再次运行
time_event.when = unix_ts_in_ms() + retval
当时间事件处理器被执行时，它遍历所有链表中的时间事件，检查它们的到达事件（when 属
性），并执行其中的已到达事件：
def process_time_event(server):
# 遍历时间事件链表
for time_event in server.time_event_linked_list:
# 检查事件是否已经到达
if time_event.when >= unix_ts_in_ms():
# 处理已到达事件
handle_time_event(server, time_event)
Note: 无序链表并不影响时间事件处理器的性能
在目前的版本中，正常模式下的Redis 只带有serverCron 一个时间事件，而在benchmark 模
式下，Redis 也只使用两个时间事件。
在这种情况下，程序几乎是将无序链表退化成一个指针来使用，所以使用无序链表来保存时间
事件，并不影响事件处理器的性能。
5.4.3 时间事件应用实例：服务器常规操作
对于持续运行的服务器来说，服务器需要定期对自身的资源和状态进行必要的检查和整理，从
而让服务器维持在一个健康稳定的状态，这类操作被统称为常规操作（cron job）。
在Redis 中，常规操作由redis.c/serverCron 实现，它主要执行以下操作：
. 更新服务器的各类统计信息，比如时间、内存占用、数据库占用情况等。
150 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
. 清理数据库中的过期键值对。
. 对不合理的数据库进行大小调整。
. 关闭和清理连接失效的客户端。
. 尝试进行AOF 或RDB 持久化操作。
. 如果服务器是主节点的话，对附属节点进行定期同步。
. 如果处于集群模式的话，对集群进行定期同步和连接测试。
Redis 将serverCron 作为时间事件来运行，从而确保它每隔一段时间就会自动运行一次，又
因为serverCron 需要在Redis 服务器运行期间一直定期运行，所以它是一个循环时间事件：
serverCron 会一直定期执行，直到服务器关闭为止。
在Redis 2.6 版本中，程序规定serverCron 每隔10 毫秒就会被运行一次。从Redis 2.8 开始，
10 毫秒是serverCron 运行的默认间隔，而具体的间隔可以由用户自己调整。
5.4.4 事件的执行与调度
既然Redis 里面既有文件事件，又有时间事件，那么如何调度这两种事件就成了一个关键问题。
简单地说，Redis 里面的两种事件呈合作关系，它们之间包含以下三种属性：
1. 一种事件会等待另一种事件执行完毕之后，才开始执行，事件之间不会出现抢占。
2. 事件处理器先处理文件事件（处理命令请求），再执行时间事件（调用serverCron）
3. 文件事件的等待时间（类poll 函数的最大阻塞时间），由距离到达时间最短的时间事件
决定。
这些属性表明，实际处理时间事件的时间，通常会比时间事件所预定的时间要晚，至于延迟的
时间有多长，取决于时间事件执行之前，执行文件事件所消耗的时间。
比如说，以下图表就展示了，虽然时间事件TE 1 预定在t1 时间执行，但因为文件事件FE 1
正在运行，所以TE 1 的执行被延迟了：
t1
|V
time -----------------+------------------->|
| FE 1 | TE 1 |
|<------>|
TE 1
delay
time
另外，对于像serverCron 这类循环执行的时间事件来说，如果事件处理器的返回值是t ，那
么Redis 只保证：
. 如果两次执行时间事件处理器之间的时间间隔大于等于t ，那么这个时间事件至少会被
处理一次。
. 而并不是说，每隔t 时间，就一定要执行一次事件——这对于不使用抢占调度的Redis
事件处理器来说，也是不可能做到的
5.4. 事件151
Redis 设计与实现, 第一版
举个例子，虽然serverCron （sC）设定的间隔为10 毫秒，但它并不是像如下那样每隔10 毫
秒就运行一次：
time ----------------------------------------------------->|
|<---- 10 ms ---->|<---- 10 ms ---->|<---- 10 ms ---->|
| FE 1 | FE 2 | sC 1 | FE 3 | sC 2 | FE 4 |
^ ^ ^ ^ ^
| | | | |
file event time event | time event |
handler handler | handler |
run run | run |
file event file event
handler handler
run run
在实际中，serverCron 的运行方式更可能是这样子的：
time ----------------------------------------------------------------------->|
|<---- 10 ms ---->|<---- 10 ms ---->|<---- 10 ms ---->|<---- 10 ms ---->|
| FE 1 | FE 2 | sC 1 | FE 3 | FE 4 | FE 5 | sC 2 |
|<-------- 15 ms -------->| |<------- 12 ms ------->|
>= 10 ms >= 10 ms
^ ^ ^ ^
| | | |
file event time event | time event
handler handler | handler
run run | run
file event
handler
run
根据情况，如果处理文件事件耗费了非常多的时间，serverCron 被推迟到一两秒之后才能执
行，也是有可能的。
整个事件处理器程序可以用以下伪代码描述：
def process_event():
# 获取执行时间最接近现在的一个时间事件
te = get_nearest_time_event(server.time_event_linked_list)
# 检查该事件的执行时间和现在时间之差
# 如果值<= 0 ，那么说明至少有一个时间事件已到达
# 如果值> 0 ，那么说明目前没有任何时间事件到达
nearest_te_remaind_ms = te.when - now_in_ms()
if nearest_te_remaind_ms <= 0:
# 如果有时间事件已经到达
# 那么调用不阻塞的文件事件等待函数
poll(timeout=None)
else:
152 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
# 如果时间事件还没到达
# 那么阻塞的最大时间不超过te 的到达时间
poll(timeout=nearest_te_remaind_ms)
# 处理已就绪文件事件
process_file_events()
# 处理已到达时间事件
process_time_event()
通过这段代码，可以清晰地看出：
. 到达时间最近的时间事件，决定了poll 的最大阻塞时长。
. 文件事件先于时间事件处理。
将这个事件处理函数置于一个循环中，加上初始化和清理函数，这就构成了Redis 服务器的主
函数调用：
def redis_main():
# 初始化服务器
init_server()
# 一直处理事件，直到服务器关闭为止
while server_is_not_shutdown():
process_event()
# 清理服务器
clean_server()
5.4.5 小结
. Redis 的事件分为时间事件和文件事件两类。
. 文件事件分为读事件和写事件两类：读事件实现了命令请求的接收，写事件实现了命令
结果的返回。
. 时间事件分为单次执行事件和循环执行事件，服务器常规操作serverCron 就是循环事
件。
. 文件事件和时间事件之间是合作关系：一种事件会等待另一种事件完成之后再执行，不
会出现抢占情况。
. 时间事件的实际执行时间通常会比预定时间晚一些。
5.5 服务器与客户端
前面的章节介绍了所有Redis 的重要功能组件：数据结构、数据类型、事务、Lua 环境、事件
处理、数据库、持久化，等等，但是我们还没有对Redis 服务器本身做任何介绍。
不过，服务器本身并没有多少需要介绍的新东西，因为服务器除了维持服务器状态之外，最重
要的就是将前面介绍过的各个功能模块组合起来，而这些功能模块在前面的章节里已经介绍过
了，所以本章将焦点放在服务器的初始化过程，以及服务器对命令的处理过程上。
5.5. 服务器与客户端153
Redis 设计与实现, 第一版
本章首先介绍服务器的初始化操作，观察一个Redis 服务器从启动到可以接受客户端连接，需
要经过什么步骤。
然后介绍客户端是如何连接到服务器的，而服务器又是如何维持多个客户端的不同状态的。
文章最后将介绍命令从发送到处理的整个过程，并列举了一个SET 命令的执行过程作为例子。
5.5.1 初始化服务器
从启动Redis 服务器，到服务器可以接受外来客户端的网络连接这段时间，Redis 需要执行一
系列初始化操作。
整个初始化过程可以分为以下六个步骤：
1. 初始化服务器全局状态。
2. 载入配置文件。
3. 创建daemon 进程。
4. 初始化服务器功能模块。
5. 载入数据。
6. 开始事件循环。
以下各个小节将介绍Redis 服务器初始化的各个步骤。
1. 初始化服务器全局状态
redis.h/redisServer 结构记录了和服务器相关的所有数据，这个结构主要包含以下信息：
. 服务器中的所有数据库。
. 命令表：在执行命令时，根据字符来查找相应命令的实现函数。
. 事件状态。
. 服务器的网络连接信息：套接字地址、端口，以及套接字描述符。
. 所有已连接客户端的信息。
. Lua 脚本的运行环境及相关选项。
. 实现订阅与发布（pub/sub）功能所需的数据结构。
. 日志（log）和慢查询日志（slowlog）的选项和相关信息。
. 数据持久化（AOF 和RDB）的配置和状态。
. 服务器配置选项：比如要创建多少个数据库，是否将服务器进程作为daemon 进程来运
行，最大连接多少个客户端，压缩结构（zip structure）的实体数量，等等。
. 统计信息：比如键有多少次命令、不命中，服务器的运行时间，内存占用，等等。
Note: 为了简洁起见，上面只列出了单机情况下的Redis 服务器信息，不包含SENTINEL 、
MONITOR 、CLUSTER 等功能的信息。
在这一步，程序创建一个redisServer 结构的实例变量server 用作服务器的全局状态，并将
server 的各个属性初始化为默认值。
154 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
当server 变量的初始化完成之后，程序进入服务器初始化的下一步：读入配置文件。
2. 载入配置文件
在初始化服务器的上一步中，程序为server 变量（也即是服务器状态）的各个属性设置了默
认值，但这些默认值有时候并不是最合适的：
. 用户可能想使用AOF 持久化，而不是默认的RDB 持久化。
. 用户可能想用其他端口来运行Redis ，以避免端口冲突。
. 用户可能不想使用默认的16 个数据库，而是分配更多或更少数量的数据库。
. 用户可能想对默认的内存限制措施和回收策略做调整。
等等。
为了让使用者按自己的要求配置服务器，Redis 允许用户在运行服务器时，提供相应的配置文
件（config file）或者显式的选项（option），Redis 在初始化完server 变量之后，会读入配置
文件和选项，然后根据这些配置来对server 变量的属性值做相应的修改：
1. 如果单纯执行redis-server 命令，那么服务器以默认的配置来运行Redis 。
2. 另一方面，如果给Redis 服务器送入一个配置文件，那么Redis 将按配置文件的设置来
更新服务器的状态。
比如说，通过命令redis-server /etc/my-redis.conf ，Redis 会根据my-redis.conf
文件的内容来对服务器状态做相应的修改。
3. 除此之外，还可以显式地给服务器传入选项，直接修改服务器配置。
举个例子，通过命令redis-server --port 10086 ，可以让Redis 服务器端口变更为
10086 。
4. 当然，同时使用配置文件和显式选项也是可以的，如果文件和选项有冲突的地方，那么优
先使用选项所指定的配置值。
举个例子，如果运行命令redis-server /etc/my-redis.conf --port 10086 ，并且
my-redis.conf 也指定了port 选项，那么服务器将优先使用--port 10086 （实际上是
选项指定的值覆盖了配置文件中的值）。
3. 创建daemon 进程
Redis 默认以daemon 进程的方式运行。
当服务器初始化进行到这一步时，程序将创建daemon 进程来运行Redis ，并创建相应的pid
文件。
4. 初始化服务器功能模块
在这一步，初始化程序完成两件事：
. 为server 变量的数据结构子属性分配内存。
. 初始化这些数据结构。
5.5. 服务器与客户端155
Redis 设计与实现, 第一版
为数据结构分配内存，并初始化这些数据结构，等同于对相应的功能进行初始化。
比如说，当为订阅与发布所需的链表分配内存之后，订阅与发布功能就处于就绪状态，随时可
以为Redis 所用了。
在这一步，程序完成的主要动作如下：
. 初始化Redis 进程的信号功能。
. 初始化日志功能。
. 初始化客户端功能。
. 初始化共享对象。
. 初始化事件功能。
. 初始化数据库。
. 初始化网络连接。
. 初始化订阅与发布功能。
. 初始化各个统计变量。
. 关联服务器常规操作（cron job）到时间事件，关联客户端应答处理器到文件事件。
. 如果AOF 功能已打开，那么打开或创建AOF 文件。
. 设置内存限制。
. 初始化Lua 脚本环境。
. 初始化慢查询功能。
. 初始化后台操作线程。
完成这一步之后，服务器打印出Redis 的ASCII LOGO 、服务器版本等信息，表示所有功能
模块已经就绪，可以等待被使用了：
_._
_.-``__ ''-._
_.-`` `. `_. ''-._ Redis 2.9.7 (7a47887b/1) 32 bit
.-`` .-```. ```\/ _.,_ ''-._
( ' , .-` | `, ) Running in stand alone mode
|`-._`-...-` __...-.``-._|'` _.-'| Port: 6379
| `-._ `._ / _.-' | PID: 6717
`-._ `-._ `-./ _.-' _.-'
|`-._`-._ `-.__.-' _.-'_.-'|
| `-._`-._ _.-'_.-' | http://redis.io
`-._ `-._`-.__.-'_.-' _.-'
|`-._`-._ `-.__.-' _.-'_.-'|
| `-._`-._ _.-'_.-' |
`-._ `-._`-.__.-'_.-' _.-'
`-._ `-.__.-' _.-'
`-._ _.-'
`-.__.-'
虽然所有功能已经就绪，但这时服务器的数据库还是一片空白，程序还需要将服务器上一次执
行时记录的数据载入到当前服务器中，服务器的初始化才算真正完成。
156 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
5. 载入数据
在这一步，程序需要将持久化在RDB 或者AOF 文件里的数据，载入到服务器进程里面。
如果服务器有启用AOF 功能的话，那么使用AOF 文件来还原数据；否则，程序使用RDB 文
件来还原数据。
当执行完这一步时，服务器打印出一段载入完成信息：
[6717] 22 Feb 11:59:14.830 * DB loaded from disk: 0.068 seconds
6. 开始事件循环
到了这一步，服务器的初始化已经完成，程序打开事件循环，开始接受客户端连接。
以下是服务器在这一步打印的信息：
[6717] 22 Feb 11:59:14.830 * The server is now ready to accept connections on port 6379
以下是初始化完成之后，服务器状态和各个模块之间的关系图：
5.5.2 客户端连接到服务器
当Redis 服务器完成初始化之后，它就准备好可以接受外来客户端的连接了。
当一个客户端通过套接字函数connect 到服务器时，服务器执行以下步骤：
1. 服务器通过文件事件无阻塞地accept 客户端连接，并返回一个套接字描述符fd 。
5.5. 服务器与客户端157
Redis 设计与实现, 第一版
2. 服务器为fd 创建一个对应的redis.h/redisClient 结构实例，并将该实例加入到服务
器的已连接客户端的链表中。
3. 服务器在事件处理器为该fd 关联读文件事件。
完成这三步之后，服务器就可以等待客户端发来命令请求了。
Redis 以多路复用的方式来处理多个客户端，为了让多个客户端之间独立分开、不互相干扰，
服务器为每个已连接客户端维持一个redisClient 结构，从而单独保存该客户端的状态信息。
redisClient 结构主要包含以下信息：
. 套接字描述符。
. 客户端正在使用的数据库指针和数据库号码。
. 客户端的查询缓存（query buffer）和回复缓存（reply buffer）。
. 一个指向命令函数的指针，以及字符串形式的命令、命令参数和命令个数，这些属性会在
命令执行时使用。
. 客户端状态：记录了客户端是否处于SLAVE 、MONITOR 或者事务状态。
. 实现事务功能（比如MULTI 和WATCH）所需的数据结构。
. 实现阻塞功能（比如BLPOP 和BRPOPLPUSH）所需的数据结构。
. 实现订阅与发布功能（比如PUBLISH 和SUBSCRIBE）所需的数据结构。
. 统计数据和选项：客户端创建的时间，客户端和服务器最后交互的时间，缓存的大小，等
等。
Note: 为了简洁起见，上面列出的客户端结构信息不包含复制（replication）的相关属性。
5.5.3 命令的请求、处理和结果返回
当客户端连上服务器之后，客户端就可以向服务器发送命令请求了。
从客户端发送命令请求，到命令被服务器处理、并将结果返回客户端，整个过程有以下步骤：
1. 客户端通过套接字向服务器传送命令协议数据。
2. 服务器通过读事件来处理传入数据，并将数据保存在客户端对应redisClient 结构的查
询缓存中。
3. 根据客户端查询缓存中的内容，程序从命令表中查找相应命令的实现函数。
4. 程序执行命令的实现函数，修改服务器的全局状态server 变量，并将命令的执行结果保
存到客户端redisClient 结构的回复缓存中，然后为该客户端的fd 关联写事件。
5. 当客户端fd 的写事件就绪时，将回复缓存中的命令结果传回给客户端。至此，命令执行
完毕。
5.5.4 命令请求实例：SET 的执行过程
为了更直观地理解命令执行的整个过程，我们用一个实际执行SET 命令的例子来讲解命令执
行的过程。
158 Chapter 5. 内部运作机制
Redis 设计与实现, 第一版
假设现在客户端C1 是连接到服务器S 的一个客户端，当用户执行命令SET YEAR 2013 时，客
户端调用写入函数，将协议内容*3\r\n$3\r\nSET\r\n$4\r\nYEAR\r\n$4\r\n2013\r\n" 写
入连接到服务器的套接字中。
当S 的文件事件处理器执行时，它会察觉到C1 所对应的读事件已经就绪，于是它将协议文本
读入，并保存在查询缓存。
通过对查询缓存进行分析（parse），服务器在命令表中查找SET 字符串所对应的命令实现函数，
最终定位到t_string.c/setCommand 函数，另外，两个命令参数YEAR 和2013 也会以字符串
的形式保存在客户端结构中。
接着，程序将客户端、要执行的命令、命令参数等送入命令执行器：执行器调用setCommand
函数，将数据库中YEAR 键的值修改为2013 ，然后将命令的执行结果保存在客户端的回复缓存
中，并为客户端fd 关联写事件，用于将结果回写给客户端。
因为YEAR 键的修改，其他和数据库命名空间相关程序，比如AOF 、REPLICATION 还有事
务安全性检查（是否修改了被WATCH 监视的键？）也会被触发，当这些后续程序也执行完毕之
后，命令执行器退出，服务器其他程序（比如时间事件处理器）继续运行。
当C1 对应的写事件就绪时，程序就会将保存在客户端结构回复缓存中的数据回写给客户端，
当客户端接收到数据之后，它就将结果打印出来，显示给用户看。
以上就是SET YEAR 2013 命令执行的整个过程。
5.5.5 小结
. 服务器经过初始化之后，才能开始接受命令。
. 服务器初始化可以分为六个步骤：
1. 初始化服务器全局状态。
2. 载入配置文件。
3. 创建daemon 进程。
4. 初始化服务器功能模块。
5. 载入数据。
6. 开始事件循环。
. 服务器为每个已连接的客户端维持一个客户端结构，这个结构保存了这个客户端的所有
状态信息。
. 客户端向服务器发送命令，服务器接受命令然后将命令传给命令执行器，执行器执行给
定命令的实现函数，执行完成之后，将结果保存在缓存，最后回传给客户端。
5.5. 服务器与客户端159
Redis 设计与实现, 第一版
160 Chapter 5. 内部运作机制
第6 章
关于
本书由huangz 编写。
我在研究Redis 源码并创作本书的过程中获得了极大的快乐，希望你在阅读本书时也能有同感。
评论、问题、意见或建议都可以发表在本书自带的disqus 论坛里，也可以通过豆瓣、微博或
Twitter 联系我，我会尽可能地回复。
要获得本书的最新动态，请关注redisbook 项目。
要了解编写本书时用到的工具（源码管理、文档的生成和托管、图片生成，等等），请阅读这
篇文章。
下载本书离线版本：pdf 格式或html 格式。在线阅读本书请访问redisbook.com 。
161
Redis 设计与实现, 第一版
162 Chapter 6. 关于
第7 章
通过捐款支持本书
如果你喜欢这本《Redis 设计与实现》的话，可以通过捐款的方式，支持作者继续更新本书：比
如为本书修补漏洞、添加更多有趣的章节，或者发行有更多更棒内容的下一版，等等。
捐款地址：https://me.alipay.com/huangz
163
