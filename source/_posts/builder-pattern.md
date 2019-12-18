---
title: 设计模式-建造者模式
category: Design-pattern
tags: [设计模式, 创造型模式, 建造者模式]
date: 2019-12-18 17:04:20
---
#### 建造者模式
定义：
> 将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。
> 建造者模式是一种对象创建型模式。
> 

建造者模式结构图：
![建造者模式结构图.png](/images/back-end/design-pattern/建造者模式结构图.png)

在建造者模式结构图中包含如下几个角色：
● Builder（抽象建造者）：它为创建一个产品Product对象的各个部件指定抽象接口，在该接口中一般声明两类方法，一类方法是buildPartX()，它们用于创建复杂对象的各个部件；另一类方法是getResult()，它们用于返回复杂对象。Builder既可以是抽象类，也可以是接口。
●ConcreteBuilder（具体建造者）：它实现了Builder接口，实现各个部件的具体构造和装配方法，定义并明确它所创建的复杂对象，也可以提供一个方法返回创建好的复杂产品对象。
●Product（产品角色）：它是被构建的复杂对象，包含多个组成部件，具体建造者创建该产品的内部表示并定义它的装配过程。
● Director（指挥者）：指挥者又称为导演类，它负责安排复杂对象的建造次序，指挥者与抽象建造者之间存在关联关系，可以在其construct()建造方法中调用建造者对象的部件构造与装配方法，完成复杂对象的建造。客户端一般只需要与指挥者进行交互，在客户端确定具体建造者的类型，并实例化具体建造者对象（也可以通过配置文件和反射机制），然后通过指挥者类的构造函数或者Setter方法将该对象传入指挥者类中。

例：
![建造者模式1.png](/images/back-end/design-pattern/建造者模式1.png)

```
class Actor
{
       private  String type; //角色类型
       private  String sex; //性别
       private  String face; //脸型
       private  String costume; //服装
       private  String hairstyle; //发型

       public  void setType(String type) {
              this.type  = type;
       }
       public  void setSex(String sex) {
              this.sex  = sex;
       }
       public  void setFace(String face) {
              this.face  = face;
       }
       public  void setCostume(String costume) {
              this.costume  = costume;
       }
       public  void setHairstyle(String hairstyle) {
              this.hairstyle  = hairstyle;
       }
       public  String getType() {
              return  (this.type);
       }
       public  String getSex() {
              return  (this.sex);
       }
       public  String getFace() {
              return  (this.face);
       }
       public  String getCostume() {
              return  (this.costume);
       }
       public  String getHairstyle() {
              return  (this.hairstyle);
       }
}

//角色建造器：抽象建造者
abstract class ActorBuilder
{
       protected  Actor actor = new Actor();

       public  abstract void buildType();
       public  abstract void buildSex();
       public  abstract void buildFace();
       public  abstract void buildCostume();
       public  abstract void buildHairstyle();

      //工厂方法，返回一个完整的游戏角色对象
       public Actor createActor()
       {
              return actor;
       }
}

//英雄角色建造器：具体建造者
class HeroBuilder extends ActorBuilder
{
       public  void buildType()
       {
              actor.setType("英雄");
       }
       public  void buildSex()
       {
              actor.setSex("男");
       }
       public  void buildFace()
       {
              actor.setFace("英俊");
       }
       public  void buildCostume()
       {
              actor.setCostume("盔甲");
       }
       public  void buildHairstyle()
       {
              actor.setHairstyle("飘逸");
       }    
}

//天使角色建造器：具体建造者
class AngelBuilder extends ActorBuilder
{
       public  void buildType()
       {
              actor.setType("天使");
       }
       public  void buildSex()
       {
              actor.setSex("女");
       }
       public  void buildFace()
       {
              actor.setFace("漂亮");
       }
       public  void buildCostume()
       {
              actor.setCostume("白裙");
       }
       public  void buildHairstyle()
       {
              actor.setHairstyle("披肩长发");
       }    
}

//恶魔角色建造器：具体建造者
class DevilBuilder extends ActorBuilder
{
       public  void buildType()
       {
              actor.setType("恶魔");
       }
       public  void buildSex()
       {
              actor.setSex("妖");
       }
       public  void buildFace()
       {
              actor.setFace("丑陋");
       }
       public  void buildCostume()
       {
              actor.setCostume("黑衣");
       }
       public  void buildHairstyle()
       {
              actor.setHairstyle("光头");
       }    
}
```

```
//游戏角色创建控制器：指挥者
class ActorController
{
    //逐步构建复杂产品对象
       public Actor construct(ActorBuilder ab)
       {
              Actor actor;
              ab.buildType();
              ab.buildSex();
              ab.buildFace();
              ab.buildCostume();
              ab.buildHairstyle();
              actor=ab.createActor();
              return actor;
       }
}
```

