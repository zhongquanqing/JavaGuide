# 第一章 Mybatis的概述
## 1.1.为什么要用ORM框架
    对象关系映射简称：ORM   

###1.1.1.传统的JDBC编程存在的弊端：
    工作量大，操作数据库至少要5步；
    业务代码和技术代码耦合；
    连接资源手动关闭，带来了隐患；
###1.1.2.ORM带来的好处：
    更加贴合面向对象的编程语意，Java程序员喜欢的姿势；
    技术和业务解耦，Java程序员无需对数据库相关的知识深入了解
    妈妈再也不用担心我，不释放数据库连接资源了
##1.2.Mybatis是什么？
    Mybatis前身是iBatis,其源于“Internet”和“ibatis”的组合，本质是一种半自动的ORM框架，除了POJO（  JavaBeans的简称）和映射关系之外，还需要编写SQL语句；
###1.2.1.Mybatis映射文件三要素：
    SQL
    映射规则
    POJO
##1.3.Mybatis的配置
###1.3.1.属性
    格式如下属性名	说明	备注
    properties	定义配置，配置的属性可以在整个配置文件中其他位置进行引用；	重要，优先使用property配置文件解耦
    settings	设置，用于指定MyBatis的一些全局配置属性，这些属性非常重要，它们会改变MyBatis的运行时行为；	重要，后面专门说明
    typeAliases	别名，为Java类型设置一个短的名字，映射时方便使用；分为系统定义别名和自定义别名；	可以通过xml和注解配置
    typeHandlers	用于jdbcType与javaType之间的转换；	无特殊需求不需要调整；后面专题说明
    ObjectFactory	MyBatis每次创建结果对象的新实例时，它都会使用对象工厂（ObjectFactory）去构建POJO	大部分场景下无需修改
    plugins	插件，MyBatis允许你在已映射的语句执行过程中的某一点进行拦截调用；	后面专题说明
    environments	用于配置多个数据源，每个数据源分为数据库源和事务的配置；	在多数据源环境使用
    databaseIdProvider	MyBatis可以根据不同的数据库厂商执行不同的语句，用于一个系统内多厂商数据源支持。	大部分场景下无需修改
    
    mappers	配置引入映射器的方法。可以使用相对于类路径的资源引用、或完全限定资源定位符（包括file:///的URL），或类名和包名等等	后面会专题说明
    1.3.2.设置参数
    设置参数	描述	有效值	默认值
    cacheEnabled	该配置影响的所有映射器中配置的缓存的全局开关	true | false	true
    lazyLoadingEnabled	延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置fetchType属性来覆盖该项的开关状态	true | false	false
    aggressiveLazyLoading	当启用时，对任意延迟属性的调用会使带有延迟加载属性的对象完整加载；反之，每种属性将会按需加载。	true | false	true
    multipleResultSetsEnabled	是否允许单一语句返回多结果集（需要兼容驱动）。	true | false	true
    useColumnLabel	使用列标签代替列名。不同的驱动在这方面会有不同的表现， 具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。	true | false	true
    useGeneratedKeys	允许 JDBC 支持自动生成主键，需要驱动兼容。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby）。	true | false	False
    autoMappingBehavior	指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。	NONE, PARTIAL, FULL	PARTIAL
    defaultExecutorType	配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。	SIMPLE、REUSE、BATCH	SIMPLE
    defaultStatementTimeout	设置超时时间，它决定驱动等待数据库响应的秒数。	Any positive integer	Not Set (null)
    safeRowBoundsEnabled	允许在嵌套语句中使用分页（RowBounds）。	true | false	False
    mapUnderscoreToCamelCase	是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。	true | false	False
    localCacheScope	MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速重复嵌套查询。 默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlSession 的不同调用将不会共享数据。	SESSION | STATEMENT	SESSION
    jdbcTypeForNull	当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。	JdbcType 枚举，最常见的是: NULL, VARCHAR and OTHER	OTHER
    lazyLoadTriggerMethods	指定哪个对象的方法触发一次延迟加载。	如果是方法列表用逗号隔开；	equals,clone,hashCode,toString
    callSettersOnNulls	指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法，这对于有 Map.keySet() 依赖或 null 值初始化的时候是有用的。注意基本类型（int、boolean等）是不能设置成 null 的。	true | false	false
    logPrefix	指定 MyBatis 增加到日志名称的前缀。	Any String	Not set
    logImpl	指定 MyBatis 所用日志的具体实现，未指定时将自动查找。	SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING	Not set
    proxyFactory	指定 Mybatis 创建具有延迟加载能力的对象所用到的代理工具。	CGLIB | JAVASSIST	版本3.3.0以上JAVASSIST

##1.4.Mybaties配置Mapper
###1.4.1.用classPath下资源引用
      <mappers>
      <!--直接映射到相应的mapper文件 -->
      <mapper resource="sqlmapper/TUserMapper.xml" />
      </mappers>
###1.4.2.用类注册方式引用
    <mappers>
    <!—通过类扫描mapper文件 TUserMapper为类名-->
    <mapper class="com.enjoylearning.mybatis.mapper.TUserMapper" />
    </mappers>
###1.4.3.使用包名引入引射器名
    <mappers>
    <!—扫描包下所有的mapper文件 -->
    <package name="com.enjoylearning.mybatis.mapper"/>
    </mappers>
###1.4.4.用文件的全路径引用（舍弃）
