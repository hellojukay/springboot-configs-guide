# springboot-configs-guide
一篇文章说清楚 springboot 的配置文件是咋回事

## 创建项目
创建一个标准的 springboot 项目，可以从这个页面生成模板项目
```
https://start.spring.io/
```
## 基本配置文件
创建项目以后默认会在 `resources` 目录生成 `application.properties` 文件，如果这个文件不存在也没有关系，可以自己生成这个文件.文件名字取别的不行

```
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── example
    │   │           └── springboot
    │   │               └── Application.java
    │   └── resources
    │       ├── application.properties
    │       ├── static
    │       └── templates
```
resouces 目录下的 application.properties ，还有一些其他的配置也会自动读取(优先级从高到低)

0. resources 目录下的 bootstrap.yml(我们一般用这个配置来覆盖 spring 的一些默认行为)
1. 通过命令行指定的 --spring.config.location=xxx.yml
2. ${PWD}/config/application.yml 也会自动读取
3. ${PWD}} 下面的 application.yml 

```
备注： 这里的 PWD 是程序的工作空间
```

### 文件格式
目前 springboot 支持2种格式的配置文件
* yml
* properties

如果 `properties` 格式文件不喜欢，没有关系，可以只将这个文件转化成 `yml` 格式的配置文件。
### 多文件
推荐的做法是，在 application.yml 中写入默认配置，通过其他的配置文件覆盖里面的配置，关于环境有关的信息，我们写在其他的文件中，比如数据库的帐号密码，其他服务的配置信息。这里我们就需要用到多个配置文件组合来配置了

```
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── example
    │   │           └── springboot
    │   │               ├── Application.java
    │   │               └── DemoController.java
    │   └── resources
    │       ├── application-local.yml
    │       ├── application.yml
```
类似的 `application-{env}.yml` 的形式，
这里的 {env} 就是环境，如果我们想要激活 application-local.yml 的配置，我们有三种方式

方法1: 

通过命令行参数指定,在 spring 的启动参数上加上
```
java -jar --spring.profiles.active=local
```

方法2:

通过`SPRING_PROFILES_ACTIVE` 环境变量来控制

方法1：

在 application.yml 中配置 spring.profiles.active
```
spring:
    profiles:
        active: local
```



