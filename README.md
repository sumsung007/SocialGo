# 社会化工具库

[![License](https://img.shields.io/badge/License%20-Apache%202-337ab7.svg)](https://www.apache.org/licenses/LICENSE-2.0) [![JCenter](https://img.shields.io/badge/Jcenter-1.1.1-brightgreen.svg)](http://jcenter.bintray.com/com/gentriolee/socialgo/)

第三方分享和登录，支持微信、QQ、微博、钉钉

## 支持情况

| 第三方     |  授权  |    登录     |
| :-------  | :---- |:-----------|
|     微信    | :ballot_box_with_check: | :ballot_box_with_check: |
|     QQ     | :ballot_box_with_check: | :ballot_box_with_check: |
|     微博 | :ballot_box_with_check: | :ballot_box_with_check: |
|     钉钉  | :ballot_box_with_check: | :ballot_box_with_check: |

| 第三方     |  分享文字  |    分享图片   |    分享链接   |    分享小程序   | 唤起小程序 |
| :-------  | :---- |:-----------|:-----------|:-------------| :-------  |
|   微信    | :ballot_box_with_check: | :ballot_box_with_check: |:ballot_box_with_check:|:ballot_box_with_check:| :ballot_box_with_check: |
| 朋友圈 | :ballot_box_with_check: | :ballot_box_with_check: |:ballot_box_with_check:||  |
|     QQ     | :ballot_box_with_check: 无回调 | :ballot_box_with_check: |:ballot_box_with_check:||  |
| QQ空间 | :ballot_box_with_check: | :ballot_box_with_check: |:ballot_box_with_check:||  |
|     微博 | :ballot_box_with_check: | :ballot_box_with_check: |:ballot_box_with_check:||  |
|     钉钉  | :ballot_box_with_check: | :ballot_box_with_check: |:ballot_box_with_check:||  |

> ### :exclamation: Tips
>
> * 微信分享回调 不再返回用户是否分享完成事件，cancel事件和success事件统一为success事件。[微信官方分享功能调整公告](https://mp.weixin.qq.com/cgi-bin/announce?action=getannouncement&announce_id=11526372695t90Dn&version=&lang=zh_CN&scene=21#wechat_redirect)
> * QQ原生SDK不支持纯文本分享，本库通过指定Activity跳转实现，故无分享回调。

## 工程结构

👉️ `socialgo` 第三方社会化库的配置中心

👉️ `sharego` 分享核心

👉️ `authgo` 授权登录核心

👉️ `sample` 例子

## 使用

### Gradle Dependency

```gradle
implementation 'com.gentriolee.socialgo:sharego:1.1.1' //分享
implementation 'com.gentriolee.socialgo:authgo:1.1.1'   //授权&登录
```

### AndroidManifest.xml配置

```xml
<!--QQ配置开始-->
<activity
    android:name="com.tencent.tauth.AuthActivity"
    android:launchMode="singleTask"
    android:noHistory="true">
    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>

        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="android.intent.category.BROWSABLE"/>
        <!-- tencent + qqId -->
        <data android:scheme="tencent*********"/>
    </intent-filter>
</activity>
<activity
    android:name="com.tencent.connect.common.AssistActivity"
    android:configChanges="orientation|keyboardHidden|screenSize"
    android:theme="@android:style/Theme.Translucent.NoTitleBar"/>
<!--QQ配置结束-->

<!-- 钉钉分享授权配置开始（该Activity自行创建放在当前工程的包下） -->
<activity
    android:name=".ddshare.DDShareActivity"
    android:exported="true"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />
<!-- 钉钉分享授权配置结束 -->


<!-- 微信分享授权配置开始（该Activity自行创建放在当前工程的包下） -->
<activity
    android:name=".wxapi.WXEntryActivity"
    android:configChanges="keyboardHidden|orientation|screenSize"
    android:exported="true"
    android:launchMode="singleTask"
    android:theme="@android:style/Theme.Translucent.NoTitleBar" />
<!-- 微信分享授权配置结束 --> 
```

### 工具类初始化

推荐在Application生命周期中初始化

```java
SocialConfig.Builder builder = new SocialConfig.Builder()
                .setQqAppId("")
                .setWxAppId("", "")
                .setWbAppId("", "")
                .setDdAppId("", "")
                .build();
SocialConfig.init(getApplicationContext(), builder);
```

### 回调配置

#### 钉钉的回调在packageName.ddshare.DDShareActivity中配置

```java
@Override
public void onReq(BaseReq baseReq) {
    SocialGo.onDDAPIHandlerReq(baseReq);
}

@Override
public void onResp(BaseResp baseResp) {
    SocialGo.onDDAPIHandlerResp(baseResp);
    finish();
}
```

#### 微信的回调在packageName.wxapi.WXEntryActivity

```java
@Override
public void onReq(BaseReq baseReq) {
    SocialGo.onWXAPIHandlerReq(baseReq);
}

@Override
public void onResp(BaseResp baseResp) {
    SocialGo.onWXAPIHandlerResp(baseResp);
    finish();
}
```

#### QQ回调在调用的Activity中重写onActivityResult()

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    SocialGo.onActivityResult(requestCode, resultCode, data);
    super.onActivityResult(requestCode, resultCode, data);
}
```

#### 微博回调在调用的Activity中重写onActivityResult()和onNewIntent()

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    SocialGo.onActivityResult(requestCode, resultCode, data);
    super.onActivityResult(requestCode, resultCode, data);
}

@Override
protected void onNewIntent(Intent intent) {
    SocialGo.onNewIntent(intent);
    super.onNewIntent(intent);
}
```

## 调用

### 分享

```java
ShareGo.getInstance().share();
ShareGo.getInstance().shareWX()；
ShareGo.getInstance().shareQQ();
ShareGo.getInstance().shareWB();
ShareGo.getInstance().shareDD();

ShareGo.getInstance().launchWX();
```
### 授权

```java
AuthGo.getInstance().auth();
AuthGo.getInstance().authWX();
AuthGo.getInstance().authQQ();
AuthGo.getInstance().authWB();
AuthGo.getInstance().authDD();
```

### 登录
```java
AuthGo.getInstance().login();
AuthGo.getInstance().loginWX();
AuthGo.getInstance().loginQQ();
AuthGo.getInstance().loginWB();
AuthGo.getInstance().loginDD();
```

License
-------

    Copyright 2018 gentrio
    
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
       http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
