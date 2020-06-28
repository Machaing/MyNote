# 一、文档结构
	1. redis
		1. 概念
		2. 下载安装
		3. 命令操作
			1. 数据结构
		4. 持久化操作
		5. 使用java客户端操作redis

#二、Redis
	1. 概念：redis是一款高性能的NOSQL系列的非关系型数据库


	2. 下载安装
		1. 官网：https://redis.io/
		2. 中文网：http://www.redis.cn/
		3. 解压直接可以使用：
			* redis.windows.conf
			* redis-cli.exe：redis客户端
			* redis-sver.exe：redis服务器端
 	3. 命令操作：
	 	1. redis的数据结构：
	 	* redis存储的是：key，value格式的数据，其中key都是字符串，value有5种不同的数据结构
			* value的数据结构：
				1） 字符串类型 stirng
				2） 哈希类型 hash	：	map格式 
				3） 列表类型 list	：	linkedlist格式
				4） 集合类型 set		：	
				5)  有序集合类型 sortedset

		2. 字符串类型 string
			1. 存储： set key value
			2. 获取： get key
			3. 删除： del key

		3. 哈希类型 hash 
			1. 存储： hset key field value
				* key是hash变量名 field和value是键值对
			2. 获取： hget key field value
			3. 删除：hdel key field

		4. 列表类型 list：可以添加一个元素到列表头部(左边)或者尾部(右边)
			1. 添加：
				1. lpush key value：将元素加入列表左边
				2. rpush key value：将元素加入列表右边
			2. 获取：
				* lrange key start end： 范围获取
			3. 删除：
				* lpop key： 删除列表最左边的元素，并将元素返回
				* rpop key： 删除列表最右边的元素，并将元素返回
		5. 集合类型 set：不允许重复元素
			1. 存储：sadd key value
			2. 获取：smembers key：获取set集合中所有元素
			3. 删除：srem key value：

		6. 有序集合类型 sortedset：不允许重复元素，且元素有顺序
			1. 存储：zadd key score value
			2. 获取：zrange key start end
			3. 删除：zrem key value

		7. 通用命令
			1. keys *：查询所有的键
			2. type key：获取键对应的value的类型
			3. del key：删除指定的key value
 	4. 持久化
	 	1. redis是一个内存数据库，当redis服务器重启，或者电脑重启，数据丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中
	 	2. redis持久机制
		 	1. RDB：默认方式，不配置。
			 	* 在一定的间隔时间中，检测key的变化情况，然后持久化数据
			
				1. 编辑redis.windows.conf文件
					#Save second changekeyNumberLimit   
					save 900 1
					save 300 10  //300秒后有大于10个keys被改变了就持久化。到一个.rdb文件中
					save 60	 10000
				
				2. 重新启动redis服务器，并指定配置文件名称
		\redis-server.exe redis.windows.conf
			
		 	2. AFO：日志记录的方式，可以记录每一条命令的操作。可以每一次命令操作后，持久哈数据
			 	1. 编辑redis.windows.conf文件，查找 appendonly，更改 no 为 yes即可
				 	appendonly no （关闭aof） -->  appendonly yes (开启aof)
					
					# appendfsync always ： 每一次操作都进行持久化
					appendfsync	everysec ： 每隔一秒进行一次持久化	
			# appendfsync no	 ： 不进行持久化	
		
	5. java客户端 jedis
		* jedis：一款java操作redis数据库的工具
		1. 基本操作
			1. 操作string
			2. hash
			3. list
			4. set
		5. sortset
	
		2. 连接池
			* 使用
				1. 创建jedisPool连接池对象
				2. 调用方法getResource()方法获取Jedis连接


		3. jedis 工具类
			1. 

# 二、截图

### 1.Redis数据库应用架构
![](/img/redis1.JPG)

### 2.Redis数据类型
![](/img/redis2.JPG)

### 3.jedis操作string  
![](/img/redis3.JPG)

### 4.jedis演示 ——可以应用在验证码场景 
![](/img/redis4.JPG)

### 5.jedis操作hash
![](/img/redis5.JPG)

### 6.jedis操作list
![](/img/redis6.JPG)

### 7.jedis操作set 
![](/img/redis7.JPG)

### 8.jedis操作sortset
![](/img/redis8.JPG)

### 9.jedis连接池
![](/img/redis9.JPG)

### 10.jedis配置
![](/img/redis10.JPG)

### 11.jedis 工具类核心代码1
![](/img/redis11.JPG)

### 12.jedis 工具类核心代码2
![](/img/redis12.JPG)



# 三、案例 

## 3.1案例分析
	案例需求：
		1. 提供index.html页面，页面有一个 省份 下列列表
		2. 当 页面加载完成后 发送ajax请求，加载所有省份

### 功能架构图
![](/img/rediscase1.JPG)

### 数据库环境
![](/img/rediscase2.JPG)

### 最终的Project文件目录
![](/img/rediscase3.JPG)

### Project 依赖包
	* 导入到lib文件下。
![](/img/rediscase4.JPG)

### 配置文件文件
	* jedis配置文件 参考上面
	* druid配置文件 参考如下图：
![](/img/rediscase5.JPG)  

### jQuery需要的js文件夹
![](/img/rediscase6.JPG)



## 3.2查询数据库的实现

### 1 domain层实现
![](/img/rediscase7.JPG)

### 2 Dao 层接口
![](/img/rediscase8.JPG)
### Dao 层实现
![](/img/rediscase9.JPG)

### 3 Service 层接口
![](/img/rediscase10.JPG)
### 4 Service 层实现
![](/img/rediscase11.JPG)

### 5 JDBCUtils
![](/img/rediscase12.JPG)
![](/img/rediscase13.JPG)

### 6 Servlet 展示层
![](/img/rediscase14.JPG)

### 7 前端代码
![](/img/rediscase15.JPG)

![](/img/rediscase16.JPG)


## 3.3 redis 缓存优化

### 1 架构更新
![](/img/rediscase17.JPG)

### 2 Dao层： 实现类改写
![](/img/rediscase18.JPG)

![](/img/rediscase19.JPG)

### 3 Service层： 实现类改写
![](/img/rediscase20.JPG)



#总结
	* 使用redis缓存一些不经常发送变化的数据
		* 注意数据库的数据一旦发生变化，则需要更新缓存
			* 数据库的表执行 增删改的相关方法操作，需要将redis缓存数据情况，再次存入
			* 在service对应的增删改方法中，将redis数据删除