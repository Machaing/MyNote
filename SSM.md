
## 环境配置
	1. POM文件依赖
	2. 目录结构完善

## Spring配置
1. applicationContext.xml
	1. 约束
		<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:aop="http://www.springframework.org/schema/aop"
		xmlns:tx="http://www.springframework.org/schema/tx"
		xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/aop
		http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx.xsd">

	2. 开启扫描注解：扫描Service 和 Dao层的注解，忽略Controll层的注解
		<context:component-scan base-package="">
			<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
		</context:component-scan>

	3. 整合Mybatis
		1. 配置连接池
			<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
				<property name="driverClass" value="com.mysql.cj.jdbc.Driver" />
				<property name="jdbcUrl" value="jdbc:mysql:///xxxxx ?serverTimezone=GMT" />
				<property name="user" value="" />
				<property name="password" value="" />
			</bean>	

		2. 配置SqlSessionFactory工厂
		    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		        <property name="dataSource" ref="dataSource"/>
		    </bean>

		3. 扫描dao层的interface文件
		    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		        <property name="basePackage" value=""/>
		    </bean>
		4. 配置事务管理器
		    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		        <property name="dataSource" ref="dataSource"/>
		    </bean>

		5. 配置事务通知
			<tx:advice id="txAdvice" transaction-manager="transactionManager">
				<tx:attributes>
					<tx:method name="find*" read-only="true"/>
					<tx:method name="*" isolation="DEFAULT"/>
				</tx:attributes>
			</tx:advice>

		6. 配置AOP增强
			<aop:config>
				<aop:advisor advice-ref="txAdvice" pointcut="execution(* com.dtsjy.service.Impl.*ServiceImpl.*(..))"/>
			</aop:config>

2. Web.xml 增加Spring的监听器
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext.xml</param-value>
	</context-param>

## SpringMVC 配置
1. springmvc.xml文件配置：
	1. 约束
		<beans xmlns="http://www.springframework.org/schema/beans"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
			xmlns:context="http://www.springframework.org/schema/context"
			xmlns:mvc="http://www.springframework.org/schema/mvc"
			xmlns:tx="http://www.springframework.org/schema/tx"
			xsi:schemaLocation="
			http://www.springframework.org/schema/beans
			http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
			http://www.springframework.org/schema/context
			http://www.springframework.org/schema/context/spring-context-4.0.xsd
			http://www.springframework.org/schema/aop
			http://www.springframework.org/schema/aop/spring-aop-4.0.xsd
			http://www.springframework.org/schema/mvc
			http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
			http://www.springframework.org/schema/tx
			http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
			">
			.........
		</beans>

	2. 扫描Conroller层的注解，别的不扫描
		<context:component-scan base-package="">
			<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
		</context:component-scan>

	3. 配置视图解析器
		<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
			<!--JSP文件所在的目录-->
			<property name="prefix" value="/WEB-INF/pages/"/>
			<!--文件的后缀名-->
			<property name="suffix" value=".jsp"/>
		</bean>

	4. 设置静态资源不过滤
		<mvc:resources mapping="/css/" location="/css/"/>
		<mvc:resources mapping="/images/" location="/images/"/>
		<mvc:resources mapping="/js/" location="/js/"/>
			
	5. 开启对SpringMVC注解的支持
		<mvc:annotation-driven/>

2. web.xml文件配置：
	1. 配置前端控制器：服务器启动时加载springmvc.xml文件
		<servlet>
			<servlet-name>dispatcherServlet</servlet-name>
			<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
			<!--设置初始化参数：创建完dispatcherServlet对象后，加载SpringMVC.xml-->
			<init-param>
				<param-name>contextConfigLocation</param-name>
				<param-value>classpath:</param-value>
			</init-param>
			<!--服务器启动时，让dispatcherServlet对象创建-->
			<load-on-startup>1</load-on-startup>
		</servlet>
		
		<servlet-mapping>
			<servlet-name>dispatcherServlet</servlet-name>
			<url-pattern>/</url-pattern>
		</servlet-mapping>
	2. 解决中文乱码的过滤器
		<filter>
			<filter-name>characterEncoding</filter-name>
			<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
			<init-param>
				<param-name>encoding</param-name>
				<param-value>UTF-8</param-value>
			</init-param>
		</filter>
		<filter-mapping>
			<filter-name>characterEncoding</filter-name>
			<url-pattern>/*</url-pattern>
		</filter-mapping>


## 三层写法
1. dao层
	* interface文件：
	@Repository
	public interface UserDao {
	    // 查找所有用户
	    @Select("select * from user")
	    public List<User> findAll();
	
	    //通过工号id改变用户名字
	    @Update("update user SET name = #{name} where id =#{id}")
	    public void ChangeNameById(User user);
	}
2. Service层
	1. Impl包
		@Service("userService")
		public class UserServiceImpl implements UserService {
		
		    @Autowired
		    private UserDao userDao;
		
		    @java.lang.Override
		    public List<User> findAll() {
		        System.out.println("逻辑层执行:UserServie：findAll");
		        return  userDao.findAll();
		    }
		
		    @java.lang.Override
		    public void ChangeNameById(User user) {
		        userDao.ChangeNameById(user);
		        System.out.println("逻辑层执行:UserServie:findAll");
		    }
		}

	2. 接口
		public interface UserService {
		    // 查找所有用户
		    public List<User> findAll();
		
		    // 根据工号id修改用户名字
		    public void ChangeNameById(User user);
		}

3. controller层
	* interface文件
	@Controller
	@RequestMapping("/user")
	public class UserController {	
	    @Autowired
	    private UserService userService;
	
	    @RequestMapping("/findAll")
	    public String findAll(Model model){
	        System.out.println("表象层：查询所有账户....");
	        List<User>list=userService.findAll();
	        model.addAttribute("list",list);
	        return "list";
	    }
	
	}