---
title: Spring总结
date: 2022-04-20 16:05:16
tags: Java
categories: 
    - Java
    - Spring
---

# BeanDifinition
## 什么是BeanDifinition？
- BeanDifinition表示Bean定义，Spring根据BeanDifinition来创建Bean对象
- BeanDifinition有很多属性来描述Bean
- BeanDifinition是Spring中非常核心的概念

## BeanDifinition中的重要属性
- **beanClass：**
表示一个bean的类型，比如：UserService.class、OrderService.class，Spring在创建Bean的过程中会根据此属性来实例化得到对象

- **scope：**
表示一个bean的作用域，比如：
scope等于singleton，该bean就是一个**单例bean**
scope等于prototype，该bean就是一个**原型bean**

- **isLazy：**
表示一个bean是不是需要**懒加载**，原型bean的isLazy属性不起作用，懒加载的单例bean，会在第一次getBean的时候生成该bean，非懒加载的单例bean，则会在Spring启动过程中直接生成好。

- **dependsOn：**
表示一个bean在创建之前所依赖的其他bean，在一个bean创建之前，它所依赖的这些bean得先全部创建好。

- **primary：**
表示一个bean是**主bean**，在Spring中一个类型可以有多个bean对象，在进行依赖注入时，如果根据类型找到了多个bean，此时会判断这些bean中是否存在一个主bean，如果存在，则直接将这个bean给注入属性。

- **initMethodName：**
表示一个bean的初始化方法，一个bean的生命周期过程中有一个步骤叫初始化，Spring会在这个步骤中取调用bean的初始化方法，初始化逻辑由程序员自己控制，表示程序员可以自定义逻辑对bean进行加工。
平时使用到的@Component、@Bean、```<bean/>```这些都会解析为BeanDifinition

# BeanFactory
## 什么是BeanFactory？
- BeanFactory是一种“Spring容器”
- BeanFactory翻译过来就是Bean工厂，顾名思义，它可以用来创建Bean，获取Bean
- BeanFactory是Spring中非常核心的组件

## BeanFactory和BeanDifinition、Bean对象的关系
- BeanFactory将利用Beandifinition来生成Bean对象，Beandifinition相当于BeanFactory的原材料
- Bean对象就相当于BeanFactory所生产出来的产品

## BeanFactory的核心子接口和实现类
- ListableBeanFactory
- ConfigurableBeanFactory
- AutowireCapableBeanFactory
- AbstractBeanFactory
- DefaultListableBeanFActory（最重要）

**DefaultListableBeanFActory**的功能：
支持单例Bean、支持Bean别名、支持父子BeanFactory、支持Bean类型转化、支持Bean后置处理、支持FactoryBean、支持自动装配...

# Bean生命周期
Bean生命周期描述的是Spring中一个Bean创建过程和销毁过程中所经历的步骤，其中Bean创建过程是重点。
可以利用Bean生命周期机制对于Bean进行自定义加工。
## Bean创建的核心步骤
1. **BeanDifinition**：Bean定义
2. **构造方法推断**：选出一个构造方法
一个Bean中可以有多个构造方法、此时就需要Spring来判断到底用哪个构造方法
3. **实例化**：构造方法反射得到对象
在Spring中，可以通过BeanPostProcessor机制对实例化进行干预
4. **属性填充**：给属性进行自动装配
实例化得到的对象是不完整的对象，需要进行属性填充，属性填充就是通常说的自动注入、依赖注入
5. **初始化**：对其他属性赋值、校验
Spring提供了初始化机制，可以利用初始化机制对Bean进行自定义加工，比如利用InitializingBean接口来对Bean中的其他属性进行复制，或对Bean中的某些属性进行校验
6. **初始化后**：AOP、生成代理对象
初始化后是Bean创建生命周期中最后一个步骤，我们常说的AOP就是在这个步骤中通过BeanPostProcessor机制实现的，初始化之后得到的对象才是真正的Bean对象。

# 依赖注入
## @Autowired 是如何工作的？
@Autowired表示某个属性是否需要进行依赖注入，可以写在属性和方法上。注解中的required属性默认为true，表示如果没有对象可以注入给属性则抛异常

