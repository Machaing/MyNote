# 一、Mybatis基础

## 1. 框架的定义
	* 软件开发中的一套解决方案，不同的框架解决的是不同的问题。
	* 好处：
		* 框架封装了很多细节，使开发者可以使用极简的方式实现功能。大大提高开发效率

## 2. 三层架构
	1. 表现层
		* 展示数据
	2. 业务逻辑层
		* 处理业务需求
	3. 数据访问层
		* 是和数据库交互的

## 3. 三层架构和ssm框架的对应关系
![](/img/mybatis1.JPG)

## 4. 持久层技术解决方案
	1. JDBC技术：
		* Connection
		* PrepareStatement
		* ResultSet
	2. Spring的JdbcTemplate
		* Spring中的jdbc的简单封装
	3. Apache的DBUtils
		* 它和Spring的jdbcTemplate很像也是对jdbc的简单封装
	
	* 以上这些都不是框架
		* JDBC是规范
		* Spring的jdbcTemplate和Apache的DBUtils都只是工具类
###没有工具包的传统JDBC代码：
![](/img/mybatis2.JPG)
![](/img/mybatis3.JPG)

	* question：
		1. Class.forName("com.mysql.jdbc.Driver") 为什么不用ConnectionRegister
		2. 为什么用preparedStatement？
		3. 给参数复制，索引是从多少开始
		4. 释放资源时，为什么先关闭resultset？

## 5. mybatis概述
![](/img/mybatis4.JPG)

## 6. mybatis 环境搭建
### 数据库环境
![](/img/mybatis5.JPG)
### 无骨架环境创建
![](/img/mybatis6.JPG) 
### 项目名称 参考
![](/img/mybatis7.JPG)
###pom.xml 依赖
![](/img/mybatis8.JPG)
### 对数据库ORM映射 主代码 请写在：main/java目录的一个包：网域名(com).公司名(itheima).项目名。 java名称同表名
![](/img/mybatis9.JPG)
### dao层 注意位置
![](/img/mybatis10.JPG)
### Mybatis confg.xml文件   		:	随意.xml
![](/img/mybatis11.JPG)
### Mybatis 实体类的映射xml文件	:  Dao接口名.xml
![](/img/mybatis12.JPG)

## 7. mybatis 环境搭建 总结与注意事项 
	* Mybatis的环境搭建
		1. 第一步：创建maven工程并导入坐标
		2. 创建实体类和dao接口
		3. 创建Mybatis的主配置文件 SqlMapConfig.xml
		4. 创建映射配置文件 IUserDao.xml
	
	* 注意
		1. 创建IUserDao.xml 和 IUserDao.java时，起该“xxx_dao名"是为了与历史传承知识，即dao层概念保持一致。Mybatis把持久层的操作接口名称和映射文件称为：Mapper。
			* 所以：IUserDao 和 IUserMapper是一样的。
	
		2. 在idea中创建目录的时候，它和包是不一样的，包在创建时：com.itheima.dao它是三级结构。目录在创建时：com.itheima.dao是一级目录。
			* 因此在resource文件夹下创建目录时，不要奢侈地像包创建一样一步到位。
	
		3. Mybatis的映射配置文件位置 “必须” 和dao层接口的包结构相同。
	
		4. 映射位置文件的mapper标签namespace属性的取值 “必须” 是dao接口的全限定类名。
	
		5. 映射配置文件的操作配置(select),id属性的取值 “必须” 是dao接口的方法名
	
		* 当遵循了3 4 5点之后，我们在开发中就无须再写dao的实现类。
		* 一般返回的是“实体”数据。

## 8. 案例
### 入门案例
![](/img/mybatis13.JPG)
![](/img/mybatis14.JPG) 

### 入门案例解析
![](/img/mybatis15.JPG) 

### 总结
1. 案例步骤：
	1. 读取配置文件
	2. 创建SqlSessionFactory工厂
	3. 创建SqlSession
	4. 创建Dao接口的代理对象
	5. 执行dao的方法
	6. 释放资源

2. 注意：
	1. 不要忘记在映射配置中告知Mybatis要封装到哪个实体类中
		* 配置方式：指定实体类的全限定类名

3. Mybatis基于注解的入门案例：
	1. 把IUserDao.xml移除，在dao接口的方法上使用@Select注解，并指定SQL语句
	2. 修改SqlMaoConfig.xml中的mapper配置，使用class属性指定dao接口的全局限定类名
		* (xxxx.xml 放在maven 主源码配置文件下，一般为main/resource  file 下) 

![](/img/mybatis16.JPG)
![](/img/mybatis17.JPG)

4. 明确：
	* 实际开发中，遵循简便原则，故不写dao实现类， “不管” 使用xml还是注解配置。
	* 但是Mybatis还是支持写dao实现类的。
	* 深刻理解 IUserDao.xml 文件中 namespace的作用。
![](/img/mybatis18.JPG)
![](/img/mybatis19.JPG)










# 二、Mybatis 的解析

### 基本的总体流程
![](/img/mybatis20.JPG)
### 配置文件分析
![](/img/mybatis21.JPG)
### 代理分析
![](/img/mybatis22.JPG)


