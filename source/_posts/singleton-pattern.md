---
title: 单例模式
category: Back-end
tags: [设计模式, 创造型模式, 单例模式]
date: 2019-12-16 15:44:20
---

#### 单例模式
定义：
> 确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，它提供全局访问的方法。
> 单例模式是一种对象创建型模式。
> 

单例模式三个要点：
> + 某个类只能有一个实例；
> + 它必须自行创建这个实例；
> + 它必须自行向整个系统提供这个实例；
> 

单例模式结构图：
![单例模式结构图.png](/images/back-end/design-pattern/单例模式结构图.png)

单例模式结构图中只包含一个单例角色：
+ Singleton（单例）：在单例类的内部实现只生成一个实例，同时它提供一个静态的getInstance()工厂方法，让客户可以访问它的唯一实例；为了防止在外部对其实例化，将其构造函数设计为私有；在单例类内部定义了一个Singleton类型的静态对象，作为外部共享的唯一实例。


例：
![单例模式1.png](/images/back-end/design-pattern/单例模式1.png)

```
import java.util.*;  

//负载均衡器LoadBalancer：单例类，真实环境下该类将非常复杂，包括大量初始化的工作和业务方法，考虑到代码的可读性和易理解性，只列出部分与模式相关的核心代码  
class LoadBalancer {  
    //私有静态成员变量，存储唯一实例  
    private static LoadBalancer instance = null;  
    //服务器集合  
    private List serverList = null;  

    //私有构造函数  
    private LoadBalancer() {  
        serverList = new ArrayList();  
    }  

    //公有静态成员方法，返回唯一实例  
    public static LoadBalancer getLoadBalancer() {  
        if (instance == null) {  
            instance = new LoadBalancer();  
        }  
        return instance;  
    }  

    //增加服务器  
    public void addServer(String server) {  
        serverList.add(server);  
    }  

    //删除服务器  
    public void removeServer(String server) {  
        serverList.remove(server);  
    }  

    //使用Random类随机获取服务器  
    public String getServer() {  
        Random random = new Random();  
        int i = random.nextInt(serverList.size());  
        return (String)serverList.get(i);  
    }  
}
```

```
class Client {  
    public static void main(String args[]) {  
        //创建四个LoadBalancer对象  
        LoadBalancer balancer1,balancer2,balancer3,balancer4;  
        balancer1 = LoadBalancer.getLoadBalancer();  
        balancer2 = LoadBalancer.getLoadBalancer();  
        balancer3 = LoadBalancer.getLoadBalancer();  
        balancer4 = LoadBalancer.getLoadBalancer();  

        //判断服务器负载均衡器是否相同  
        if (balancer1 == balancer2 && balancer2 == balancer3 && balancer3 == balancer4) {  
            System.out.println("服务器负载均衡器具有唯一性！");  
        }  

        //增加服务器  
        balancer1.addServer("Server 1");  
        balancer1.addServer("Server 2");  
        balancer1.addServer("Server 3");  
        balancer1.addServer("Server 4");  

        //模拟客户端请求的分发  
        for (int i = 0; i < 10; i++) {  
            String server = balancer1.getServer();  
            System.out.println("分发请求至服务器： " + server);  
      }  
    }  
}
```
##### 饿汉式单例类
饿汉式单例类结构图：
![饿汉式单例模式.png](/images/back-end/design-pattern/饿汉式单例模式.png)

```
class EagerSingleton {   
    private static final EagerSingleton instance = new EagerSingleton();   
    private EagerSingleton() { }   

    public static EagerSingleton getInstance() {  
        return instance;   
    }     
}
```

##### 懒汉式单例类与线程锁定
懒汉式单例类结构图：
![懒汉式单例模式.png](/images/back-end/design-pattern/懒汉式单例模式.png)

```
class LazySingleton {   
    private static LazySingleton instance = null;   

    private LazySingleton() { }   

    synchronized public static LazySingleton getInstance() {   
        if (instance == null) {  
            instance = new LazySingleton();   
        }  
        return instance;   
    }  
}
```
优化：
```
public static LazySingleton getInstance() {   
    if (instance == null) {  
        synchronized (LazySingleton.class) {  
            instance = new LazySingleton();   
        }  
    }  
    return instance;   
}
```
进一步优化：
```
class LazySingleton {   
    //被volatile修饰的成员变量可以确保多个线程都能够正确处理;
    private volatile static LazySingleton instance = null;   

    private LazySingleton() { }   

    public static LazySingleton getInstance() {   
        //第一重判断  
        if (instance == null) {  
            //锁定代码块  
            synchronized (LazySingleton.class) {  
                //第二重判断  
                if (instance == null) {  
                    instance = new LazySingleton(); //创建单例实例  
                }  
            }  
        }  
        return instance;   
    }  
}
```

##### IoDH
```
//Initialization on Demand Holder  
class Singleton {  
    private Singleton() {  
    }  

    private static class HolderClass {  
            private final static Singleton instance = new Singleton();  
    }  

    public static Singleton getInstance() {  
        return HolderClass.instance;  
    }  

    public static void main(String args[]) {  
        Singleton s1, s2;   
            s1 = Singleton.getInstance();  
        s2 = Singleton.getInstance();  
        System.out.println(s1==s2);  
    }  
}
```
