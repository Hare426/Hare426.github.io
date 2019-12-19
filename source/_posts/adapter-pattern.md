---
title: 设计模式-适配器模式
category: Design-pattern
tags: [设计模式, 结构型模式, 适配器模式]
date: 2019-12-19 15:44:20
---
#### 适配器模式
定义：
> 将一个接口转换成客户希望的另一个接口，使接口不兼容的那些类可以一起工作，其别名为包装器(Wrapper)。
> 适配器模式既可以作为类结构型模式，也可以作为对象结构型模式。
> 

注：在适配器模式定义中所提及的接口是指广义的接口，它可以表示一个方法或者方法的集合。

根据适配器类与适配者类的关系不同，适配器模式可分为***对象适配器***和***类适配器***两种，在对象适配器模式中，适配器与适配者之间是关联关系；在类适配器模式中，适配器与适配者之间是继承（或实现）关系。

##### 对象适配器模式
对象适配器模式结构图：
![对象适配器模式结构图.png](/images/design-pattern/对象适配器模式结构图.png)

在对象适配器模式结构图中包含如下几个角色：
+ Target（目标抽象类）：目标抽象类定义客户所需接口，可以是一个抽象类或接口，也可以是具体类。
+ Adapter（适配器类）：适配器可以调用另一个接口，作为一个转换器，对Adaptee和Target进行适配，适配器类是适配器模式的核心，在对象适配器中，它通过继承Target并关联一个Adaptee对象使二者产生联系。
+ Adaptee（适配者类）：适配者即被适配的角色，它定义了一个已经存在的接口，这个接口需要适配，适配者类一般是一个具体类，包含了客户希望使用的业务方法，在某些情况下可能没有适配者类的源代码。

例：
![适配器模式1.png](/images/design-pattern/适配器模式1.png)

```
//抽象成绩操作类：目标接口  
interface ScoreOperation {  
    public int[] sort(int array[]); //成绩排序  
    public int search(int array[],int key); //成绩查找  
}  

//快速排序类：适配者  
class QuickSort {  
    public int[] quickSort(int array[]) {  
        sort(array,0,array.length-1);  
        return array;  
    }  

    public void sort(int array[],int p, int r) {  
        int q=0;  
        if(p<r) {  
            q=partition(array,p,r);  
            sort(array,p,q-1);  
            sort(array,q+1,r);  
        }  
    }  

    public int partition(int[] a, int p, int r) {  
        int x=a[r];  
        int j=p-1;  
        for (int i=p;i<=r-1;i++) {  
            if (a[i]<=x) {  
                j++;  
                swap(a,j,i);  
            }  
        }  
        swap(a,j+1,r);  
        return j+1;   
    }  

    public void swap(int[] a, int i, int j) {     
        int t = a[i];     
        a[i] = a[j];     
        a[j] = t;     
    }  
}  

//二分查找类：适配者  
class BinarySearch {  
    public int binarySearch(int array[],int key) {  
        int low = 0;  
        int high = array.length -1;  
        while(low <= high) {  
            int mid = (low + high) / 2;  
            int midVal = array[mid];  
            if(midVal < key) {    
low = mid +1;    
}  
            else if (midVal > key) {    
high = mid -1;    
}  
            else {    
return 1; //找到元素返回1    
}  
        }  
        return -1;  //未找到元素返回-1  
    }  
}  

//操作适配器：适配器  
class OperationAdapter implements ScoreOperation {  
    private QuickSort sortObj; //定义适配者QuickSort对象  
    private BinarySearch searchObj; //定义适配者BinarySearch对象  

    public OperationAdapter() {  
        sortObj = new QuickSort();  
        searchObj = new BinarySearch();  
    }  

    public int[] sort(int array[]) {    
return sortObj.quickSort(array); //调用适配者类QuickSort的排序方法  
}  

    public int search(int array[],int key) {    
return searchObj.binarySearch(array,key); //调用适配者类BinarySearch的查找方法  
}  
}
```

