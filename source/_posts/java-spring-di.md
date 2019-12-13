---
title: Spring 依赖注入
category: Back-end
tags: [Java, Spring, 依赖注入]
date: 2019-12-12 15:52:20
---

### spring 依赖注入

#### 依赖注入的方式
依赖注入有3种方式：构造器注入、set注入和注解注入；

##### 1. 构造器注入
构造器注入保证一些必要的属性在Bean实例化时就得到设置，并且确保了Bean实例在实例化后就可以使用。

使用方式：
1. 在类中，不用为属性设置setter方法，但是需要生成该类带参的构造方法。
2. 在配置文件中配置该类的bean，并配置构造器，在配置构造器中用到了<constructor-arg>节点，该节点有四个属性：
   
   + index：指定注入属性的顺序索引，从0开始；
   + type：指该属性所对应的类型；
   + ref：引用的依赖对象；
   + value：当注入的不是依赖对象，而是基本数据类型时，就用value；

例：
```
public class SpringAction {  
    //注入对象springDao  
    private SpringDao springDao;  
    private User user;  
      
    public SpringAction(SpringDao springDao,User user){  
        this.springDao = springDao;  
        this.user = user;  
        System.out.println("构造方法调用springDao和user");  
    }  
          
    public void save(){  
        user.setName("hare");  
        springDao.save(user);  
    }  
}  
```

```
    <bean name="springAction" class="com.bless.springdemo.action.SpringAction">  
        <!--(2)创建构造器注入,如果主类有带参的构造方法则需添加此配置-->  
        <constructor-arg index="0" ref="springDao"></constructor-arg>  
        <constructor-arg index="1" ref="user"></constructor-arg>  
    </bean>  
    <bean name="springDao" class="com.bless.springdemo.dao.impl.SpringDaoImpl"></bean>  
    <bean name="user" class="com.bless.springdemo.vo.User"></bean>  
```

例：
联合使用type和index才能确定匹配项和构造参数的入参对应关系；
```
public Car(String brand, String corp,double price){
    this.brand=brand;
    this.corp=corp;
    this.price=price;
}

public Car(String brand, String corp,int maxSpeed){
    this.brand=brand;
    this.corp=corp;
    this.maxSpeed=maxSpeed;
}
```
```
<!-- 构造函数注入(通过入参类型和位置索引确定对应关系) -->
<!-- 对应public Car(String brand, String corp,int maxSpeed)构造函数 -->
<bean id="car3" class="com.spring.model.Car">  
    <constructor-arg index="0" type="java.lang.String" value="奔驰"></constructor-arg>
    <constructor-arg index="1" type="java.lang.String" value="中国一汽"></constructor-arg>
    <constructor-arg index="2" type="int" value="200"></constructor-arg>
</bean>
```

##### 2. set注入
set注入要求Bean提供一个默认的构造函数，并为需要注入的属性提供对应的Setter方法。
> + Spring先调用Bean的默认构造函数实例化Bean对象，然后通过反射的方式调用Setter方法注入属性值;
> + 如果Bean显示定义了一个带参的构造函数，则需要同时提供一个默认无参的构造函数，否则使用属性注入时将抛出异常;
> 
例：
```
package com.bless.springdemo.action;  
public class SpringAction {  
    //注入对象springDao  
    private SpringDao springDao;  
    //一定要写被注入对象的set方法  
    public void setSpringDao(SpringDao springDao) {  
        this.springDao = springDao;  
    }  
  
    public void ok(){  
        springDao.ok();  
    }  
}  
```

```
    <!--配置bean,配置后该类由spring管理-->  
    <bean name="springAction" class="com.bless.springdemo.action.SpringAction">  
        <!--(1)依赖注入,配置当前类中相应的属性-->  
        <property name="springDao" ref="springDao"></property>  
    </bean>  
　　 <bean name="springDao" class="com.bless.springdemo.dao.impl.SpringDaoImpl"></bean>
```

##### 3. 注解注入
注解注入其实是使用注解的方式进行构造器注入或者set注入。
spring2.5开始提供了基于注解（Annotation-based）的配置，我们可以通过注解的方式来完成注入依赖。在Java代码中可以使用 @Resource或者@Autowired注解方式来经行注入。

@Resource和@Autowired的区别：
+ @Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入；
+ @Autowired默认是按照类型装配注入的，如果想按照名称来转配注入，则需要结合@Qualifier一起使用；
+ @Resource注解是由J2EE提供，而@Autowired是由Spring提供；

```
package com.bless.springdemo.action;  
public class SpringAction {  
        //注入对象springDao 
    @Resource
    private SpringDao springDao;  
        //不需要要写被注入对象的set方法，spring会帮你get/set 
       
    public void ok(){  
        springDao.ok();  
    }  
} 
```
```
//<context:annotation-config/> 作用是开启注解;
<context:annotation-config/>

<bean name="springAction" class="com.bless.springdemo.action.SpringAction">  
        <!--不需要配置属性，@Resource默认按照名称装配注入-->
</bean>  
<bean name="springDao" class="com.bless.springdemo.dao.impl.SpringDaoImpl"></bean>
```
@Autowired的同理，只不过默认是按照类型装配注入。

##### 补充
例：
```
@Autowired
private User user;
private String school;

public UserAccountServiceImpl(){
    this.school = user.getSchool();
}
```
这段代码执行会报错，因为Java类会先执行构造方法，然后再给注解了@Autowired 的user注入值，所以在执行构造方法的时候，就会报错。

解决办法是，使用构造器注入，如下：
```
private User user;
private String school;

@Autowired
public UserAccountServiceImpl(User user){
    this.user = user;
    this.school = user.getSchool();
}
```