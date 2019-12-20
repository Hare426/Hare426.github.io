---
title: 设计模式-组合模式
category: Design-pattern
tags: [设计模式, 结构型模式, 组合模式]
date: 2019-12-20 13:39:20
---
#### 组合模式
定义：
> 组合多个对象形成树形结构以表示具有“整体—部分”关系的层次结构。
> 组合模式对单个对象（即叶子对象）和组合对象（即容器对象）的使用具有一致性，组合模式又可以称为“整体—部分”(Part-Whole)模式。
> 它是一种对象结构型模式。
> 


组合模式结构图：
![组合模式结构图.png](/images/design-pattern/组合模式结构图.png)

在组合模式结构图中包含如下几个角色：
+ Component（抽象构件）：它可以是接口或抽象类，为叶子构件和容器构件对象声明接口，在该角色中可以包含所有子类共有行为的声明和实现。在抽象构件中定义了访问及管理它的子构件的方法，如增加子构件、删除子构件、获取子构件等。
+ Leaf（叶子构件）：它在组合结构中表示叶子节点对象，叶子节点没有子节点，它实现了在抽象构件中定义的行为。对于那些访问及管理子构件的方法，可以通过异常等方式进行处理。
+ Composite（容器构件）：它在组合结构中表示容器节点对象，容器节点包含子节点，其子节点可以是叶子节点，也可以是容器节点，它提供一个集合用于存储子节点，实现了在抽象构件中定义的行为，包括那些访问及管理子构件的方法，在其业务方法中可以递归调用其子节点的业务方法。

例：
![组合模式1.png](/images/design-pattern/组合模式1.png)

```
import java.util.*;  

//抽象文件类：抽象构件  
abstract class AbstractFile {  
    public abstract void add(AbstractFile file);  
    public abstract void remove(AbstractFile file);  
    public abstract AbstractFile getChild(int i);  
    public abstract void killVirus();  
}  

//图像文件类：叶子构件  
class ImageFile extends AbstractFile {  
    private String name;  

    public ImageFile(String name) {  
        this.name = name;  
    }  

    public void add(AbstractFile file) {  
       System.out.println("对不起，不支持该方法！");  
    }  

    public void remove(AbstractFile file) {  
        System.out.println("对不起，不支持该方法！");  
    }  

    public AbstractFile getChild(int i) {  
        System.out.println("对不起，不支持该方法！");  
        return null;  
    }  

    public void killVirus() {  
        //模拟杀毒  
        System.out.println("----对图像文件'" + name + "'进行杀毒");  
    }  
}  

//文本文件类：叶子构件  
class TextFile extends AbstractFile {  
    private String name;  

    public TextFile(String name) {  
        this.name = name;  
    }  

    public void add(AbstractFile file) {  
       System.out.println("对不起，不支持该方法！");  
    }  

    public void remove(AbstractFile file) {  
        System.out.println("对不起，不支持该方法！");  
    }  

    public AbstractFile getChild(int i) {  
        System.out.println("对不起，不支持该方法！");  
        return null;  
    }  

    public void killVirus() {  
        //模拟杀毒  
        System.out.println("----对文本文件'" + name + "'进行杀毒");  
    }  
}  

//视频文件类：叶子构件  
class VideoFile extends AbstractFile {  
    private String name;  

    public VideoFile(String name) {  
        this.name = name;  
    }  

    public void add(AbstractFile file) {  
       System.out.println("对不起，不支持该方法！");  
    }  

    public void remove(AbstractFile file) {  
        System.out.println("对不起，不支持该方法！");  
    }  

    public AbstractFile getChild(int i) {  
        System.out.println("对不起，不支持该方法！");  
        return null;  
    }  

    public void killVirus() {  
        //模拟杀毒  
        System.out.println("----对视频文件'" + name + "'进行杀毒");  
    }  
}  

//文件夹类：容器构件  
class Folder extends AbstractFile {  
    //定义集合fileList，用于存储AbstractFile类型的成员  
    private ArrayList<AbstractFile> fileList=new ArrayList<AbstractFile>();  
    private String name;  

    public Folder(String name) {  
        this.name = name;  
    }  

    public void add(AbstractFile file) {  
       fileList.add(file);    
    }  

    public void remove(AbstractFile file) {  
        fileList.remove(file);  
    }  

    public AbstractFile getChild(int i) {  
        return (AbstractFile)fileList.get(i);  
    }  

    public void killVirus() {  
        System.out.println("****对文件夹'" + name + "'进行杀毒");  //模拟杀毒  

        //递归调用成员构件的killVirus()方法  
        for(Object obj : fileList) {  
            ((AbstractFile)obj).killVirus();  
        }  
    }  
}
```
```
class Client {  
    public static void main(String args[]) {  
        //针对抽象构件编程  
        AbstractFile file1,file2,file3,file4,file5,folder1,folder2,folder3,folder4;  

        folder1 = new Folder("Sunny的资料");  
        folder2 = new Folder("图像文件");  
        folder3 = new Folder("文本文件");  
        folder4 = new Folder("视频文件");  

        file1 = new ImageFile("小龙女.jpg");  
        file2 = new ImageFile("张无忌.gif");  
        file3 = new TextFile("九阴真经.txt");  
        file4 = new TextFile("葵花宝典.doc");  
        file5 = new VideoFile("笑傲江湖.rmvb");  

        folder2.add(file1);  
        folder2.add(file2);  
        folder3.add(file3);  
        folder3.add(file4);  
        folder4.add(file5);  
        folder1.add(folder2);  
        folder1.add(folder3);  
        folder1.add(folder4);  

        //从“Sunny的资料”节点开始进行杀毒操作  
        folder1.killVirus();  
    }  
}
```

##### 透明组合模式

透明组合模式结构图：
![透明组合模式结构图.png](/images/design-pattern/透明组合模式结构图.png)

##### 安全组合模式

安全组合模式结构图：
![安全组合模式结构图.png](/images/design-pattern/安全组合模式结构图.png)
