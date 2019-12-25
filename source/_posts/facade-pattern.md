---
title: 设计模式-外观模式
category: Design-pattern
tags: [设计模式, 结构型模式, 外观模式]
date: 2019-12-25 10:09:20
---

#### 外观模式
定义：
> 为子系统中的一组接口提供一个统一的入口。外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。
> 外观模式又称为门面模式，它是一种对象结构型模式。
> 

外观模式是迪米特法则的一种具体实现，通过引入一个新的外观角色可以降低原有系统的复杂度，同时降低客户类与子系统的耦合度。

外观模式结构图：
![外观模式结构图.png](/images/design-pattern/外观模式结构图.png)

外观模式包含如下两个角色：
+ Facade（外观角色）：在客户端可以调用它的方法，在外观角色中可以知道相关的（一个或者多个）子系统的功能和责任；在正常情况下，它将所有从客户端发来的请求委派到相应的子系统去，传递给相应的子系统对象处理。
+ SubSystem（子系统角色）：在软件系统中可以有一个或者多个子系统角色，每一个子系统可以不是一个单独的类，而是一个类的集合，它实现子系统的功能；每一个子系统都可以被客户端直接调用，或者被外观角色调用，它处理由外观类传过来的请求；子系统并不知道外观的存在，对于子系统而言，外观角色仅仅是另外一个客户端而已。

例：
![外观模式1.png](/images/design-pattern/外观模式1.png)

```
//FileReader.cs  
using System;  
using System.Text;  
using System.IO;  

namespace FacadeSample  
{  
    class FileReader  
    {  
        public string Read(string fileNameSrc)   
        {  
       Console.Write("读取文件，获取明文：");  
            FileStream fs = null;  
            StringBuilder sb = new StringBuilder();  
       try  
            {  
                fs = new FileStream(fileNameSrc, FileMode.Open);  
                int data;  
               while((data = fs.ReadByte())!= -1)   
                {  
            sb = sb.Append((char)data);  
               }  
               fs.Close();  
               Console.WriteLine(sb.ToString());  
       }  
       catch(FileNotFoundException e)   
            {  
           Console.WriteLine("文件不存在！");  
       }  
       catch(IOException e)   
            {  
           Console.WriteLine("文件操作错误！");  
       }  
       return sb.ToString();  
        }  
    }  
}
```
```
//CipherMachine.cs  
using System;  
using System.Text;  

namespace FacadeSample  
{  
    class CipherMachine  
    {  
       public string Encrypt(string plainText)   
       {  
       Console.Write("数据加密，将明文转换为密文：");  
       string es = "";  
            char[] chars = plainText.ToCharArray();  
       foreach(char ch in chars)   
            {  
                string c = (ch % 7).ToString();  
           es += c;  
       }  
            Console.WriteLine(es);  
       return es;  
    }  
    }  
}
```

```
//FileWriter.cs  
using System;  
using System.IO;  
using System.Text;  

namespace FacadeSample  
{  
    class FileWriter  
    {  
        public void Write(string encryptStr,string fileNameDes)   
        {  
        Console.WriteLine("保存密文，写入文件。");  
            FileStream fs = null;  
        try  
            {  
               fs = new FileStream(fileNameDes, FileMode.Create);  
               byte[] str = Encoding.Default.GetBytes(encryptStr);  
               fs.Write(str,0,str.Length);  
               fs.Flush();  
               fs.Close();  
       }      
       catch(FileNotFoundException e)   
            {  
        Console.WriteLine("文件不存在！");  
       }  
       catch(IOException e)   
            {  
                Console.WriteLine(e.Message);  
           Console.WriteLine("文件操作错误！");  
       }          
        }  
    }  
}
```
```
// EncryptFacade.cs  
namespace FacadeSample  
{  
    class EncryptFacade  
    {  
        //维持对其他对象的引用  
        private FileReader reader;  
        private CipherMachine cipher;  
        private FileWriter writer;  

        public EncryptFacade()  
        {  
            reader = new FileReader();  
            cipher = new CipherMachine();  
            writer = new FileWriter();  
        }  

        //调用其他对象的业务方法  
         public void FileEncrypt(string fileNameSrc, string fileNameDes)  
        {  
            string plainStr = reader.Read(fileNameSrc);  
            string encryptStr = cipher.Encrypt(plainStr);  
            writer.Write(encryptStr, fileNameDes);  
        }  
    }  
}
```
```
//Program.cs  
using System;  

namespace FacadeSample  
{  
    class Program  
    {  
        static void Main(string[] args)  
        {  
            EncryptFacade ef = new EncryptFacade();  
            ef.FileEncrypt("src.txt", "des.txt");  
            Console.Read();  
        }  
    }  
}
```

优化方案：
![外观模式2.png](/images/design-pattern/外观模式2.png)