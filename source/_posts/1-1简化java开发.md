---
title: 第一章 Spring之旅
date: 2019-05-12 10:42:45
categories: 
- Spring实战读书笔记
tags:
- javabean
---

### 1.1 简化Java开发
相对于EJB来说，Spring提供了更加轻量级和简单的编程模型。它增强了简单老式Java对象的功能，使其具备了之前只有EJB和其他企业级Java规范才具有的功能。

为了降低Java开发的复杂性，Spring采取了以下4种关键策略。
* 基于POJO的轻量级和最小入侵性编程；
* 通过依赖注入和面向接口编程实现松耦合；
* 基于切面和惯例进行声明式编程；
* 通过切面和模版减少样板式代码；
<!--more-->

#### 1.1.1 激发POJO的潜能
Spring竭力避免因自身的API而弄乱你的应用代码。Spring不会强迫你实现Spring规范的接口或继承Spring规范类，相反，在基于Spring构建的应用中，它的类通常没有任何痕迹表明你使用了Spring。

#### 1.1.2 依赖注入

**DI功能是如何实现的**
任何一个有实际意义的应用都会由两个或更多的类组成，这些类之间相互进行协作来完成特定的业务逻辑。按照传统的做法，每个对象负责管理与自己相互协作的对象的引用，这将会导致高度耦合和难以测试的代码。

通过DI，对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设置。对象无需自行创建或管理他们的依赖关系，依赖关系将被自动注入到需要他们的对象中。 


Spring有多种装bean的方式，采用XML是很常见的一种装配方式，Spring还支持使用Java来描述配置。

Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。



#### 1.1.3 应用切面

DI能够让相互协作的组件保持松散耦合，而面向切面编程（aspect-oriented-programiming，AOP）允许你把遍布应用各处的功能分离出来形成可重用的组件。

AOP能够使这些服务模块化，并以声明的方法将他们应用到它们需要影响的组件中去。所造成的结果就是这些组件会具有更高的内聚性并且会更关注自身的业务，完全不需要了解涉及系统服务所带来的复杂性。总之，AOP能够确保POJO的简单性。


#### 1.1.4 使用模版消除样式代码

Spring旨在通过模板封装来消除样板式代码。Spring的JdbcTemplate使得执行数据库操作时，避免传统的JDBC样板代码成为可能。


### 1.2 容纳你的bean

容器是Spring框架的核心。Spring容器使用DI管理构成应用的组件，它会创建相互协作的组件之间的关联。
Spring的容器并不是只有一个。Spring自带了多个容器实现，可以归为两种不同的类型。

**bean工厂**（由org.springframework.beans.factroy.BeanFactory接口定义）是简单的容器，提供基本的DI支持。
**应用上下文**（由org.springframwork.context.ApplicationContext接口定义）基于BeanFactory构建，并提供应用框架级别的服务，例如从属性文件解析文本信息以及发布应用事件给感兴趣的事件监听者。

bean工厂对大多数应用来说往往太低级了，因此，应用上下文要比bean工厂更受欢迎。


#### 1.2.1 使用应用上下文

Spring自带了多种类型的应用上下文。下面罗列的几个是你最有可能遇到的

* AnnotationConfigAppliationContext：从一个或多个基于Java的配置类中加载Spring应用上下文。
* AnnotationConfigWwbbApplicationContext:从一个或多个基于Java的配置中加载Spring Web应用上下文。
* ClassPathXmlApplicationCOntext：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
* FileSystemXmlApplicationCOntext；从文件系统下的一个或多个XML配置文件中加载上下文定义。
* XmlWebApplicationContext：从Web应用下的一个或多个XMl配置文件中加载上下文定义。


应用上下文准备就绪之后，我们就可以调用上下文的getBean（）方法从Spring容器中获取bean。



#### 1.2.2 bean的声明周期

在传统的Java应用中，bean的声明周期很简单。使用Java关键字new进行bean实例化，然后该bean就可以使用了。一旦该bean不再被使用，则由Java自动进行垃圾回收。