```
import javax.xml.parsers.*;  
import org.w3c.dom.*;  
import org.xml.sax.SAXException;  
import java.io.*;  
class XMLUtil {  
//该方法用于从XML配置文件中提取具体类类名，并返回一个实例对象  
    public static Object getBean() {  
        try {  
            //创建文档对象  
            DocumentBuilderFactory dFactory = DocumentBuilderFactory.newInstance();  
            DocumentBuilder builder = dFactory.newDocumentBuilder();  
            Document doc;                             
            doc = builder.parse(new File("config.xml"));   

            //获取包含类名的文本节点  
            NodeList nl = doc.getElementsByTagName("className");  
            Node classNode=nl.item(0).getFirstChild();  
            String cName=classNode.getNodeValue();  

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
<?xml version="1.0"?>  
<config>  
    <className>OperationAdapter</className>  
</config>
```

```
class Client {  
    public static void main(String args[]) {  
        ScoreOperation operation;  //针对抽象目标接口编程  
        operation = (ScoreOperation)XMLUtil.getBean(); //读取配置文件，反射生成对象  
        int scores[] = {84,76,50,69,90,91,88,96}; //定义成绩数组  
        int result[];  
        int score;  

        System.out.println("成绩排序结果：");  
        result = operation.sort(scores);  

        //遍历输出成绩  
        for(int i : scores) {  
            System.out.print(i + ",");  
        }  
        System.out.println();  

        System.out.println("查找成绩90：");  
        score = operation.search(result,90);  
        if (score != -1) {  
            System.out.println("找到成绩90。");  
        }  
        else {  
            System.out.println("没有找到成绩90。");  
        }  

        System.out.println("查找成绩92：");  
        score = operation.search(result,92);  
        if (score != -1) {  
            System.out.println("找到成绩92。");  
        }  
        else {  
            System.out.println("没有找到成绩92。");  
        }  
    }  
}
```

##### 类适配器

类适配器模式结构图：
![类适配器模式结构图.png](/images/design-pattern/类适配器模式结构图.png)

```
class Adapter extends Adaptee implements Target {  
    public void request() {  
        specificRequest();  
    }  
}
```
由于Java、C#等语言不支持多重类继承，因此类适配器的使用受到很多限制，例如如果目标抽象类Target不是接口，而是一个类，就无法使用类适配器；此外，如果适配者Adapter为最终(Final)类，也无法使用类适配器。在Java等面向对象编程语言中，大部分情况下我们使用的是对象适配器，类适配器较少使用。


##### 双向适配器

双向适配器结构图：
![双向适配器结构图.png](/images/design-pattern/双向适配器结构图.png)

```
class Adapter implements Target,Adaptee {  
    //同时维持对抽象目标类和适配者的引用  
    private Target target;  
    private Adaptee adaptee;  

    public Adapter(Target target) {  
        this.target = target;  
    }  

    public Adapter(Adaptee adaptee) {  
        this.adaptee = adaptee;  
    }  

    public void request() {  
        adaptee.specificRequest();  
    }  

    public void specificRequest() {  
        target.request();  
    }  
}
```

##### 缺省适配器
定义：
> 当不需要实现一个接口所提供的所有方法时，可先设计一个抽象类实现该接口，并为接口中每个方法提供一个默认实现（空方法），那么该抽象类的子类可以选择性地覆盖父类的某些方法来实现需求，它适用于不想使用一个接口中的所有方法的情况，又称为单接口适配器模式。

缺省适配器模式结构图：
![缺省适配器模式结构图.png](/images/design-pattern/缺省适配器模式结构图.png)

在缺省适配器模式中，包含如下三个角色：
+ ServiceInterface（适配者接口）：它是一个接口，通常在该接口中声明了大量的方法。
+ AbstractServiceClass（缺省适配器类）：它是缺省适配器模式的核心类，使用空方法的形式实现了在ServiceInterface接口中声明的方法。通常将它定义为抽象类，因为对它进行实例化没有任何意义。
+ ConcreteServiceClass（具体业务类）：它是缺省适配器类的子类，在没有引入适配器之前，它需要实现适配者接口，因此需要实现在适配者接口中定义的所有方法，而对于一些无须使用的方法也不得不提供空实现。在有了缺省适配器之后，可以直接继承该适配器类，根据需要有选择性地覆盖在适配器类中定义的方法。
