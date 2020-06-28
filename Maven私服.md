## nexus私服安装和启动（经常不用）
1. 官网下载 压缩包

2. 解压安装
	* 解决后会有两个文件：
		1. nexus-版本：	程序
		2. sonatype-work：	私服仓库 

	* 安装：
		1. 进入 	nexus-版/bin/  下启动nexus.bat
			* 推荐用 管理员身份 打开 cmd 再运行该bat文件
				* nexus.bat install
				* nexus.bat uninstall
			* 绝对文件路径最好是 全英文路径

	* 配置 nexus-版本/conf/nexus.properties

![](/img/nexus1.JPG)
![](/img/nexus2.JPG)


## 在Maven中 上传项目到 私服（重要）
	1. 配置本地maven的 conf/setting.xml文件
![](/img/nexus3.JPG)

	2. 配置Idea maven 项目的POM.xml文件
![](/img/nexus4.JPG)

	3. 执行生命周期deploy

## 在Maven中 下载依赖到 本地（重要）
	1. 配置本地maven的 conf/setting.xml文件
![](/img/nexus5.JPG)
![](/img/nexus6.JPG)

	2. 写依赖坐标 和 运行周期指令即可 

## 安装 第三方 jar包 到本地仓库（次要）
	1. 进入jar包所在目录 运行
		* mvn install ： install-file -DgroupId=xxxxxx -DartifactId=xxxxx -Dversion=x.x.x -Dfile=jar包文件名 -Dpackaging=jar

	2. 任意目录
		* mvn install ： install-file -DgroupId=xxxxxx -DartifactId=xxxxx -Dversion=x.x.x -Dfile=路径\jar包文件名 -Dpackahing=jar



## 安装 第三方 jar包 到本地仓库（次要）

	0. 配置本地maven的 conf/setting.xml文件（同上 为了能进入私服进程）	

  	1. 进入jar包所在目录 运行
		* mvn install ： install-file -DgroupId=xxxxxx -DartifactId=xxxxx -Dversion=x.x.x -Dfile=jar包文件名 -Durl=http://localhost:xxx/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty

	2. 任意目录
		* mvn install ： install-file -DgroupId=xxxxxx -DartifactId=xxxxx -Dversion=x.x.x -Dfile=路径\jar包文件名 -Durl=http://localhost:xxx/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty

