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

# Spring Framework 简介

## The IoC Container

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

在 Spring 中，**构成应用主干并由 Spring IoC 容器管理的对象**统称为 Bean。一个 Bean 就是一个被 Spring IoC 容器实例化、组装和管理的对象。Bean 和它们的依赖体现在容器所使用的配置元数据中。

这里的 Bean 不同于 JavaBean。JavaBean 是一个标准，它规定如果一个类满足下列要求，则是一个 JavaBean：

* 所有的属性都是私有的，需要使用 getters/setters 访问；
* 有一个公共的无参构造方法；
* 实现了 `Serializable` 接口。

<br>
<v-click>

> Java 名称来源于一个盛产咖啡豆的小岛（所以 Java 的图标是一杯咖啡），而 bean 直译为豆子，所以 JavaBean 其实就是咖啡豆。此外，如果查看 Java 编译后生成的 class 文件的二进制信息，可以看到前四个字节的值为“cafe babe”。

</v-click>

---

# 容器概览

<br>

接口 `org.springframework.context.ApplicationContext` 代表着 Spring IoC 容器，并通过读取*配置元数据*来实例化、配置和组装 bean。配置元数据主要用于表示组成应用的对象以及它们之间的依赖关系。

除了 `ApplicationContext` 接口以外，Spring 也附带了几个它的实现类。在单应用中，常见的做法是创建一个 `ClassPathXmlApplicationContext` 或者 `FileSystemXmlApplicationContext`。

虽然 XML 一直是定义配置元数据的传统格式，但我们可以通过提供少量 XML 配置来以声明方式支持额外的元数据格式，即使用 Java 注解或者代码。

---

# 容器概览

<br>

下图显示了 Spring 如何工作的高级视图。应用里的业务对象结合配置元数据，当 `ApplicationContext` 被创建和实例化后，就会有一个完全配置且可运行的系统或应用。

<img style="margin: auto" src="/images/container-magic.png">

--- 

# 依赖注入

<br>

在详细介绍配置元数据之前，首先需要了解一下容器是怎么进行依赖注入的。

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

实际开发过程中，我们一般推荐必选依赖使用构造方法，而可选依赖使用 setter 方法。Spring 团队更推荐使用构造方法注入，因为这让我们将应用组件实现为不可变对象，同时确保了必须的依赖不会为 `null`。同时，通过构造方法注入过依赖的组件总是以一个完全初始化好的状态被返回。另一方面，随着依赖增多，构造方法的参数也会显而易见地变得越来越多，这更容易让我们意识到代码可能需要被优化或重构。

---

# 方法注入

<br>

在大多数应用场景下，容器中大部分的 bean 都是单例。当单例 bean 和单例 bean 或非单例 bean 和非单例 bean 一起工作时，一般没有什么问题，然而当单例 bean 依赖一个非单例 bean 时，问题就来了：

设想单例 bean A 需要使用非单例 bean B，可能在每一个方法中都要用到一个新的 B 实例。由于 bean A 是单例，因此容器只会创建它一次，也就意味着依赖只会被注入一次，容器无法在每次 bean A 需要一个新的 bean B 实例时提供给它。

---

# 方法注入

一个解决方法是放弃一些控制反转，我们可以通过让 bean A 实现 `ApplicationContextAware` 接口意识到容器的存在，从而手动从容器中获取所需的依赖。

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

然而这种方法虽然可行，但违背了控制反转的理念，因为业务代码和 Spring 框架强耦合了。

---

# 方法注入

这种情况下，Spring 提供了一种名为方法注入的进阶功能来处理这类问题。

方法注入分为两种：

* 查找方法注入
* 任意方法替换

由于任意方法替换基本不会用到，所以就不再介绍。

---

# 查找方法注入

查找方法注入就是容器实现或覆盖所管理的 bean 的特定方法，以提供容器中的其他 bean。如前所述，这一般涉及到非单例 bean 依赖。

Spring 框架是通过使用 CGLIB 库提供的字节码生成功能来动态生成对应 bean 的子类对象来实现或覆盖特定方法，因此要求 bean 的类、以及要覆盖的方法不能是 `final` 的。

---

# 查找方法注入

通过使用查找方法注入，上述例子可以改写成：

```java
public abstract class A {
    @Lookup("b")
    public abstract B getB();
}
```

这里的属性不是必须的，如果不提供 bean 的名称，容器则会尝试寻找指定类型（即 `B`）的 bean。注意由于抽象类或接口无法被实例化，更常见的做法是提供一个 stub 实现：

```java
public class A {
    @Lookup("b")
    public B getB() {
        return null;
    }
}
```

---

# 配置元数据

<br>

如前所属，配置元数据主要是 bean 及其相关依赖的信息，用于告诉 Spring IoC 容器如何创建、管理 bean，一共有三种提供方式：

<v-clicks v-click-hide="4">

* 基于 XML 的配置元数据
* 基于 Java 注解的配置元数据
* 基于 Java 的配置元数据

</v-clicks>

<v-click>

1. 基于 XML 的配置元数据
2. 基于 Java 的配置元数据
3. 基于 Java 注解的配置元数据

</v-click>

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

`Bean` 和 `@Configuration`

基于 Java 的配置元数据的核心部分就是标注了注解 `@Configuration` 的类和标注了注解 `@Bean` 的方法。

注解 `@Bean` 用于指示被标注的方法负责实例化、配置并初始化一个需要被 Spring IoC 容器管理的对象，和基于 XML 配置的元数据里的 `<bean/>` 元素作用相同。默认情况下，bean 定义的名称和方法名相同。

在一个类上标注 `@Configuration` 方法指示它的主要作用就是作为 bean 定义的来源。此外，`@Configuration` 类允许通过调用同一类中的其他 `@Bean` 方法来定义 bean 间的依赖关系。

```java
@Configuration
public class AppConfiguration {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```

这里的 `AppConfiguration` 类充当了 XML 中 `<beans/>` 标签的角色。

---

# 基于 Java 的配置元数据

通过使用 `AnnotationConfigApplicationContext` 初始化 Spring IoC 容器

使用 `AnnotationConfigApplicationContext` 时，我们需要提供一个 `@Configuration` 类作为输入。

```java
public void main(String[] args) {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfiguration.class);
    MyService myService = applicationContext.getBean(MyService.class);

    myService.doStuff();
}
```

此时除了 `Bean` 方法提供的 bean 定义以外，作为输入的 `AppConfiguration` 类本身也会在容器中被注册为一个 bean 定义。

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

# 基于 Java 的配置元数据

使用 `@Bean` 注解

如前所述，`@Bean` 注解一般使用在标注了 `@Configuration` 注解的类中，但也可以使用在标注了 `@Component` 注解的类中。

前者被称为全量模式，后者被称为“轻量”模式。不同于全量模式，当 `@Bean` 方法被定义在标注了 `@Component` 注解的类中时，它们无法声明同类 bean 之间的依赖，且不应该调用其他的 `@Bean` 方法。

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






---

# Bean 范围

当创建 bean 定义时，相当于是创建一个用于创建 bean 实例的“食谱”。这也意味着，就像使用类一样，我们可以根据一份 bean 定义创建很多对象实例。Spring 支持 6 种范围，

| 范围 | 描述 |
| --- | --- |
| 单例（singleton）| 默认的范围。一个 bean 定义在容器中只会存在一个实例。 |
| 原型（prototype）| 一个 bean 定义在容器中可以存在多个实例。 |
| 请求（request）| 限定一个 bean 定义在单个 HTTP 请求生命周期中。即每个 HTTP 请求都会使用一个新的 bean，仅在适用于 Web 的 `ApplicationContext` 中可用。 |
| 会话（session）| 限定一个 bean 定义在 HTTP `Session` 生命周期中，仅在适用于 Web 的 `ApplicationContext` 中可用。 |
| 应用（application）| 限定一个 bean 定义在 `ServletContext` 生命周期中，仅在适用于 Web 的 `ApplicationContext` 中可用。 |
| Web 套接字（websocket）| 限定一个 bean 定义在 `WebSocket` 生命周期中，仅在适用于 Web 的 `ApplicationContext` 中可用。 |

---

# Bean 范围

<br>

我们可以