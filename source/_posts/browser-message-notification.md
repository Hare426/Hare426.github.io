---
title: 浏览器消息提示
category: Front-end
tag: browser-notification
date: 2019-11-26 23:24:20
---
### 浏览器消息提醒

#### 1. 提示框

```

if(window.Notification && Notification.permission !== "denied") {
  Notification.requestPermission(function(status) {
    var n = new Notification('通知标题', { body: '这里是通知内容！' }); 
  });
}

```


![notification.png](/images/front-end/notification.png)

#### 2. 提示音

```
<audio ref="messageTip" :src="require('@/assets/audio/msg_tip.mp3')"></audio>
```

```
this.$refs.messageTip.play();
```