# AssetBundle_加载



#### 关于加载

假设我们存在一个文件，它同时存在与 **Resources**、**StreamingAssets**、**PersistentDataPath**下。  

甚至你可以能使Addressable，你可以放在任意路径，比如 **Assets/MyRes/** 下。

那么，我们使用API进行加载的时候，需要传入的路径分别是？

- **Resources**
  
  - path = Android/demo/prefab
  
  - ```c#
    var res = Resources.Load(path);
    ```
  
- **StreamingAssets**

  - path = Android/demo/prefab

  - ```
    var res = Resources.Load(path);
    ```

  - 








