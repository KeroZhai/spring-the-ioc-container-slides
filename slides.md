---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

# Spring Framework

## 核心技术简介之 IoC 容器

---
layout: section
---

# 什么是 IoC 容器？

---

# 什么是 IoC 容器？

<br>

Spring Framework 提供了很多核心功能，在这些功能中，最重要的就是 IoC 容器。

某种程度上来说，可以说 Spring Framework 就是一个 IoC 容器。

---

# IoC

<br>

IoC，全称 Inversion of Control，是一种设计思想，翻译成中文就是“控制反转”，也被熟知为依赖注入（Dependency Injection，DI）。

在应用中会有各种各样的对象，其中一些对象需要**依赖**其他对象才能正常工作，例如有一个代表车的类，它依赖了引擎：

```java {all|2,5}
public class Car {
    private SuperEngine engine;

    public Car() {
        this.engine = new SuperEngine();
    }
}
```

我们在构造方法中**显式、主动地**实例化了所依赖的对象，也即 `Car` 这个类直接依赖了 `SuperEngine` 这个类。

---

# IoC


如果在 IoC 场景中，我们可能会改写成：

```diff
public class Car {
-   private SuperEngine engine;
+   private Engine engine;


-   public Car() {
-       this.engine = new SuperEngine();
+   public Car(Engine engine) {
+       this.engine = engine;
    }
}
```

---

# IoC

即：

```java {all|2,4,5,6}
public class Car {
    private Engine engine; // 一般是接口类

    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

<v-click>

这里类 `Car` 依赖了 `Engine` 这个接口类，但并没有明确依赖哪个**具体的实现类**，相反，我们可以在实例化类 `Car` 时将依赖传递进去：

```java
SuperEngine superEngine = new SuperEngine(); // SuperEngine 实现了 Engine 接口
Car car = new Car(superEngine);
```

</v-click>

---

# 容器

<br>

简单来说，容器就是用于*存放*对象的一个地方。

有了容器之后，我们就不需要实例化对象，而是从容器中获取，换句话说，容器负责创建我们所需要的对象。

对象的依赖关系（即所依赖的其他对象）通过构造方法参数（也即前面第二段代码所示）、（实例化它们的）工厂方法参数或者在对象被实例化或从工厂方法返回后要被设置的属性来定义，当容器创建对象时，就会把这些依赖**注入**其中。

---
layout: section
---

# Spring IoC 容器和 Bean

---

# Spring IoC 容器和 Bean

Spring IoC 容器

包 `org.springframework.beans` 和 `org.springframework.context` 是 Spring IoC 容器的基础。接口类 `BeanFactory` 提供了一种高级配置机制，能够管理任何类型的对象，而 `ApplicationContext` 是其子接口，添加了以下内容：

* 更容易和 Spring 的 AOP 功能集成
* 消息资源处理（用于国际化）
* 事件发布
* 应用层特定的上下文，例如用于 Web 应用的 `WebApplicationContext`

简言之，`BeanFactory` 提供了配置框架和基础功能，而 `ApplicationContext` 增加了更多复杂应用特定的功能。

---

# Spring IoC 容器和 Bean

Bean

在 Spring 中，**构成应用主干并由 Spring IoC 容器管理的对象**统称为 Bean。一个 Bean 就是一个被 Spring IoC 容器**实例化、组装和管理**的对象。Bean 和它们的依赖体现在容器所使用的*配置元数据*中。

<v-click>

这里的 Bean 不同于 JavaBean。JavaBean 是一个标准，它规定如果一个类满足下列要求，则是一个 JavaBean：

</v-click>
<v-clicks>

* 所有的属性都是私有的，需要使用 getters/setters 访问；
* 有一个 `public` 的无参构造方法；
* 实现了 `Serializable` 接口。

</v-clicks>
<br>
<v-click>

> Java 名称来源于一个盛产咖啡豆的小岛（所以 Java 的图标是一杯咖啡），而 bean 直译为豆子，所以 JavaBean 其实就是咖啡豆。此外，如果查看 Java 编译后生成的 class 文件的二进制信息，可以看到前四个字节的值为“cafe babe”。

</v-click>

---
layout: section
---

# 容器概览

---

# 容器概览

<br>

接口 `org.springframework.context.ApplicationContext` 代表着 Spring IoC 容器，并通过读取*配置元数据*来实例化、配置和组装 bean。配置元数据主要用于表示组成应用的对象以及它们之间的依赖关系。

除了 `ApplicationContext` 接口以外，Spring 也附带了几个它的实现类。在单应用中，常见的做法是创建一个 `ClassPathXmlApplicationContext` 或者 `FileSystemXmlApplicationContext`。

虽然 XML 一直是定义配置元数据的传统格式，但可以通过提供少量 XML 配置来以声明方式支持额外的元数据格式，即使用 Java 注解或者代码。

---

# 容器概览

<br>

下图显示了 Spring 是如何工作的。应用里的业务对象结合配置元数据，当 `ApplicationContext` 被创建和实例化后，就会有一个完全配置且可运行的系统或应用。

<img style="margin: auto" src="/images/container-magic.png">

<!-- 容器扩展点介绍？ -->

---

# 依赖注入

<br>

首先了解一下容器是怎么进行依赖注入的。

依赖注入主要有两个变种：

* 基于构造方法的依赖注入
* 基于 setter 方法的依赖注入


---

# 依赖注入

基于构造方法的依赖注入

基于构造方法的依赖注入就是通过容器调用具有一个或多个参数的构造方法来完成的，每个参数代表一个依赖项。

<div style="display: flex; justify-content: space-around;">

<column>

```java
public class Car {

