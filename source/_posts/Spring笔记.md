---
title: Spring笔记
date: 2022-01-14 16:02:16
tags: Java
categories: 
    - Java
    - Spring
---

# IOC

IoC：控制反转
把对象的创建、赋值、管理交给代码之外的容器实现

DI 是ioc的技术实现
DI：依赖注入
Spring是用 DI 实现了ioc的功能

## 步骤
- 导入Spring的主要jar包
- 创建普通类，在这个类创建普通方法
- 创建Spring配置文件，在配置文件中配置要创建的对象

（1）Spring配置文件用xml文件
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.1qnfkpoyajj4.webp" width="70%">

进行测试代码编写
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.4p1l0spu6hw0.webp" width="70%">

## IOC容器
**IOC底层原理：**
XML解析、工厂模式、反射
- 工厂模式：通过工厂来调用另一个对象，在工厂中创建对象
- 降低耦合度

1、IOC思想基于IOC容器完成，IOC容器底层就是对象工厂
2、Spring 提供了IOC容器实现的两种方式：
（1）BeanFactory：IOC容器基本实现，是Spring内部的使用接口，不提供开发人员进行使用
*加载配置文件的时候不会创建对象，在获取对象的时候才去创建对象
（2）ApplicationContext：BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员进行使用（更好）
*加载配置文件的时候就会创建对象
3、ApplicationContext 接口有实现类
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.1m1f7cmarvpc.webp" width="50%">

IOC 操作：**Bean管理**

1、什么是Bean管理
Bean管理指的是两个操作：
（1）Spring创建对象
（2）Spring注入属性

2、Bean管理操作有两种方式
（1）基于XML配置文件实现
（2）基于注解方式

## 基于XML

1、创建对象
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.1oe7700ja0xs.webp" width="50%">
1. 在spring配置文件中使用bean标签，标签里添加相应属性，可以实现对象创建
2. 介绍常用属性：
  - id：取个名（唯一标识）
  - class：类全路径（包类路径，从src出发）

（3）创建对象的时候，默认执行无参构造方法
2、注入属性
（1）DI：依赖注入，就是注入属性
3、第一种：set方式
（1）创建类，写set方法
（2）在spring配置文件配置对象创建，配置属性注入
```
<!--2 set注入属性-->
<bean id="book" class="com.atguigu.spring5.Book">
    <property name="bname" value="yjj"></property>
    <property name="bauthor" value="dmlz"></property>
    </property>
</bean>
```
4、第二种：有参构造注入
（1）创建类，写相应构造方法
（2）spring配置文件中配置
```
<!--3 有参构造注入属性-->
<bean id="orders" class="com.atguigu.spring5.Orders">
    <constructor-arg name="oName" value="abc"></constructor-arg>
    <constructor-arg name="address" value="China"></constructor-arg>
</bean>
```
5、p名称空间注入（用的不多）
（1）用p空间注入，可以简化基于XML配置方式
添加p名称空间在配置文件中

1、字面量
（1）null值
<!--设置null值-->
<property name="address">
    <null></null>
</property>
（2）属性值包含特殊符号
<property name="address">
    <value><![CDATA[<<南京>>]]></value>
</property>

2、注入属性-外部bean
（1）创建两个类service和dao
（2）在service调用dao里面的方法
（3）在spring配置文件中进行配置
```
<!--service-->
<bean id="userService" class="com.atguigu.spring5.service.UserService">
    <!--注入userDao对象-->
    <property name="userDao" ref="userDaoImpl"></property>
</bean>
<!--dao-->
<bean id="userDaoImpl" class="com.atguigu.spring5.dao.UserDaoImpl"></bean>
```
3、注入属性-内部bean和级联赋值
（1）一对多关系：部门和员工
一个部门有多个员工，一个员工属于一个部门
部门是一，员工是多
（2）在实体类至间表示一对多关系
（3）在spring配置文件中进行配置
```
<!--内部bean-->
<bean id="emp" class="com.atguigu.spring5.bean.Emp">
    <!--设置普通属性-->
    <property name="ename" value="Lucy"></property>
    <property name="gender" value="女"></property>
    <!--对象类型属性-->
    <property name="dept">
        <bean id="dept" class="com.atguigu.spring5.bean.Dept">
            <property name="dname" value="安保部"></property>
        </bean>
    </property>
</bean>
```
4、注入属性-级联赋值
（1）第一种
（2）第二种（结果是技术部）
```
<!--内部bean-->
<bean id="emp" class="com.atguigu.spring5.bean.Emp">
    <!--设置普通属性-->
    <property name="ename" value="Lucy"></property>
    <property name="gender" value="女"></property>
    <!--级联赋值-->
    <property name="dept" ref="dept"></property>
    <property name="dept.dname" value="技术部"></property>
</bean>
<bean id="dept" class="com.atguigu.spring5.bean.Dept">
    <property name="dname" value="财务部"></property>
</bean>
```


