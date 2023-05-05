#### [**Unity目录结构**](http://www.cnblogs.com/liudq/p/5540051.html)

**Unity中有几个默认目录 Unity5.x**

**Resources**

项目中默认的资源路径，会直接打包到游戏包中。即使没有被使用过的资源，放在该目录下也会被打包。

**Plugins**

插件目录，该目录在编译项目时，会优先编译，方便项目中代码调用。它与Standard Assets目录的代码共同编译到同一个.sln里。    构建 Android 与 ios 项目时，该目录下会存放相应的配置文件等资源。

**Editor**

该目录下的代码可调用Unity Editor 的API，存放扩展编辑器的代码。编译时不会被打包到游戏包中。

**Standard Assets**

该目录下的代码优先编译，方便项目中代码调用。它与Plugins目录的代码共同编译到同一个.sln里。

**StreamingsAssets**

该目录下的文件会在打包时打包到项目中，与Resources一样，没有被使用过的资源也会被打包。

**Resources 与 StreamingAssets 区别**

Resources目录下的文件在打包时会进行压缩与加密，但StreamingAssets下的文件是直接打包到包中的。 Resources目录下的材质球、预制件等资源，会在打包时自动寻找引用资源，打包到Resources中。 两者都是只读文件。

**Unity中路径获取API**

**Application.dataPath**

项目根目录  ../../../Assets  注意在移动设备下无法访问。

**Application.persistentDataPath**

该路径下的文件可读写。

**Application.streamingAssetsPath**

访问项目目录中Streaming Assets的路径。

**Application.temporaryCachePath**

该路径下的文件可读写。临时数据，与presistentDataPath的区别只知道是IOS上presistentDataPath的数据会被自动备份到iCloud上。

**各平台下路径API对应具体路径值（转）**

IOS:    Application.dataPath : Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/xxx.app/Data    Application.streamingAssetsPath : Application/xxxxxxxx-xxxx-xxxx-xxxxxxx/xxx.app/Data/Raw    Application.persistentDataPath : Application/xxxxxxxx-xxxx-xxxx-xxxxxxxxx/Documents    Application.temporaryCachePath : Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxx/Library/Caches Android:    Application.dataPath : /data/app/xxx.xxx.xxx.apk    Application.streamingAssetsPath : jar:file:///data/app/xxx.xxx.xxx.apk/!/assets    Application.persistentDataPath : /data/data/xxx.xxx.xxx/files    Application.temporaryCachePath : /data/data/xxx.xxx.xxx/cache



**关于StreamingAssetPath**

```csharp
string path =
#if UNITY_ANDROID && !UNITY_EDITOR
        Application.streamingAssetsPath; 
        //or 可以写成 "jar:file://" + Application.dataPath + "!/assets";
       //安卓Application.streamingAssetsPath已默认有"file://"
#elif UNITY_IOS && !UNITY_EDITOR
        "file://" + Application.streamingAssetsPath; 
         //or 写成： "file://" + Application.dataPath +"/Raw";
#elif UNITY_STANDLONE_WIN || UNITY_EDITOR
        "file://" + Application.streamingAssetsPath;
           // or 写成： "file://" + Application.dataPath + "/StreamingAssets";
#else
        string.Empty;
#endif
```