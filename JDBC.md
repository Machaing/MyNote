##JDBC
	1. 概念：java DataBase Connectivity java 数据库连接，java语言操作数据库
		* JDBC本质：其实是官方(sun公司)定义的一套操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口(JDBC)编程，真正执行的代码是驱动jar包中的实现类。
	
	2. base：
		* 步骤：
			1. 导入驱动jar包 mysql-connector-java-5.1.18-bin.jar
				1.复制 mysql-connector-java-5.1.18-bin.jar 到项目libs目录下
				2.右键-->add as Library

			2. 注册驱动
			3. 获取数据库连接对象 Connection
			4. 定义sql
			5. 获取执行sql语句的对象 Statement
			6. 执行sql，接受返回结果
			7. 处理结果
			8. 释放资源

	3. 各个对象
		1.  DriverManager：驱动管理对象
			* 功能： 
				1. 注册驱动：告诉程序该使用哪个数据库驱动jar
				--以后完善	

				注意：mysql5之后的驱动jar包可以省略注册驱动的步骤
					
				2. 获取数据库连接 
					* 方法：static Connection getConnection(string url, String user, String password)
					* 参数：
						* url：指定连接的路劲
							* 语法：
							* 例子：
							* 细节：如果连接的是本机mysql服务器，并且mysql服务默认端口是3306，则url可以简写为：jdbc:mysql:///数据库名称
						* user：用户名
						* pasword：密码
				
		2.  Connection：数据库连接对象
			1. 功能：
				1. 获取执行sql的对象
					* Statement createStatement()
					* PreparedStatement prepareStatement(String sql) 
			 	2. 管理实务：
				 	* 开启事务：setAutoCommit(boolean autoCommit)：调用该方法设置参数为false，即开启事务
				 	* 提交事务：commit()
				 	* 回滚事务：rollback()
				 
		3.  Statement：执行sql的对象
			1. 执行sql
				1. Boolean execute(String sql)：可以执行任意的sql --了解即可
				2. int executeUpdate(String sql)：执行DML(insert、update、delete)语句、DDL(create、alter、drop)
					* 返回影响的行数  
			 	3. ResultSet executeQuery(String sql)：执行DQL(select)语句
			 	
		4.  ResultSet：结果集对象
			* next()：游标移向下一行
			* getXxx(参数)：获取数据类型
				* Xxx：代表数据类型  如：int getInt()，string getString() 
				* 参数：
					1. int：代表列的编号，从1开始   	如：getString(1)
					2. String：代表列名。   			如： getDouble("balance") 
					
		5.  PreparedStatement：执行sql的对象，比Statement功能强大些  
			1. SQL注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接。会造成安全性问题
				1. 输入用户随便,输入密码：a' or 'a' = 'a
				2. sql：select * from user where username = 'fhasd' and password = 'a' or 'a' = 'a'  
		
			2. 解决：使用 PreparedStatement (extends Statement)
			3. 预编译的sql：参数使用？作为占位符
			4. 步骤：
				1. 导入驱动jar包 mysql-connector-java-5.1.18-bin.jar
				2. 注册驱动
				3. 获取数据库连接对象 Connection
				4. 定义sql
					* 注意：sql的参数使用？作为占位符号。如： select * from user where username = ? and password =?;
				5. 获取执行sql语句的对象 PreparedStatement    如： Connection.prepareStatement(String sql)
				6. 给？赋值：
					* 方法：setXxx(参数1，参数2)
						* 参数1：？的位置编号 从1开始
						* 参数2：？的值
				7. 执行sql，接受返回结果，
					* 不需要传递sql语句
				8. 处理结果
				9. 释放资源
			5. 注意： 后期都会使用PrepareStatement来完成增删改查的所有操作。

## 抽取JDBC工具类： JDBCUtils
	* 目的：简化书写
	* 分析：
		1. 注册驱动也抽取
		2. 抽取一个方法获取连接对象
			* 需求：不想传参数麻烦，还得保证工具类的通用性。
			* 解决：配置文件
				* 静态加载读取 static{
					........	
				  }
				* jdbc.properties
					url=
					user=
					password= 
		3. 抽取一个方法释放资源

## 登录逻辑案例
	

## JDBC控制事务
	1. 事务：一个包含多个步骤的业务操作。如果这个业务操作被事务管理，则这个步骤要么同时成功，要么同时失败
	
	2. 操作：
		1. 开启事务
		2. 提交事务
		3. 回滚事务
		
	3. 使用Connection对象来管理事务
		* 开启事务：setAutoCommit(boolean autoCommit)：调用该方法设置参数为false，即开启事务
			* 在执行sql之前开启事务
		* 提交事务：commit()
			* 当前所有sql都执行提交事务
		* 回滚事务：rollback
			* 在catch中回滚事务
		
	4. 一般规范写法：
		try{
			conn = JDBCUtils.getConnection();
			conn.setAutoCommit(false);
			......
			conn.commit		
		}catch(Exception e){
			try{
				if(conn != null)
					conn.rollback();
			}catch(SQLException e1){
				e1.printStackTrace();
			}
			e.printStackTrace();
		}

## 照片截图
![](/img/jdbc1.JPG)
 
![](/img/jdbc2.JPG)

![](/img/jdbc3.JPG)   