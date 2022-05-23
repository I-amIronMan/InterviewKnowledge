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