```java
@Service
public class OrderService {
    @Autowired
    private UserService userService;
}
```
@Autowired的**注解加载某个属性上**，Spring在进行Bean的生命周期过程中，在属性填充这一步，会基于实例化出来的对象，对该对象中加了@Autowired的属性自动给属性赋值。
Spring会先**根据属性的类型**去Spring容器中找出该类型所有的Bean对象，如果找出来多个，则**再根据属性的名字**从多个中再确定一个，如果required属性为true，并且根据属性信息找不到对象，则直接抛异常。

```java
@Service
public class OrderService {
    private UserService userService;
    
    @Autowired
    public void setUserService (UserService userService) {
        this.userService = userService;
    }
}
```
当@Autowired**注解写在某个方法上**时，Spring在Bean生命周期的属性填充阶段，会根据方法的参数类型、参数名字从Spring容器找到对象当作方法入参，自动反射调用该方法。

```java
@Service
public class OrderService {
    private UserService userService;
    
    @Autowired
    public OrderService (UserService userService) {
        this.userService = userService;
    }
    public OrderService (UserService userService, UserService userService1) {
        this.userService = userService;
    }
}
```
@Autowired**加在构造方法上**时，Spring会在推断构造方法阶段，选择该构造方法来进行实例化，在反射调用构造方法之前，会先根据构造方法参数类型、参数名从Spring容器中找到Bean对象，当做构造方法入参。

## @Resource 是如何工作的？
@Resource中有一个name属性，针对name属性是否有值，@Resource的依赖注入底层流程是不同的。

@Resource如果name属性有值，那么Spring会直接根据所指定的name值去Spring容器找Bean对象，如果找到了则成功，如果没有找到，则报错。

@Resource如果name属性没有值，则：
1. 先判断该属性名字在Spring容器中是否存在Bean对象。
2. 如果存在，则成功找到Bean对象进行注入。
3. 如果不存在，则根据属性类型去Spring容器找Bean对象，找到一个则进行注入。

## @Value 是如何工作的？
@Value注解和@Resource、@Autowired类似，也是用来对属性进行依赖注入的，只不过@Value是用来从Properties文件中获取值的，并且@Value可以解析SpEL（Spring表达式）

```
@Value("zhouyu")
```
直接将字符串 "zhouyu" 赋值给属性，如果属性类型不是 String ，或无法进行类型转化，则报错。

```
@Value("${zhouyu}")
```
将会把 ${} 中的字符串当作key，从 Properties 文件中找到对应的 value 赋值给属性，如果没找到，则会把 “${zhouyu}” 当作普通字符串注入给属性。

```
@Value("#{zhouyu}")
```
会将 #{} 中的字符串当作Spring表达式进行解析，Spring会把 "zhouyu" 当作 beanName，并从Spring容器中找对应 bean ，如果找到则进行属性注入，没找到则报错。

# FactoryBean是什么？
FactoryBean是Spring所提供的一种较灵活的创建Bean的方式，可以通过实现FactoryBean接口中的getObject()方法来返回一个对象，这个对象就是最终的Bean对象。
## FactoryBean接口中的方法
Object getObject()：	返回的是Bean对象
boolean isSingleton)()：	返回的是否是单例Bean对象
Class getObjectType()：	返回的是Bean对象的类型

```
@Component("zhouyu")
public class ZhouyuFactoryBean implements FactoryBean
    @override
    // Bean对宏
    pubLic object get0bject() throws Exception {
        return new User() ;
    }
    @override
    // Bean对象的类型
    public Class<?> get0bjectType() {
        return User.class;
    }
    @0verride
    // 所定义的Bean是中例还是原型
    public boolean isSingleton() {
        return true;
    }
}
```
上述代码，实际上对应了两个Bean对象：
1. beanName为"zhouyu"，bean对象为getObject方法所返回的User对象。
2. beanName为"&zhouyu"，bean对象为ZhouyuFactoryBean类的实例对象
FactoryBean对象本身也是一个Bean，同时它相当于一个小型工厂，可以生产出另外的Bean。

FactoryBean是一个Spring容器，是一个大型工厂，它可以生产出各种各样的Bean。
FactoryBean机制被广泛的应用在Spring内部与第三方框架或组件的整合过程中。

