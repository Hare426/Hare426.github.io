---
title: 设计模式-原型模式
category: Design-pattern
tags: [设计模式, 创造型模式, 原型模式]
date: 2019-12-18 15:56:20
---


#### 原型模式
定义：
> 使用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。
> 原型模式是一种对象创建型模式。
> 

需要注意的是通过克隆方法所创建的对象是全新的对象，它们在内存中拥有新的地址，通常对克隆所产生的对象进行修改对原型对象不会造成任何影响，每一个克隆对象都是相互独立的。通过不同的方式修改可以得到一系列相似但不完全相同的对象。

原型模式结构图：
![原型模式结构图.png](/images/back-end/design-pattern/原型模式结构图.png)

在原型模式结构图中包含如下几个角色：
+ Prototype（抽象原型类）：它是声明克隆方法的接口，是所有具体原型类的公共父类，可以是抽象类也可以是接口，甚至还可以是具体实现类。
+ ConcretePrototype（具体原型类）：它实现在抽象原型类中声明的克隆方法，在克隆方法中返回自己的一个克隆对象。
+ Client（客户类）：让一个原型对象克隆自身从而创建一个新的对象，在客户类中只需要直接实例化或通过工厂方法等方式创建一个原型对象，再通过调用该对象的克隆方法即可得到多个相同的对象。由于客户类针对抽象原型类Prototype编程，因此用户可以根据需要选择具体原型类，系统具有较好的可扩展性，增加或更换具体原型类都很方便。

##### 通用实现方法
```
class ConcretePrototype implements Prototype
{
private String  attr; //成员属性
public void  setAttr(String attr)
{
    this.attr = attr;
}
public String  getAttr()
{
    return this.attr;
}
public Prototype  clone() //克隆方法
{
    Prototype  prototype = new ConcretePrototype(); //创建新对象
    prototype.setAttr(this.attr);
    return prototype;
}
}
```

```
Prototype obj1  = new ConcretePrototype();
obj1.setAttr("Sunny");
Prototype obj2  = obj1.clone();
```

##### Java语言提供的clone()方法
```
class ConcretePrototype implements  Cloneable
{
……
public Prototype  clone()
{
　　Object object = null;
　　try {
　　　　　object = super.clone();
　　} catch (CloneNotSupportedException exception) {
　　　　　System.err.println("Not support cloneable");
　　}
　　return (Prototype )object;
}
……
}
```

```
Prototype obj1  = new ConcretePrototype();
Prototype obj2  = obj1.clone();
```

一般而言，Java语言中的clone()方法满足：
1. 对任何对象x，都有x.clone() != x，即克隆对象与原型对象不是同一个对象；
2. 何对象x，都有x.clone().getClass() == x.getClass()，即克隆对象与原型对象的类型一样；
3. 对象x的equals()方法定义恰当，那么x.clone().equals(x)应该成立。


为了获取对象的一份拷贝，我们可以直接利用Object类的clone()方法，具体步骤如下：
1. 在派生类中覆盖基类的clone()方法，并声明为public；
2. 在派生的clone()方法中，调用super.clone()；
3. 派生类需实现Cloneable接口。
此时，Object类相当于抽象原型类，所有实现了Cloneable接口的类相当于具体原型类。

例：
![原型模式1.png](/images/back-end/design-pattern/原型模式1.png)

```
//工作周报WeeklyLog：具体原型类，考虑到代码的可读性和易理解性，只列出部分与模式相关的核心代码
class WeeklyLog implements Cloneable
{
       private  String name;
       private  String date;
       private  String content;
       public  void setName(String name) {
              this.name  = name;
       }
       public  void setDate(String date) {
              this.date  = date;
       }
       public  void setContent(String content) {
              this.content  = content;
       }
       public  String getName() {
              return  (this.name);
       }
       public  String getDate() {
              return  (this.date);
       }
       public  String getContent() {
              return  (this.content);
       }
     //克隆方法clone()，此处使用Java语言提供的克隆机制
       public WeeklyLog clone()
       {
              Object obj = null;
              try
              {
                     obj = super.clone();
                     return (WeeklyLog)obj;     
              }
              catch(CloneNotSupportedException e)
              {
                     System.out.println("不支持复制！");
                     return null;
              }
       }
}
```

