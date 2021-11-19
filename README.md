# RedisRoute

#### 介绍
Redis学习路线 (37小时)

#### 学习路线
1. https://coding.imooc.com/class/151.html (作者：阿里redis开发规范)
2. https://coding.imooc.com/class/467.html  (从实战角度使用redis)



#### Redis特点
1. 内存数据块，速度快，支持数据的持久化
2. 支持数据备份(主从模式)以及集群分片存储，哨兵监控机制
3. 支持事务
4. Redis 是纯内存数据库
5. Redis使用的非阻塞IO,IO多路复用，减少了线程切换时对上下文的切换和竞争
6. Redis采用单线程模型，保证了操作原子性

#### Redis 基本数据类型
1. 字符串（SDS）。采用预分配冗余空间的方式减少内存的频繁分配。最大为512M。应用场景，缓存，分布式锁。
2. 散列。用来存储对象信息。key->field:value。filed不能相同。有序。
3. 列表。主要用于热搜榜单，Feed流。支持重复元素。无序。
4. 集合。主要具备并集，交集，差集的功能。不支持插入重复元素。
5. 有序集合。根据权重进行排序,比如游戏积分榜,设置优先级任务列表,学生成绩等。
6. 位图。内部是使用字符串实现的。操作bit
7. HyperLogLog。存在误差，无法取出存储数据。
8. GEO  地理信息定位

#### Redis主要应用场景
1. 缓存系统
2. 计数器
3. 排行榜
4. 实时系统
5. 消息队列系统
6. 社交系统

#### Redis其他功能
1. 慢查询
	1. 先进先出队列
	2. 固定长度
	3. 保存在内存中
	4. 配置
		1. slowlog-max-len。 默认值 128
		2. slowlog-log-slower-than .默认值 10000
	5. 定期持久化慢查询
2. pipeline流水线
	1. pipline每次条数要控制
	2. 非原子性 操作
3. 发布订阅
	1. 无法消息堆积，获取历史消息

#### Redis持久化
1. 持久化概念：将内存数据保存到磁盘
2. 持久化方式
	1. RDB。快照。
		1. 触发方式
			1. save 同步 阻塞。替换旧的RDB文件。
			2. bgsave 异步。fork进程
			3. 自动
			4. 主从复制
			5. debug reload
			6. shutdown
		2. 问题点
			1. 耗时，耗性能
			2. 不可控，容易丢失数据
	2. AOF。写日志。
		1. 触发方式
			1.  always
			2.  everysec
			3.  no
		2. AOF重写
			1. 减少磁盘占用
			2. 加快恢复速度
	3. RDB与AOF的比较

#### Redis主从复制
1. 作用
	1. 数据副本
	2. 扩展读性能
2. 实现方式
	1. slaveof命令
	2. 配置方式

#### Redis哨兵模式
1. 三个定时任务
    1. 每10每个sentinel对master 和slave进行info 
        1. 发现slave节点
        2. 确认主从关系
    2. 每2秒每个sentinel通过对master节点的channel交换信息
        1. 通过_sentinel_:hello 频道交互
        2. 交互对节点的看法和自身信息
    3. 每1秒每个sentinel 对其他sentinel 和 redis 执行ping.
2. 主观下线和客观下线
    1. 主观下线：每个sentinel节点对redis节点失败的偏见
    2. 客观下线：所有sentinel节点对redis节点失败达成共识
3. 领导者选举
    1. 选举通过sentinel is-master-down-by-addr命令都希望成为领导者
4. 故障转移
    1. 从slave节点选出一个合适的节点作为新的master节点
    2. 对上面的slave节点执行 slave of no one 命令 让其成为master节点
    3. 向剩余的slave节点发送命令，让它们成为新master节点的slave节点，复制规则和parallel-sync参数有关
    4. 更新对原来master节点配置为slave，并保持对其的关注，当其恢复后命令它去复制新的master节点
5. 注意点
    1. 客户端初始化连接的Sentinel节点集合，不再是具体的redis节点，但sentinel只是配置中心不是代理。
