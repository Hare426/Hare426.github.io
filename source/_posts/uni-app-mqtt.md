---
title: uni-app MQTT
category: uni-app
tag: MQTT
date: 2019-09-09 21:53:20
---

### 背景
1. 使用uni-app 打包 app；
2. 适用很多版本 mqtt 支持H5端，但是app端并不支持；

### 资料
1. mqttws31.js
> * mqttws31.js 其实是 paho-mqtt 的早期版本；
> * .vue 无法直接引入  mqttws31.js，所以对 mqttws31.js进行了一些修改；
> * mqttws31.js 除了网上常见的版本外，git上还有一个自动重连的分支版本，这里使用的就是这个分支版本；
> * 文档地址：<https://github.com/eclipse/paho.mqtt.javascript/blob/automatic-reconnect/src/mqttws31.js>
2. plus-websocket
> * plus-websocket 是 uni-app的websocket通讯部分的一个插件；
> * 文档地址： [plus-websocket - DCloud 插件市场](https://ext.dcloud.net.cn/plugin?id=647)

### 说明
> 在原有mqttws31.js的基础上，进行如下修改：
> 1. 支持 .vue文件引用；
> 2. storage部分，使用uniapp封装的storage；
> 3. WebSocket通讯部分，使用uniapp的plus-websocket插件；

### 撸代码
1. chat.js 
```
// 引入修改过的 mqttws31-auto.js 文件
let $mqtt = require('@/utils/js/mqttws31-auto.js');

// paho-mqtt connenctOptions 连接配置项
let options = {
	timeout: 1000 * 1000,
	userName: 'Re', 
	password: 'pass',
	useSSL: true, //如果使用 HTTPS 加密则配置为 true
	keepAliveInterval: 10, //心跳
	mqttVersion: 4, //默认为4
	onSuccess: function onConnect() { //success callback function
		console.log('连接成功');
	},
	onFailure: function(message) { //failure callback function
		console.log('连接失败', JSON.stringify(message));
	}
};

let clientId = Math.floor(Math.random()*10000) + "";

let url = 'youselfUrl';
let port = 12345;

/* mqtt.Client()的构造方法其实是4个参数
 * url 域名或IP string
 * port 端口 number
 * path 默认是'/mqtt',可以不进行配置 string
 * clientId 这里采用了一个随机数，不能重复 number
 */
let client = new $mqtt.Client(url, port, clientId);

// 当连接丢失/断开时的 callback function
client.onConnectionLost = function onConnectionLost(response) {
	console.log('onConnectionLost',JSON.stringify(response));
};
// 接收到消息 callback function
client.onMessageArrived = function onMessageArrived(message) {
	let payload = message.payloadString;
	let chat = JSON.parse(payload.toString());
	console.log('收到来自', chat.source, '的消息', payload.toString())
	addMessage('home',JSON.parse(payload).content,false)
	saveMsg();
	// console.log('收到保存之后messageAll'+JSON.stringify(messageAll))
};

let messageAll = [];
// business condition 获取缓存
/* uni.getStorage({
	key: 'storage_msg',
	success: function (res) {
		console.log(res.data)
		 messageAll= res.data;
	}
}); */

// business condition 用于页面显示
function addMessage(user, content, hasSub, subcontent) {
	messageAll.push({
		user: user,
		content: content,
		hasSub: hasSub,
		subcontent: subcontent
	});
	// console.log(messageAll)
}

// business condition 写入缓存
function saveMsg(){
	// uni.setStorage({
	// 	key: 'storage_msg',
	// 	data: messageAll,
	// 	success: function () {
	// 		console.log('消息已存储');
	// 		// console.log(messageAll)
	// 	}
	// });
}

export {
	client,
	options,
	messageAll,
	addMessage,
	saveMsg
}
```

2. chat.vue
```
// 引用
var $mqtt = require("@/utils/js/mqttws31-auto.js");

created() {
      //获取连接
			delete this.mqtt.options.mqttVersionExplicit;
			this.mqtt.client.connect(this.mqtt.options);
		},
    
methods:{
      // 发送消息
      sendMessage: function (info) {
				var message = new $mqtt.Message(JSON.stringify(
				    {type: 0x10, target: "To", content:  info, extend: "123"}));
					message.qos =1;
					message.destinationName = 'sendMessage';
				this.mqtt.client.send(message);	
			},
}

```

### 效果展示
![uniapp-mqtt.png](/images/uniapp-mqtt.png)



### demo code
[GitHub - Hare426/uni-app-mqtt-demo: uni-app mqtt app 消息通讯 demo](https://github.com/Hare426/uni-app-mqtt-demo.git)