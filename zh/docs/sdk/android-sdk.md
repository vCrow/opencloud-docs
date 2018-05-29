# Android SDK开发文档

本文档旨在说明如何使用LumiSDK进行网关设备快联入网的开发，仅用于Android手机APP应用。



## 前期准备

1、访问并登录[AIOT开放平台](https://opencloud.aqara.cn/)网站，创建新应用后，在“应用管理”-“应用概览”页面获取“AppId”和“AppKey”。

2、根据[云端开发手册](http://docs.opencloud.aqara.cn/development/cloud-development/#oauth20)中“OAuth 2.0”章节，获取openID。

3、下载并解压[Android SDK](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/sdk/aiot_sdk_fastlink_android_v0.9_beta.zip)，在LHSDKLib文件夹下可找到LumiSDK.aar库。

4、下载并安装Andriod Studio或其他Andriod集成开发环境。



## 使用说明

#### 1、配置Andriod项目。（以Android Studio为例）

1）创建Android项目，在主菜单中选择File->New->New Module->Import .JAR/.AAR Package，选择LumiSDK.aar所在的路径，单击Finish完成。

2）重新编译工程，LumiSDK模块图标会变成带茶杯的文件夹，且自动生成一个.iml文件。

![模块](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/doc-images/zh/sdk/lumisdk.png)

3）在主菜单中选择File->Project Structure->app->Dependencies，单击右上角的绿色加号，选择Module Dependency，把LumiSDK的module添加进去。

![依赖](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/doc-images/zh/sdk/dependencies.png)

4）配置src/main/AndroidManifest.xml，为项目添加联网权限。如不配置，则无法完成授权操作。

```
<uses-permission android:name="android.permission.INTERNET"/>
```



#### 2、网关入网操作说明。

1）手机连接外网，调用授权接口（aiotAuth），进行授权，返回授权结果。

授权接口：**LumiSDK.aiotAuth(appID,appKey,openID)**

| 参数     | 说明               |
| ------ | ---------------- |
| appID  | 第三方应用ID，AppID    |
| appKey | 第三方应用秘钥，AppKey   |
| openID | 通过OAuth授权获得的用户ID |

2）授权成功后，长按网关按钮至指示灯为黄色闪烁，且语音提示”等待连接中“，网关会创建一个以”lumi-gateway-xxxx“开头的热点。

3）手机Wi-Fi连接此网关热点，调用入网连接接口（gatewayFastLink），等待网关语音提示”连接成功“。

> 注意：网关成功入网需要一点时间，请耐心等待30s。

入网连接接口：**LumiSDK.gatewayFastLink(String param)**

参数param采用JSON格式，包含cid, ssid, passwd, uid, lang, positionId, positionType, longitude, latitude。

```
{
  cid: xxxxxxxxxxxxxxxx, 
  ssid：test_123, 
  passwd: 12345678, 
  uid: xxxxxxxxxxxxxxxx, 
  lang: zh-CN, 
  positionId: xxxxxxxxxxxxxxxxxx, 
  positionType: home, 
  longitude: 0, 
  latitude: 0
}
```

详细子参数说明如下表。

| 参数           | 是否必须 | 说明                         |
| ------------ | ---- | -------------------------- |
| cId          | 是    | 手机的唯一标识。若未设置，将无法收到消息推送。    |
| ssid         | 是    | 网关需加入的外网网络Wi-Fi名称          |
| passwd       | 是    | 对应的Wi-Fi密码                 |
| uid          | 是    | 第三方应用的用户id。若未设置，将无法收到消息推送。 |
| lang         | 否    | 语言设置，默认中文zh-CN             |
| positionId   | 否    | 位置ID                       |
| positionType | 否    | 位置类型                       |
| longitude    | 否    | 设备所在位置的经度                  |
| latitude     | 否    | 设备所在位置的纬度                  |



> 注意：1、系统不识别有特殊字符的Wi-Fi，且不支持5G频段的Wi-Fi。若Wi-Fi包含特殊字符，请先更换Wi-Fi名称或更换其他Wi-Fi尝试。
>
> 2、由于SDK中的网络请求是同步的，所以调用时必须在非UI线程中调用，具体可参考DEMO。
>
> 3、调用接口时，可能获得正确或错误的返回结果，可参考[云端开发手册](http://docs.opencloud.aqara.cn/development/cloud-development/#_14)中“返回码说明”章节排查错误。