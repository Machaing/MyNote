## 数据库连接池
	1. 概念:连接池是一个容器(集合)，存放数据库连接的容器
		当系统初始化好后，容器就被创建，容器中会申请一些连接对象，当用户来访问数据时，从容器中获取连接对象，用户访问之后，会将连接对象归还给容器。

	2. 好处：
		1. 节约资源
		2. 用户访问高效
	
	3. 实现
		1. 标准接口：DataSource   javax.sql包下的
			1. 方法：
				* 获取连接：getConnection
				* 归还连接：Connection
		2. 一般我们不去实现它，有数据库厂商来实现
			1. C3P0：数据库连接池技术
			2. Druid：数据库连接池实现技术，由阿里巴巴提供的
	
	4. C3P0：数据库连接池技术
		* 步骤：
			1. 导入jar包：
				* c3p0-0.9.2.1.jar
				* mchange-commons-java-0.2.3.4.jar		
			2. 定义配置文件：
				* 名称 c3p0.properties 或者 c3p0-config.xml
				* 路径:直接将文件放在src目录即可。
			
			3. 创建核心对象 数据库连接池对象 
				* DataSource ds = new ComboPooledDataSource();
				* DataSource ds = new ComboPooledDataSource(String configName);
			
			4. 获取连接
				* Connection conn = ds.getConnection();
				
			5. 归还连接
				* conn.close();
				
	5. druid
		1. 步骤：
			1. 导入jar包：druid-1.0.9.jar
			2. 定义配置文件： 
				* 是properties形式的
				* 可以叫任意名称，可以放在任意目录下
			
			3. 加载配置文件。 Properties
			4. 获取数据库连接池对象：通过工厂来获取  DruidDataSourceFactory
			5. 获取连接：getConnection
		
	6. 定义工具类（新）
		1. 定义一个类JDBCUtils
		2. 提供静态代码块加载配置文件，初始化连接池对象
		3. 提供方法
			1. 获取
			
			 
## Spring JDBC
	* Spring框架对JDBC的简单封装。提供了一个JDBCTemplate对象简化JDBC的开发
	* 步骤：
		1. 导入jar包
		2. 创建jdbcTemplate对象。依赖于数据源DataSource
			* jdbcTemplate Template = new jdbcTemplate(ds);
		3. 调用jdbcTemplate的方法来完成CRUD的操作
			* update()：			执行 DML 语句。增、删、改语句
			* queryForMap：		查询结果将结果集封装为 map 集合
				* 注意：这个方法查询的结果集长度只能是 1
			* queryForList：		查询结果将结果封装为 list 集合
				* 注意：将每一条记录封装为一个map集合，再将 Map 集合装在到List
			* query()：			查询结果，将结果封装为 javaBean 对象
				* 一般我们使用BeanPropertyRowMapper
				* new BeanPropertyRowMapper<类型>(类型.class)  
			* queryForObject：	查询结果，将结果封装为对象			
			
				
			1. 添加：
				* String sql = "insert into emp(id, ename, dept_id) values(?, ?, ?)"
				* int count = template.update(sql, 1015, "郭靖", 10);
			2. 删除：
				* String sql = "delete from emp where id = ?"
				* int count = template.update(sql, 1015);
			3. 改：略
			4. 查询
				/*1. queryForMap() 和 queryForList()*/
			
					* String sql = "select * from emp where id = ?"; 
					* Map<String, Object> map = template.queryForMap(sql, 1001); //System.out.println(map);
					* //返回{字段名 = 值}
					* 注意：
						* String sql = "select * from emp where id = ? id =？";
						* 会失败， 结果集长度只能为 1
	
					* List<Map<String, Object>> list = template.queryForList(sql, 1001);
					* for (Map<String, Object> map : list){
					*     System.out.println(map)
					* }

				/*2. 查询所有记录，将其封装为Emp对象的List集合*/
			
					* String sql = "select * from emp "; 
					* template.query(sql,new BeanPropertyRowMapper<Emp>(Emp.class)); 
				
				/*3. 查询总记录数*/
								
					* String sql = "select count(id) from emp "; 
					* Long total = template.queryForObject(sql, Long.class);  //使用在聚合函数场景

## 照片截图

![](/img/C3P01.JPG)

![](/img/druid1.JPG)

![](/img/druid2.JPG)

![](/img/druid3.JPG)