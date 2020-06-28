## 一、Maven概述
	功能：
		* 依赖管理：maven对jar包的管理过程
			* 每个项目的jar包在Maven的位置被称为坐标，放在工程项目根目录中的pom.xml文件

		* 一键构建：一个命令可以请轻松完成整个工作（自己集成了Tomcat）
			* 构建：编译 测试 运行 打包 安装 部署
			
			* Maven将构建分了以下阶段：
				* 清理 编译 测试 报告 打包 部署
				* 每个阶段 使用了一个命令
### maven的应用场景：
![](/img/maven1.JPG)







## 二、Maven安装

### 1. 官网下载bin.zip文件（百度搜索Maven即可）：	
![](/img/maven2.JPG)
#### 注意:解压在“无中文”的目录中。

### 2. 环境变量配置

新建MAVEN_HOME环境变量
![](/img/maven3.JPG)

添加MAVEN_HOME到path变量中
![](/img/maven4.JPG)

cmd 输入 mvn -v
![](/img/maven5.JPG)

### 3.注意：JDK的环境变量设置好是前提！！！





## 三、Maven目录
![](/img/maven6.JPG)
![](/img/maven7.JPG)
![](/img/maven8.JPG)
![](/img/maven9.JPG)






## 四、Maven仓库关系&本地仓库设置

###仓库关系：
![](/img/maven10.JPG)

###本地位置修改仓库：
![](/img/maven11.JPG)	







## 五、Maven工程标准目录&常用命令

### 标准目录
![](/img/maven12.JPG)
 
### 命令0-进入maven项目的根目录
![](/img/maven13.JPG)

### 命令1-mvn Clean
	* 作用：
		* Target目录是项目构建好生成的，存储了源代码和测试代码编译生成的字节码。
		* 其编译过程依赖于本地环境，若拿到别人的一个项目，需先clean一下。因为每个人的运行环境不是都一样。
![](/img/maven14.JPG)

### 命令2-mvn compile
	* 作用：
		* 编译源代码（src/main/java下的代码），生成字节码文件在Target/calsses文件夹下。
![](/img/maven15.JPG)

### 命令3-mvn test
	* 作用：
		* 编译源代码（src/main/java下的代码），生成字节码文件在Target/calsses文件夹下。
		* 编译测试代码（src/test/java下的代码），生成字节码文件在Target/test-calsses文件夹下。

### 命令4-mvn package
	* 作用：
		* 编译源代码（src/main/java下的代码），生成字节码文件 ——>Target/calsses文件夹下。
		* 编译测试代码（src/test/java下的代码），生成字节码文件	  ——>Target/test-calsses文件夹下。
		* 打成war包， ——>Target文件夹下。  名字：项目名.版本-SNAPSHOT.war
	* 可以设置打包方式（不一定是war包，war包是Tomcat服务器需要的）
		* 工程目录/pom.xml 文件下的  <packing>war</packing> 修改
	
### 命令5-mvn install
		* 编译源代码（src/main/java下的代码），生成字节码文件 ——>Target/calsses文件夹下。
		* 编译测试代码（src/test/java下的代码），生成字节码文件	  ——>Target/test-calsses文件夹下。
		* 打成war包， ——>Target文件夹下。  名字：项目名.版本-SNAPSHOT.war
		* 把war包 放到本地仓库	——>本地仓库目录/源代码包名/maven/项目名字(根目录名字)/版本-SNAPSHOT/....war 







## 六、Maven生命周期&概念模型图

### Maven生命周期：
	* 一般我们只关心默认生命周期
	* 每个开发阶段，用相应时期的命令
![](/img/maven16.JPG)

### Maven概念模型图
![](/img/maven17.JPG)







## 七、IDEA集成Maven
	*前提：
		* 已经正确安装本地maven 和设置环境变量
		* 已经正确安装本地java  和设置环境变量

### 1.1 Maven工程配置1
![](/img/maven18_1.JPG)
![](/img/maven18_2.JPG)
![](/img/maven18.JPG)

### 1.2 Maven工程配置
	* 使用maven提供的骨架创建工程，是需要联网下载插件。
	* 若想在不联网的情况下 + 之前下载过相关工程的插件，则会在本地找到插件，不用从网络上下载
![](/img/maven19.JPG)

### 2.1 使用骨架创建maven的 java 工程
![](/img/maven20.JPG)
![](/img/maven21.JPG)
![](/img/maven22.JPG)
![](/img/maven23.JPG)
![](/img/maven24.JPG)
![](/img/maven25.JPG)

### 2.2 不使用骨架创建maven的 java 工程
![](/img/maven26.JPG)
![](/img/maven27.JPG)

### 3. 使用框架创建maven的web工程

![](/img/maven28.JPG)
![](/img/maven29.JPG)





## 八、maven工程Servlet实例
 
### 1. 指定web资源，使创建的任意文件夹能 右键new一个web文件，如jsp文件
	* 严格的IDEA对每个文件夹做出限制，为了让web文件都放在统一的文件夹下，如web-app文件夹（使用工程创建的项目）

![](/img/maven30.JPG)

### 2.1 导入项目依赖
![](/img/maven31.JPG)
![](/img/maven32.JPG)
![](/img/maven33.JPG)

### 2.2 解决jar包冲突问题
	1. 问题：
		* jar冲突：Tomcat的lib文件夹下也有一些支持javaee的jar包
			* Servlet-api.jar
			* jsp-api.jar
		* 那么从仓库导入的jar包 也有和Tomcat lib 下的jar包一样时，启动后会发送冲突，报错。
		* 打包war包到（war包里有 Servlet-api.jar）到tomcat 是相同的报错原理。
	1. 解决：
		* 在pom.xml文件下，添加<scope>xxxx</scope>标签
		* 默认范围是 compile	
![](/img/maven34.JPG)
![](/img/maven39.JPG)

### 2.3 IDEA下运行生命周期 指令
![](/img/maven35.JPG)
![](/img/maven36.JPG)

### 2.4 利用模板修改工程运行环境（Tomcat版本和编译JDK版本）
![](/img/maven37.JPG)
![](/img/maven38.JPG) 


### 2.5 Maven下数据库连接测试
1. dao层 接口
![](/img/maven40.JPG)
2. domain 封装类
![](/img/maven41.JPG)
3. 依赖
![](/img/maven42.JPG)
4. dao层 实现类
![](/img/maven43.JPG)
![](/img/maven44.JPG)
5. 运行测试
![](/img/maven45.JPG)
6. 测试环境
![](/img/maven46.JPG)