    private Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }

}
```

</column>

<column v-click>

也可以通过提供一个静态工厂方法供容器调用：

```java
public class Car {

    private Engine engine;

    public static Car newInstance(Engine engine) {
        Car car = new Car();
        car.setEngine(engine);

        return car;
    }

}
```

</column>

</div>

<!-- 由于这两者基本上是类似地，因此之后主要以构造方法为例进行介绍。 -->

---

# 依赖注入

基于 setter 方法的依赖注入

基于 setter 方法的依赖注入就是通过容器在调用一个构造方法或静态工厂方法实例化 bean 后，调用相应地 setter 方法来初始化它。例如：

```java
public class Car {

    private Engine engine;

    public void setEngine(Engine engine) {
        this.engine = engine;
    }

}
```

---

# 依赖注入

<br>

由于基于 setter 方法的依赖注入实际上是在通过调用构造方法实例化 bean 之后，因此这两者实际上可以混用。

实际开发过程中，我们一般推荐必选依赖使用构造方法，而可选依赖使用 setter 方法。

Spring 团队更推荐使用构造方法注入，因为这让我们将应用组件实现为不可变对象，同时确保了必须的依赖不会为 `null`。同时，通过构造方法注入过依赖的组件总是以一个完全初始化好的状态被返回。另一方面，随着依赖增多，构造方法的参数也会显而易见地变得越来越多，这更容易让我们意识到代码可能需要被优化或重构。

---

# 方法注入

<br>

在大多数应用场景下，容器中大部分的 bean 都是单例。当单例 bean 依赖单例 bean 或非单例 bean 依赖非单例 bean 时，一般没有什么问题，然而当单例 bean 依赖一个非单例 bean 时，问题就来了：

设想单例 bean A 需要使用非单例 bean B，可能在每一个方法中都要用到一个新的 B 实例。由于 bean A 是单例，因此容器只会创建它一次，也就意味着依赖只会被注入一次，容器无法在每次 bean A 需要一个新的 bean B 实例时提供给它。

---

# 方法注入

<br>

一个解决方法是放弃一些控制反转，我们可以通过让 bean A 实现 `ApplicationContextAware` 接口意识到容器的存在，从而手动从容器中获取所需的依赖。

<v-click>

```java
public class A implements ApplicationContextAware {
    private ApplicationContext applicationContext;

