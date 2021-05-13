# springboot-configs-guide
总结一下 springboot 配置文件的用法

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

1. resources 目录下的 bootstrap.yml(我们一般用这个配置来覆盖 spring 的一些默认行为)
2. 通过命令行指定 --spring.config.additional-location,
3. 通过命令行指定的 --spring.config.location=xxx.yml
3. ${PWD}/config/application.yml 也会自动读取
5. classpath 下面的 config/application.yml
6. ${PWD}} 下面的 application.yml 

```
备注： 这里的 PWD 是程序的工作空间
```
需要接介绍的是，我们可以同时指定多个文件当成配置文件，如果有冲突，后面的配置文件会覆盖前面的配置，例如：
```
java -jar aap.jar --spring.config.location=classpath:application.yml,app.yml
```
这里  `app.yml` 中的配置在冲突情况下会覆盖 `classhpath:application.yml` 中的配置.
### 文件格式
目前 springboot 支持2种格式的配置文件
* yml
* properties

如果 `properties` 格式文件不喜欢，没有关系，可以只将这个文件转化成 `yml` 格式的配置文件。
### profiles
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
这里的 {env} 就是环境，

因为 `yaml` 文件的特性，我们也可以将多个 `profile`写在一个 yml 文件中，只需要用 `---` 分开即可，比如
```
spring:
  profile: local
demo:
  mame: aaa

---

spring:
  profile: prod
demo:
  name: bbb
```

如果我们想要激活 如果我们想要激活某一个 `profile` 的配置，我们有三种方式,优先级从高到低

方法1: 

通过命令行参数指定,在 spring 的启动参数上加上
```
java -jar --spring.profiles.active=local
```

方法3：

在 application.yml 中配置 spring.profiles.active
```
spring:
    profiles:
        active: local
```

方法3:

通过`SPRING_PROFILES_ACTIVE` 环境变量来控制，如下
```
export SPRING_PROFILES_ACTIVE=local
java -jar app.jar
```

如果 `application-{env}.yml` 被激活，那么`application-{env}.yml` 中的配置将覆盖掉 `application.yml` 中的配置

## 配置引用
在定义一个公共配置以后，在其他的配置项中，还可以引用这个配置
```
demo:
  msg: hello ${name}
name: Bob
```

## 取值方式
springboot 有三种取值方式，优先级从高到低

1. 命令行参数

在 springboot 项目及中用到的配置，我们都可以通过命令行的方式指定，不管是 springboot 的一些内置配置项目，还是我们自己定义的配置，比如：
```yaml
demo:
    name: Bod
```
我们定义了 `demo.name`,并且在代理里面引用了这个配置
```
  @Value("${demo.name}")
```
我们也可以通过命令行设置 `demo.name` 的值，它将覆盖掉 `application.yml`的配置
```
java -jar app.jar --demo.name=Nick
```

2. 配置文件

写在 yml 或者 properties 文件中的值都能被读取

3. 环境变量

xxx.yyy.zzz 这样的配置，我们可以通过设置环境变量 XXX_YYY_ZZZ 这样的方式来注入值


## 默认值
我们在通过  Value 注解配置一些默认，如下：
```
@Value("${demo.name：Bob}")
```
在没有配置`demo.name` 或者环境变量，以及命令行参数的时候，就会使用默认值 `Bob`