```
class Client
{
       public  static void main(String args[])
       {
              WeeklyLog log_previous = new WeeklyLog();  //创建原型对象
              log_previous.setName("张无忌");
              log_previous.setDate("第12周");
              log_previous.setContent("这周工作很忙，每天加班！");

              System.out.println("****周报****");
              System.out.println("周次：" +  log_previous.getDate());
              System.out.println("姓名：" +  log_previous.getName());
              System.out.println("内容：" +  log_previous.getContent());
              System.out.println("--------------------------------");

              WeeklyLog  log_new;
              log_new  = log_previous.clone(); //调用克隆方法创建克隆对象
              log_new.setDate("第13周");
              System.out.println("****周报****");
              System.out.println("周次：" + log_new.getDate());
              System.out.println("姓名：" + log_new.getName());
              System.out.println("内容：" + log_new.getContent());
       }
}
```

##### 浅克隆
> 在浅克隆中，如果原型对象的成员变量是值类型，将复制一份给克隆对象；如果原型对象的成员变量是引用类型，则将引用对象的地址复制一份给克隆对象，也就是说原型对象和克隆对象的成员变量指向相同的内存地址。简单来说，在浅克隆中，当对象被复制时只复制它本身和其中包含的值类型的成员变量，而引用类型的成员对象并没有复制
> 

![原型模式2.png](/images/back-end/design-pattern/原型模式2.png)

```
//附件类
class Attachment
{
       private  String name; //附件名
       public  void setName(String name)
       {
              this.name  = name;
       }
       public  String getName()
       {
              return  this.name;
       }
       public void download()
       {
            System.out.println("下载附件，文件名为" + name);
       }
}
```

```
//工作周报WeeklyLog
class WeeklyLog implements Cloneable
{
     //为了简化设计和实现，假设一份工作周报中只有一个附件对象，实际情况中可以包含多个附件，可以通过List等集合对象来实现
       private Attachment attachment;
	   private String name;
       private  String date;
       private  String content;
       public void setAttachment(Attachment  attachment) {
              this.attachment = attachment;
       }
       public  void setName(String name) {
              this.name  = name;
       }
       public  void setDate(String date) {
              this.date  = date;
       }
       public  void setContent(String content) {
              this.content  = content;
       }
	   public Attachment  getAttachment(){
              return (this.attachment);
       }
       public  String getName() {
              return  (this.name);
       }
       public  String getDate() {
              return  (this.date);
       }
       public  String getContent() {
              return  (this.content);
       }
     //使用clone()方法实现浅克隆
       public WeeklyLog clone()
       {
              Object obj = null;
              try
              {
                     obj = super.clone();
                     return (WeeklyLog)obj;
              }
              catch(CloneNotSupportedException  e)
              {
                     System.out.println("不支持复制！");
                     return null;
              }
       }
}
```
##### 深克隆
> 在深克隆中，无论原型对象的成员变量是值类型还是引用类型，都将复制一份给克隆对象，深克隆将原型对象的所有引用对象也复制一份给克隆对象。简单来说，在深克隆中，除了对象本身被复制外，对象所包含的所有成员变量也将复制
> 

在Java语言中，如果需要实现深克隆，可以通过序列化(Serialization)等方式来实现。序列化就是将对象写到流的过程，写到流中的对象是原有对象的一个拷贝，而原对象仍然存在于内存中。通过序列化实现的拷贝不仅可以复制对象本身，而且可以复制其引用的成员对象，因此通过序列化将对象写到一个流中，再从流里将其读出来，可以实现深克隆。需要注意的是能够实现序列化的对象其类必须实现Serializable接口，否则无法实现序列化操作。下面我们使用深克隆技术来实现工作周报和附件对象的复制，由于要将附件对象和工作周报对象都写入流中，因此两个类均需要实现Serializable接口，其结构如图7-7所示：

![原型模式3.png](/images/back-end/design-pattern/原型模式3.png)

```
import  java.io.*;
//附件类
class  Attachment implements Serializable
{
       private  String name; //附件名
       public  void setName(String name)
       {
              this.name  = name;
       }
       public  String getName()
       {
              return  this.name;
       }
       public void download()
       {
            System.out.println("下载附件，文件名为" + name);
       }
}
```

