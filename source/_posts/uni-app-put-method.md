---
title: uni-app PUT method
category: uni-app
tag: Basic
date: 2019-09-09 22:15:20
---

**问题：前端传值后端接收到的均是null**
**解决方法：**
> 前端:
> * 设置header['content-type'] 为 'application/x-www-form-urlencoded'
> * 根据uni-app API文档，content-type 为 application/x-www-form-urlencoded 的数据，会将数据转换为 query string

```
let url = '/targetUrl';
let model = {
  a:'a',
  b:'b',
  c:'c'
}
uni.request({
  url: url,
	method:'PUT',
	data: model,
	header: {
		'content-type': "application/x-www-form-urlencoded",
	},
	success: (res) => {
		console.log(res.data);
	  uni.showToast({title:"修改成功！",icon:''});
	}
});	
```

> 后端：
>*  Model实体具有属性 a,b,c

```
@PutMapping('/targetUrl')
@ResponstBody
@ApiOperation('更新操作')
Public ResponseBo updateModel (Model model){
  return ResponseBo.ok("message");
}
```