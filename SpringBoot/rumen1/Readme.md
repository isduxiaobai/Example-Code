# Spring Boot（一）：入门

---

`以下是本篇文章正文内容`

# 一、什么是Spring Boot

## 1.Spring Boot简介
[Spring Boot](https://baike.baidu.com/item/Spring%20Boot/20249767?fr=aladdin)是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。通过这种方式，Spring Boot致力于在蓬勃发展的快速应用开发领域(rapid application development)成为领导者。
>通俗理解就是 Spring Boot 其实不是什么新的框架，它默认配置了很多框架的使用方式

## 2.Spring Boot的特点
* **可独立运行的Spring项目**：基于其Maven或Gradle插件，可以创建可执行的JARs和WARs。

* **内嵌Servlet容器**：Spring Boot可以选择内嵌Tomcat、Jetty或者Undertow，不用以war包形式部署项目。

* **简化的Maven配置**：提供自动配置的“starter”项目对象模型（POMS）来简化Maven 配置。

* **自动配置Spring容器**：Spring Boot会根据项目依赖来自动配置Spring 框架，极大地减少项目要使用的配置。

* **提供准备好的特性**：提供可以直接在生产环境中使用的功能，如性能指标、应用信息和应用健康检查。

* **无代码生成、不需要xml配置**：Spring Boot不生成代码。完全不需要任何xml配置即可实现Spring的所有配置。
 
# 二、入门操作上手
## 1.Maven构建项目
（1） 访问 [https://start.spring.io/](https://start.spring.io/)
（2） 选择构建工具 Maven Project、编译语言Java、Spring Boot 版本以及一些工程基本信息，如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/b505485a7a2a4304b46e9bbdb0db87e2.png)


（3） 点击 Generate Project 下载项目压缩包
（4） 解压后，使用 Idea 导入项目，File -> New -> Model from Existing Source.. -> 选择解压后的文件夹 -> OK，选择 Maven 一路 Next，OK done!
（5） 如果使用的是 Eclipse，Import -> Existing Maven Projects -> Next -> 选择解压后的文件夹 -> Finsh，OK done!
## 2.Idea构建项目
(1) 选择 File -> New —> Project… 弹出新建项目的框
(2) 选择 Spring Initializr，Next 也会出现上述类似的配置界面，Idea 做了集成
(3) 填写相关内容后，点击 Next 选择依赖的包再点击 Next，最后确定信息无误点击 Finish。
## 3.项目结构
![在这里插入图片描述](https://img-blog.csdnimg.cn/02d0d9b6bfa1460a87e2c9e334233aef.png)

如上图，Spring Boot的基础结构为三个部分：
>* src/main/java 程序开发以及主程序入口
>* src/main/resources 配置文件
>* src/test/java 测试程序

目录结构（建议）：
```java
com
├── example
│   ├── myproject
│       ├── Application.java
│       ├── domain
│          └──  Customer.java
│          └──  CustomerRepository.java
│       ├── service
│          └── CustomerService.java
│       ├── controller
│          └── CustomerController.java
```
>Application.java ： 建议放到根目录下面,主要用于做一些框架配置
domain： 目录主要用于实体（Entity）与数据访问层（Repository）控制
service ：层主要是业务类代码
controller ：负责页面访问控制

最后，启动 Application main 方法，至此一个 Java 项目搭建好了！
## 4.引入web模块
（1） 在pom.xml中添加支持web的模块：
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
pom.xml 文件中默认有两个模块：
* `spring-boot-starter `：核心模块，包括自动配置支持、日志和 YAML，如果引入了 `spring-boot-starter-web web` 模块可以去掉此配置，因为 `spring-boot-starter-web` 自动依赖了 `spring-boot-starter`。
* `spring-boot-starter-test` ：测试模块，包括 JUnit、Hamcrest、Mockito。

（2） 编写 Controller 内容：

```java
@RestController
public class HelloWorldController {
    @RequestMapping("/hello")
    public String index() {
        return "Hello World";
    }
}
```
`@RestController` 的意思就是 Controller 里面的方法都以 json 格式输出，不用再写什么 jackjson 配置的了！

启动主程序，打开浏览器访问 `http://localhost:8080/hello`，就可以看到效果了，有木有很简单！
![在这里插入图片描述](https://img-blog.csdnimg.cn/eb1e2eec9f334e0f89f36ecc190e4a68.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/f81466fff97f405796867fae3496d4ee.png)
## 5.单元测试（例）
>打开的`src/test/`下的测试入口，编写简单的 http 请求来测试；使用 mockmvc 进行，利用`MockMvcResultHandlers.print()`打印出执行结果。

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class HelloWorldControlerTests {

    private MockMvc mvc;

    @Before
    public void setUp() throws Exception {
        mvc = MockMvcBuilders.standaloneSetup(new HelloWorldController()).build();
    }

    @Test
    public void getHello() throws Exception {
        mvc.perform(MockMvcRequestBuilders.get("/hello").accept(MediaType.APPLICATION_JSON))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andDo(MockMvcResultHandlers.print())
                .andReturn();
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b7a46d7eced64ce59a2f29ca3b779f65.png)
## 6.开发调试（热启动）
>`Spring-boot-devtools`是一个为开发者服务的一个模块，其中最重要的功能就是自动应用代码更改到最新的App上面去。原理是在发现代码有更改之后，重新启动应用，但是速度比手动停止后再启动还要更快，更快指的不是节省出来的手工操作的时间。

（1）在pom.xml中添加`spring-boot-devtools` maven依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-devtools</artifactId>
	<optional>true</optional>
</dependency>
```
（2）完成配置

```xml
<plugin>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-maven-plugin</artifactId>
	<configuration>
		<fork>true</fork>
	</configuration>
</plugin>
```
>若出现爆红，查看具体报错，一般是需要刷新maven添加一下包
>![在这里插入图片描述](https://img-blog.csdnimg.cn/6e5b44963f124f17b37c1dcb7a56d444.png)



注：修改类时idea不会自动编译，因此需要开启自动编译![在这里插入图片描述](https://img-blog.csdnimg.cn/32ff6316dc654d49a3703ae0c7ff6c6c.png)

---

# 总结
`这里对文章进行总结：`
>使用 Spring Boot 可以非常方便、快速搭建项目，使我们不用关心框架之间的兼容性，适用版本等各种问题，我们想使用任何东西，仅仅添加一个配置就可以，所以使用 Spring Boot 非常适合构建微服务。

![在这里插入图片描述](https://img-blog.csdnimg.cn/35b738e70473480ca8d714d3a778c620.png)

Authors
:  杜小白
