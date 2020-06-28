# JSON
## 一、JSON基础
	1. 概念：javaScrip object Notation 
		Person p = new Person();
		p.setName();
		p.setAge();
		p.setGender();
	
		var p = {"name":"张三","age":23,"gender":男}；
	
		* json现在多用于存储和交换文本信息的语法。
		* 进行数据的传输
		* json 比 xml 更小、更快、更易解析。

	2. 语法：
		1. 基本规则
			* 数据在名称/值对中：json数据是由键值对构成的
				* 键用 单引号 或 双引号 或不使用引号
				* 值的取值类型：
					1. 数字(整数或浮点数)
					2. 字符串(在双引号中)
					3. 逻辑值(true或 false)
					4. 数组(在方括号){"persons":[{},{}]}
					5. 对象(在花括号){"address":{"province":"广东"...}
					6. null
				* 数据由括号分隔：多个键值对由逗号分隔
				* 花括号保存对象：多使用{}定义json格式
				* 方括号保存数组：[]
		2. 获取数据：
			1. json对象.键名
			2. json对象.["键名"]
			3. 数组对象.[索引]

### 嵌套获取值演示
![](/img/json1.JPG)
### 遍历获取值演示
![](/img/json2.JPG)

## 二、JSON解析器——jackson	

	1.Json数据和java对象的相互转换
		* JSON解析器
			* 常见的解析器：
				* Jsonlib 	官方接口的一个实现类
				* Gson		google
				* fastjson 	阿里
				* jackson	springMVC框架内置的解析器 	
		
		1. java对象转换JSON
			* 使用步骤：
				1. 导入jackson的相关jar包
				2. 创建jackson核心对象 objectMapper
				3. 调用ObjectMapper的相关方法进行转换
					1. 转换方法
						* writeValue(参数1，obj)
							参数1：
								File：将obj对象转换为JSON字符串，并保存到指定的文件中
								Writer：将obj对象转换为JSON字符串，并将json数据填充到字符串输出流中
								OutPutStream：将obj对象转换为JSON字符串，并将json数据填充到字节输出流中
						* writeValueAsString(obj)：将对象转为json字符串

					2. 注解：
						1. @JsonIgnore：排除属性。
						2. @jsonFormat：属性值的格式化

					3. 复杂java对象转换：
						1. List：数组
						2. Map：对象格式一样
							
		2. JSON转化为Java对象
			* 使用步骤：
				1. 导入jackson的相关jar包
				2. 创建jackson核心对象 objectMapper
				3. 调用ObjectMapper的相关方法进行转换 
					1. readValue(json字符串数据，Class)
			
		
### jackson——Java对象 =》json数据
![](/img/json3.JPG)

### @JsonIgnore：排除属性  
![](/img/json4.JPG)

### @jsonFormat：日期属性值的格式化 
![](/img/json5.JPG)
![](/img/json6.JPG)

### List对象转换
![](/img/json7.JPG)
![](/img/json8.JPG)
![](/img/json9.JPG)

### Map对象转换
![](/img/json10.JPG)
![](/img/json11.JPG)

### 字符串转JSON
![](/img/json12.JPG)

## 三、案例
	#校验用户名是否存在
		1. 服务器响应的数据，在客户端使用时，要想当做json数据格式使用
			1. $.get(type)：将最后一个参数type指定为"json"
			2. 在服务器端设置MIME类型
				response.setContentType("application/json;charset=utf-8");
	
### 前端核心代码
![](/img/json13.JPG)
### 后端核心代码
![](/img/json14.JPG)

![](/img/json15.JPG)