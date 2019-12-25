---
title: 设计模式-命令模式
category: Design-pattern
tags: [设计模式, 行为型模式, 命令模式]
date: 2019-12-25 16:17:20
---

#### 命令模式
定义：
> 将一个请求封装为一个对象，从而让我们可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。
> 命令模式是一种对象行为型模式，其别名为动作(Action)模式或事务(Transaction)模式。
> 

命令模式结构图：
![命令模式结构图.png](/images/design-pattern/命令模式结构图.png)

在命令模式结构图中包含如下几个角色：
+ Command（抽象命令类）：抽象命令类一般是一个抽象类或接口，在其中声明了用于执行请求的execute()等方法，通过这些方法可以调用请求接收者的相关操作。
+ ConcreteCommand（具体命令类）：具体命令类是抽象命令类的子类，实现了在抽象命令类中声明的方法，它对应具体的接收者对象，将接收者对象的动作绑定其中。在实现execute()方法时，将调用接收者对象的相关操作(Action)。
+ Invoker（调用者）：调用者即请求发送者，它通过命令对象来执行请求。一个调用者并不需要在设计时确定其接收者，因此它只与抽象命令类之间存在关联关系。在程序运行时可以将一个具体命令对象注入其中，再调用具体命令对象的execute()方法，从而实现间接调用请求接收者的相关操作。
+ Receiver（接收者）：接收者执行与请求相关的操作，它具体实现对请求的业务处理。

例：
![命令模式1.png](/images/design-pattern/命令模式1.png)

```
import java.util.*;  

//功能键设置窗口类  
class FBSettingWindow {  
    private String title; //窗口标题  
    //定义一个ArrayList来存储所有功能键  
    private ArrayList<FunctionButton> functionButtons = new ArrayList<FunctionButton>();  

    public FBSettingWindow(String title) {  
        this.title = title;  
    }  

    public void setTitle(String title) {  
        this.title = title;  
    }  

    public String getTitle() {  
        return this.title;  
    }  

    public void addFunctionButton(FunctionButton fb) {  
        functionButtons.add(fb);  
    }  

    public void removeFunctionButton(FunctionButton fb) {  
        functionButtons.remove(fb);  
    }  

    //显示窗口及功能键  
    public void display() {  
        System.out.println("显示窗口：" + this.title);  
        System.out.println("显示功能键：");  
        for (Object obj : functionButtons) {  
            System.out.println(((FunctionButton)obj).getName());  
        }  
        System.out.println("------------------------------");  
    }     
}  

//功能键类：请求发送者  
class FunctionButton {  
    private String name; //功能键名称  
    private Command command; //维持一个抽象命令对象的引用  

    public FunctionButton(String name) {  
        this.name = name;  
    }  

    public String getName() {  
        return this.name;  
    }  

    //为功能键注入命令  
    public void setCommand(Command command) {  
        this.command = command;  
    }  

    //发送请求的方法  
    public void onClick() {  
        System.out.print("点击功能键：");  
        command.execute();  
    }  
}  

//抽象命令类  
abstract class Command {  
    public abstract void execute();  
}  

//帮助命令类：具体命令类  
class HelpCommand extends Command {  
    private HelpHandler hhObj; //维持对请求接收者的引用  

    public HelpCommand() {  
        hhObj = new HelpHandler();  
    }  

    //命令执行方法，将调用请求接收者的业务方法  
    public void execute() {  
        hhObj.display();  
    }  
}  

//最小化命令类：具体命令类  
class MinimizeCommand extends Command {  
    private WindowHanlder whObj; //维持对请求接收者的引用  

    public MinimizeCommand() {  
        whObj = new WindowHanlder();  
    }  

//命令执行方法，将调用请求接收者的业务方法  
    public void execute() {  
        whObj.minimize();  
    }  
}  

//窗口处理类：请求接收者  
class WindowHanlder {  
    public void minimize() {  
        System.out.println("将窗口最小化至托盘！");  
    }  
}  

//帮助文档处理类：请求接收者  
class HelpHandler {  
    public void display() {  
        System.out.println("显示帮助文档！");  
    }  
}
```

```
import javax.xml.parsers.*;  
import org.w3c.dom.*;  
import org.xml.sax.SAXException;  
import java.io.*;  

public class XMLUtil {  
//该方法用于从XML配置文件中提取具体类类名，并返回一个实例对象，可以通过参数的不同返回不同类名节点所对应的实例  
    public static Object getBean(int i) {  
        try {  
            //创建文档对象  
            DocumentBuilderFactory dFactory = DocumentBuilderFactory.newInstance();  
            DocumentBuilder builder = dFactory.newDocumentBuilder();  
            Document doc;                             
            doc = builder.parse(new File("config.xml"));   

            //获取包含类名的文本节点  
            NodeList nl = doc.getElementsByTagName("className");  
            Node classNode = null;  
            if (0 == i) {  
                classNode = nl.item(0).getFirstChild();  
            }  
            else {  
                classNode = nl.item(1).getFirstChild();  
            }   

            String cName = classNode.getNodeValue();  

            //通过类名生成实例对象并将其返回  
            Class c = Class.forName(cName);  
            Object obj = c.newInstance();  
            return obj;  
        }     
        catch(Exception e){  
            e.printStackTrace();  
            return null;  
        }  
    }  
}
```
```
<?xml version="1.0"?>  
<config>  
    <className>HelpCommand</className>  
    <className>MinimizeCommand</className>  
</config>  
```

