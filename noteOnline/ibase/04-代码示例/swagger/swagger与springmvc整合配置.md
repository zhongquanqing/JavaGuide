# swagger与springmvc配置

<div style="float:right">

|作者|日期|
|----|---|
|黄勇|2019年3月14日|

</div>

## swagger实现

* swagger所需依赖
  ```xml
  <!-- swagger2 核心依赖 -->
    <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>2.4.0</version>
    </dependency>
    <!-- swagger-ui 为项目提供api展示及测试的界面 -->
    <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>2.4.0</version>
    </dependency>
    <!--json解析依赖-->
    <dependency>
		<groupId>com.fasterxml.jackson.core</groupId>
		<artifactId>jackson-databind</artifactId>
		<version>2.8.0</version>
	</dependency>
    <!--谷歌guava依赖-->
    <dependency>
      <groupId>com.google.guava</groupId>
      <artifactId>guava</artifactId>
      <version>18.0</version>
    </dependency>
    ```

* springmvc.xml文件swagger配置

  ```xml
  <!-- swagger静态资源 -->
  <mvc:resources location="classpath:/META-INF/resources/" mapping="swagger-ui.html"/>
  <mvc:resources location="classpath:/META-INF/resources/webjars/" mapping="/webjars/**"/>
  <bean class="com.southgis.ibase.swagger.SwaggerApiConfig" id="swaggerApiConfig"/>
  ```

  * 在springmvc.xml文件没有配置到自定义bean时，要使用默认的配置，得加一行代码：<context:component-scan base-package="springfox"/>，也能扫到controller接口的包，但不能自定义title那些内容。所以自定义的swagger配置类，在springmvc配置文件上要添加这个类的bean

    ![pic](/ibase/功能示例/swagger/images/pic.png "pic")  

* swagger与com.google.guava版本问题
  
  * 如果swagger版本是2.6.1，但guava在18或以下可能初始化bean时会失败。我在测试demo里面是能运行的，集成到综合监管就不行了。会报“Failed to start bean 'documentationPluginsBootstrapper'; nested exception is com.google.common.util.concurrent.ExecutionError:...”的错误，其实就是swagger与guava版本不兼容的问题。
     
    ![exception](/ibase/功能示例/swagger/images/documentationPluginsBootstrapper.png "exception")
  
    目前ibase2.0项目的guava是18.0，经过选用swagger 2.4.0版本，就可以在打开swagger-ui.html页面时，成功显示。

      当然本人觉得用较新版本就比较好，swagger 2.9.2版本在ui方面加以优化，体验更好，guava选用27.0.1-jre

* 创建SwaggerConfig类（名字自定义，但要见名知意，规范命名）以自定义初始化配置
  
  *  网上在配置SwaggerConfig时，加了许多类上加了许多注解，比如，@Component、