    public B getB() {
        return applicationContext.getBean("b", B.class);
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        this.applicationContext = applicationContext;
    }
}
```

</v-click>

---

# 方法注入

这种情况下，Spring 提供了一种名为方法注入的进阶功能来处理这类问题。

方法注入分为两种：

* 查找方法注入
* 任意方法替换

<!-- 由于任意方法替换基本不会用到，所以就不再介绍。 -->

---

# 方法注入

查找方法注入

查找方法注入就是容器实现或覆盖所管理的 bean 的特定方法，以提供容器中的其他 bean。如前所述，这一般涉及到非单例 bean 依赖。

<flex-container>

<column v-click>

通过使用查找方法注入，上述例子可以改写成：

```java
public abstract class A {
    @Lookup("b")
    public abstract B getB();
}
```

这里的属性不是必须的，如果不提供 bean 的名称，容器则会尝试寻找指定类型（即 `B`）的 bean。

</column>
<column v-click>

注意如果要使用到 Spring 的 bean 扫描机制，则应该是提供一个附带 stub 实现的普通类，因为抽象类会被默认忽略。

```java
public class A {
    @Lookup("b")
    public B getB() {
        return null;
    }
}
```

</column>

</flex-container>

<v-click>

> Spring 框架是通过使用 CGLIB 库提供的字节码生成功能来动态生成对应 bean 的子类对象来实现或覆盖特定方法，因此要求 bean 的类、以及要覆盖的方法不能是 `final` 的。

</v-click>

---

# Bean 范围

<br>
<p v-if="$slidev.nav.clicks === 0">
当创建 bean 定义时，相当于是创建一个用于创建 bean 实例的“食谱”。这也意味着，就像使用类一样，我们可以根据一份 bean 定义创建很多对象实例。除了可以控制从 bean 定义创建的对象的依赖、配置等，还可以控制对象的作用范围。
</p>

<v-click>

| 范围 | 描述 |
| --- | --- |
| 单例（singleton）| 默认的范围。一个 bean 定义在容器中只会存在一个实例。 |
| 原型（prototype）| 一个 bean 定义在容器中可以存在多个实例。 |
| 请求（request）| 限定一个 bean 定义在单个 HTTP 请求生命周期中。即每个 HTTP 请求都会使用一个新的 bean，仅在适用于 Web 的 `ApplicationContext` 中可用。 |
| 会话（session）| 限定一个 bean 定义在 HTTP `Session` 生命周期中，仅在适用于 Web 的 `ApplicationContext` 中可用。 |
| 应用（application）| 限定一个 bean 定义在 `ServletContext` 生命周期中，仅在适用于 Web 的 `ApplicationContext` 中可用。 |
| Web 套接字（websocket）| 限定一个 bean 定义在 `WebSocket` 生命周期中，仅在适用于 Web 的 `ApplicationContext` 中可用。 |

</v-click>

---

# Bean 范围

<br>

当依赖了限定了范围的 bean 时，典型的即前面提到的单例 bean 依赖了非单例 bean，除了方法注入以外，我们也可以使用代理。配置代理的方式取决于配置元数据的提供方式，以基于 Java 的配置元数据为例：

<!-- 后面会再次提到 -->

```java
@Scope(scopeName = "session", proxyMode = ScopedProxyMode.TARGET_CLASS)
```

如此当 bean A 依赖了范围为 `session` 的 bean B 时，被注入的其实是 bean B 的一个**代理类**的对象实例。此时调用该对象实例的相关方法时，会去动态获取真正的 bean B，再调用对应的方法。

除了 `TARGET_CLASS` 以外，`ScopedProxyMode` 还有 `INTERFACES`、`DEFAULT` 以及 `NO`。`TARGET_CLASS` 和 `INTERFACES` 分别表示创建基于类的 CGLIB 代理和基于接口的 JDK 动态代理。`DEFAULT` 通常等同于 `NO`，即不创建代理，区别在于它依然会遵从通过组件扫描机制指定的其他配置。

---
layout: section
---

# 配置元数据

---
clicks: 5
---


# 配置元数据

<br>

如前所述，配置元数据主要是 bean 及其相关依赖的信息，用于告诉 Spring IoC 容器如何创建、管理 bean，一共有三种提供方式：

<v-clicks v-if="$slidev.nav.clicks < 4">

* 基于 XML 的配置元数据
* 基于 Java 注解的配置元数据
* 基于 Java 的配置元数据

</v-clicks>
<div v-else-if="$slidev.nav.clicks === 4">

* 基于 XML 的配置元数据
* 基于 Java 注解的配置元数据（依赖前者或后者才能工作）
* 基于 Java 的配置元数据

</div>

<v-clicks at="5">

<div>

1. 基于 XML 的配置元数据
2. 基于 Java 的配置元数据
3. 基于 Java 注解的配置元数据

</div>

</v-clicks>

---
clicks: 2
---

# 基于 XML 的配置元数据

<br>

将 bean 配置为顶级 `<beans/>` 元素内的 `<bean/>` 元素

<div v-if="$slidev.nav.clicks <= 1">

```xml {all|9}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="car" class="com.foo.Car">
        <!-- collaborators and configuration for this bean go here -->
        <property name="engine" autowire="byType">
    </bean>

    <bean id="superEngine" class="com.foo.SuperEngine">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->
