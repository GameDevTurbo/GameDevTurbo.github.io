# AssetBundle_生成

#### 分组打标签

在Project视图下选择一个资源或文件夹，即可在Inspector面板最下方找到Asset Label面板，点击菜单可以指定该资源所属的AssetBundle名称。

![image-20220713172441316](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/image-20220713172441316.png)　

若要命名一个新的AssetBundle标签，可点击New创建一个，其他情况则可添加到已有的AssetBundle标签下。

通过以上方式，我们可以设置资源打包成AssetBundle的对应关系，未设置的资源将不会包含在任何AssetBundle中。

- AssetBundle的分组标签会自动转为小写。
- 相同的分组标签下可以指定多个资源。
- 如果你创建了一个分组标签，却没有为它指定任何资源，可以使用”Remove Unused Names”移除所有空的AssetBundle标签。



---



#### 通过脚本生成 AssetBundle 资源包

对资源进行分组打标签，我们就可以导出AssetBundle资源包。

这个时候就需要使用官方的API。大致如下：

```c#
public class AssetBundleBuilder
    {
        private const string _assetBundlePath = "Assets/AssetBundles";

        [MenuItem("HotFix/Build AssetBundles")]
        static void DoBuildAllAssetBundles()
        {
            if (!Directory.Exists(_assetBundlePath)) Directory.CreateDirectory(_assetBundlePath);
            BuildPipeline.BuildAssetBundles(_assetBundlePath, GetBuildOptions(), GetBuildTarget());
        }

        private static BuildAssetBundleOptions GetBuildOptions()
        {
            return BuildAssetBundleOptions.None;
        }

        private static BuildTarget GetBuildTarget()
        {
#if UNITY_ANDROID
            return BuildTarget.Android;
#endif
            return BuildTarget.StandaloneWindows;
        }
    }
```

我们只需要提供一个输出AssetBundle的地址即可导出AssetBundles；

而且我们也只能一次性打包所有的AssetBundles；

打包后我么那就可以在输出路径下找到对应的Assetbundle文件。

![image-20220714144258755](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/image-20220714144258755.png)



#### 关于导出的方法和参数

public static AssetBundleManifest BuildAssetBundles(string outputPath, 
    　　BuildAssetBundleOptions assetBundleOptions, BuildTarget targetPlatform);

- **outputPath**：导出路径
- **BuildAssetBundleOptions**：导出选项设置
- **BuildTarget** ：导出的平台



**BuildAssetBundleOptions**