Spring容器中的bean声明周期就显得相对复杂多了。下图展示了bean装载到Spring应用上下文的一个典型的生命周期过程。
* 1 Spring对bean进行实例化；
* 2 Spring将值和bean的引用注入到bean对应的属性中；
* 3 如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBeanName()方法；
* 4 如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory()方法，将BeanFactroy容器实例传入。
* 5 如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext（）方法，将bean所在的应用上下文的引用传入进来。
* 6 如果bean实现了BeanPostProcessor接口，Spring将调用它们的postProcessBeforeInitialization()方法。
* 7 如果bean实现了InitializingBean接口，Spring将调用他们的afterPropertiesSet()方法。类似地，如果bean使用initmethod声明了初始化方法，该发放也会被调用。
* 8 如果bean实现了BeanPostProcessor接口，Spring将调用他们的postProcesAfterInitialization()方法；
* 9 此时，bean已经准备就绪，可以被应用程序使用了，他们将一直驻留在应用上下文中，知道该应用上下文被销毁；
* 10 如果bean实现了DisPosableBean接口，Spring将调用它的destroy()接口方法。同样，如果bean使用了destroy-method声明了销毁方法，该方法也会被调用。



### 1.3 俯览Spring风景线
#### 1.3.1 Spring模块
**Spring核心容器**
容器是Spring框架最核心的部分，它管理着Spring应用中bean的创建、配置和管理。在该模块中，包括了Spring bean工厂，它为Spring 提供了DI的功能。基于bean工厂，我们还会发现多种Spring应用上下文的实现，每一种都提供了配置Spring的不同方式。
除了bean工厂和应用上下文，该模块也提供了许多企业服务，例如E-mail，JNDI访问，EJB集成和调度。

**Spring的AOP模块**
在AOP模块中，Spring对面向切面编程提供了丰富的支持。这个模块是Spring应用系统开发切面的基础。与DI一样，AOP可以忙著应用对象解耦。借助于AOP，可以将遍布系统的关注点（例如事物和安全）从它们所应用的对象中解耦出来。

**数据访问集成**
使用JDBC编写代码通常会导致大量的样板式代码、例如获取数据连接、创建语句、处理结果集到最后关闭数据库连接。
模块包括ORM、JMS、Spring应用中的对象提供事务管理服务。

**Web与远程调用**
MVC模式是一种普遍被接受的构建Web应用的方法，它可以帮助用户将界面逻辑与应用逻辑分离。Java从来不缺少MVC框架，APache的Struts、JSF‘WebWork和Tapestry都可是可选的最流行的MVC框架。
该模块还提供了多种构建与其他应用交互的远程调用方案。

**instrumentation**
此模块提供了为JVM添加代理的功能。具体来讲，它为Tomacat提供了一个织入代理，能够为Tomacat传递类文件，就像这些文件是被类加载器加载一样。

**测试**
通过该模块，你会发现Spring为使用JNDI、Servlet和Portlet编写单元测试提供了一系列的mock对象实现。对于集成测试，该模块为加载SPring上下文中的bean集合以及与Spring上下文中的bean进行交互提供了支持。


#### 1.3.2 Spring Portfolio
整个Spring Portfolio包括多个构建于核心Spring框架之上的框架和类库。

* Spring Web Flow
建立于SpringMVC框架之上，它为基于流程的会话式Web应用提供支持。
* Spring Web Service
提供了契约优先的WebService模型，服务的实现都是为了满足服务的契约而编写的。
* Spring Security
为Spring应用提供了声明式的安全机制。
* Spring Integration
提供了多种通用应用集成模式的Spring声明式风格实现。
* Spring Batch
通过Spring Batch，使用Spring强大的面向POJO的编程模型。
* Spring Data
它使得在Spring使用任何数据库都变得非常容易。
* Spring Social
Spring的一个社交网络扩展模块。
* Spring Mobile
它是Spring MVC新的扩展模块，用于支持移动web应用开发。
* Spring for Android
通过Spring框架为开发基于Android设备的本地应用提供某些简单的支持。
* Spring Boot
Spring Boot 大量依赖于自动配置技术，它能够消除大部分Spring配置。它还提供了多个Starter项目，不管你使用Maven还是Gradle，这都会减少Spring工程构建文件的大小。




### 1.4 Spring的新功能

#### 1.4.1 Spring3.1新特性
其中很多都是关于如何简化和改善配置的。除此之外，Spring3.1还提供了声明式缓存的支持以及众多针对SpringMVC的功能增强。
#### 1.4.2 Spring3.2新特性
Spring3.1在很大程度上聚焦于配置改善以及其他的一些增强，包括SPringMVC的增强，而Spring3.2是主要关注SpringMVC的一个发布版本。