</beans>
```

</div>

<div v-else>

```xml {all|9}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="car" class="com.foo.Car">
        <!-- collaborators and configuration for this bean go here -->
        <property name="engine" ref="superEngine">
    </bean>

    <bean id="superEngine" class="com.foo.SuperEngine">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->
</beans>
```

</div>

---

# 基于 Java 的配置元数据

`@Bean` 和 `@Configuration`

基于 Java 的配置元数据的核心部分就是标注了注解 `@Configuration` 的类和标注了注解 `@Bean` 的方法。

在一个类上标注 `@Configuration` 方法指示它的主要作用就是作为 bean 定义的来源。此外，`@Configuration` 类允许通过调用同一类中的其他 `@Bean` 方法来定义 bean 间的依赖关系。

注解 `@Bean` 用于指示被标注的方法负责实例化、配置并初始化一个需要被 Spring IoC 容器管理的对象。默认情况下，bean 定义的名称和方法名相同。

<flex-container>
<column>

```java
@Configuration
public class AppConfiguration {

    @Bean
    public MyServiceImpl myService() {
        return new MyServiceImpl();
    }
}
```

</column>
<column>

这里的 `AppConfiguration` 类充当了 XML 中 `<beans/>` 元素的角色，方法 `myService()` 则与 `<bean/>` 元素作用相同。

</column>
</flex-container>

---

# 基于 Java 的配置元数据

通过使用 `AnnotationConfigApplicationContext` 初始化 Spring IoC 容器

使用 `AnnotationConfigApplicationContext` 时，我们需要提供一个 `@Configuration` 类作为输入。

```java
public void main(String[] args) {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfiguration.class);
    MyServiceImpl myService = applicationContext.getBean(MyServiceImpl.class);

    myService.doStuff();
}
```

此时除了 `Bean` 方法提供的 bean 定义以外，作为输入的 `AppConfiguration` 类本身也会在容器中被注册为一个 bean 定义。

---

# 基于 Java 的配置元数据

编程式地初始化容器

不同于上面通过构造方法传入配置类，也可以使用 `register(Class...)` 方法动态注册。

```java
public void main(String[] args) {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext();

    applicationContext.register(AppConfiguration.class, OtherConfiguration.class);
    applicationContext.refresh();

    MyServiceImpl myService = applicationContext.getBean(MyServiceImpl.class);

    myService.doStuff();
}
```

---

# 基于 Java 的配置元数据

启用组件扫描

可以在 `@Configuration` 类上额外使用 `@ComponentScan` 注解来启用组件扫描：

```java
@Configuration
@ComponentScan(basePackages = "com.acme")
public class AppConfiguration {
    // ...
}
```

默认组件扫描会在给定的包下寻找标注了 `@Component` 注解的类，并注册为容器中的 bean 定义。使用 `scan(String...)` 方法也可以手动扫描指定的包，例如：

```java
public void main(String[] args) {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfiguration.class);

    applicationContext.scan("com.foo");
    applicationContext.refresh();
    // ...
}
```

---

# 基于 Java 的配置元数据

<br>

如前所述，`@Bean` 注解一般使用在标注了 `@Configuration` 注解的类中，但也可以使用在标注了 `@Component` 注解的类中。

前者被称为全量模式，后者被称为“轻量”模式。不同于全量模式，当 `@Bean` 方法被定义在标注了 `@Component` 注解的类中时，它们无法声明同类 bean 之间的依赖，且不应该调用其他的 `@Bean` 方法。

<!-- 后面会详细讲到依赖 -->

---
layout: section
---

# 使用 `@Bean` 注解

---

# 使用 `@Bean` 注解

声明 bean

<flex-container>
<column>

```java
@Configuration
public class AppConfiguration {

