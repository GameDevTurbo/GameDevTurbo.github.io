# AssetBundle_简介

**AssetBundle** 是一个存档文件，包含可在运行时由 Unity 加载的特定于平台的非代码资源（比如模型、纹理、预制件、音频剪辑甚至整个场景）。

一个 AssetBundle 中的材质可以引用另一个 AssetBundle 中的纹理。

为了提高通过网络传输的效率，可以根据用例要求（LZMA 和 LZ4）选用内置算法选择来压缩 AssetBundle。

AssetBundle 可用于可下载内容（DLC），减小初始安装大小，加载针对最终用户平台优化的资源，以及减轻运行时内存压力。



#### 学习资料
[AssetBundle杂记--AssetBundle的二三事](https://www.cnblogs.com/msxh/p/8506274.html)