```
import javax.xml.parsers.*;
import org.w3c.dom.*;
import org.xml.sax.SAXException;
import java.io.*;
class XMLUtil
{
//该方法用于从XML配置文件中提取具体类类名，并返回一个实例对象
       public  static Object getBean()
       {
              try
              {
                     //创建文档对象
                     DocumentBuilderFactory  dFactory = DocumentBuilderFactory.newInstance();
                     DocumentBuilder  builder = dFactory.newDocumentBuilder();
                     Document  doc;                                                
                     doc  = builder.parse(new File("config.xml"));

                     //获取包含类名的文本节点
                     NodeList  nl = doc.getElementsByTagName("className");
            Node  classNode=nl.item(0).getFirstChild();
            String  cName=classNode.getNodeValue();

            //通过类名生成实例对象并将其返回
            Class c=Class.forName(cName);
                 Object obj=c.newInstance();
            return obj;
         }  
         catch(Exception e)
         {
              e.printStackTrace();
              return null;
          }
       }
}
```
```
<?xml version="1.0"?>
<config>
       <className>AngelBuilder</className>
</config>   
// 编写如下客户端测试代码：
class Client
{
       public  static void main(String args[])
       {
              ActorBuilder ab; //针对抽象建造者编程
              ab =  (ActorBuilder)XMLUtil.getBean(); //反射生成具体建造者对象

         ActorController ac = new  ActorController();
              Actor actor;
              actor = ac.construct(ab); //通过指挥者创建完整的建造者对象

              String  type = actor.getType();
              System.out.println(type  + "的外观：");
              System.out.println("性别：" + actor.getSex());
              System.out.println("面容：" + actor.getFace());
              System.out.println("服装：" + actor.getCostume());
              System.out.println("发型：" + actor.getHairstyle());
       }
}
```

##### Director优化
**省略Director**
```
abstract class ActorBuilder
{
       protected static Actor actor = new  Actor();

       public  abstract void buildType();
       public  abstract void buildSex();
       public  abstract void buildFace();
       public  abstract void buildCostume();
       public  abstract void buildHairstyle();

       public static Actor  construct(ActorBuilder ab)
       {
              ab.buildType();
              ab.buildSex();
              ab.buildFace();
              ab.buildCostume();
              ab.buildHairstyle();
              return actor;
       }
}
```
```
 ……
              ActorBuilder  ab;
              ab  = (ActorBuilder)XMLUtil.getBean();

              Actor  actor;
              actor =  ActorBuilder.construct(ab);
 ……
```
进一步优化：
```
abstract class ActorBuilder
{
       protected  Actor actor = new Actor();

       public  abstract void buildType();
       public  abstract void buildSex();
       public  abstract void buildFace();
       public  abstract void buildCostume();
       public  abstract void buildHairstyle();

       public Actor construct()
       {
              this.buildType();
              this.buildSex();
              this.buildFace();
              this.buildCostume();
              this.buildHairstyle();
              return actor;
       }
}
```
```
……
              ActorBuilder  ab;
              ab  = (ActorBuilder)XMLUtil.getBean();

              Actor  actor;
              actor = ab.construct();
……
```

   
2. 引入钩子方法
钩子方法的返回类型通常为boolean类型，方法名一般为isXXX()，钩子方法定义在抽象建造者类中。
```
abstract class ActorBuilder
{
       protected  Actor actor = new Actor();

       public  abstract void buildType();
       public  abstract void buildSex();
       public  abstract void buildFace();
       public  abstract void buildCostume();
       public  abstract void buildHairstyle();

       //钩子方法
       public boolean isBareheaded()
       {
              return false;
       }

       public  Actor createActor()
       {
              return  actor;
       }
}
```
```
class DevilBuilder extends ActorBuilder
{
       public  void buildType()
       {
              actor.setType("恶魔");
       }
       public  void buildSex()
       {
              actor.setSex("妖");
       }
       public  void buildFace()
       {
              actor.setFace("丑陋");
       }
       public  void buildCostume()
       {
              actor.setCostume("黑衣");
       }
       public  void buildHairstyle()
       {
              actor.setHairstyle("光头");
       }
       //覆盖钩子方法
       public boolean isBareheaded()
       {
              return true;
       }     
}
```
```
class ActorController
{
       public  Actor construct(ActorBuilder ab)
       {
              Actor  actor;
              ab.buildType();
              ab.buildSex();
              ab.buildFace();
              ab.buildCostume();
              //通过钩子方法来控制产品的构建
              if(!ab.isBareheaded())
              {
                     ab. buildHairstyle();
              }
              actor=ab.createActor();
              return  actor;
       }
}
```