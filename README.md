# Elastos.NET.Carrier.Android.Demo
## 概述

本Demo介绍了如何用亦来云的Carrier实现android手机间的通信。
实现了简易版的聊天的Dapp。

## 加载库文件
把如下地址的Elastos.NET.Carrier.Android.SDK加载到app的lib路径下

```
https://github.com/elastos/Elastos.NET.Carrier.Android.SDK
```

加载后的目录结构如下:
```
localhost:libs xuxinlai$ pwd
/Users/xuxinlai/blockchain/Elastos.NET.Carrier.Android.Demo/app/libs
localhost:libs xuxinlai$ tree
.
├── arm64-v8a
│   └── libcarrierjni.so
├── armeabi
│   └── libcarrierjni.so
├── armeabi-v7a
│   └── libcarrierjni.so
└── elacarrier.jar
```

## 配置和加载ndk

如果没有安装ndk的需要安装ndk，并且在build.gradle文件中加入如下配置

```
ndk {
    moduleName "carrierjni"
    ldLibs "log"
    abiFilters "armeabi"
}

sourceSets {
    main {
        jniLibs.srcDirs = ['libs']
    }
}
```

## 初始化实例
通过初始化Carrier实例获得用户地址和ID
```
//1.初始化实例，获得相关信息
try {
   //1.1获得Carrier的实例
   carrierInst = Carrier.getInstance(options, handler);

   //1.2获得Carrier的地址
   carrierAddr = carrierInst.getAddress();
   Log.i(TAG,"address: " + carrierAddr);

   //1.3获得Carrier的用户ID
   carrierUserID = carrierInst.getUserId();
   Log.i(TAG,"userID: " + carrierUserID);

   //1.4启动网络
   carrierInst.start(1000);
   handler.synch.await();
   Log.i(TAG,"carrier client is ready now");

} catch (ElastosException e) {
    e.printStackTrace();
}
```

**注意不要忘记设置手机文件权限**


```
<uses-permission android:name="android.permission.INTERNET"/>
```

## 加好友

通过addFriend增加好友，
```
//2.1添加好友
try {
    Log.i(TAG,"start add Frind");
    carrierInst.addFriend(friendAddr, "auto-accepted");
    Log.i(TAG,"end add Frind");
}catch (ElastosException e) {
    e.printStackTrace();
}
```

通过onFriendRequest回调函数,通过好友验证
```
//2.2 通过好友验证
public void onFriendRequest(Carrier carrier, String userId, UserInfo info, String hello) {
    try {

        if (hello.equals("auto-accepted")) {
            carrier.AcceptFriend(userId);
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

## 发送消息
通过sendFriendMessage给好友发消息
```
//3.1发送信息
try {
    Log.i(TAG,"start send message");
    carrierInst.sendFriendMessage("3uHutUcGb9Sc2VX87bwxuoejX7oDFRc2FuUkyYHxbNpG", "hello e");
    Log.i(TAG,"end send message");
}catch (ElastosException e) {
    e.printStackTrace();
}
```

通过onFriendMessage回调函数,获得好友发送过来的信息
```
//3.2 接受好友信息
public void onFriendMessage(Carrier carrier,String fromId, String message) {

    Log.i(CALLBACK,"address:" + fromId + "connection changed to: " + message);
}        
```
        