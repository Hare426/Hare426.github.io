---
title: 设计模式-代理模式
category: Design-pattern
tags: [设计模式, 结构型模式, 代理模式]
date: 2019-12-25 11:20:20
---


#### 代理模式
定义：
> 给某一个对象提供一个代理或占位符，并由代理对象来控制对原对象的访问。
> 代理模式是一种对象结构型模式。
>

代理模式结构图：
![代理模式结构图.png](/images/design-pattern/代理模式结构图.png)

代理模式包含如下三个角色：
+ Subject（抽象主题角色）：它声明了真实主题和代理主题的共同接口，这样一来在任何使用真实主题的地方都可以使用代理主题，客户端通常需要针对抽象主题角色进行编程。
+ Proxy（代理主题角色）：它包含了对真实主题的引用，从而可以在任何时候操作真实主题对象；在代理主题角色中提供一个与真实主题角色相同的接口，以便在任何时候都可以替代真实主题；代理主题角色还可以控制对真实主题的使用，负责在需要的时候创建和删除真实主题对象，并对真实主题对象的使用加以约束。通常，在代理主题角色中，客户端在调用所引用的真实主题操作之前或之后还需要执行其他操作，而不仅仅是单纯调用真实主题对象中的操作。
+ RealSubject（真实主题角色）：它定义了代理角色所代表的真实对象，在真实主题角色中实现了真实的业务操作，客户端可以通过代理主题角色间接调用真实主题角色中定义的操作。

几种代理模式：
1. 远程代理(Remote Proxy)：为一个位于不同的地址空间的对象提供一个本地的代理对象，这个不同的地址空间可以是在同一台主机中，也可是在另一台主机中，远程代理又称为大使(Ambassador)。
2. 虚拟代理(Virtual Proxy)：如果需要创建一个资源消耗较大的对象，先创建一个消耗相对较小的对象来表示，真实对象只在需要时才会被真正创建。
3. 保护代理(Protect Proxy)：控制对一个对象的访问，可以给不同的用户提供不同级别的使用权限。
4. 缓冲代理(Cache Proxy)：为某一个目标操作的结果提供临时的存储空间，以便多个客户端可以共享这些结果。
5. 智能引用代理(Smart Reference Proxy)：当一个对象被引用时，提供一些额外的操作，例如将对象被调用的次数记录下来等。

例：
![代理模式1.png](/images/design-pattern/代理模式1.png)

```
//AccessValidator.cs  
using System;  

namespace ProxySample  
{  
    class AccessValidator  
    {  
        //模拟实现登录验证  
        public bool Validate(string userId)   
        {  
            Console.WriteLine("在数据库中验证用户'" + userId + "'是否是合法用户？");  
            if (userId.Equals("杨过")) {  
                Console.WriteLine("'{0}'登录成功！",userId);  
                return true;  
            }  
            else {  
                Console.WriteLine("'{0}'登录失败！", userId);  
                return false;  
            }  
        }  
    }  
}
```
```
//Logger.cs  
using System;  

namespace ProxySample  
{  
    class Logger  
    {  
        //模拟实现日志记录  
        public void Log(string userId) {  
            Console.WriteLine("更新数据库，用户'{0}'查询次数加1！",userId);  
        }  
    }  
}
```
```
//Searcher.cs  
namespace ProxySample  
{  
    interface Searcher  
    {  
        string DoSearch(string userId, string keyword);  
    }  
}
```
```
//RealSearcher.cs  
using System;  

namespace ProxySample  
{  
    class RealSearcher : Searcher  
    {  
        //模拟查询商务信息  
        public string DoSearch(string userId, string keyword) {  
            Console.WriteLine("用户'{0}'使用关键词'{1}'查询商务信息！",userId,keyword);  
            return "返回具体内容";  
        }  
    }  
}>
```
```
//ProxySearcher.cs  
namespace ProxySample  
{  
    class ProxySearcher : Searcher  
    {  
        private RealSearcher searcher = new RealSearcher(); //维持一个对真实主题的引用  
        private AccessValidator validator;  
        private Logger logger;  

        public string DoSearch(string userId, string keyword)  
        {  
            //如果身份验证成功，则执行查询  
            if (this.Validate(userId))  
            {  
                string result = searcher.DoSearch(userId, keyword); //调用真实主题对象的查询方法  
                this.Log(userId); //记录查询日志  
                return result; //返回查询结果  
            }  
            else  
            {  
                return null;  
            }  
        }  

        //创建访问验证对象并调用其Validate()方法实现身份验证  
        public bool Validate(string userId)  
        {  
            validator = new AccessValidator();  
            return validator.Validate(userId);  
        }  

        //创建日志记录对象并调用其Log()方法实现日志记录  
        public void Log(string userId)  
        {  
            logger = new Logger();  
            logger.Log(userId);  
        }  
    }  
}
```
```
<?xml version="1.0" encoding="utf-8" ?>  
<configuration>  
  <appSettings>  
    <add key="proxy" value="ProxySample.ProxySearcher"/>  
  </appSettings>  
</configuration>
```

```
//Program.cs  
using System;  
using System.Configuration;  
using System.Reflection;  

namespace ProxySample  
{  
    class Program  
    {  
        static void Main(string[] args)  
        {  
            //读取配置文件  
            string proxy = ConfigurationManager.AppSettings["proxy"];  

            //反射生成对象，针对抽象编程，客户端无须分辨真实主题类和代理类  
            Searcher searcher;  
            searcher = (Searcher)Assembly.Load("ProxySample").CreateInstance(proxy);  

            String result = searcher.DoSearch("杨过", "玉女心经");  
            Console.Read();  
        }  
    }  
}
```

##### 远程代理
定义：
> 远程代理(Remote Proxy)是一种常用的代理模式，它使得客户端程序可以访问在远程主机上的对象，远程主机可能具有更好的计算性能与处理速度，可以快速响应并处理客户端的请求。远程代理可以将网络的细节隐藏起来，使得客户端不必考虑网络的存在。客户端完全可以认为被代理的远程业务对象是在本地而不是在远程，而远程代理对象承担了大部分的网络通信工作，并负责对远程业务方法的调用。
> 
![远程代理示意图.png](/images/design-pattern/远程代理示意图.png)

在基于.NET平台的分布式技术，例如DCOM(Distribute Component Object Model，分布式组件 对象模型)、Web Service中，都应用了远程代理模式，大家可以查阅相关资料进行扩展学习。

##### 虚拟代理
定义：
> 虚拟代理(Virtual Proxy)也是一种常用的代理模式，对于一些占用系统资源较多或者加载时间较长的对象，可以给这些对象提供一个虚拟代理。在真实对象创建成功之前虚拟代理扮演真实对象的替身，而当真实对象创建之后，虚拟代理将用户的请求转发给真实对象。
> 

##### 缓冲代理
定义：
> 缓冲代理(Cache Proxy)也是一种较为常用的代理模式，它为某一个操作的结果提供临时的缓存存储空间，以便在后续使用中能够共享这些结果，从而可以避免某些方法的重复执行，优化系统性能。
>

