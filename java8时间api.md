## 一、jdk7时间API存在的问题

- 线程安全——Date和Calendar类不是线程安全的，这让开发人员不得不处理难以调试并发性的问题，并编写额外的代码来处理线程安全。相反，Java 8中引入的新的日期和时间API是不可变的，并且是线程安全的，因此不会出现并发性问题。


- API的设计和易于理解——java 7日期和日历API的设计很差，没有足够的方法来执行日常操作。新的日期/时间API以ISO为中心，并遵循日期、时间、持续时间和周期的一致域模型。有各种各样的实用方法支持最常见的操作。


- ZonedDate和Time—开发人员必须编写额外的逻辑来使用旧API处理时区逻辑，而在新API中，可以使用Local和ZonedDateTime API处理时区。



### 1.1 JDK7时间API线程安全详细说明

####  1.1.1 SimpleDateFormat 和 DateFormat 不是线程安全的。

​	SimpleDateFormat 是 Java 中常用类，用来对日期字符串进行解析和格式化输出 SimpleDateFormat 继承 DateFormat。在 DateFormat 定义了 protected 属性的 Calendar 成员变量：calendar。
​	在 DateFormat 的成员函数 format() 中有如下代码片段：

```java
private StringBuffer format(Date date, StringBuffer toAppendTo,
                                FieldDelegate delegate) {
        // Convert input date to time field list
        calendar.setTime(date);

    	boolean useDateFormatSymbols = useDateFormatSymbols();
	........
}
```
在多线程情况下会出现以下情况

```
1.线程1调用format方法，改变了calendar这个字段。
2.中断。
3.线程2开始执行，它也改变了calendar。
4.中断。
5.线程1回来，此时，calendar已然不是它所设的值，而是线程2改变后的值。如果多个线程同时争抢calendar对象，则会出现各种问题，时间不对，线程挂死等等。
```

####  1.1.2 解决方法法1——使用局部变量，方法独享

```java
package com.peidasoft.dateformat;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateUtil {
    
    public static  String formatDate(Date date)throws ParseException{
         SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        return sdf.format(date);
    }
    
    public static Date parse(String strDate) throws ParseException{
         SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        return sdf.parse(strDate);
    }
}
```

####  1.1.3 解决方法法2—— 使用synchronized同步变量

```java
package com.peidasoft.dateformat;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateSyncUtil {

    private static SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
      
    public static String formatDate(Date date)throws ParseException{
        synchronized(sdf){
            return sdf.format(date);
        }  
    }
    
    public static Date parse(String strDate) throws ParseException{
        synchronized(sdf){
            return sdf.parse(strDate);
        }
    } 
}
```

####  1.1.4 解决方法法3—— **使用ThreadLocal**，线程独享

```java
package com.peidasoft.dateformat;

import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class ConcurrentDateUtil {

    private static ThreadLocal<DateFormat> threadLocal = new ThreadLocal<DateFormat>() {
        @Override
        protected DateFormat initialValue() {
            return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        }
    };

    public static Date parse(String dateStr) throws ParseException {
        return threadLocal.get().parse(dateStr);
    }

    public static String format(Date date) {
        return threadLocal.get().format(date);
    }
}
```

#### 1.1.5三种方法比较

| 方法名 | 特点说明                                                     |
| ------ | :----------------------------------------------------------- |
| 方法1  | 在需要用到SimpleDateFormat 的地方新建一个实例，不管什么时候，**将有线程安全问题的对象由共享变为局部私有都能避免多线程问题**，不过也加重了创建对象的负担。在一般情况下，这样其实对性能影响比不是很明显的。 |
| 方法2  | 当线程较多时，当一个线程调用该方法时，其他想要调用此方法的线程就要block，多线程并发量大的时候会对性能有一定的影响。 |
| 方法3  | 使用ThreadLocal, 将共享变量变为线程独享变量，线程独享肯定比方法独享在并发环境中能减少不少创建对象的开销。如果对性能要求比较高的情况下，一般推荐使用这种方法。 |

​		性能表现：3 > 2 > 1， **但是就算是最慢的方法一性能也不差** ，很难成为系统的瓶颈。 从简单的角度来说，建议使用方法一或者方法二，如果在必要的时候，追求那么一点性能提升的话，可以考虑用方法三，用ThreadLocal做缓存。 

#### 1.1.6 启发

​		这个问题背后隐藏着一个更为重要的问题——**无状态**：

​		无状态方法的好处之一，就是它在各种环境下，都可以安全的调用。

