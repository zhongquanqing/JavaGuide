# 概述
# 第一章 Spring注解的认识
## 1.1.注解的初步认识
### 1.1.1.@controller
    标注控制层

### 1.1.2.@service
    标注服务层，进行业务的逻辑处理
### 1.1.3.@repository
    标注数据访问层，也可以说用于标注数据访问组件，即DAO组件
### 1.1.4.@component 
    把普通pojo实例化到spring容器中，相当于配置文件中的 
    <bean id="" class=""/>
### 1.1.5.@Transactional
    使用位置：
    1、用在接口或接口方法上，AOP必须是接口代理方式。不推荐	
    2、可以使用在类以及类方法上。推荐
    3、注解应该只被应用到 public 方法上。其它级别（ protected ，private无效）
    4、只有来自外部的方法调用，事务才生效。（不能由本地方法直接调用）
    回滚控制：
    1、默认配置下，方法体只有在抛出RuntimeException或其子类时，才回滚事务。
    2、可以指定哪些异常回滚： @Transactional(rollbackFor=XxxException.class)
    3、可以指定哪些异常不回滚： @Transactional(noRollbackFor=XxxException.class)

# 第二章 class
    

    