**XML注入集合属性：**
1. 注入数组类型
2. 注入List集合类型
3. 注入Map集合类型
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.3hnugwmfit00.webp" width="50%">

4、在集合里设置对象类型值
（1）创建多个对象
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.59knkkoqy4g0.webp" width="70%">

（2）注入list
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.2v88t0sl7yg0.webp" width="40%">

5、把集合注入部分提取出来
（1）在spring配置文件种引入名称空间util
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.5979hpb955o0.webp" width="70%">

（2）使用util标签完成list集合注入提取
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.3pcq6eaaq840.webp" width="70%">

**FactoryBean：**
1、Spring有两种bean，一种普通bean，另一种叫工厂bean（FactoryBean）
2、普通bean：在配置文件中定义的bean类型就是返回类型
3、工厂bean：在配置文件中定义的bean类型可以和返回类型不同
（1）创建类，让这个类作为工厂bean，实现接口FactoryBean
（2）实现接口里的方法，在实现的方法中定义返回的bean类型

**bean的作用域：**
1、在Spring里，设置创建bean实例是单实例还是多实例
2、在Spring里，默认bean是单实例对象
3、如何设置是单实例还是多实例
（1）bean标签里属性scope设置是单实例还是多实例
（2）scope属性
第一个：singleton（单实例，默认）
第二个：prototype（多实例）
（3）创建对象的时候
singleton在加载配置文件的时候就创建了，prototype在getBean的时候才创建对象

**Bean生命周期（面试重点？）：**
1、生命周期
从对象创建到对象销毁的过程
2、bean生命周期
（1）创建bean实例（无参数构造）
（2）为bean的属性设置值和对其他bean的引用（调用set方法）
（3）调用bean的初始化的方法（需要进行配置初始化方法）
（4）bean可以使用了（对象获取到了）
（5）当容器关闭的时候，调用bean的销毁的方法（需要进行配置销毁的方法）
3、演示bean的生命周期






4、bean的后置处理器，bean生命周期有7步
（1）创建bean实例（无参数构造）
（2）为bean的属性设置值和对其他bean的引用（调用set方法）
（3）把bean的实例传递给后置处理器的方法
postProcessBeforeInitialization
（4）调用bean的初始化的方法（需要进行配置初始化方法）
（5）把bean的实例传递给后置处理器的方法
postProcessAfterInitialization
（6）bean可以使用了（对象获取到了）
（7）当容器关闭的时候，调用bean的销毁的方法（需要进行配置销毁的方法）
5、演示添加后置处理器效果
（1）创建类，实现接口BeanPostProcessor，创建后置处理器
（2）XML里配置

**XML自动装配（用的少）：**
1、什么是自动装配
根据指定装配规则（属性名称或属性类型），Spring自动将匹配的属性值进行注入
2、演示自动装配过程

**外部属性文件：**
1、直接配置数据库信息
（1）配置德鲁伊连接池
（2）引入德鲁伊连接池jar包
2、引入外部属性文件配置数据库连接池
（1）创建外部属性文件，properties格式文件，写数据库信息
（2）把外部properties属性文件引入到spring配置文件中
* 引入context名称空间

## 基于注解方式

