

#### mac 下 打开/关闭 隐藏文件夹

```shell
defaults write com.apple.finder AppleShowAllFiles -bool true; killall Finder
```

```shell
defaults write com.apple.finder AppleShowAllFiles -bool false; killall Finder
```



#### 终端自动补全的配置

```shell
# 使用tab自动补全

# nano是一个字符终端的文本编辑器
nano .inputrc

# 输入
set completion-ignore-case on
set show-all-if-ambiguous on
TAB: menu-complete

# ctrl + 0 输入任意文件名，回车
# ctrl + x 退出编辑模式，重启终端
```