- None ： 使用 LZMA 格式压缩，它是序列化数据文件的单个压缩 LZMA 流。这个方式会让文件尽可能小，但LZMA 压缩要求在使用之前解压缩整个包，加载时间会稍长。  

  解压缩包后，它将使用 LZ4 压缩在磁盘上重新压缩，这不需要在使用包中的资产之前解压缩整个包。  

  由于文件较小，建议仅在从异地主机初始下载 AssetBundle 时使用 LZMA 压缩。  

  [通过UnityWebRequestAssetBundle](https://docs.unity3d.com/ScriptReference/Networking.UnityWebRequestAssetBundle.html)加载的 LZMA 压缩资产包会自动重新压缩为 LZ4 压缩并缓存在本地文件系统上。  

  我们也可以使用[AssetBundle.RecompressAssetBundleAsync](https://docs.unity3d.com/ScriptReference/AssetBundle.RecompressAssetBundleAsync.html) API下载并用不同的格式存储该捆绑包。

- UncompressedAssetBundle ： 不压缩，加载时间快。未压缩的 AssetBundle 是 16 字节对齐的。

- DisableWriteTypeTree ： Assetbundle中不包含Type信息。

- DeterministicAssetBundle ： 使用资源的Hash ID来导出AssetBundle。使用ID可避免资源改名、移动位置等导致重新导出。	

- ForceRebuildAssetBundle ： 强制重新导出。对已有的AssetBundle，在资源没有变化时，Unity不会重新导出。

- IgnoreTypeTreeChanges ： 增量打包时忽略Type信息变化。

- AppendHashToAssetBundleName ： 在AssetBundle名称后添加”_”加上Hash

- ChunkBasedCompression ：LZ4 的压缩方法，这会产生比 LZMA 更大的压缩文件大小，但与 LZMA 不同，它不需要在使用之前解压缩整个捆绑包。LZ4 使用基于块的算法，允许 AssetBundle 以片段或“块”的形式加载。即使 AssetBundle 的其他块未解压缩，解压缩单个块也允许使用包含的资产。

###### 其他

Using`ChunkBasedCompression`具有与未压缩包相当的加载时间，并具有减小磁盘大小的额外好处。

[关于字节对齐](https://zhuanlan.zhihu.com/p/44625744)



#### BuildTarget

AssetBundle在不同平台下是不兼容的，对于不同平台的AssetBundle要分别进行导出。  

BuildTarget用来指定导出平台，例如BuildTarget.iOS。  

可以使用EditorUserBuildSettings.activeBuildTarget，指定为Unity编辑器当前选择的平台。

  

#### Assetbundle变体（Variants）

具有相同 AssetBundle 名称但不同 AssetBundle 变体的 AssetBundle 将具有相同的内部 ID。  

因此可以使用不同变体的 AssetBundle 任意切换它们。

例如可以根据语言种类打包 UI 贴图，使用 AssetBundle Variants 机制将资源打包成中文、英文与日文版本等三个 AssetBundles，游戏过程中，根据玩家语言设定载入对应语言包的 AssetBundles，让游戏 UI 自动使用不同语言版本的贴图。

#### TypeTree

Manifeset里面还有一些如Class ID的字样，指的是AssetBundle的TypeTree。关于TypeTree的资料非常少，可以知道它记录了一个Class ID，Class ID对应的Class可以在这里找到，Unity利用Class ID来序列化或反序列化一个类，这份信息也可以包含在引擎里，所以AssetBundle导出时有一个选项可以不导出这部分，可以轻微减少包大小和提高加载速度。



---



#### 普通 AssetBundle 的结构

![img](https://raw.githubusercontent.com/GameDevTurbo/ResHub/main/picGoGoGo/AssetBundles-Building-0.png)

----



#### 清单文件

对于生成的每个包，包括附加的清单包，都会生成一个关联的清单文件。清单文件可以使用任何文本编辑器打开，并包含诸如循环冗余校验 (CRC) 数据和**依赖**捆绑包的数据。

###### 普通的 AssetBundle 清单

```
ManifestFileVersion: 0
CRC: 2422268106
Hashes:
  AssetFileHash:
    serializedVersion: 2
    Hash: 8b6db55a2344f068cf8a9be0a662ba15
  TypeTreeHash:
    serializedVersion: 2
    Hash: 37ad974993dbaa77485dd2a0c38f347a
HashAppended: 0
ClassTypes:
- Class: 91
  Script: {instanceID: 0}
Assets:
  Asset_0: Assets/Mecanim/StateMachine.controller
Dependencies: {}
```

其中显示了包含的资产、依赖项和其他信息。



##### Assetbundle主清单

```
ManifestFileVersion: 0
AssetBundleManifest:
  AssetBundleInfos:
    Info_0:
      Name: scene1assetbundle
      Dependencies: {}
```

这将显示 AssetBundles 如何关联以及它们的依赖关系是什么。



---



#### 分组Tips

无论您采用何种策略，以下是一些需要牢记的额外提示：

- 将频繁更新的对象拆分为与很少更改的对象分开的 AssetBundle
- 对可能同时加载的对象进行分组。例如模型、它的纹理和它的动画
- 如果您注意到跨多个 AssetBundle 的多个对象依赖于来自完全不同 AssetBundle 的单个资产，请将依赖项移至单独的 AssetBundle。如果多个 AssetBundle 引用其他 AssetBundle 中的同一组资产，则可能值得将这些依赖项拉入共享 AssetBundle 以减少重复。
- 如果不可能同时加载两组对象，例如标准和高清资产，请确保它们位于各自的 AssetBundle 中。
- 如果少于 50% 的捆绑包经常同时加载，请考虑拆分 AssetBundle
- 考虑组合较小（少于 5 到 10 个资产）但内容经常同时加载的 AssetBundle
- 如果一组对象只是同一对象的不同版本，请考虑 AssetBundle Variants



---

```C#
/// <summary>
    /// 查看所有的Assetbundle名称（设置Assetbundle Name的对象）
    /// </summary>
    [MenuItem("AssetBundle/Get AssetBundle names")]
    static void GetNames()
    {
        var names = AssetDatabase.GetAllAssetBundleNames(); //获取所有设置的AssetBundle
        foreach (var name in names)
            Debug.Log("AssetBundle: " + name);
    }
```



