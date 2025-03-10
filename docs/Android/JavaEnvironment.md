# <center>环境部署

---

# 安装JDK

### 参考文档

> [参考文档 - 菜鸟教程](https://www.runoob.com/java/java-environment-setup.html) 

##### 安装步骤
- [下载安装JDK](https://www.oracle.com/java/technologies/downloads/)
- 配置变量环境(Windows)
    - 右键我的电脑→属性→高级系统设置→高级→环境变量
    - 在 "系统变量" 中设置 3 项属性，JAVA_HOME、PATH、CLASSPATH(大小写无所谓),若已存在则点击"编辑"，不存在则点击"新建"。
        - 变量设置参数如下：
        - 变量名： JAVA_HOME
        - 变量值： C:\Program Files\Java\jdk1.8.0_111
        - 变量名： Path
        - 变量值： %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
        - 变量名： CLASSPATH
        - 变量值： .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;       注意：这前面有一个点‘.’
    - 检查安装结果
        - 打开命令行
        - 输入 java -version
        - 安装成功会显示当前的Java版本
        ```
        java version "1.8.0_261"
        Java(TM) SE Runtime Environment (build 1.8.0_261-b12)
        Java HotSpot(TM) Client VM (build 25.261-b12, mixed mode, sharing)
        ```

        ​    
---

# 安装SDK
 > [参考文档 - 菜鸟教程](https://www.runoob.com/java/java-environment-setup.html)

#### 安装步骤
- [下载安装SDK](https://developer.android.com/studio)





































