# 目录 #

- [异常处理](#1)
- [日志规约](#2)

<a name="1"></a>
<p>
<br>
<br>
</p>
## 异常处理 ##



1. 【强制】不要捕获Java类库中定义的继承自RuntimeException的运行时异常类，如：IndexOutOfBoundsException / NullPointerException，这类异常由程序员预检查来规避，保证程序健壮性。 
	
	**正例**：
		if(obj != null) {...} 

	**反例**：
		try { obj.method() } catch(NullPointerException e){...} 
2. 【强制】异常不要用来做流程控制，条件控制，因为异常的处理效率比条件分支低。 
3. 【强制】对大段代码进行try-catch，这是不负责任的表现。catch时请分清稳定代码和非稳定代码，稳定代码指的是无论如何不会出错的代码。对于非稳定代码的catch尽可能进行区分异常类型，再做对应的异常处理。 
4. 【强制】捕获异常是为了处理它，不要捕获了却什么都不处理而抛弃之，如果不想处理它，请将该异常抛给它的调用者。最外层的业务使用者，必须处理异常，将其转化为用户可以理解的内容。 
5. 【强制】有try块放到了事务代码中，catch异常后，如果需要回滚事务，一定要注意手动回滚事务。 
6. 【强制】finally块必须对资源对象、流对象进行关闭，有异常也要做try-catch。 
	
	**说明：**如果JDK7+，可以使用try-with-resources方式。 

		private static void printFile() throws IOException {
		    try(FileInputStream input = new FileInputStream("file.txt")) {
		        int data = input.read();
		        while(data != -1){
		            System.out.print((char) data);
		            data = input.read();
		        }
		    }
		}

7. 【强制】不能在finally块中使用return，finally块中的return返回后方法结束执行，不会再执行try块中的return语句。 
8. 【强制】捕获异常与抛异常，必须是完全匹配，或者捕获异常是抛异常的父类。 
	
	**说明：**如果预期对方抛的是绣球，实际接到的是铅球，就会产生意外情况。 
9. 【推荐】方法的返回值可以为null，不强制返回空集合，或者空对象等，必须添加注释充分说明什么情况下会返回null值。调用方需要进行null判断防止NullPointException问题。 
	
	**说明：**本规约明确防止NullPointException是调用者的责任。即使被调用方法返回空集合或者空对象，对调用者来说，也并非高枕无忧，必须考虑到远程调用失败，运行时异常等场景返回null的情况。 
10. 【推荐】防止NullPointException，是程序员的基本修养，注意NullPointException产生的场景： 
	
	- 	1） 返回类型为包装数据类型，有可能是null，返回int值时注意判空。 
	- 	**反例**：public int f(){ return Integer对象}; 如果为null，自动解箱抛NullPointException。 
	- 	2） 数据库的查询结果可能为null。 
	- 	3） 集合里的元素即使isNotEmpty，取出的数据元素也可能为null。 
	- 	4） 远程调用返回对象，一律要求进行NullPointException判断。 
	- 	5） 对于Session中获取的数据，建议NullPointException检查，避免空指针。 
	- 	6） 级联调用obj.getA().getB().getC()；一连串调用，易产生NullPointException。 

11. 【推荐】在代码中使用“抛异常”还是“返回错误码”，对于公司外的http/api开放接口必须使用“错误码”；而应用内部推荐异常抛出；跨应用间RPC调用优先考虑使用Result方式，封装isSuccess、“错误码”、“错误简短信息”。 
	
	**说明：**关于RPC方法返回方式使用Result方式的理由： 
	- 	1）使用抛异常返回方式，调用方如果没有捕获到就会产生运行时错误。 
	- 	2）如果不加栈信息，只是new自定义异常，加入自己的理解的error message，对于调用端解决问题的帮助不会太多。如果加了栈信息，在频繁调用出错的情况下，数据序列化和传输的性能损耗也是问题。 
12. 【推荐】定义时区分unchecked / checked 异常，避免直接使用RuntimeException抛出，更不允许抛出Exception或者Throwable，应使用有业务含义的自定义异常。推荐业界已定义过的自定义异常，如：DAOException / ServiceException等。 
13. 【参考】避免出现重复的代码（Don’t Repeat Yourself），即DRY原则。 
	
	**说明：**随意复制和粘贴代码，必然会导致代码的重复，在以后需要修改时，需要修改所有的副本，容易遗漏。必要时抽取共性方法，或者抽象公共类，甚至是共用模块。 
	
	**正例**：一个类中有多个public方法，都需要进行数行相同的参数校验操作，这个时候请抽取： 
		private boolean checkParam(DTO dto){...}

<a name="2"></a>
<p>
<br>
<br>
</p>
## 日志规约 ##


1. 【强制】应用中不可直接使用日志系统（Log4j、Logback）中的API，而应依赖使用日志框架SLF4J中的API，使用门面模式的日志框架，有利于维护和各个类的日志处理方式统一。
 
		import org.slf4j.Logger; 
		import org.slf4j.LoggerFactory; 
		private static final Logger logger = LoggerFactory.getLogger(Abc.class); 
2. 【强制】日志文件推荐至少保存15天，因为有些异常具备以“周”为频次发生的特点。 
3. 【强制】对trace/debug/info级别的日志输出，必须使用条件输出形式或者使用占位符的方式。 
	
	**说明：**logger.debug("Processing trade with id: " + id + " symbol: " + symbol); 如果日志级别是warn，上述日志不会打印，但是会执行字符串拼接操作，如果symbol是对象，会执行toString()方法，浪费了系统资源，执行了上述操作，最终日志却没有打印。 
	
	**正例**：（条件） 

		if (logger.isDebugEnabled()) { 
			logger.debug("Processing trade with id: " + id + " symbol: " + symbol); 
		} 

	**正例**：（占位符） 
		logger.debug("Processing trade with id: {} symbol : {} ", id, symbol); 
4. 【强制】避免重复打印日志，浪费磁盘空间，务必在log4j.xml中设置additivity=false。 
	
	**正例**：
		<logger name="com.taobao.dubbo.config" additivity="false"> 
5. 【强制】异常信息应该包括两类信息：案发现场信息和异常堆栈信息。如果不处理，那么往上抛。 
	
	**正例**：logger.error(各类参数或者对象toString + "_" + e.getMessage(), e); 
6. 【推荐】可以使用warn日志级别来记录用户输入参数错误的情况，避免用户投诉时，无所适从。注意日志输出的级别，error级别只记录系统逻辑出错、异常等重要的错误信息。如非必要，请不要在此场景打出error级别。 
7. 【推荐】谨慎地记录日志。生产环境禁止输出debug日志；有选择地输出info日志；如果使用warn来记录刚上线时的业务行为信息，一定要注意日志输出量的问题，避免把服务器磁盘撑爆，并记得及时删除这些观察日志。 
	
	**说明：**大量地输出无效日志，不利于系统性能提升，也不利于快速定位错误点。记录日志时请思考：这些日志真的有人看吗？看到这条日志你能做什么？能不能给问题排查带来好处？ 