​		衡量一个方法是否是有状态的，就看它是否改动了其它的东西，比如全局变量，比如实例的字段。format方法在运行过程中改动了SimpleDateFormat的calendar字段，所以，它是有状态的。 

在开发和设计系统时注意:

**1.自己写公用类的时候，要对多线程调用情况下的后果在注释里进行明确说明**

**2.对线程环境下，对每一个共享的可变变量都要注意其线程安全性**

**3.我们的类和方法在做设计的时候，要尽量设计成无状态的**







## 二、java8时间API介绍

### 2.1 定义声明

#### 2.1.1 现在本地时间——now()

```java
LocalTime localTime = LocalTime.now();                  //本地现在时间
LocalDate localDate = LocalDate.now();                  //本地现在日期
LocalDateTime localDateTime = LocalDateTime.now();      //本地现在日期时间 (没有时区)
ZonedDateTime zonedDateTime = ZonedDateTime.now();      //本地现在日期时间时区
```

#### 2.1.2 自定义时间——pares() | of()

**1.LocalDate  | LocalDateTime | LocalTime**

```java
System.out.println(LocalDate.of(2019,11,14));
System.out.println(LocalDate.parse("2019-11-15"));
System.out.println(LocalTime.parse("06:30"));System.out.println(LocalTime.of(6, 30));
System.out.println(LocalDateTime.of(2019,Month.NOVEMBER, 14, 9, 30));
System.out.println(LocalDateTime.parse("2019-11-14T09:31:00"));     
```

***注意：1.LocalDatetime parse的日期和时间之间必须加T 否则解析报错**

**2.ZonedDateTime**

```java
System.out.println(ZonedDateTime.of(LocalDateTime.of(2019,Month.NOVEMBER,14,9,30),ZoneId.of("Europe/Paris")));       
System.out.println(ZonedDateTime.of(LocalDateTime.parse("2019-11-15T00:06:30"),ZoneOffset.of("+08:00")));
System.out.println(ZonedDateTime.of(LocalDateTime.parse("2019-11-15T00:06:30"),ZoneId.of("Europe/Paris")));

System.out.println(ZonedDateTime.parse("2019-11-14T10:15:30+0800"));           //报错 
System.out.println(ZonedDateTime.parse("2019-11-14T10:15:30[Europe/Paris]"));  //报错
System.out.println(ZonedDateTime.parse("2019-11-14T10:15:30+08:00"));
System.out.println(ZonedDateTime.parse("2019-11-14T10:15:30+08:00[Europe/Paris]"));
System.out.println(ZonedDateTime.parse("2019-11-14T10:15:30+08:00[Asia/Shanghai]"));
```

 	ZonedDateTime（以下简称ZDT）的of写法有三重写法，上面只是展示了(LocalDateTime ldt,ZoneID zone)这种常用方法。

​	ZoneID是时区ID类，可通过静态函数**of(string id)**创建。我国的id是"Asia/Shanghai"。可以通过**ZoneId.getAvailableZoneIds()** 获得所有有效的 时区id字符串。

```java
System.out.println(ZoneId.getAvailableZoneIds()); //非常多，不一一打印。
```



### 2.2 时间的处理——plus() | min() | at()

#### 2.2.1 plus() 和 min()

plus函数还有很多类似方法，下面仅仅展示增减一天。

```java
System.out.println(LocalDate.parse("2019-11-15").plusDays(1));  
System.out.println(LocalDate.parse("2019-11-15").minusDays(1));  
```

#### 2.2.2 at()

at函数的作用是给当前时间类型添加其他时间类型。最常用是给LocalDateTime添加时区（转为ZLDT）

```java
System.out.println(LocalTime.parse("06:30").atDate(LocalDate.parse("2019-11-15")));
System.out.println(LocalDate.parse("2019-11-15").atTime(LocalTime.parse("06:30")));
System.out.println(LocalDateTime.parse("2019-11-15T00:06:30").atZone(ZoneId.of("Europe/Paris")));
System.out.println(LocalDateTime.parse("2019-11-15T00:06:30").atOffset(ZoneOffset.of("+08:00")));
```



### 2.3 时间差——Duration | Period | ChronoUnit |

时间差计算都是使用Duration、Period和ChronoUnit 的between(时间类型参数1,时间类型参数2)方法。差值 = 参数2 - 参数1。且两参数类型相同，否则报错。

#### 2.3.1 Duration类

