---
title: JSONString To HashMap
category: Back-end
tags: [Java, Basic]
date: 2019-11-10 22:59:50
---

**Description:**
&nbsp;&nbsp;&nbsp;&nbsp;Converting an JSONString into an HashMap

```
HashMap<String,String> params = JSONObject.parseObject(jsonData,newTypeReference<HashMap<String,String>>(){});
```