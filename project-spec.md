# 目录 #
- 	[字符编码](#encoding)
- 	[工程构建基础](#base)
- 	[微服务profiles](#profiles)
- 	[微服务工程结构](#dirs)
- 	[源码管理](#code)
- 	[日志文件](#logfile)
- 	[微服务配置](#config)
- 	[三方库 规约](#depedency)
- 	[工具库推荐](#tools)

<a name="encoding"></a>
<p><br><br></p>
## 字符编码 ##
1. 工作空间:UTF-8
2. 网络传输:UTF-8
3. 工程:UTF-8
4. 数据库、表：UTF-8
5. Tomcat配置： UTF-8
		
*除非外部接口对接有指明需要的字符编码，否则默认UTF-8*

<a name="base"></a>
<p><br><br></p>
## 工程构建基础 ##

1. jdk1.8
2. maven3.3.x
3. git

<a name="profiles"></a>
<p><br><br></p>
## 微服务profiles ##

|profile name	|备注		|默认	|
|--------		|-----		|-----	|
|dev			|开发环境	|N 		|
|test			|测试环境	|Y		|
|sandbox		|沙箱环境	|N	 	|
|stage			|预生产环境	|N	 	|
|online			|生产环境	|N		|
	
同一工程多环境部署时，如果需要调用内部下层应用时，需要配置下层应用的域名，域名配置到配置中心
或者配置文件，请务配置到数据库中。

<a name="dirs"></a>
<p><br><br></p>
## 微服务工程结构 ##
IDE当前只支持eclipse 、 STS，其它IDE须自行设定。

包括但不限于以下目录结构，需要纳入版本控制。

	工程
		|.gitignore文件
		|.settings目录
		|src/main/resources
			|mapper目录（存放mybatis mapper文件）
			|config目录
				|application-{profile}.yml
				|application.yml
				|bootstrap.yml
			｜logback-spring.xml
		|src/main/java
			|com.ppcredit
				|controller
				|service
				|dao
				|entity
				|vo
				|utils
		|src/test/resources
		|src/test/java
		|README.md
		|pom.xml

采用微服务脚手架生产的工程，`.gitignore` 、`.settings`会自动生成，请将他们
提交到版本库中

###  目录&文件说明  ###
- .gitignore：配合git，忽略不应该加入到版本仓库的文件列表
- .settings：
	- eclipse-code-formatter.xml 代码格式化
	- org.eclipse.jdt.core.prefs 代码风格定义
	- org.eclipse.jdt.ui.prefs eclipse IDE工程属性设置
- README.md	工程自述文件，自述规范如下：

		项目介绍
		特性（可选）
		该如何使用? (系统环境参数,部署要素)
		注意事项
		目录结构描述
		版本更新重要摘要

### Java包说明 ###

微服务工程目录结构遵循应用分层思想:

- controller：接口层
- service：业务逻辑层
- dao：数据访问层，包括mysql、hbase、redis等
- entity：实体模型层，和数据库表一一对应
- vo:接口层的契约或者前端web页面上需要使用的数据载体
- utils：工具类

<a name="code"></a>
<p><br><br></p>
## 源码管理 ##


1. 分支管理

	|分支名称			|使用说明		|
	|--------			|-----			|
	|master				|master分支是生产环境的镜像、或者是即将发布到生产环境的状态	|
	|branches 1 .. N	|这是由许多分别负责不同feature开发的分支组成的一个分支系列。	|




2. 分支流程
	
	1. 各branche分支衍生自master
	2. branche分支功能开发完毕，提测
	3. 测试通过后，在git上提Merge Request
	4. 架构或者Leader执行Merge到master主干操作	
	
	*在提交Merge Request时，建议先将master主干代码合并到自己的分支，避免Merge时冲突*

3. 代码静态检查

	代码静态规则扫描工具： 统一使用Sonar进行扫描，检查规则由架构师统一配置

		通过标准：
			新代码技术债务比率小于5%
			无阻断违规
			无严重违规
			单元测试的分支覆盖率需要大于65%
			代码审查

<a name="logfile"></a>
<p><br><br></p>
## 日志文件 ##
    
1. 微服务
	
	 	日志文件位置					:/opt/MicroService/logs
	    info.log				：业务日志，包含info、warn、error级别的日志内容
	    error.log				:仅包含error级别日志，主要用于运维统计报警
	    access_yyyy_MM_dd log	：应用访问日志
	
	    日志原则：
	    	生产环境默认为warn
	    	对应的业务如果需要info级别，单独打开
	    	生产环境不允许debug、trace级别


2. Tomcat 应用
		
		日志文件
		    错误日志 
		    /usr/local/tomcat/logs/catalina.out
		
		    业务日志 
		    /usr/local/tomcat/logs/info_yyyy_mm_dd.log （info_ + 日期/每天程序自动生成）

<a name="config"></a>
<p><br><br></p>
## 微服务配置 ##
	
1. 为了区分tomcat应用与微服务，8070端口为微服务专用

		server:
	  		port: 8070

2. 2222端口为管理端口
3. /management管理端点

		management:
		  context-path: /management
		  port: 2222		

4. 程序默认profile 为 test

		spring:
			profiles:
	    		active: test

5. 微服务controller中URI 必须**/api**开头
6. jmx默认开启,端口：12345
7. 服务名、工程名，不能用后缀-management，因为consul管理容易混乱
8. 微服务工程开发、测试、部署时，不要配置context-path，使其默认运行在根下


<a name="depedency"></a>
<p><br><br></p>
## 三方库 规约 ##

1. 依赖于一个**三方库群**时，必须定义一个统一版本变量，避免版本号不一致
	
		<?xml version="1.0" encoding="UTF-8"?>
		<project xmlns="http://maven.apache.org/POM/4.0.0" 
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
			xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
		...
			<properties>
		        <spring.version>1.3.1.RELEASE</spring.version>
		    </properties>


2. 禁止在子项目的pom依赖中出现相同的GroupId，相同的ArtifactId，但是不同的Version。
3. 所有pom文件中的依赖声明放在<dependencies>语句块中，所有版本仲裁放在<dependencyManagement>语句块中

		<?xml version="1.0" encoding="UTF-8"?>
		<project xmlns="http://maven.apache.org/POM/4.0.0" 
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
			xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
			...
			<dependencyManagement>
		        <dependencies>
		            <dependency>
					    <groupId>org.springframework.ldap</groupId>
					    <artifactId>spring-ldap-core</artifactId>
					    <version>${spring.version}</version>
					</dependency>
					<dependency>
			    		<groupId>org.springframework</groupId>
			    		<artifactId>spring-test</artifactId>
					    <version>${spring.version}</version>
						<scope>test</scope>
					</dependency>
		        </dependencies>
		    </dependencyManagement>
			...

4. pom中移除一切不使用的三方库依赖
5. 提供统一的工具包，避免重复写工具类，比如：json操作、集合操作、日期操作、数值操作等，
并且建议使用全局统一的工具包：如org.apache.commons.lang3

<a name="tools"></a>
<p><br><br></p>
## 工具库推荐 ##

### Google Guava ###

[Guava](https://github.com/google/guava) 是一个 Google 的基于java1.6的类库集合的扩展项目，包括 collections, caching, primitives support, concurrency libraries, common annotations, string processing, I/O, 等等. 这些高质量的 API 可以使你的JAVa代码更加优雅，更加简洁，让你工作更加轻松愉悦。


**源码包的简单说明：**

|源码包				|使用说明		|
|--------			|-----			|
|com.google.common.annotations	|	普通注解类型。|
|com.google.common.base			|	基本工具类库和接口。|
|com.google.common.cache		|	缓存工具包，非常简单易用且功能强大的JVM内缓存。|
|com.google.common.collect		|	带泛型的集合接口扩展和实现，以及工具类，这里你会发现很多好玩的集合。|
|com.google.common.eventbus		|	发布订阅风格的事件总线。|
|com.google.common.hash			|	哈希工具包。|
|com.google.common.io			|	I/O工具包。|
|com.google.common.math			|	原始算术类型和超大数的运算工具包。|
|com.google.common.net			|	网络工具包。|
|com.google.common.primitives	|	八种原始类型和无符号类型的静态工具包。|
|com.google.common.reflect		|	反射工具包。|
|com.google.common.util.concurrent|	多线程工具包。|

**类库使用手册：**

		1	工具类：让使用Java语言更令人愉悦。
			- 避免 null：null 有语言歧义， 会产生令人费解的错误， 反正他总是让人不爽。很多 Guava 的工具类在遇到 null 时会直接拒绝或出错，而不是默默地接受他们。
			- 前提条件：更容易的对你的方法进行前提条件的测试。
			- 常见的对象方法： 简化了Object常用方法的实现， 如 hashCode() 和 toString()。
			- 排序： Guava 强大的 "fluent Comparator"比较器， 提供多关键字排序。
			- Throwable类： 简化了异常检查和错误传播。
		2	类：集合类库是 Guava 对 JDK 集合类的扩展， 这是 Guava 项目最完善和为人所知的部分。
			- utable collections（不变的集合）： 防御性编程， 不可修改的集合，并且提高了效率。
			- New collection types(新集合类型)：JDK collections 没有的一些集合类型，主要有：multisets，multimaps，tables， bidirectional maps等等。
			- Powerful collection utilities（强大的集合工具类）： java.util.Collections 中未包含的常用操作工具类。
			- Extension utilities（扩展工具类）: 给 Collection 对象添加一个装饰器? 实现迭代器? 我们可以更容易使用这些方法。
		3	缓存: 本地缓存，可以很方便的操作缓存对象，并且支持各种缓存失效行为模式。
		4	Functional idioms（函数式）: 简洁, Guava实现了Java的函数式编程，可以显著简化代码。
		5	Concurrency（并发）：强大,简单的抽象,让我们更容易实现简单正确的并发性代码。
			- ListenableFuture（可监听的Future）: Futures,用于异步完成的回调。
			- Service: 控制事件的启动和关闭，为你管理复杂的状态逻辑。
		6	Strings: 一个非常非常有用的字符串工具类: 提供 splitting，joining， padding 等操作。
		7	Primitives: 扩展 JDK 中未提供的对原生类型（如int、char等）的操作， 包括某些类型的无符号的变量。
		8	Ranges: Guava 一个强大的 API，提供 Comparable 类型的范围处理， 包括连续和离散的情况。
		9	I/O: 简化 I/O 操作, 特别是对 I/O 流和文件的操作, for Java 5 and 6.
		10	Hashing: 提供比 Object.hashCode() 更复杂的 hash 方法, 提供 Bloom filters.
		11	EventBus: 基于发布-订阅模式的组件通信，但是不需要明确地注册在委托对象中。
		12	Math: 优化的 math 工具类，经过完整测试。
		13	Reflection: Guava 的 Java 反射机制工具类。

### Apache Commons ###

[Apache Commons](http://commons.apache.org/)是一个非常有用的工具包，解决各种实际的通用问题，我们常用的组件如下:

|组件名称	|	使用说明|
|----------	|---------|
|BeanUtils	|	提供对 Java 反射和自省API的包装
|Chain	|	提供实现组织复杂的处理流程的“责任链模式”.
|CLI 	|	CLI 提供针对命令行参数，选项，选项组，强制选项等的简单API.
|Codec	|	Codec 包含一些通用的编码解码算法。包括一些语音编码器， Hex, Base64, 以及URL encoder.
|Collections 	|	Commons-Collections 提供一个类包来扩展和增加标准的 Java Collection框架
|Configuration 	|	Commons-Configuration 工具对各种各式的配置和参考文件提供读取帮助.
|Daemon	|	一种 unix-daemon-like java 代码的替代机制
|IO 	|	IO 是一个 I/O 工具集
|Lang 	|	Commons-Lang 提供了许多许多通用的工具类集，提供了一些java.lang中类的扩展功能
|Launcher	|	Launcher 组件是一个交叉平台的Java 应用载入器。Commons-launcher 消除了需要批处理或者Shell脚本来载入Java 类。.原始的 Java 类来自于Jakarta Tomcat 4.0 项目
|Math	|	Math 是一个轻量的，自包含的数学和统计组件，解决了许多非常通用但没有及时出现在Java标准语言中的实践问题.
|Net 	|	Net 是一个网络工具集，基于 NetComponents 代码，包括 FTP 客户端等等。
|Pool 	|	Commons-Pool 提供了通用对象池接口，一个用于创建模块化对象池的工具包，以及通常的对象池实现.
|Validator 	|	The commons-validator提供了一个简单的，可扩展的框架来在一个XML文件中定义校验器 (校验方法)和校验规则。支持校验规则的和错误消息的国际化。