```java
//秒差
System.out.println(Duration.between(LocalDateTime.parse("2019-12-15T00:09:00"),     
LocalDateTime.parse("2020-12-15T00:09:00")).getSeconds());

///纳秒差-1s =1000000ns，只计算ms差,其他时间忽略
System.out.println(Duration.between(LocalDateTime.parse("2019-12-15T00:06:30.111"),   LocalDateTime.parse("2019-12-15T00:06:31.113")).getNano());
```

#### 2.3.2 Period类

```java
System.out.println("1. "+Period.between(LocalDate.parse("2019-11-01"),      LocalDate.parse("2019-11-30")).getDays());

/**get函数只是简单的同单位相减  其他单位差忽略**/
//日差
System.out.println("2. "+Period.between(LocalDate.parse("2018-11-01"),      LocalDate.parse("2019-11-30")).getDays());
System.out.println("3. "+Period.between(LocalDate.parse("2019-11-15"),      LocalDate.parse("2019-12-16")).getMonths());
System.out.println("4. "+Period.between(LocalDate.parse("2019-12-15"),      LocalDate.parse("2018-12-16")).getYears());
System.out.println("5. "+Period.between(LocalDate.parse("2019-12-15"),      LocalDate.parse("2018-12-16")).getYears());

//between().toTotalMonths 计算月差
System.out.println(Period.between(LocalDate.parse("2018-12-15"),LocalDate.parse("2019-12-15")).toTotalMonths());
System.out.println(Period.between(LocalDate.parse("2019-12-15"),LocalDate.parse("2019-12-15")));
```

#### 2.3.3 ChronoUnit类

```java
System.out.println(ChronoUnit.DAYS.between(LocalDate.parse("2019-11-15"),LocalDate.parse("2019-12-15")));
System.out.println(ChronoUnit.NANOS.between(LocalDateTime.parse("2019-12-15T22:22:00"),LocalDateTime.parse("2019-12-15T22:22:01")));
```

#### 2.3.4 特点比较

| 类名       | 特点                                                         | between()函数中的参数说明 |
| ---------- | ------------------------------------------------------------ | ------------------------- |
| Duration   | 计算**除了ms单位**的s差 和 ms单位的ns值。  //1(ms)=1E+6(ns)， | 同参数类型即可            |
| Period     | 计算各个单位的相应单位差(日差、月差、年差) \| 计算月单位+年单位的所有月差  // | 只能是LocalDate类型       |
| ChronoUnit | 计算全单位的日差、月差、年差、秒差、纳秒差                   | 同类型即可                |



## 三、Springboot Mybatis 集成 jdk8 时间API

### 3.1 在创建Springboot工程时勾选Mybatis Framework

### 3.2 pom.xml文件添加依赖

```xml
<!--mybatis处理jdk8 时间api-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-typehandlers-jsr310</artifactId>
    <version>1.0.2</version>
</dependency>
<!--druid连接池-->
<dependency>    
    <groupId>com.alibaba</groupId>    
    <artifactId>druid</artifactId>    
    <version>1.1.21</version>
</dependency>
```

### 3.3 失败原因分析

**1.Druid 版本低，1.1.21为本文当前写作时的最新版本。**

2.连接mysql-connector-java-xxxx.jar版本低，由于高版本的springboot（本文2.2.1）都会集成高版本的mysql-connector（本文8.0.18）。所有升高springboot版本即可(在pom.parent.version节点)。



## 四、更新插入mysql时间字段出现14/13/8小时时差分析

### 4.1 JDBC 与 MySQL 的 协商过程

​		1.确定数据库服务器所在时区

​		2.确定本地应用服务器所在时区

​		3.若有时间差，转换

#### 4.1.1确定数据库服务器所在时区

**1. 从数据库配置参数 `time_zone`获取。**

**time_zone**可以在创建Mysql服务时的**my.ini**文件中指定 **default-time-zone** 的值，也可以事后修改（见4.5）。

| time_zone 取值 | 说明                                 |
| -------------- | ------------------------------------ |
| SYSTEM         | 默认                                 |
| UTC 偏移量     | ’+10:00‘                             |
| 时区id         | ‘Europe/Helsinki’ \| ‘Asia/ShangHai’ |

​	time_zone = SYSTEM 时， 取 system_time_zone ，即服务器机器的本地时区，国内Ubuntu16.04机器一般为CST。可以使用 `Show variable like 'time_zone'` Sql语句查看数据库的time_zone取值情况。

![image-20191118111016087](C:\Users\003586\AppData\Roaming\Typora\typora-user-images\image-20191118111016087.png)

**2. 从连接字符串`serverTimezone`获取。**