```
class Client {  
    public static void main(String args[]) {  
        FBSettingWindow fbsw = new FBSettingWindow("功能键设置");  

        FunctionButton fb1,fb2;  
        fb1 = new FunctionButton("功能键1");  
        fb2 = new FunctionButton("功能键1");  

        Command command1,command2;  
        //通过读取配置文件和反射生成具体命令对象  
        command1 = (Command)XMLUtil.getBean(0);  
        command2 = (Command)XMLUtil.getBean(1);  

        //将命令对象注入功能键  
        fb1.setCommand(command1);  
        fb2.setCommand(command2);  

        fbsw.addFunctionButton(fb1);  
        fbsw.addFunctionButton(fb2);  
        fbsw.display();  

        //调用功能键的业务方法  
        fb1.onClick();  
        fb2.onClick();  
    }  
}
```

##### 命令队列的实现
```
import java.util.*;  

class CommandQueue {  
    //定义一个ArrayList来存储命令队列  
    private ArrayList<Command> commands = new ArrayList<Command>();  

    public void addCommand(Command command) {  
        commands.add(command);  
    }  

    public void removeCommand(Command command) {  
        commands.remove(command);  
    }  

    //循环调用每一个命令对象的execute()方法  
    public void execute() {  
        for (Object command : commands) {  
            ((Command)command).execute();  
        }  
    }  
}
```
```
class Invoker {  
    private CommandQueue commandQueue; //维持一个CommandQueue对象的引用  

    //构造注入  
    public Invoker(CommandQueue commandQueue) {  
        this. commandQueue = commandQueue;  
    }  

    //设值注入  
    public void setCommandQueue(CommandQueue commandQueue) {  
        this.commandQueue = commandQueue;  
    }  

    //调用CommandQueue类的execute()方法  
    public void call() {  
        commandQueue.execute();  
    }  
}
```
##### 撤销操作的实现
例：
![命令模式2.png](/images/design-pattern/命令模式2.png)

```
//加法类：请求接收者  
class Adder {  
    private int num=0; //定义初始值为0  

    //加法操作，每次将传入的值与num作加法运算，再将结果返回  
    public int add(int value) {  
        num += value;  
        return num;  
    }  
}  

//抽象命令类  
abstract class AbstractCommand {  
    public abstract int execute(int value); //声明命令执行方法execute()  
    public abstract int undo(); //声明撤销方法undo()  
}  

//具体命令类  
class ConcreteCommand extends AbstractCommand {  
    private Adder adder = new Adder();  
    private int value;  

    //实现抽象命令类中声明的execute()方法，调用加法类的加法操作  
public int execute(int value) {  
        this.value=value;  
        return adder.add(value);  
    }  

    //实现抽象命令类中声明的undo()方法，通过加一个相反数来实现加法的逆向操作  
    public int undo() {  
        return adder.add(-value);  
    }  
}  

//计算器界面类：请求发送者  
class CalculatorForm {  
    private AbstractCommand command;  

    public void setCommand(AbstractCommand command) {  
        this.command = command;  
    }  

    //调用命令对象的execute()方法执行运算  
    public void compute(int value) {  
        int i = command.execute(value);  
        System.out.println("执行运算，运算结果为：" + i);  
    }  

    //调用命令对象的undo()方法执行撤销  
    public void undo() {  
        int i = command.undo();  
        System.out.println("执行撤销，运算结果为：" + i);  
    }  
}
```

```
class Client {  
    public static void main(String args[]) {  
        CalculatorForm form = new CalculatorForm();  
        AbstractCommand command;  
        command = new ConcreteCommand();  
        form.setCommand(command); //向发送者注入命令对象  

        form.compute(10);  
        form.compute(5);  
        form.compute(10);  
        form.undo();  
    }  
}
```

##### 请求日志
![命令模式3.png](/images/design-pattern/命令模式3.png)