@Configuration、@ComponentScan("com.qinzx.controller")，其实这些都没什么用。(-_-)!!!，实际只要两个注解就行了，@EnableSwagger2（使swagger2生效）
@EnableWebMvc（让swagger支持springmvc），当然在spring-boot使用swagger更加方便，这里大家可以去百度了解一下。

  * java代码

   ```java
   package com.southgis.ibase.swagger;
   import io.swagger.annotations.Api;
   import org.springframework.context.annotation.Bean;
   import org.springframework.web.servlet.config.annotation.EnableWebMvc;
   import springfox.documentation.builders.ApiInfoBuilder;
   import springfox.documentation.builders.PathSelectors;
   import springfox.documentation.builders.RequestHandlerSelectors;
   import springfox.documentation.service.ApiInfo;
   import springfox.documentation.service.Contact;
   import springfox.documentation.spi.DocumentationType;
   import springfox.documentation.spring.web.plugins.Docket;
   import springfox.documentation.swagger2.annotations.EnableSwagger2;

   /**
    * swagger接口集成自定义配置bean
    *
    * @author huangyong
    */
   @EnableSwagger2   // 使swagger2生效
   @EnableWebMvc
   public class SwaggerApiConfig {

        @Bean
        public Docket createAPI() {
            return new Docket(DocumentationType.SWAGGER_2)
                .forCodeGeneration(true)
                .groupName("comprehensiveMonitor-api")
                .select()

                /*扫描指定包中的注解,如果controller分布在各个包，
                  也想扫描，则不方便使用，还不如使用扫描所有RequestHandlerSelectors.any()。
                 */
                //.apis(RequestHandlerSelectors.basePackage("com.southgis.ibase.achievementManagement.webservice"))

                //扫描所有带Controller注解包
                .apis(RequestHandlerSelectors.any())

                //扫描所有有swagger注解的api，用这种方式更灵活
                //.apis(RequestHandlerSelectors.withMethodAnnotation(Api.class))

                //过滤所有路径
                .paths(PathSelectors.any())
                .build()
                .apiInfo(apiInfo());
        }

        private ApiInfo apiInfo() {
            return new ApiInfoBuilder()
                .title("综合监管接口测试")
                .description("swagger接口集成测试")
                .contact(new Contact("自然资源开发部", "http://192.168.10.103:3000/#/", ""))
                .termsOfServiceUrl("综合监管：http://192.168.10.103:3000/#/ibase/%E9%85%8D%E7%BD%AE%E6%A8%A1%E6%9D%BF/%E7%BB%BC%E5%90%88%E7%9B%91%E7%AE%A1%E9%85%8D%E7%BD%AE/config.properties")
                .version("1.0.0")
                .build();
        }

   }
   ```

     * 对SwaggerConfig配置类进行一些解释。
     
       扫描controller接口包的问题。
       
       1.  RequestHandlerSelectors.basePackage()

            该方法扫描指定包，如果controller分布在各个包，也想扫描，则不方便使用，还不如使用扫描所有RequestHandlerSelectors.any()。该方法本以为能像配置spring.xml时扫描多个包用逗号隔开，实际上是不行的。所以不同的包的接口，要用改方法，那么接口的统一父包应该是controller，这样写参数时可以这样写：“com.southgis.ibase.controller”。但根据需要来吧。
            
        2.  RequestHandlerSelectors.withMethodAnnotation()
           
            这个方法是根据接口使用的注解来扫描的，比如扫描swagger注解的controller，但试扫描Api.class注解的接口，结果不行。嗯，大家可以去尝试其他注解接口，看能扫到吗。

        3. ApiInfo apiInfo()

            这个方法是自定义标题、创建人等等的东西。

* 运行看效果
  
  * 访问swagger ui测试页面地址

    ip:port/项目路径/swagger-ui.html，比如，

    http://localhost:8080/comprehensiveMonitorWebService/swagger-ui.html#/

    ![success](/ibase/功能示例/swagger/images/success.png "success")

* 关于swagger集成到springmvc的一些配置就讲到这些，要了解swagger的注解使用什么的，可以百度找些博文了解。下面是关于汉化的简单说明。

* swagger汉化

  1.  在resources文件下新建如下文件路径：

      ![lujing](/ibase/功能示例/swagger/images/lujing.png "lujing")

   2. swagger-ui.html页面

      直接在idea开发工具里面 的Maven依赖包找到该页面，然后拷贝到自己web项目的resources相关文件下即可。

      ![swagger](/ibase/功能示例/swagger/images/swag.png "swagger")

   3. 添加中文汉化脚本路径

      ```html
         <!--国际化操作：选择中文版 -->
        <script src='webjars/springfox-swagger-ui/lang/translator.js' type='text/javascript'></script>
        <script src='webjars/springfox-swagger-ui/lang/zh-cn.js' type='text/javascript'></script>
      ```

      其实swagger依赖包里，已经有相关js文件，如果想自定义汉化效果，可以参考依赖包去项目的resources下建立文件路径，项目运行时，会先加载使用项目的META-INF里的内容。然而我却实现不了自定义的汉化，所以默认使用了swagger包下的js文件。大家可以去尝试几下，或许找到问题的原因。

* 好了，以上就是关于swagger与springmvc配置以及汉化的简单说明，希望对大家有帮助。