    @Bean
    public MyServiceImpl myService() {
        return new MyServiceImpl();
    }
}
```

</column>
<column>

```xml
<beans>
  <bean id="myService" class="com.acme.MyServiceImpl"/>
</beans>
```

</column>
</flex-container>

两种声明都使得容器中有一个名为 `myService` 的 bean 可用，并且绑定到了一个类型为 `com.acme.MyServiceImpl` 的对象实例上。

---

# 使用 `@Bean` 注解

声明 bean

默认 bean 的名称即为方法名，但也可以额外指定，例如：

<flex-container>
<column>

```java
@Bean("theThing")
public Thing thing() {
    return new Thing();
}
```

</column>
<column>

一个主要的 bean 名称以及一些别名：

```java
@Bean({ "theThing", "aThing"})
public Thing thing() {
    return new Thing();
}
```

</column>
</flex-container>

---

# 使用 `@Bean` 注解

声明 bean

也可以使用 `default` 方法来定义 bean，这使得可以通过实现其他接口来组合 bean 配置：

```java
public interface BaseConfig {

    @Bean
    default MyServiceImpl myService() {
        return new MyServiceImpl();
    }
}

@Configuration
public class AppConfig implements BaseConfig {
    // ...
}
```

---

# 使用 `@Bean` 注解

声明 bean

也可以将 `@Bean` 方法的返回值声明为接口或者基类类型，例如：

```java
@Configuration
public class AppConfiguration {

    @Bean
    public MyService myService() {
        return new MyServiceImpl(); // `MyServiceImpl` 实现了 `MyService`
    }
}
```

只有在实例化受影响的单例 bean 后，容器才知道实际类型（`MyServiceImpl`）。非懒加载的单例 bean 会根据它们的声明顺序进行实例化，因此可能会看到不同的类型匹配结果，具体取决于另一个组件何时尝试通过未声明的类型进行匹配（例如 `@Autowired MyServiceImpl myService`，它仅在 `myService` 实例化后才能被正确解析）。

<!-- 后面会讲到 `@Autowired` 注解 -->

> 如果使用和定义 bean 时同样的类型来引用它，则也不会存在这样的问题。然而，对于实现了多个接口或可能通过实现类型引用的 bean，则 `@Bean` 方法的返回值类型应尽可能地准确。

---

# 使用 `@Bean` 注解

Bean 依赖

`@Bean` 方法可以有任意数量的参数，这些参数描述了构建这个 bean 所需要的依赖。例如：

```java
@Configuration
public class AppConfiguration {
    @Bean
    public Foo(Bar bar) {
        return new Foo(bar);
    }
}
```

其实就是基于构造器的依赖注入。

---

# 使用 `@Bean` 注解

生命周期回调

通过 `@Bean` 定义的 bean 支持常规的生命周期回调，可以使用 JSR-250 提供的 `@PostConstruct` 和 `@PreDestroy` 注解。也可以使用 Spring 提供的的生命周期回调，当一个 bean 实现了 `InitializingBean`、`DisposableBean` 或 `LifeCycle` 接口时，对应的方法会自动被容器在合适的时机调用。

<!-- 后面会提到 JSR-250 -->

标准的 `*Aware` 接口（例如 `BeanFactoryAware`、`BeanNameAware`、`MessageSourceAware`、`ApplicationContextAware` 等）同样也全量支持。

此外，`@Bean` 注解本身也支持指定任意的初始化或者销毁回调方法，例如:

<flex-container>
<column>

```java
public class BeanOne {
    public void init() {
        // initialization logic
    }

