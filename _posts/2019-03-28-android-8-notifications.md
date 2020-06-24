---
layout: post
title: android 8.0及以上版本的通知显示
date: 2019-03-28
tags: android
---
#### 方法来自[这里](https://blog.csdn.net/Mr_Leixiansheng/article/details/84942589)
<!-- more -->

android 8.0以上版本修改了notification的使用方法。如果按如下以前版本的方法调用 `NotificationCompat.Builder`，则会显示已经被弃用：
```java
Notification notification = new NotificationCompat.Builder(context).build();
```
原因在于android 8.0对通知构造器新增加了一个`channelId`参数。

判断当前机器版本是否大于android O，从而决定是否需要创建notification channel。在传参时传入`"default"`即可。另外，android 8.0还要求通知必须实现一个或多个通知渠道（notification channel），才可以显示通知。为了与低版本相兼容，可采用如下写法：
```java
    private void notification() {
        NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
        //8.0 以后需要实现notification channel
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            String channelId = "default";
            String channelName = "默认通知";
            manager.createNotificationChannel(new NotificationChannel(channelId, channelName, NotificationManager.IMPORTANCE_HIGH));
        }

        Intent intent = new Intent(this, NotificationActivity.class);
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, intent, 0);


        Notification notification = new NotificationCompat.Builder(this, "default") //此处传入channelId参数"default"
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon("标题")
                .setContentText("这是内容，点击我可以跳转")
                .setAutoCancel(true)
                .setDefaults(Notification.DEFAULT_ALL)
                .setWhen(System.currentTimeMillis())
                .setContentIntent(pendingIntent)
                .setAutoCancel(true)
                .build();

        manager.notify(1, notification);
    }
```