```
import java.io.*;  
import java.util.*;  

//抽象命令类，由于需要将命令对象写入文件，因此它实现了Serializable接口  
abstract class Command implements Serializable {  
    protected String name; //命令名称  
    protected String args; //命令参数  
    protected ConfigOperator configOperator; //维持对接收者对象的引用  

    public Command(String name) {  
        this.name = name;  
    }  

    public String getName() {  
        return this.name;  
    }  

    public void setName(String name) {  
        this.name = name;  
    }  

    public void setConfigOperator(ConfigOperator configOperator) {  
        this.configOperator = configOperator;  
    }  

    //声明两个抽象的执行方法execute()  
    public abstract void execute(String args);  
    public abstract void execute();  
}  

//增加命令类：具体命令  
class InsertCommand extends Command {  
    public InsertCommand(String name) {  
        super(name);  
    }  

    public void execute(String args) {  
        this.args = args;  
        configOperator.insert(args);  
    }  

    public void execute() {  
        configOperator.insert(this.args);  
    }  
}  

//修改命令类：具体命令  
class ModifyCommand extends Command {  
    public ModifyCommand(String name) {  
        super(name);  
    }  

    public void execute(String args) {  
        this.args = args;  
        configOperator.modify(args);  
    }  

    public void execute() {  
        configOperator.modify(this.args);  
    }  
}  

//省略了删除命令类DeleteCommand  

//配置文件操作类：请求接收者。由于ConfigOperator类的对象是Command的成员对象，它也将随Command对象一起写入文件，因此ConfigOperator也需要实现Serializable接口  
class ConfigOperator implements Serializable {  
    public void insert(String args) {  
        System.out.println("增加新节点：" + args);  
    }  

    public void modify(String args) {  
        System.out.println("修改节点：" + args);  
    }  

    public void delete(String args) {  
        System.out.println("删除节点：" + args);  
    }  
}  

//配置文件设置窗口类：请求发送者  
class ConfigSettingWindow {  
    //定义一个集合来存储每一次操作时的命令对象  
    private ArrayList<Command> commands = new ArrayList<Command>();  
    private Command command;   

    //注入具体命令对象  
    public void setCommand(Command command) {  
        this.command = command;  
    }  

    //执行配置文件修改命令，同时将命令对象添加到命令集合中  
    public void call(String args) {  
        command.execute(args);  
        commands.add(command);  
    }  

    //记录请求日志，生成日志文件，将命令集合写入日志文件  
    public void save() {  
        FileUtil.writeCommands(commands);  
    }  

    //从日志文件中提取命令集合，并循环调用每一个命令对象的execute()方法来实现配置文件的重新设置  
    public void recover() {  
        ArrayList list;  
        list = FileUtil.readCommands();  

        for (Object obj : list) {  
            ((Command)obj).execute();  
        }  
    }  
}  

//工具类：文件操作类  
class FileUtil {  
    //将命令集合写入日志文件  
    public static void writeCommands(ArrayList commands) {  
        try {  
            FileOutputStream file = new FileOutputStream("config.log");  
            //创建对象输出流用于将对象写入到文件中  
            ObjectOutputStream objout = new ObjectOutputStream(new BufferedOutputStream(file));  
            //将对象写入文件  
            objout.writeObject(commands);  
            objout.close();  
            }  
        catch(Exception e) {  
                System.out.println("命令保存失败！");    
                e.printStackTrace();  
            }  
    }  

    //从日志文件中提取命令集合  
    public static ArrayList readCommands() {  
        try {  
            FileInputStream file = new FileInputStream("config.log");  
            //创建对象输入流用于从文件中读取对象  
            ObjectInputStream objin = new ObjectInputStream(new BufferedInputStream(file));  

            //将文件中的对象读出并转换为ArrayList类型  
            ArrayList commands = (ArrayList)objin.readObject();  
            objin.close();  
            return commands;  
            }  
        catch(Exception e) {  
                System.out.println("命令读取失败！");  
                e.printStackTrace();  
                return null;      
            }         
    }  
}
```

```
class Client {  
    public static void main(String args[]) {  
        ConfigSettingWindow csw = new ConfigSettingWindow(); //定义请求发送者  
        Command command; //定义命令对象  
        ConfigOperator co = new ConfigOperator(); //定义请求接收者  

        //四次对配置文件的更改  
        command = new InsertCommand("增加");  
        command.setConfigOperator(co);  
        csw.setCommand(command);  
        csw.call("网站首页");  

        command = new InsertCommand("增加");  
        command.setConfigOperator(co);  
        csw.setCommand(command);  
        csw.call("端口号");  

        command = new ModifyCommand("修改");  
        command.setConfigOperator(co);  
        csw.setCommand(command);  
        csw.call("网站首页");  

        command = new ModifyCommand("修改");  
        command.setConfigOperator(co);  
        csw.setCommand(command);          
        csw.call("端口号");  

        System.out.println("----------------------------");  
        System.out.println("保存配置");  
        csw.save();  

        System.out.println("----------------------------");   
        System.out.println("恢复配置");  
        System.out.println("----------------------------");   
        csw.recover();    
    }  
}
```

##### 宏命令
定义：
>宏命令(Macro Command)又称为组合命令，它是组合模式和命令模式联用的产物。
>宏命令是一个具体命令类，它拥有一个集合属性，在该集合中包含了对其他命令对象的引用。通常宏命令不直接与请求接收者交互，而是通过它的成员来调用接收者的方法。当调用宏命令的execute()方法时，将递归调用它所包含的每个成员命令的execute()方法，一个宏命令的成员可以是简单命令，还可以继续是宏命令。执行一个宏命令将触发多个具体命令的执行，从而实现对命令的批处理，
>

宏命令结构图：
![宏命令结构图.png](/images/design-pattern/宏命令结构图.png)