    public void cleanup() {
        // destruction logic
    }
}
```

</column>
<column>

```java
@Configuration
public class AppConfig {
    @Bean(initMethod = "init", destroyMethod = "cleanup")
    public BeanOne beanOne() {
        return new BeanOne();
    }
}
```

</column>
</flex-container>

---

# 使用 `@Bean` 注解

生命周期回调

默认情况下，基于 Java 配置定义的 bean 如果有名为 `close` 或 `shutdown` 的 `public` 方法，则会自动被识别为销毁回调方法。如果有这类方法，但并不想被注册为回调方法，则需要指明 `destroyMethod = ""` 来禁用默认自动推断模式。

当然，由于通过 `@Bean` 方法我们是手动实例化 bean，因此初始化逻辑可以手动完成：

```java
@Configuration
public class AppConfig {
    @Bean(destroyMethod = "cleanup")
    public BeanOne beanOne() {
        BeanOne beanOne = new BeanOne();

        beanOne.init();
    
        return beanOne;
    }
}
```

---

# 使用 `@Bean` 注解

指明 bean 范围

使用 `@Scope` 注解来指明 bean 的作用范围，默认为 `singleton`，例如：

```java
@Configuration
public class AppConfig {
    @Bean
    @Scope("prototype")
    public Encryptor encryptor() {
        // ...
    }
}
```

前面提到依赖限定了范围的 bean 时，我们可以使用代理，除了直接通过 `@Scope` 注解以外，Spring 也提供了额外几个基于 `@Scope` 的*组合注解*：`@RequestScope`、`@SessionScope` 和 `@ApplicationScope`。它们的作用就是在指明了 `scopeName` 同时，指明要使用的 `scopedProxyMode` 为 `TARGET_CLASS`。

<!-- 关于组合注解之后会介绍 -->

---
layout: section
---

# 使用 `@Configuration` 注解

---

# 使用 `@Configuration` 注解

注入 bean 间依赖

前面提到，在 `@Configuration` 类中定义 bean 时可以直接注入同类中的其他 bean，而在 `@Component` 类中则不支持。例如：

```java
@Configuration
public class AppConfig {
    @Bean
    public BeanOne beanOne() {
        return new BeanOne(beanTwo());
    }

    @Bean
    public BeanTwo beanTwo() {
        return new BeanTwo()
    }
}
```

---

# 使用 `@Configuration` 注解

查找方法注入

<flex-container>
<column>

```java
public abstract class CommandManager {
    protected abstract Command createCommand();
}
```

</column>
<column>

```java
@Configuration
public class AppConfig {
    @Bean
    @Scope("prototype")
    public AsyncCommand asyncCommand() {
        return new AsyncCommand(); // 实现了 `Command` 接口
    }

    @Bean
    public CommandManager {
        return new CommandManger() {
            @Override
            protected Command createCommand() {
                return asyncCommand();
            }
        };
    }
}
```

</column>
</flex-container>

---

# 使用 `@Configuration` 注解

工作原理简介

```java
@Configuration
public class AppConfig {
    @Bean // 单例
    public Foo foo() {
        return new Foo(baz()); // 调用 `baz()` 方法返回一个 `Baz` 对象实例
    }

    @Bean // 单例
    public Bar bar() {
        return new Bar(baz()); // 调用 `baz()` 方法返回一个 `Baz` 对象实例
    }

    @Bean // 单例
    public Baz baz() {
        return new Baz();
    }
}
```

<v-click>

所有的 `@Configuration` 类实际上都借助 CGLIB 创建了其代理子类，在子类实现中，`@Bean` 方法会首先在容器中尝试寻找是否已存在对应的 bean，如果没有，再去调用父类方法。

</v-click>

---

# 使用 `@Configuration` 注解

`@Component` 注解及元注解、组合注解简介

前面提到，启用组件扫描机制后，容器会寻找标注了 `@Component` 的类并注册为 bean 定义，而 `@Configuration` 注解类本身被标注了 `@Component` 注解。这里的 `@Component` 则是一种元注解，即可以应用到其他注解类上的注解。组合注解简单来说就是通过一个或多个元注解组合得来的注解，例如：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component // 用作为元注解
public @interface Configuration {
    // ...
}
```

