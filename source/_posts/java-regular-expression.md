---
title: Java正则表达式
category: Back-end
tags: [Java, 正则表达式]
date: 2019-12-09 14:07:20
---
### Java正则表达式

#### Matcher类常用方法介绍

##### boolean matches()
> 尝试对整个目标字符展开匹配检测,也就是只有整个目标字符串完全匹配时才返回true
> 

```
String regEx = "\\d{18}";
String str = "123456789012345678";
Pattern pattern = Pattern.compile(regEx);
Matcher matcher = pattern.matcher(str);
boolean matches = matcher.matches(); //true
```

##### boolean lookingAt()
> 对前面的字符串进行匹配,只有匹配到的字符串在最前面才会返回true
> 

```
String regEx = "\\d+";
String str1 = "123aaaaa";
String str2 = "aaaaa123";
Pattern pattern = Pattern.compile(regEx);
Matcher matcher = pattern.matcher(str1);
boolean matches = matcher.lookingAt(); //true
matcher = pattern.matcher(str2);
matches = matcher.lookingAt(); //false
```

##### boolean find()
> 对字符串进行匹配,匹配到的字符串可以在任何位置
> 

```
String regEx = "\\d+";
String str1 = "123aaaaa";
String str2 = "aaaaa123";
String str3 = "aaaaaaaa";
Pattern pattern = Pattern.compile(regEx);
Matcher matcher = pattern.matcher(str1);
boolean matches = matcher.find(); //true
matcher = pattern.matcher(str2);
matches = matcher.find(); //true
matcher = pattern.matcher(str3);
matches = matcher.find(); //false
```

##### other
> + int start() 返回当前匹配到的字符串在原目标字符串中的位置
> + int end() 返回当前匹配的字符串的最后一个字符在原目标字符串中的索引位置
> + String group() 返回匹配到的子字符串
> 

```
String regEx = "\\d+";
String str1 = "aaaaa123";
Pattern pattern = Pattern.compile(regEx);
Matcher matcher = pattern.matcher(str1);
boolean matches = matcher.find(); //true
int start = matcher.start();  //5
String group = matcher.group();//123
int end = m.end();//8
```