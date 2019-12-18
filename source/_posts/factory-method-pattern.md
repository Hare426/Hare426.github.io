---
title: 设计模式-工厂方法模式
category: Design-pattern
tags: [设计模式, 创造型模式, 工厂方法模式]
date: 2019-12-13 15:49:20
---
#### 工厂方法模式
定义：
> 定义一个用于创建对象的接口，让子类决定将哪一个类实例化。
> 工厂方法模式让一个类的实例化延迟到其子类。
> 工厂方法模式又简称为工厂模式(Factory Pattern)，又可称作虚拟构造器模式(Virtual Constructor Pattern)或多态工厂模式(Polymorphic Factory Pattern)。
> 工厂方法模式是一种类创建型模式。
> 

工程方法模式结构图：
![工程方法模式结构图.png](/images/back-end/design-pattern/工程方法模式结构图.png)

在工厂方法模式结构图中包含如下几个角色：
+ Product（抽象产品）：它是定义产品的接口，是工厂方法模式所创建对象的超类型，也就是产品对象的公共父类。
+ ConcreteProduct（具体产品）：它实现了抽象产品接口，某种类型的具体产品由专门的具体工厂创建，具体工厂和具体产品之间一一对应。
+ Factory（抽象工厂）：在抽象工厂类中，声明了工厂方法(Factory Method)，用于返回一个产品。抽象工厂是工厂方法模式的核心，所有创建对象的工厂类都必须实现该接口。
+ ConcreteFactory（具体工厂）：它是抽象工厂类的子类，实现了抽象工厂中定义的工厂方法，并可由客户端调用，返回一个具体产品类的实例。

例：
![工程方法模式1.png](/images/back-end/design-pattern/工程方法模式1.png)

```
//日志记录器接口：抽象产品  
interface Logger {  
    public void writeLog();  
}  

//数据库日志记录器：具体产品  
class DatabaseLogger implements Logger {  
    public void writeLog() {  
        System.out.println("数据库日志记录。");  
    }  
}  

//文件日志记录器：具体产品  
class FileLogger implements Logger {  
    public void writeLog() {  
        System.out.println("文件日志记录。");  
    }  
}  

//日志记录器工厂接口：抽象工厂  
interface LoggerFactory {  
    public Logger createLogger();  
}  

//数据库日志记录器工厂类：具体工厂  
class DatabaseLoggerFactory implements LoggerFactory {  
    public Logger createLogger() {  
            //连接数据库，代码省略  
            //创建数据库日志记录器对象  
            Logger logger = new DatabaseLogger();   
            //初始化数据库日志记录器，代码省略  
            return logger;  
    }     
}  

//文件日志记录器工厂类：具体工厂  
class FileLoggerFactory implements LoggerFactory {  
    public Logger createLogger() {  
            //创建文件日志记录器对象  
            Logger logger = new FileLogger();   
            //创建文件，代码省略  
            return logger;  
    }     
}
```

```
class Client {  
    public static void main(String args[]) {  
        LoggerFactory factory;  
        Logger logger;  
        factory = new FileLoggerFactory(); //可引入配置文件实现  
        logger = factory.createLogger();  
        logger.writeLog();  
    }  
}
```

重载的工厂方法：

![重载的工程方法.png](/images/back-end/design-pattern/重载的工程方法.png)

```
interface LoggerFactory {  
    public Logger createLogger();  
    public Logger createLogger(String args);  
    public Logger createLogger(Object obj);  
}
```

```
class DatabaseLoggerFactory implements LoggerFactory {  
    public Logger createLogger() {  
            //使用默认方式连接数据库，代码省略  
            Logger logger = new DatabaseLogger();   
            //初始化数据库日志记录器，代码省略  
            return logger;  
    }  

    public Logger createLogger(String args) {  
            //使用参数args作为连接字符串来连接数据库，代码省略  
            Logger logger = new DatabaseLogger();   
            //初始化数据库日志记录器，代码省略  
            return logger;  
    }     

    public Logger createLogger(Object obj) {  
            //使用封装在参数obj中的连接字符串来连接数据库，代码省略  
            Logger logger = new DatabaseLogger();   
            //使用封装在参数obj中的数据来初始化数据库日志记录器，代码省略  
            return logger;  
    }     
}  
```

工厂方法的隐藏：
![工厂方法的隐藏.png](/images/back-end/design-pattern/工厂方法的隐藏.png)

