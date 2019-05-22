---
title: 第2章 装配bean
date: 2019-05-22 10:15:38
categories: 
- Spring实战读书笔记
tags:
- 装配bean
---

### 2.1 Spring配置的可选方案

创建应用对象之间协作关系的行为通常称为装配，<font color=red>这也是依赖注入(DI)的本质。</font>
Spring提供了三种主要的装配机制：
* 在XML中进行显示装配。
* 在Java中进行显示装配。
* 隐式的bean发现机制和自动装配。

### 2.2 自动化装配bean
Spring从两个角度来实现自动化装配：
* 组件扫描：Spring会自动发现应用上下文中所创建的bean。
* 自动装配：Spring自动满足bean之间的依赖。

#### 2.2.1 创建可被发现的bean
@Component注解表示类会作为组件类，并告知Spring要为这个类创建bean。
不过组件扫描默认不是开启的。我们还需要显示配置一下Spring，从而命令它寻找带有@Component注解的类，并为其创建bean。如果没有其他配置的话，@ComponentScan默认会扫描与配置类相同的包。

如果你更倾向于使用XML来启用组件扫描的话，那么可以使用Spring context命名空间的<context:component-scan>元素。

#### 2.2.2 为组件扫描的bean命名

Spring应用上下文中所有的bean都会给定一个ID。默认情况下会根据类名为其指定一个ID。类名的第一个字母变为小写。
如果想设置不同的ID，有两种方式：
* 1 只需要在@Component("名字")即可。
* 2 使用Java依赖注入规范中所提供的@Named注解来为bean设置ID
Spring支持将@Named作为@Component注解的代替方案。

