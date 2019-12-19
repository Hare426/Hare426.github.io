---
title: 设计模式-桥接模式
category: Design-pattern
tags: [设计模式, 结构型模式, 桥接模式]
date: 2019-12-19 17:00:20
---

#### 桥接模式
定义：
> 将抽象部分与它的实现部分分离，使它们都可以独立地变化。
> 它是一种对象结构型模式，又称为柄体(Handle and Body)模式或接口(Interface)模式。
> 

桥接模式结构图：
![桥接模式结构图.png](/images/design-pattern/桥接模式结构图.png)

在桥接模式结构图中包含如下几个角色：
+ Abstraction（抽象类）：用于定义抽象类的接口，它一般是抽象类而不是接口，其中定义了一个Implementor（实现类接口）类型的对象并可以维护该对象，它与Implementor之间具有关联关系，它既可以包含抽象业务方法，也可以包含具体业务方法。
+ RefinedAbstraction（扩充抽象类）：扩充由Abstraction定义的接口，通常情况下它不再是抽象类而是具体类，它实现了在Abstraction中声明的抽象业务方法，在RefinedAbstraction中可以调用在Implementor中定义的业务方法。
+ Implementor（实现类接口）：定义实现类的接口，这个接口不一定要与Abstraction的接口完全一致，事实上这两个接口可以完全不同，一般而言，Implementor接口仅提供基本操作，而Abstraction定义的接口可能会做更多更复杂的操作。Implementor接口对这些基本操作进行了声明，而具体实现交给其子类。通过关联关系，在Abstraction中不仅拥有自己的方法，还可以调用到Implementor中定义的方法，使用关联关系来替代继承关系。
+ ConcreteImplementor（具体实现类）：具体实现Implementor接口，在不同的ConcreteImplementor中提供基本操作的不同实现，在程序运行时，ConcreteImplementor对象将替换其父类对象，提供给抽象类具体的业务操作方法。

```
interface Implementor {  
    public void operationImpl();  
}

abstract class Abstraction {  
    protected Implementor impl; //定义实现类接口对象  

    public void setImpl(Implementor impl) {  
        this.impl=impl;  
    }  

    public abstract void operation();  //声明抽象业务方法  
}

class RefinedAbstraction extends Abstraction {  
    public void operation() {  
        //业务代码  
        impl.operationImpl();  //调用实现类的方法  
        //业务代码  
    }  
}
```

例：
![桥接模式1.png](/images/design-pattern/桥接模式1.png)

```
//像素矩阵类：辅助类，各种格式的文件最终都被转化为像素矩阵，不同的操作系统提供不同的方式显示像素矩阵  
class Matrix {  
    //此处代码省略  
}  

//抽象图像类：抽象类  
abstract class Image {  
    protected ImageImp imp;  

    public void setImageImp(ImageImp imp) {  
        this.imp = imp;  
    }   

    public abstract void parseFile(String fileName);  
}  

//抽象操作系统实现类：实现类接口  
interface ImageImp {  
    public void doPaint(Matrix m);  //显示像素矩阵m  
}   

//Windows操作系统实现类：具体实现类  
class WindowsImp implements ImageImp {  
    public void doPaint(Matrix m) {  
        //调用Windows系统的绘制函数绘制像素矩阵  
        System.out.print("在Windows操作系统中显示图像：");  
    }  
}  

//Linux操作系统实现类：具体实现类  
class LinuxImp implements ImageImp {  
    public void doPaint(Matrix m) {  
        //调用Linux系统的绘制函数绘制像素矩阵  
        System.out.print("在Linux操作系统中显示图像：");  
    }  
}  

//Unix操作系统实现类：具体实现类  
class UnixImp implements ImageImp {  
    public void doPaint(Matrix m) {  
        //调用Unix系统的绘制函数绘制像素矩阵  
        System.out.print("在Unix操作系统中显示图像：");  
    }  
}  

//JPG格式图像：扩充抽象类  
class JPGImage extends Image {  
    public void parseFile(String fileName) {  
        //模拟解析JPG文件并获得一个像素矩阵对象m;  
        Matrix m = new Matrix();   
        imp.doPaint(m);  
        System.out.println(fileName + "，格式为JPG。");  
    }  
}  

//PNG格式图像：扩充抽象类  
class PNGImage extends Image {  
    public void parseFile(String fileName) {  
        //模拟解析PNG文件并获得一个像素矩阵对象m;  
        Matrix m = new Matrix();   
        imp.doPaint(m);  
        System.out.println(fileName + "，格式为PNG。");  
    }  
}  

//BMP格式图像：扩充抽象类  
class BMPImage extends Image {  
    public void parseFile(String fileName) {  
        //模拟解析BMP文件并获得一个像素矩阵对象m;  
        Matrix m = new Matrix();   
        imp.doPaint(m);  
        System.out.println(fileName + "，格式为BMP。");  
    }  
}  

//GIF格式图像：扩充抽象类  
class GIFImage extends Image {  
    public void parseFile(String fileName) {  
        //模拟解析GIF文件并获得一个像素矩阵对象m;  
        Matrix m = new Matrix();   
        imp.doPaint(m);  
        System.out.println(fileName + "，格式为GIF。");  
    }  
}
```
```
<?xml version="1.0"?>  
<config>  
    <!--RefinedAbstraction-->  
    <className>JPGImage</className>   
    <!--ConcreteImplementor-->  
    <className>WindowsImp</className>  
</config>
```

```
import javax.xml.parsers.*;  
import org.w3c.dom.*;  
import org.xml.sax.SAXException;  
import java.io.*;  
public class XMLUtil {  
//该方法用于从XML配置文件中提取具体类类名，并返回一个实例对象  
    public static Object getBean(String args) {  
        try {  
            //创建文档对象  
            DocumentBuilderFactory dFactory = DocumentBuilderFactory.newInstance();  
            DocumentBuilder builder = dFactory.newDocumentBuilder();  
            Document doc;                             
            doc = builder.parse(new File("config.xml"));   
            NodeList nl=null;  
            Node classNode=null;  
            String cName=null;  
            nl = doc.getElementsByTagName("className");  

            if(args.equals("image")) {  
                //获取第一个包含类名的节点，即扩充抽象类  
                classNode=nl.item(0).getFirstChild();  

            }  
            else if(args.equals("os")) {  
               //获取第二个包含类名的节点，即具体实现类  
                classNode=nl.item(1).getFirstChild();  
            }  

             cName=classNode.getNodeValue();  
             //通过类名生成实例对象并将其返回  
             Class c=Class.forName(cName);  
             Object obj=c.newInstance();  
             return obj;          
           }     
           catch(Exception e) {  
              e.printStackTrace();  
              return null;  
          }  
     }  
}
```
```
class Client {  
    public static void main(String args[]) {  
        Image image;  
        ImageImp imp;  
        image = (Image)XMLUtil.getBean("image");  
        imp = (ImageImp)XMLUtil.getBean("os");  
        image.setImageImp(imp);  
        image.parseFile("小龙女");  
    }  
}
```

##### 适配器模式与桥连接模式
例：
![桥接模式与适配器模式.png](/images/design-pattern/桥接模式与适配器模式.png)