```properties
spring.datasource.url=jdbc:mysql://88.168.0.156:3306/test_time?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
```

​		高版本mysql驱动(8以上)**连接高版本Mysql**必须要求连接字符串指定serverTimezone，否则报错。低版本驱动或高版本驱动连接**低版本Mysql**不报错。

**3. 优先级**：连接字符串>从数据库配置参数

可以参见\maven_repository\mysql\mysql-connector-java\8.0.18\mysql-connector-java-8.0.18.jar!\com\mysql\cj\protocol\a\NativeProtocol.class 中的configureTimezone()方法。

![image-20191118112806371](C:\Users\003586\AppData\Roaming\Typora\typora-user-images\image-20191118112806371.png)

```
configuredTimeZoneOnServer 是获取数据库配置参数time_zone局部变量。
canonicalTimezone 是获取连接字符串局部变量。
最后一个if说明协商过程中数据库服务器所在时区在该会话设置的优先级。
```

#### 4.1.2确定本地应用服务器所在时区

本地应用（本文是springboot）所在时区是通过jvm获得，可以通过查看 TimeZone.getDeafault()注释可知其取值情况。

```
* Use the user.timezone property value as the default time zone ID if it's available.
* Detect the platform time zone ID. The source of the platform time zone and ID mapping may vary with implementation。
* Use GMT as the last resort if the given or detected time zone ID is unknown.
```

默认用户时区(即电脑本地时区) > 检测的平台设置的时区 > GMT（最后无奈的选择）



### 4.2 CST时区的BUG

CST 的时区是一个很混乱的时区，有四种含义 ：

- 美国中部时间 Central Standard Time (USA) UTC-06:00 |05：00

- 澳大利亚中部时间 Central Standard Time (Australia) UTC+09:30 

- 中国标准时 China Standard Time UTC+08:00 

- 古巴标准时 Cuba Standard Time UTC-04:00

CST在java中默认为美国中部时间。 美国从“3月11日”至“11月7日”实行夏令时 ,此时**CST=' UTC-05:00',其他日子为CST=' UTC-06:00'**，与中国（UTC+08:00）分别相差**13和14小时**。



### 4.3 14/13小时时差造成原因

1. mysql数据库时区参数time_zone = system_time_zone 。

2. 数据库所在服务器系统时间正确设置（一定为CST）。

3. 应用服务器本地时区正确设置（Asia/shanghai）。

4. 连接字符没有指定时区。



### 4.4 8小时时差造成原因

第一种常见：

1. mysql数据库时区参数time_zone = UTC，或time_zone = system_time_zone ，但本地时区没有正确设置为UTC。
2. 应用服务器本地时区正确设置（Asia/shanghai）。
3. 连接字符没有指定时区。

第二种常见：

1. 应用服务器本地时区正确设置（Asia/shanghai）。
2. 连接字符指定时区为UTC。（直接拷贝网上的连接字符串方式造成）



### 4.5 解决方法

1.检查连接字符串，只要确定数据库服务器所在区域为中国，直接指定时区为:

```
...&serverTimezone=Asia/Shanghai
```

2.检查应用服务器本地用户时区。可以通过java时区函数检查

```java
System.out.println(TimeZone.getDefault());
```

3.检查Mysql数据库服务器所在时区

```sql
Show variable like 'time_zone'
```

若为CST：

方法一：

```sql
set global time_zone = '+8:00'; ##修改mysql全局时区为北京时间，即我们所在的东8区
set time_zone = '+8:00'; ##修改当前会话时区
flush privileges; #立即生效
```

方法二：脚本语言初始化

```php
 mysql_query("SET time_zone = '+8:00'") //php
```

方法三：配置文件修改

```
# vim /etc/my.cnf ##在[mysqld]区域中加上
default-time_zone = '+8:00'
# /etc/init.d/mysqld restart ##重启mysql使新时区生效
```



## 参考文献

[1]: https://www.baeldung.com/java-8-date-time-intro	"Introduction to the Java 8 Date/Time API"
[2]: https://www.iteye.com/blog/log-cd-368238	"Java TimeZone"
[3]: https://juejin.im/post/5902e087da2f60005df05c3d?utm_source=tuicool&amp;utm_medium=referral	"一次 JDBC 与 MySQL 因 “CST” 时区协商误解导致时间差了 14 或 13 小时的排错经历"
[4]: https://www.cnblogs.com/peida/archive/2013/05/31/3070790.html	"深入理解Java：SimpleDateFormat安全的时间格式化"

