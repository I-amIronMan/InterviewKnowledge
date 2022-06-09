### SpringBoot自动装配原理

#### pom.xml
+ spring-boot-dependencies:核心依赖在父工程中
+ 我们在写入或者引入一些Springboot依赖的时候，不需要指定版本，就因为有这些版本仓库

#### 启动器
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```
+ 启动器：springboot的启动场景；
+ 比如spring-boot-starter-web，他会帮助我们自动导入web环境所有的依赖；
+ springboot会将所有的功能场景，都变成一个个启动器；
+ 如果需要使用什么功能，只需要找到对应的启动器

### 主程序
```java
@SpringBootApplication
public class HellowordApplication {

    public static void main(String[] args) {
        // 将springboot应用启动
        SpringApplication.run(HellowordApplication.class, args);
    }

}
```
+ 注解  
@SpringBootApplication
```java
@SpringBootConfiguration //springboot的配置
    @Configuration //spring配置类
    @Component //说明这是一个spring组件

@EnableAutoConfiguration //自动配置
    @AutoConfigurationPackage //自动配置包
        @Import({Registrar.class}) // 自动配置包注册
    @Import({AutoConfigurationImportSelector.class}) // 自动配置导入选择

// 获取所有的配置
List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
```

获取候选的配置
```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```
META-INF/spring.factories


### 结论
springboot所有自动配置都是在启动的时候扫描并加载：spring.factories所有的自动配置类都在这里面，但是不一定都生效，要判断条件是否成立，
只要导入了对应的start，就有对应的启动器了，有了启动器，我们自动装配就会生效，然后就能配置成功。
1. springboot启动时。从类路径下META-INF/spring.factories获取指定的值；
2. 将这些自动配置的类导入容器，自动配置类就会生效，帮我们进行自动配置；
3. 以前需要手动配置的东西，现在springboot帮我们做了；
4. 整合javaEE解决方案和自动配置的东西都在spring-boot-autoconfigure-2.6.6.RELEASE.jar包下
5. 它会把所有需要导入的组件，以类名的方式返回，这些组件就会被添加到容器；
6. 容器中也会存在非常多的xxxAutoConfiguration的文件（@Bean），这些类给容器中导入了这个场景需要的所有组件，并自动配置。

### 主启动类怎么运行
#### SpringApplication
该类主要做了一下四件事情：
1. 推断应用的类型时普通的项目还是Web项目
2. 查找并加载所有可用初始化器，设置到initializers属性中
3. 找出所有的应用程序监听器，设置到listeners属性中
4. 推断并设置main方法的定义类，找到运行的主类

###对比一下xml和yaml的配置

```xml
<server>
    <port>8081</port>
</server>
```
```yaml
server:
    port: 8081
```

### webjars
1. 在springboot，我们可以使用以下方式处理静态资源
+ webjars    `localhost:8080/webjars/`
+ public, static, /**, resourses   `localhost/8080/`
2. 优先级：resourses > static > public

### SpringBoot常用注解

#### @`SpringBootApplication`

```java
// 这个注解是SpringBoot项目的基石，创建SpringBoot项目之后会默认在主类加上
@SpringBootApplication
public class SpringSecurityJwtGuideApplication {
      public static void main(java.lang.String[] args) {
        SpringApplication.run(SpringSecurityJwtGuideApplication.class, args);
    }
}
```

可以看作是 `@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解的集合。

- `@EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制
- `@ComponentScan`： 扫描被`@Component` (`@Repository`,`@Service`,`@Controller`)注解的 bean，注解默认会扫描该类所在的包下所有的类。
- `@Configuration`：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类

#### Spring Bean相关

##### `@Autowired`

自动导入对象到类中，被注入进的类同样要被 Spring 容器管理比如：Service 类注入到 Controller 类中。

##### `@Component`,`@Repository`,`@Service`, `@Controller`

我们一般使用 `@Autowired` 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 `@Autowired` 注解自动装配的 bean 的类,可以采用以下注解实现：

- `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。

##### `@RestController`

`@RestController`注解是`@Controller`和`@ResponseBody`的合集,表示这是个控制器 bean,并且是将函数的返回值直接填入 HTTP 响应体中,是 REST 风格的控制器。

单独使用 `@Controller` 不加 `@ResponseBody`的话一般是用在要返回一个视图的情况，这种情况属于比较传统的 Spring MVC 的应用，对应于前后端不分离的情况。`@Controller` +`@ResponseBody` 返回 JSON 或 XML 形式数据。

##### `@Scope`

声明 Spring Bean 的作用域。

**四种常见的 Spring Bean 的作用域：**

- singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
- prototype : 每次请求都会创建一个新的 bean 实例。
- request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- session : 每一个 HTTP Session 会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。

##### `@Configuration`

一般用来声明配置类，可以使用 `@Component`注解替代，不过使用`@Configuration`注解声明配置类更加语义化。

#### 常见的HTTP请求类型

- **GET** ：请求从服务器获取特定资源。举个例子：`GET /users`（获取所有学生）
- **POST** ：在服务器上创建一个新的资源。举个例子：`POST /users`（创建学生）
- **PUT** ：更新服务器上的资源（客户端提供更新后的整个资源）。举个例子：`PUT /users/12`（更新编号为 12 的学生）
- **DELETE** ：从服务器删除特定的资源。举个例子：`DELETE /users/12`（删除编号为 12 的学生）
- **PATCH** ：更新服务器上的资源（客户端提供更改的属性，可以看做作是部分更新）

##### Get请求

@GetMapping("users")` 等价于`@RequestMapping(value="/users",method=RequestMethod.GET)

##### Post请求

@PostMapping("users")` 等价于`@RequestMapping(value="/users",method=RequestMethod.POST)

##### Delete请求

@DeleteMapping("/users/{userId}")`等价于`@RequestMapping(value="/users/{userId}",method=RequestMethod.DELETE)

##### Put请求

@PutMapping("/users/{userId}")` 等价于`@RequestMapping(value="/users/{userId}",method=RequestMethod.PUT)

##### Patch请求

一般实际项目中，我们都是 PUT 不够用了之后才用 PATCH 请求去更新数据。

#### 前后端传值

##### `@PathVariable` 和 `@RequestParam`

`@PathVariable`用于获取路径参数，`@RequestParam`用于获取查询参数。

##### `@RequestBody`

用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且**Content-Type 为 application/json** 格式的数据，接收到数据之后会自动将数据绑定到 Java 对象上去。系统会使用`HttpMessageConverter`或者自定义的`HttpMessageConverter`将请求的 body 中的 json 字符串转换为 java 对象。
