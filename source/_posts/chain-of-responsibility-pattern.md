---
title: 设计模式-职责链模式
category: Design-pattern
tags: [设计模式, 行为型模式, 职责链模式]
date: 2019-12-25 14:37:20
---
#### 职责链模式
定义：
> 避免请求发送者与接收者耦合在一起，让多个对象都有可能接收请求，将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。
> 职责链模式是一种对象行为型模式。
> 

职责链模式结构图：
![职责链模式结构图.png](/images/design-pattern/职责链模式结构图.png)

在职责链模式结构图中包含如下几个角色：
+ Handler（抽象处理者）：它定义了一个处理请求的接口，一般设计为抽象类，由于不同的具体处理者处理请求的方式不同，因此在其中定义了抽象请求处理方法。因为每一个处理者的下家还是一个处理者，因此在抽象处理者中定义了一个抽象处理者类型的对象（如结构图中的successor），作为其对下家的引用。通过该引用，处理者可以连成一条链。
+ ConcreteHandler（具体处理者）：它是抽象处理者的子类，可以处理用户请求，在具体处理者类中实现了抽象处理者中定义的抽象请求处理方法，在处理请求之前需要进行判断，看是否有相应的处理权限，如果可以处理请求就处理它，否则将请求转发给后继者；在具体处理者中可以访问链中下一个对象，以便请求的转发。

例：
![职责链模式1.png](/images/design-pattern/职责链模式1.png)

```
//采购单：请求类  
class PurchaseRequest {  
    private double amount;  //采购金额  
    private int number;  //采购单编号  
    private String purpose;  //采购目的  

    public PurchaseRequest(double amount, int number, String purpose) {  
        this.amount = amount;  
        this.number = number;  
        this.purpose = purpose;  
    }  

    public void setAmount(double amount) {  
        this.amount = amount;  
    }  

    public double getAmount() {  
        return this.amount;  
    }  

    public void setNumber(int number) {  
        this.number = number;  
    }  

    public int getNumber() {  
        return this.number;  
    }  

    public void setPurpose(String purpose) {  
        this.purpose = purpose;  
    }  

    public String getPurpose() {  
        return this.purpose;  
    }  
}  

//审批者类：抽象处理者  
abstract class Approver {  
    protected Approver successor; //定义后继对象  
    protected String name; //审批者姓名  

    public Approver(String name) {  
        this.name = name;  
    }  

    //设置后继者  
    public void setSuccessor(Approver successor) {   
        this.successor = successor;  
    }  

    //抽象请求处理方法  
    public abstract void processRequest(PurchaseRequest request);  
}  

//主任类：具体处理者  
class Director extends Approver {  
    public Director(String name) {  
        super(name);  
    }  

    //具体请求处理方法  
    public void processRequest(PurchaseRequest request) {  
        if (request.getAmount() < 50000) {  
            System.out.println("主任" + this.name + "审批采购单：" + request.getNumber() + "，金额：" + request.getAmount() + "元，采购目的：" + request.getPurpose() + "。");  //处理请求  
        }  
        else {  
            this.successor.processRequest(request);  //转发请求  
        }     
    }  
}  

//副董事长类：具体处理者  
class VicePresident extends Approver {  
    public VicePresident(String name) {  
        super(name);  
    }  

    //具体请求处理方法  
    public void processRequest(PurchaseRequest request) {  
        if (request.getAmount() < 100000) {  
            System.out.println("副董事长" + this.name + "审批采购单：" + request.getNumber() + "，金额：" + request.getAmount() + "元，采购目的：" + request.getPurpose() + "。");  //处理请求  
        }  
        else {  
            this.successor.processRequest(request);  //转发请求  
        }     
    }  
}  

//董事长类：具体处理者  
class President extends Approver {  
    public President(String name) {  
        super(name);  
    }  

    //具体请求处理方法  
    public void processRequest(PurchaseRequest request) {  
        if (request.getAmount() < 500000) {  
            System.out.println("董事长" + this.name + "审批采购单：" + request.getNumber() + "，金额：" + request.getAmount() + "元，采购目的：" + request.getPurpose() + "。");  //处理请求  
        }  
        else {  
            this.successor.processRequest(request);  //转发请求  
        }  
    }  
}  

//董事会类：具体处理者  
class Congress extends Approver {  
    public Congress(String name) {  
        super(name);  
    }  

    //具体请求处理方法  
    public void processRequest(PurchaseRequest request) {  
        System.out.println("召开董事会审批采购单：" + request.getNumber() + "，金额：" + request.getAmount() + "元，采购目的：" + request.getPurpose() + "。");        //处理请求  
    }      
}
```

```
class Client {  
    public static void main(String[] args) {  
        Approver wjzhang,gyang,jguo,meeting;  
        wjzhang = new Director("张无忌");  
        gyang = new VicePresident("杨过");  
        jguo = new President("郭靖");  
        meeting = new Congress("董事会");  

        //创建职责链  
        wjzhang.setSuccessor(gyang);  
        gyang.setSuccessor(jguo);  
        jguo.setSuccessor(meeting);  

        //创建采购单  
        PurchaseRequest pr1 = new PurchaseRequest(45000,10001,"购买倚天剑");  
        wjzhang.processRequest(pr1);  

        PurchaseRequest pr2 = new PurchaseRequest(60000,10002,"购买《葵花宝典》");  
        wjzhang.processRequest(pr2);  

        PurchaseRequest pr3 = new PurchaseRequest(160000,10003,"购买《金刚经》");  
        wjzhang.processRequest(pr3);  

        PurchaseRequest pr4 = new PurchaseRequest(800000,10004,"购买桃花岛");  
        wjzhang.processRequest(pr4);  
    }  
}
```

##### 纯的职责链模式
一个纯的职责链模式要求一个具体处理者对象只能在两个行为中选择一个：要么承担全部责任，要么将责任推给下家，不允许出现某一个具体处理者对象在承担了一部分或全部责任后又将责任向下传递的情况。而且在纯的职责链模式中，要求一个请求必须被某一个处理者对象所接收，不能出现某个请求未被任何一个处理者对象处理的情况。在前面的采购单审批实例中应用的是纯的职责链模式。
##### 不纯的职责链模式
在一个不纯的职责链模式中允许某个请求被一个具体处理者部分处理后再向下传递，或者一个具体处理者处理完某请求后其后继处理者可以继续处理该请求，而且一个请求可以最终不被任何处理者对象所接收。