Web Core Base 中的应用：

<flex-container>
<column>

```java
@RestController // 由 `@Controller` 和 `@ResponseBody` 组合得到
@RequestMapping("/users")
public class UserController {
}
```

</column>
<column>

```java
// 由 `@RestController` 和 `@RequestMapping` 组合得到
@AopRestController("/users")
public class UserController {
}
```

</column>
</flex-container>

<!-- 关于 Java 注解以及 Spring 的注解编程模型另行介绍 -->

---

# 使用 `@Configuration` 注解

组合配置

可以在一个 `@Configuration` 配置类中导入其他配置类，例如：

<flex-container>
<column>

```java
@Configuration
public class ConfigA {
    // ...
}

@Configuration
@Import(ConfigA.class)
public class ConfigB {
    // ...
}
```

</column>
<column>

```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(ConfigB.class);
    // ...
}
```

</column>
</flex-container>

---

# 使用 `@Configuration` 注解

组合配置

在大多数实际场景下，bean 可能会有跨配置类的依赖。前面提到，`@Bean` 方法中的每一个参数都是一个依赖，容器会自动注入，即便依赖的 bean 并不是在当前配置中定义的。例如：

<flex-container>
<column>

```java
@Configuration
public class ConfigA {
    @Bean
    public Foo foo(Baz baz) {
        return new Foo(baz);
    }
}

@Configuration
public class ConfigB {
     @Bean
    public Bar bar(Baz baz) {
        return new Bar(baz);
    }
}
```

</column>
<column>

```java
@Configuration
@Import({ ConfigA.class, ConfigB.class })
public class ConfigC {
    @Bean
    public Baz bar() {
        return new Baz();
    }
}
```
```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(ConfigC.class);
    // ...
}
```

</column>
</flex-container>

---

# 使用 `@Configuration` 注解

组合配置

由于 `@Configuration` 配置类最终也会是容器里的一个 bean，所以也可以享受到依赖注入机制，例如使用 `@Autowired` 注解。例如：

```java
@Configuration
public class ConfigA {
    @Autowired
    private Baz baz;

    @Bean
    public Foo foo() {
        return new Foo(baz);
    }
}
```

<br>

> 注意由于 `@Configuration` 类会在容器初始化的早期就会被处理，因此需要确保通过注入的依赖足够简单。否则尽量还是使用基于方法参数的注入。

---

# 使用 `@Configuration` 注解

组合配置

前面的方式虽然可行，但却无法确切地知道所依赖的 bean 是在哪里定义的。如果希望能更清晰地体现出依赖来源，可以考虑直接注入另一个配置类，例如：

```java
@Configuration
public class ConfigA {
    @Autowired
    private ConfigC configC;

    @Bean
    public Foo foo() {
        return new Foo(configC.baz());
    }
}
```

<!-- 然而这样做也有代价，即导致两个配置类之间强耦合。 -->
---

# 使用 `@Configuration` 注解

组合配置

我们经常会需要有条件地启用或禁用一整个 `@Configuration` 类或其中的某个 `@Bean` 方法，一个常见的例子就是使用 `@Profile` 注解判断是否特定的 `profile` 被启用。但实际上 `@Profile` 注解本身也是通过使用一个更为灵活的注解 `@Conditional` 来实现的，因此我们也可以根据需要实现自己的条件注解。

<flex-container>
<column>

```java
@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
    Class<? extends Condition>[] value();
}
```

</column>
<column>

```java
public interface Condition {
    boolean matches(ConditionContext context,
        AnnotatedTypeMetadata metadata);
}
```

</column>
</flex-container>

Spring 提供的常用条件注解有：`@ConditionalOnClass`、`@ConditionalOnMissingClass`、`@ConditionalOnBean`、`@ConditionalOnMissingBean` 等。