```
import  java.io.*;
//工作周报类
class  WeeklyLog implements Serializable
{
       private  Attachment attachment;
       private  String name;
       private  String date;
       private  String content;
       public  void setAttachment(Attachment attachment) {
              this.attachment  = attachment;
       }
       public  void setName(String name) {
              this.name  = name;
       }
       public  void setDate(String date) {
              this.date  = date;
       }
       public  void setContent(String content) {
              this.content  = content;
       }
       public  Attachment getAttachment(){
              return  (this.attachment);
       }
       public  String getName() {
              return  (this.name);
       }
       public  String getDate() {
              return  (this.date);
       }
       public  String getContent() {
              return  (this.content);
       }
	   //使用序列化技术实现深克隆
       public WeeklyLog deepClone() throws  IOException, ClassNotFoundException, OptionalDataException
       {
              //将对象写入流中
              ByteArrayOutputStream bao=new  ByteArrayOutputStream();
              ObjectOutputStream oos=new  ObjectOutputStream(bao);
              oos.writeObject(this);

              //将对象从流中取出
              ByteArrayInputStream bis=new  ByteArrayInputStream(bao.toByteArray());
              ObjectInputStream ois=new  ObjectInputStream(bis);
              return  (WeeklyLog)ois.readObject();
       }
}
```

```
class Client
{
       public  static void main(String args[])
       {
              WeeklyLog  log_previous, log_new = null;
              log_previous  = new WeeklyLog(); //创建原型对象
              Attachment  attachment = new Attachment(); //创建附件对象
              log_previous.setAttachment(attachment);  //将附件添加到周报中
              try
              {
                     log_new =  log_previous.deepClone(); //调用深克隆方法创建克隆对象                  
              }
              catch(Exception e)
              {
                     System.err.println("克隆失败！");
              }
              //比较周报
              System.out.println("周报是否相同？ " + (log_previous ==  log_new));
              //比较附件
              System.out.println("附件是否相同？ " +  (log_previous.getAttachment() == log_new.getAttachment()));
       }
}
```

##### 原型管理器的引入和实现
带原型管理器的原型模式:
![带原型管理器的原型模式.png](/images/back-end/design-pattern/带原型管理器的原型模式.png)

例：
![原型模式4.png](/images/back-end/design-pattern/原型模式4.png)

```
import java.util.*;

//抽象公文接口，也可定义为抽象类，提供clone()方法的实现，将业务方法声明为抽象方法
interface OfficialDocument extends  Cloneable
{
       public  OfficialDocument clone();
       public  void display();
}

//可行性分析报告(Feasibility Analysis Report)类
class FAR implements OfficialDocument
{
       public  OfficialDocument clone()
      {
              OfficialDocument  far = null;
              try
              {
                     far  = (OfficialDocument)super.clone();
              }
              catch(CloneNotSupportedException  e)
              {
                      System.out.println("不支持复制！");
              }
              return  far;
       }

       public  void display()
       {
              System.out.println("《可行性分析报告》");
       }
}

//软件需求规格说明书(Software Requirements Specification)类
class SRS implements OfficialDocument
{
       public  OfficialDocument clone()
       {
              OfficialDocument  srs = null;
              try
              {
                     srs  = (OfficialDocument)super.clone();
              }
              catch(CloneNotSupportedException  e)
              { 
                     System.out.println("不支持复制！");
              }
              return  srs;
       }

       public  void display()
       {
              System.out.println("《软件需求规格说明书》");
       }
}

//原型管理器（使用饿汉式单例实现）
class  PrototypeManager
{
       //定义一个Hashtable，用于存储原型对象
       private Hashtable ht=new Hashtable();
       private static PrototypeManager pm =  new PrototypeManager();

       //为Hashtable增加公文对象   
       private  PrototypeManager()
       {
              ht.put("far",new  FAR());
              ht.put("srs",new  SRS());               
       }

       //增加新的公文对象
       public void addOfficialDocument(String  key,OfficialDocument doc)
       {
              ht.put(key,doc);
       }

       //通过浅克隆获取新的公文对象
       public OfficialDocument  getOfficialDocument(String key)
       {
              return  ((OfficialDocument)ht.get(key)).clone();
       }

       public static PrototypeManager  getPrototypeManager()
       {
              return pm;
       }
}
```

```
class Client
{
       public  static void main(String args[])
       {
              //获取原型管理器对象
              PrototypeManager pm =  PrototypeManager.getPrototypeManager();  

              OfficialDocument  doc1,doc2,doc3,doc4;

              doc1  = pm.getOfficialDocument("far");
              doc1.display();
              doc2  = pm.getOfficialDocument("far");
              doc2.display();
              System.out.println(doc1  == doc2);

              doc3  = pm.getOfficialDocument("srs");
              doc3.display();
              doc4  = pm.getOfficialDocument("srs");
              doc4.display();
              System.out.println(doc3  == doc4);
       }
}
```