1、什么是注解
（1）注解是代码特殊标记，格式：@注解名称(属性名称=属性值,属性名称=属性值,,,）
（2）使用注解，注解作用在类、方法、属性上
（3）使用注解目的：简化XML配置

2、Spring针对Bean管理中创建对象提供注解
（1）@Component
（2）@Service
（3）@Controller
（4）@Repository
* 上面四个注解功能是一样的，都可以用来创建bean实例

3、基于注解方式实现对象创建
（1）引入依赖
（2）开启组件扫描
（3）创建类，在类上面添加创建对象注解

4、扫描细节（了解即可）

5、基于注解方式实现属性注入
（1）@Autowired：根据属性类型进行自动装配
第一步	把servic和dao对象创建，在service和dao添加对象注解
第二步	在service里创建dao对象，在属性上添加注解
（2）@Qualifier：根据属性名称进行注入
和上面@Autowired一起使用
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.41i7aqie66w0.webp" width="40%">

（3）@Resource：可以根据类型注入，可以根据名称注入
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.3jsqvt3bgqe0.webp" width="35%">

<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.3gt4iserpt60.webp" width="50%">

（4）@Value：注入普通属性
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.28qvc63uhb8k.webp" width="30%">

6、完全注解开发
（1）创建配置类，替代XML配置文件
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.76fl1k0wke80.webp" width="70%">

（2）编写测试类
加载配置类换了写法：
```
ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
```

# AOP

**AOP：面向切面**
- 不修改源代码进行功能增强
- 什么是AOP：面向切面编程

## AOP底层原理
1、AOP底层使用动态代理
有两种情况动态代理

第一种	有接口，使用JDK动态代理
- 创建接口实现类代理对象，增强类的方法

第二种	没有接口，使用CGLIB动态代理
- 没有接口情况，使用CGLIB动态代理

## AOP（JDK动态代理）
1、使用JDK动态代理，使用Proxy类里面的方法创建代理对象
（1）调用newProxyInstance方法
方法有三个参数：
第一参数：类加载器
第二参数：增强方法所在的类，这个类实现的接口；支持多个接口
第三参数：实现这个接口InvocationHandler，创建代理对象，写增强的方法

2、编写JDK动态代理代码
（1）创建接口，定义方法
（2）创建接口实现类，实现方法
（3）使用Proxy类创建接口代理对象

## AOP的术语
1、连接点
- 类里面的哪些方法可以被增强，这些方法称为**连接点**

2、切入点
- 实际真正被增强的方法称为**切入点**

3、通知（增强）

（1）实际增加的逻辑部分称为通知（增强）

（2）通知有多种类型
* 前置通知（方法之前）（Before）
* 后置通知（方法之后）（AfterReturning）
* 环绕通知（方法前面和后面）（Around）
* 异常通知（出现异常执行）（AfterThrowing）
* 最终通知（finally，有无异常都会执行）（After）

4、切面
- 是一个动作：把通知应用到切入点的过程

## AOP操作（准备）
1、Spring框架一般基于 **AspectJ** 实现AOP操作
什么是 **AspectJ**：
AspectJ 不是Spring的组成部分，独立AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作。

2、基于AspectJ实现AOP操作
（1）基于XML配置文件实现
（2）基于注解方式实现（一般用这个）

3、在项目工程里引入AOP相关依赖

4、**切入点**表达式
（1）切入点表达式作用：知道对哪个类里面的哪个方法进行增强
（2）语法结构：
```
execution( [权限修饰符] [返回类型] [类全路径] [方法名称] ( [参数列表] ) )
```
举例1：对com.atguigu.dao.BookDao类里的add方法进行增强
```
execution( * com.atguigu.dao.BookDao.add(..) )
```
举例2：对com.atguigu.dao.BookDao类里所有方法进行增强
```
execution( * com.atguigu.dao.BookDao.*(..) )
```
举例3：对com.atguigu.dao包里所有类，类里所有方法进行增强
```
execution( * com.atguigu.dao.*.*(..) )
```

## AOP操作（AspectJ注解）
1、创建类，在类里定义方法

2、创建增强类（编写增强逻辑）
- 在增强类里创建方法，让不同的方法代表不同通知类型

3、进行通知的配置
1. 在spring配置文件中，开启注解扫描
2. 使用注解创建User和UserProxy对象（对象上加注解@Component）
3. 在增强类上面添加注解@Aspect
4. 在spring配置文件中开启生成代理对象
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.h5s20hujqco.webp" width="50%">

4、配置不同类型的通知
（1）在增强类的里面，在作文通知方法上添加通知类型注解，使用切入点表达式配置
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.jd8mrbdcvnc.webp" width="70%">

5、公共切入点抽取
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.101rs6im8vv4.webp" width="70%">
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.78jw6svhw7c0.webp" width="40%">


6、有多个增强类对同一个方法进行增强，设置增强类优先级
- 在增强类上面添加注解 @Order （数字类型值），数字越小优先级越高


7、完全使用注解开发
创建配置类，不需要创建XML配置文件
<img src="https://cdn.jsdelivr.net/gh/Carbda/image@master/image.55rovywl4ls0.webp" width="70%">