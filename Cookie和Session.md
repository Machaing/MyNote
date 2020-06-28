#概述
	1. 会话技术
		1. Cookie
		2. Session	
 	2. JSP：基础

#会话技术
	1. 会话：一次会话中包含多次请求和响应

		* 一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到一方断开为止。
		
	2. 功能：在一次会话的范围内的多次请求间，共享数据

	3. 方式：

		1. 客户端会话技术：Cookie      //数据存储在客户端
		2. 服务器端会话技术：Session   //数据存储在服务端

#Cookie
	1. 概念：客户端会话技术，将数据保存到客户端、

	2. 快速入门：
		* 使用步骤：
			1. 创建Cookie对象，绑定数据   //客户端
				* new Cookie(String name, String value)
			2. 发送Cookie对象、			//客户端
				* respone.addCookie(Cookie cookie)
			3. 获取Cookie，拿到数据		//服务端
				* Cookie[] request.getCookies()	

	3. 实现原理
		* 基于响应头set-cookie和请求头cookie实现


	4. cookie的细节
		1. 一次可以发送多个cookie吗？
			* 可以
			* 可以创建多个Cookie兑现，使用response调用多次addCookie方法发送cookie即可
		2. cookie在浏览器中保存多久时间？
			1. 默认情况下，当浏览器关闭后，Cookie数据被销毁
			2. 持久化存储：
				* setMaxAge(int seconds) //cookie对象下的方法
					1. 正数：将Cookie数据写到硬盘的文件中。持久化存储。cookie存活时间 //不关闭浏览器也会过期
					2. 负数：默认值——关闭就销毁
					3. 0：删除cookie信息，提供了一种方式让浏览器控制数据。
 		3. cookie能不能存中文？
	 		* 在tomcat 8 之前 不能
		 		* 需要将中文数据转码 一般采用URL编码(%E3)
	 		* 在tomcat 8 之后 能
		4. cookie获取范围多大？
			1. 假设在一个tomcat服务器中，部署了多个web项目，那么在这些web项目中cookie能不能共享？
				* 默认情况下cookie不能共享

				* setPath(String path):设置 cookie 的获取范围，默认情况下，设置为当前的虚拟目录
					* 如果要共享，则可以将 path 值设置为 "/"
					*
			2. 不同的tomcat服务器间cookie共享问题
				* setDomain(String path):如果设置一级域名相同，name多个服务器之间cookie可以共享
					* setDomain(".baidu.com")，那么tieba.baidu.com 和 news.baidu.com中cookie可以用共享

	5. cookie特点和作用
		1. cookie存储数据在客户端浏览器，容易丢失和串改（相比服务器）
		2. 浏览器对于单个cookie的大小有限制(4kb) 以及 对同一个域名下的cookie数量也有限制(20个)
				
		* 作用：
			1. cookie一般用于存储少量的不太敏感的数据
			2. 在不登录的情况下，完成服务器对客户端的身份识别

# Session
	1. 概念：服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中。
	2. 快速入门：
		1. 获取HttpSeesion对象:
			* HttpSession session = request.geSession();		
		2. 使用HttpSeesion对象:
			* Object getAttribute(String name)  		
			* void setAttribute(String name, Object value)  
			* void removeAttribute(String name)  	

		3. 原理
			*Session的实现依赖于Cookie

		4. 细节
			1. 当客户端关闭后，服务器不关闭，两次获取session是否为同一个？
				* 默认情况下，不是。
				* 如果需要相同，则可以创建Cookie，键为JSESSIONID，设置最大存活时间，让cookie持久保存化
					* Cookie c = new Cookie("JSESSIONID",sessin.getId());
					* c.setMaxAge(60*60);
					* response.addCookie(c);
						
			2. 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？
				* 不是同一个（原理上服务器关闭掉，session对象被销毁）  但是要确保数据不丢失
				* session的钝化
					* 在服务器正常关闭之前，将session对象序列化到硬盘上
				* session的活化
					* 在服务器启动后，将session文件转化为内存中的session对象即可

				*tomcat自动完成session钝化和活化，保存在work文件中。IDEA启动的Tomcat能钝化，却不能活化。
					不能活化的原因是：重新启动IDEA项目时，会把IDEA下的work文件夹 删掉 再重新 创建
			
			3. session什么时候被销毁？
				1. 服务器关闭
				2. Seesion对象调用invalidate()自杀.
				3. session的默认失效时间 30分钟
					* 选择性配置修改：在 \apache-tomcat-8.5.43\conf\web.xml 文件寻找：
						<session-config>
					        <session-timeout>30</session-timeout>
					    </session-config>
						修改为需要的失效时间即可
					* apache-tomcat-8.5.43\conf\web.xml 文件为使用Tomcat作为服务器软件的所有web项目的副配置文件。

		5. session的特点
			1. session用于存储一次会话的多次请求数据，存在服务端
			2. session可以存储任意累心，任意大小的数据

			* session与Cookie的区别
				1. Seesion存储数据在服务端，Cookie在客户端
				2. Session没有数据大小限制，Cookie有
				3. Session数据安全，Cookie相对于不安全