#### 2.2.3 设置组件扫描的基础包
可以在@ComponentScan的value属性中指明包的名称 @ComponentScan("soudssystem"),如果你想更清晰的设置，可以通过basePackages属性进行配置@ComponentScan(basePackages="soundssystem") basePackages是复数形式因此也可以设置多个@ComponentScan(basePackages={"soundssystem","video")
上面这种形式是使用String类型进行设置的，如果代码重构，所指定的基础包可能就会出现错误。

除了将包设置为简单的String类型之外，@ComponentScan()还提供了另外一种方法，那就是将其指定为包中所包含的类或接口：@ComponentScan(basePackageClasses={类名.class,接口名.class})


#### 2.2.4自动装配
如果所有对象相互独立，彼此没有任何依赖，只需要组件扫描。但是很多对象会依赖其他的对象才能完成任务。这样的话我们就需要一种方法能够将组件扫描得到的bean和它们的依赖装配在一起。要完成这项任务，就需要了解一下Spring的自动装配。

简单的说，自动装配就是让Spring自动满足bean依赖的一种方法，在满足依赖的过程中，会在Spring应用上下文中寻找匹配某个bean需求的其他bean。为了声明要进行自动装配，要借助Spring的@Autowired注解。
@Autowired注解不仅能够用在构造器上，还能用在属性的Setter方法上。

如果没有找到匹配的bean，那么在应用上下文创建的时候，Spring会抛出一个异常。为了避免异常，你可以将@Autowired的requried属性设置为false。
如果匹配到了多个bean，Spring将会抛出一个异常，表明没有明确指定要选择哪个bean进行自动装配。如果不喜欢用@Autowired，可以用@Inject代替，@Inject注解来源于Java依赖注入规范，该规范同时提供了@Named注解。


### 2.3 通过Java代码装配bean


如果你想把第三方库中的组件装配到你的应用中，没办法在它的类上使用@Component和@Autowired注解，因此就不能使用Java配置。

如果进行显示装配有两种方案：Java配置和XML配置。

在进行显示配置的时候，JavaConfig是更好的方案，同时JavaConfig和Java代码又有所区别，JavaConfig是配置代码，通常要将JavaConfig放到单独的包中，使它与其他应用逻辑分离开来，这样对于它的意图就不会产生困惑了。


#### 2.3.1 创建配置
创建配置类的关键在于为其添加@Configuration注解，此注解表明这个类是一个注解类，该类应该包含在Spring应用上下文中如何创建bean的细节。

#### 2.3.2 声明简单的bean
 要在JavaConfig中声明bean，我们需要编写一个方法，这个方法会创建所需类型的实例，然后给这个方法添加@Bean注解。
 @Bean注解会告诉Spring这个方法会返回一个对象，该对象要注入为Spring应用上下文中的bean。
 默认情况下bean的ID与带有@Bean注解的方法名是一样的。
 
 
#### 2.3.3 借助JavaConfig实现注入
在JavaConfig中装配bean的最简单的方式就是引用创建bean的方法。
默认情况下，Spring中的bean是单例的，Spring会拦截对相同方法对调用并确保返回的是Spring所创建的bean。也可以作为参数进行传递，Spring会把参数自动装配成bean，也可以通过Setter方法进行注入。


### 2.4 通过XML装配bean
#### 2.4.1 创建XML配置规范

首先创建一个XML文件，并且要以<beans>元素为根。用来装配bean最基本的XML元素包含在spring-beans模式之中。

#### 2.4.2 声明一个简单的<bean>
要在基于XML的Spring配置中声明一个bean，我们要使用spring-beans模式中的另一个元素：<bean>。<bean>元素类似于JavaConfig中的@Bean注解。

```java
<bean class="soudsystem.SgtPeppers">
```
这里声明了一个简单的bean，创建这个bean的类通过class属性指定的，并且要使用全限定名。 
因为没有明确给定ID，所以这个bean将会根据全限定名进行命名。bean的ID将会是soudsystem.SgtPeppers#0 ，#0是用来区别相同类型的其他bean。

通常最好的办法就是定义id属性，为每个bean设置一个你自己的名字：
```java
<bean  id=“compactDIsc” class="soudsystem.SgtPeppers">
```
**为了减少XML中繁琐的配置，只对那些需要按名字引用的bean进行明确地命名**


#### 2.4.3 借助构造器注入初始化bean
在SpringXML配置中，只有一种声明bean的方式：使用<bean>元素并指定class属性。Spring会从这里获取必要的信息来创建bean。


在XML中声明DI时，有两种基本的配置方案：
* <constructor-arg>元素
* 使用Spring3.0所引用的c-命名空间


 1 构造器注入bean引用
 2 将字面量注入到构造器中
 
 我们所做的DI通话指的都是类型装配--也就是将对象的引用装配到依赖于它们的其他对象之中。
 在装配方面，<constructor-arg>比c-命名空间更有优势。目前使用c-命名空间无法实现装配集合的功能。
 使用<constructor-arg>和c-命名空间实现构造器注入时，它们之间还有一些细微的差别。
 
 
 #### 2.4.4 设置属性
 可以通过构造器或者setter进行注入。
 
 **将字面量注入到属性中：**我们需要装配这些属性，可以借助<property>元素的value属性实现该功能。


### 2.5 导入和混合配置

#### 2.5.1 在JavaConfig中引用XML配置

@Import(类名.class) 这个注解会引入这个类中的所有bean。可以使用@Import将两个javaConfig的配置组合在一起。

```java
@Configuration
@Import(CDconfig.class)
public class CDPlayerConfig{

@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc){
    return new CDPyayer(compactDisc)
  }
}
```

或者
```java
@Configuration
@Import(CDconfig.class,CDPlayerConfig)
public class CDPlayerConfig{

}
```

如果使用XML配置，可以使用@ImportResoure注解
```java
@Configuration
@Import(CDPlayerConfig.class)
@ImportResource("classpath:cd-config.xml")
public class SoundSystemConfig{
}
```


#### 2.5.2 在XML配置中引用JavaConfig

在JavaConfig配置中，我们已经展现了如何使用@Import和@ImportResource来拆分JavaConfig类。在XML中，我们可以<import>元素来拆分XML配置。

**基于XML的配置该如何引用一个JavaConfig类呢?**
1. 可以使用<bean class="soundsystem.CDPlayer">
2. 或者将CDConfig bean从之前的XML文件中移除,使用第三个配置文件将这两个组合在一起。两个文件一个用XML描述，另一个使用Java描述。


