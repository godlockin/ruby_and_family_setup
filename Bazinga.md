# 环境配置

## Ruby
Mac自带有Ruby，可以查看ruby版本
```
chens-MacBook-Pro:~ chenchen$ ruby --version
ruby 2.3.7p456 (2018-03-28 revision 63024) [universal.x86_64-darwin17]
```
其中，ruby的包管理器gem也是预装过的，可以查看版本
```
chens-MacBook-Pro:~ chenchen$ gem --version
2.5.2.3
```
接下来就是Mac上的包管理器，类似Notepad++里面的pluginmanager。
这玩意同时依赖了Xcode和Ruby，本着要用就用最新的想法（希望能避免好多坑），直接搞最新版。
这命令我抄的，在命令行里敲：
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```
一大堆下一步，会有一些需要输入管理员密码的地方，用来在系统目录里创建一些文件
> 因为这玩意会依赖xcode，所以可能会提示要求装xcode-select

果断自己先手动安装掉
```
chens-MacBook-Pro:~ chenchen$ xcode-select --install
xcode-select: note: install requested for command line developer tools
```
当然，像我脸这么黑的肯定是安装失败了，只好去[Apple developer](https://developer.apple.com/download/more/)里面去下了自己装
>需要登陆appleId
>https://developer.apple.com/download/more/


## brew