# ApplicationContext是什么？
ApplicationContext是比BeanFactory更加强大的Spring容器，它既可以创建Bean、获取Bean，还支持国际化、事件广播、获取资源等BeanFactory不具备的功能。
## ApplicationContext所继承的接口
- EnvironmentCapable
- ListableBeanFactory
- HierarchicalBeanFactory
- MessageSource
- ApplicationEventPublisher
- ResourcePatternResolver

**EnvironmentCapable：**
ApplicationContext继承了这个接口，表示拥有了获取环境变量的功能，可以通过ApplicationContext获取操作系统环境变量和JVM环境变量。
**ListableBeanFactory：**
ApplicationContext继承了这个接口，就拥有了获取所有beanNames、判断某个beanName是否存在beanDefinition对象、统计BeanDefinition个数、获取某个类型对应的所有beanNames等功能。
**HierarchicalBeanFactory：**
ApplicationContext继承了这个接口，就拥有了获取父BeanFactory、判断某个name是否存在bean对象的功能。
**MessageSource：**
ApplicationContext继承了这个接口，就拥有了国际化功能，比如可以直接利用MessageSource对象获取某个国际化资源(比如不同国家语言所对应的字符)
**ApplicationEventPublisher：**
ApplicationContext继承了这个接口，就拥有了事件发布功能，可以发布事件，这是ApplicationContext相对于BeanFactory比较突出、常用的功能。
**ResourcePatternResolver：**
ApplicationContext继承了这个接口，就拥有了加载并获取资源的功能，这里的资源可以是文件，图片等某个URL资源都可以。

# BeanPostProcessor是什么？
BeanPostProcessor是Spring所提供的一种扩展机制，可以利用该机制对于Bean进行定制化加工，在Spring底层源码的实现中，也广泛的用到了该机制，BeanPostProcessor通常也叫做Bean后置处理器。
BeanPostProcessor在Spring中是一个接口，我们定义一个后置处理器，就是提供一个类实现该接口，在Spring中还存在一些接口继承了BeanPostProcessor，这些子接口是在BeanPostProcessor的基础上增加了一些其他的功能。

## BeanPostProcessor中的方法
postProcessBeforelnitialization()：	初始化前方法，表示可以利用这个方法来对Bean在初始化前进行自定义加工。
postProcessAfterlnitialization()：	初始化后方法，表示可以利用这个方法来对Bean在初始化后进行自定义加工。

**InstantiationAwareBeanPostProcessor：**
是BeanPostProcessor的一个子接口，它的方法：
postProcessBeforelnstantiation()：	实例化前
postProcessAfterInstantiation()：		实例化后
postProcessProperties()：			属性注入后

# AOP是如何工作的？
AOP就是**面向切面编程**，是一种非常适合在无需修改业务代码的前提下，对某个或某些业务增加统一的功能，比如日志记录、权限控制、事务管理等，能很好地使得代码解耦，提高开发效率。

## AOP中的核心概念
- **Advice**
Advice可以理解为通知、建议，在Spring中通过定义Advice来定义代理逻辑。
- **Pointcut**
Pointcut是切点，表示Advice对应的代理应用在哪个类、哪个方法上。
- **Advisor**
Advisor等于Advice+Pointcut，表示代理逻辑和切点的一个整体，程序员可以通过定义或封装一个Advisor，来定义切点和代理逻辑。
- **Weaving**
Weaving表示织入，将Advice代理逻辑在源代码级别嵌入到切点的过程，就叫做织入。
- **Target**
Target表示目标对象，也就是被代理对象，在AOP生成的代理对象中会持有目标对象。
- **Join Point**
Join Point表示连接点，在Spring AOP中，就是方法的执行点。

## AOP的工作原理
AOP是发生在Bean的生命周期中的：
1. Spring生成bean对象时，先实例化出来一个对象，也就是target对象
2. 再对target对象进行属性填充
3. 在初始化后步骤中，会判断target对象有没有对应的切面
4. 如果有切面，就表示当前target对象需要进行AOP
5. 通过Cglib或JDK动态代理机制生成一个代理对象，作为最终的bean对象
6. 代理对象中有一个target属性指向了target对象