---
layout: section
---

# 基于注解的配置元数据

---

# 基于注解的配置元数据

<br>

基于注解的配置元数据主要用于提供依赖相关信息，可以在一定程度上减少 XML 的使用，但仍然需要使用 XML 来提供**基础的 bean 定义**。

```xml {all|4|12,14}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean id="car" class="com.foo.Car"></bean>

    <bean id="superEngine" class="com.foo.SuperEngine"></bean>

</beans>
```

---

# 基于注解的配置元数据

使用 `@Autowired` 注解

可以将 `@Autowired` 注解应用在构造方法上：

```java
public class Car {

    private Engine engine;

    @Autowired
    public Car(Engine engine) {
        this.engine = engine;
    }

}
```

<br>

<v-click>

> 在 Spring 4.3 版本之后，如果 bean 只有一个构造方法，则 `@Autowired` 注解不是必须的，反之，如果有多个构造方法，则至少一个构造方法上必须应用此注解，以便告诉容器要用哪个。

</v-click>

---

# 基于注解的配置元数据

使用 `@Autowired` 注解

<div style="display: flex; justify-content: space-around;">

<column>

可以将 `@Autowired` 注解应用在 setter 方法上：

```java
public class Car {

    private Engine engine;

    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
    }

}
```

</column>

<column v-click>

也可以应用在任意方法上：

```java
public class Car {

    private Engine engine;

    @Autowired
    public void prepare(Engine engine) {
        this.engine = engine;
    }

}
```

</column>

</div>

---

# 基于注解的配置元数据

使用 `@Autowired` 注解

甚至可以直接应用到 bean 属性上：

```java
public class Car {

    @Autowired
    private Engine engine;

}
```

<br>

<v-click>

> 注意以上属性或参数的类型必须和 bean 定义里**保持一致**。

</v-click>

---

# 基于注解的配置元数据

使用 `@Autowired` 注解

也可以是所需类型的数组或集合：

```java
public class EngineSupplier {

    @Autowired
    private Engine[] engines; // 或者任意的集合类型：List、Set 等

}
```

也可以绑定到键类型为 `String` 的 `Map` 实例上，此时值包含了所有符合指定类型的 bean，而键为 bean 对应的名称。

同样适用于使用构造方法或普通方法的情况。

---

# 基于注解的配置元数据

使用 `@Autowired` 注解

默认情况下，如果找不到符合条件的 bean（对于是数组、集合或 Map 的情况，则需要有至少一个符合条件的 bean），自动装配会失败，但可以通过将注解属性 `required` 设置为 `false` 表示 bean 不是必须的，例如：

```java
@Autowired(required = false)
```

对于普通方法来说，如果有多个参数，则只要其中任意一个参数找不到符合条件的 bean，该方法就不会被调用。但并不适用于构造方法，因为构造方法的参数默认被认为是必须的，但也有一些例外，即如果是数组、集合或 Map，则可以是空的。可以将参数类型以 Java 8 提供的 `java.util.Optional` 类包裹或使用 `@Nullable` 注解（Spring 5.0 提供）以表示它不是必须的，例如：

<div style="display: flex; justify-content: space-around">

<column>

```java
public class Foo {

    @Autowired
    public void setBar(Optional<Bar> bar) {
        // ...
    }

}
```

</column>

<column>

```java
public class Foo {

    @Autowired
    public void setBar(@Nullable Bar bar) {
        // ...
    }

}
```

</column>

</div>

---

# 基于注解的配置元数据

使用 `@Autowired` 注解

对于构造方法来说，其实 `required` 属性的的含义已经不太一样了。如果有多个构造方法应用 `@Autowired` 注解，则它们的 `required` 属性必须设置为 `false`，Spring 容器会通过判断哪个构造方法有最多的能匹配到的 bean 来决定使用哪个构造方法。

---

# 基于注解的配置元数据

使用 `@Autowired` 注解

前面提到当找不到符合条件的 bean 时，自动装配会失败，然而当所需单个 bean 而找到了多个符合条件的 bean 时，同样会失败。这时有两种解决办法：

1. 在 bean





