# 构建一个RESTful Web Service（[Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)）
本文详细介绍了基于Spring创建一个“hello world” [RESTful web service](https://spring.io/understanding/REST)工程的步骤。

## 目标
构建一个service，接收如下HTTP GET请求：
```bash
http://localhost:8080/greeting
```

并返回如下JSON格式的问候语：
```JSON
{"id":1,"content":"Hello, World!"}
```

你也可以通过指定查询字符串中的可选参数name来定制问候语：
```bash
http://localhost:8080/greeting?name=User
```
参数name的值覆盖了默认值“World”，得到的响应为：
```JSON
{"id":1,"content":"Hello, User!"}
```
## 准备工作
* 大约15分钟
* 一个文本编辑器或IDE
* [JDK1.8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)或更高
* [Gradle 4+](https://gradle.org/install/)或[Maven 3.2+](https://maven.apache.org/download.cgi)
* 你也可以使用直接import该工程
  * [Spring Tool Suite(STS)](https://spring.io/guides/gs/sts/)
  * [IntelliJ IDEA](https://spring.io/guides/gs/intellij-idea/)

## 如何完成
如同所有的[Spring入门教程](https://spring.io/guides)，你可以选择一步一步的自己实现，也可以跳过基本的设置步骤。最终，你都将得到一份可以正常运行的代码。

如果选择按步实现，继续下一节。

如果选择跳过基本的安装部分，则执行以下命令：
* [下载](https://codeload.github.com/spring-guides/gs-rest-service/zip/master)并解压缩本教程的源代码库，或使用[Git](https://spring.io/understanding/Git)进行clone：
```bash
git clone https://github.com/spring-guides/gs-rest-service.git
```
* 切换当前目录到`gs-rest-service/initial`
* 跳到`创建资源描述类（Create a resource representation class）`步骤

**完成后**，可以与`gs-rest-service/complete`中的代码对比一下，确保正确。

## >[方法一]使用Gradle进行构建
首先建立一个基本的构建脚本。基于Spring构建应用时，可以用使用任何的构建系统。这里我们以[Gradle](https://gradle.org/)和[Maven](https://maven.apache.org/)为例。如果不熟悉它们，请参考[Building Java Projects with Gradle](https://spring.io/guides/gs/gradle/)或者[Building Java Projects with Maven](https://spring.io/guides/gs/maven/)。

### 建立目录结构
在你选定的工程目录下，建立如下子目录结构；例如，在*nix系统中使用`mkdir -p src/main/java/hello`命令：
```
└── src
    └── main
        └── java
            └── hello
```
### 创建Gradle构建文件
下面是[初始的gradle构建文件](https://github.com/spring-guides/gs-rest-service/blob/master/initial/build.gradle)。
`build.gradle`
```js
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.5.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-rest-service'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```
[Spring Boot gradle](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/)插件提供了许多方便的功能：

* 它收集类路径上的所有jar并构建一个可运行的“über-jar”，这使得执行和传输服务更加方便。

* 它搜索`public static void main（）`方法以标记为可运行的类。

* 它提供了一个内置的依赖项解析器，它设置版本号以匹配[Spring Boot依赖项](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)。您可以覆盖任何您希望的版本，但它将默认为Boot的所选版本集。

## >[方法二]使用Maven进行构建
首先建立一个基本的构建脚本。基于Spring构建应用程序时，可以使用任何的构建系统，这里包含您使用[Maven](https://maven.apache.org/)所需的代码。如果您不熟悉Maven，请参阅[使用Maven构建Java项目](https://spring.io/guides/gs/maven/)。

### 建立目录结构
在你选定的工程目录下，建立如下子目录结构；例如，在*nix系统中使用`mkdir -p src/main/java/hello`命令：
```
└── src
    └── main
        └── java
            └── hello
```

### 创建Maven构建文件
`pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-rest-service</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.jayway.jsonpath</groupId>
            <artifactId>json-path</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <properties>
        <java.version>1.8</java.version>
    </properties>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```
[Spring Boot gradle](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/)插件提供了许多方便的功能：

* 它收集类路径上的所有jar并构建一个可运行的“über-jar”，这使得执行和传输服务更加方便。

* 它搜索`public static void main（）`方法以标记为可运行的类。

* 它提供了一个内置的依赖项解析器，它设置版本号以匹配[Spring Boot依赖项](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)。您可以覆盖任何您希望的版本，但它将默认为Boot的所选版本集。

## >[方法三]使用IDE进行构建
* 阅读如何将本工程直接导入[Spring Tool Suite](https://spring.io/guides/gs/sts/)。

* 阅读如何将本工程直接导入[IntelliJ IDEA](https://spring.io/guides/gs/intellij-idea/)。

## 创建资源描述类（Create a resource representation class）
现在已经建立了工程和构建系统，下面创建你的web service。

首先考虑服务间的交互（service interactions）。

该服务将处理`/greeting`的`GET`请求，其查询字符串包含一个可选的`name`参数。`GET`请求应返回`200 OK`响应以JSON形式存放于greeting的body中。格式如下：
```JSON
{
    "id": 1,
    "content": "Hello, World!"
}
```
`id域`是问候语的唯一标识，`content域`是问候语的文本描述。

为了对问候语的描述进行建模，创建了一个资源描述类。提供了一个包含域（id和content）、构造方法和访问器（getter和setter）的pojo（pain old java object）类：

`src/main/java/hello/Greeting.java`
```Java
package hello;

public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
```
>注意：下面的步骤中，Spring使用了Jackson JSON库将Greeting类型的实例编码成JSON格式。

下面创建资源控制器（resource controller）来发送这些问候语。

## 创建资源控制器（Create a resource controller）
采用Spring构建RESTful web services时，采用控制器处理HTTP请求。这些组件可以通过`@RestController`注释轻松识别，下面的`GreetingController`通过返回`Greeting`类的新实例来处理`/greeting`的`GET`请求：

`src/main/java/hello/GreetingController.java`
```Java
package hello;

import java.util.concurrent.atomic.AtomicLong;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value="name", defaultValue="World") String name) {
        return new Greeting(counter.incrementAndGet(),
                            String.format(template, name));
    }
}
```
这个controller很简单，但是其内部做了大量工作，麻雀虽小，五脏俱全。我们一步一步来分解。

`@RequestMapping`注解确保对/greeting的HTTP请求映射到greeting()方法。

>注意：上述例子中没有写明GET、PUT、POST等等。这是因为@RequestMapping注解默认情况下映射所有的HTTP操作。使用@RequestMapping(method=GET)指定只映射GET请求。

`@RequestParam`把查询字符串中`name`参数的值绑定到`greeting()`方法的`name`参数上。该查询参数不是必须的（`required=false`）；如果请求时没有指定该参数，则使用其默认值“World”（`defaultValue`）。

方法体的实现创建并返回了一个新的`Greeting`对象，该对象的`id`属性每次自增1（基于`counter`），`content`属性采用`template`和`name`组合而来。

传统的MVC控制器和上述RESTful web service控制器的一个关键区别在于：HTTP响应体的创建方式。前者采用视图层技术（[view technology](https://spring.io/understanding/view-templates)）实现把服务器端的数据渲染为HTML，后者则返回一个`Greeting`对象。对象数据将会直接以JSON格式写到HTTP响应中。

此代码使用Spring 4的新`@RestController`注解，它将类标记为控制器，其中每个方法都返回一个域对象而不是视图。这是`@Controller`和`@ResponseBody`汇总在一起的简写。

`Greeting`对象必须转换成JSON格式。由于Spring支持HTTP报文转换，你不需要手工进行转换。由于[Jackson 2](https://github.com/FasterXML/jackson-docs/wiki/JacksonHome)在classpath中，因而Spring的[MappingJackson2HttpMessageConverter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/http/converter/json/MappingJackson2HttpMessageConverter.html)会自动将`Greeting`实例转换为JSON格式。

## 使应用程序可执行（Make the application executable）
虽然可以将这个service打包成传统的[WAR](https://spring.io/understanding/WAR)文件，并部署到一个外部的应用服务器上，但是我们采用了一个更简单的方式：创建一个独立的（standalone）应用程序。把所有文件打包到一个可执行的JAR文件中，由古老的`main()`方法驱动。采用Spring提供的嵌入的[Tomcat](https://spring.io/understanding/Tomcat) Servlet容器作为HTTP运行时环境，不需要部署一个外部的运行时环境实例。

`src/main/java/hello/Application.java`
```Java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```
`@SpringBootApplication`是一个便利注解，它包含了以下注解的所有功能：

* `@Configuration`注解将类标记为应用程序上下文的bean定义源。

* `@EnableAutoConfiguration`注解告诉Spring Boot根据类路径设置，其他bean和各种属性设置开始添加bean。它会基于你的classpath的内容打开合理的默认行为。例如，由于应用程序依赖于嵌入版的Tomcat（tomcat-embed-core.jar），一个Tomcat服务器会自动建立并进行合理的默认配置。应用程序也依赖于Spring MVC（spring-webmvc.jar），一个Spring MVC DispatcherServlet会为你配置和注册--不需要web.xml！自动配置（Auto-configuration）是一种强大的灵活的机制。详请参考API文档。

* 通常你会为Spring MVC应用程序添加`@EnableWebMvc`注解，但Spring Boot会在类路径上看到**spring-webmvc**时自动添加它。这会将应用程序标记为Web应用程序并激活关键行为，例如设置`DispatcherServlet`。

* `@ComponentScan`注解告诉Spring在hello包中寻找其他组件，配置和服务，允许它找到控制器。该注解将告诉Spring递归地搜索hello包和其子包中直接或间接标记为@Component的类。这确保Spring发现并注册GreetingController，由于它被标记为@Controller，而@Controller是一类@Component注解。

`main()`方法使用Spring Boot的`SpringApplication.run()`方法来启动应用程序。main()方法调用了SpringApplication帮助类，把Application.class传递给它的run()方法作为参数。这样Spring就会去从Application读取注解，并作为[Spring application context](https://spring.io/understanding/application-context)的一个组件进行管理。

您是否注意到没有一行XML？也没有**web.xml**文件。此Web应用程序是100％纯Java，您无需处理配置任何管道或基础结构。

## 构建可执行JAR（Build an executable JAR）
您可以使用Gradle或Maven从命令行运行该应用程序。 或者，您可以构建一个包含所有必需依赖项，类和资源的可执行JAR文件，并运行该文件。 这使得在整个开发生命周期中，跨不同环境等将服务作为应用程序发布，版本和部署变得容易。

如果您使用的是Gradle，则可以使用`./gradlew bootRun`运行该应用程序。 或者您可以使用`./gradlew build`构建JAR文件。 然后你可以运行JAR文件：
```bash
java -jar build/libs/gs-rest-service-0.1.0.jar
```

如果您使用的是Maven，则可以使用`./mvnw spring-boot:run`运行该应用程序。 或者您可以使用`./mvnw clean`包构建JAR文件。 然后你可以运行JAR文件：
```bash
java -jar target/gs-rest-service-0.1.0.jar
```

>上面的过程将创建一个可运行的JAR。 您也可以选择[构建经典WAR文件](https://spring.io/guides/gs/convert-jar-to-war/)。

控制台输出运行日志。该服务会在几秒钟内启动并运行。

## 测试（Test the service）
现在service已经启动，访问[http://localhost:8080/greeting](http://localhost:8080/greeting)，你会看到：
```JSON
{"id":1,"content":"Hello, World!"}
```

查询字符串中指定一个`name`参数，如[http://localhost:8080/greeting?name=User](http://localhost:8080/greeting?name=User)。`content`的值从“Hello, World!”变为“Hello, User!”：
```JSON
{"id":2,"content":"Hello, User!"}
```

此更改表明`GreetingController`中的`@RequestParam`注解正在按预期工作。`name`参数的默认值为“World”，但始终可以通过查询字符串显式覆盖。

另请注意`id`属性从1更改为2。这证明您在多个请求中针对相同的`GreetingController`实例，并且其计数器字段在每次调用时按预期递增。

## 总结（Summary）
恭喜！你已经开发出了一个基于Spring的RESTful web service。

## 参考
[1] [2019/03/30官方英文原文：Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)

[2] [2014/01/26翻译：构建一个RESTful Web Service（Building a RESTful Web Service）](https://blog.csdn.net/kingzone_2008/article/details/18793237)
