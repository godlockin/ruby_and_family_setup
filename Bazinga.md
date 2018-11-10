# starting out with LLS

## 前言：

```bash
本文的目标是只要高中毕业，具有基本的计算机概念，都能轻松上手的傻瓜版教程。
// 特别是我这种零基础文科小白
```

## 基础概念篇

1. 我们目前使用的开发语言都是经过封装和优化的，更接近人类语言的存在，所以我们需要：
   1. 程序运行环境，e.g. Java 的JDK，Ruby和Go的基础lib
   2. 具备诸如代码补全、高亮、运行…的编辑器/IDE
   >当然也有大神直接用记事本/notepad撸代码，那种非人类的操作只可远观不可随意模仿

2. 开发语言，无论Python、Go、Perl、Prolog…最终目的都是通过一系列的逻辑来处理数据，那么我们需要：
    1. 一个容器可以承载和运行我们的处理逻辑
    2. 一些容器用来储存我们的数据
    3. 一些路径可以让我们控制逻辑去做处理
    4. 一些方式让我们看到处理的结果

3. 在了解了这些之后我们了解到需要做以下到准备：
    1. 计算机/服务器上开发和编译程序用的环境
    2. 趁手的IDE
    3. 程序运行和部署工具

## 环境准备篇

1. 开发环境（以下的手顺都以Mac为基础环境）
    0. Java JDK (somehow bazel 的安装需要JDK 1.8…emmmm…)
        1. <https://blog.csdn.net/deliciousion/article/details/78046007>

    1. Ruby的安装
        1. Mac自带有Ruby，可以查看ruby版本
            ```bash
            chens-MacBook-Pro:~ chenchen$ ruby --version
            ruby 2.3.7p456 (2018-03-28 revision 63024) [universal.x86_64-darwin17]
            ```

        2. ruby的包管理器gem也是预装过的，可以查看版本
            ```bash
            chens-MacBook-Pro:~ chenchen$ gem --version
            2.5.2.3
            ```

    2. 接下来安装HomezBrew，这是Mac上的包管理器，类似Notepad++里面的pluginmanager。
        这玩意同时依赖了Xcode和Ruby，本着要用就用最新的想法（希望能避免好多坑），直接搞最新版。
        这命令我抄的，在命令行里敲：
        ```bash
        /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
        ```
        一大堆下一步，会有一些需要输入管理员密码的地方，用来在系统目录里创建一些文件
        > 因为这玩意会依赖xcode，所以可能会提示要求装xcode-select

        果断自己先手动安装掉
        ```bash
        chens-MacBook-Pro:~ chenchen$ xcode-select --install
        xcode-select: note: install requested for command line developer tools
        ```
        当然，像我脸这么黑的肯定是安装失败了，只好去[Apple developer](https://developer.apple.com/download/more/)里面去下了自己装
        >需要登陆appleId
        ><https://developer.apple.com/download/more/>

    3. 安装Xcode相关的东西，主要为了编译各种C++的包
        >上一步已经做好了的话，就只做3和4就好
        1. xcode-select install
            1. 如果失败了，从app store里装一下xcode
            2. 更新一把
            3. 然后重定向xcode：sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
            4. 装下xcode的license：sudo xcodebuild -license

    4. 安装protobuf
        1. brew install protobuf
            1. <https://blog.csdn.net/u014534808/article/details/80203018>
        2. 遇到类似 xxx Permission denied xxx /usr/local/Frameworks的时候：
            1. 可能是这个文件夹不存在，跑一下
            2. sudo mkdir /usr/local/Frameworks
            3. sudo chown $(whoami):admin /usr/local/Frameworks
            4. 再run一下#1 里面的 install 命令就好了

    5. 安装gRPC相关组件
        1. grpc 使用环境
           > `gem install grpc`
        2. grpc 编译工具
           > `gem install grpc-tools`

    6. 安装mysql
        1. brew install mysql@5.6 （目前线上环境有5.6和5.7两种instance，装5.7也一样。不过需要自己建my.cnf）
            > 这个功能貌似已经在新版本里被干掉了，需要确认一下是否可以指定安装版本
        2. 重装mysql链接库 mysql2
            > 由于5x和8x的路径不一样，相互又不兼容，所以可能需要重装一遍
            >>  通过`gem list | grep mysql2`确定是否存在
            > 存在就执行第三步否则跳过
        3. 卸载当前的mysql2
            > `gem uninstall mysql2`
        4. 安装mysql2 并指定配置文件位置
            > `gem install mysql2 -v '0.4.10' -- --with-mysql-config=/usr/local/Cellar/mysql@5.6/5.6.42/bin/mysql_config`
            >> 其中通过 -v 指定 mysql2 版本，通过 --with-mysql-config 指定mysql配置文件路径，这个路径可能根据安装包的不同而不同

            由于路径不同，我们可能还需要把mysql里面用到的一些应用做超链接
            > mysql本尊
            >> `ln -sf /usr/local/Cellar/mysql\@5.6/5.6.42/bin/mysql /usr/local/bin/mysql`

            > mysql.server
            >> `ln -sf /usr/local/Cellar/mysql\@5.6/5.6.42/bin/mysql.server /usr/local/bin/`
            > mysqldump
            >> `ln -sf /usr/local/Cellar/mysql\@5.6/5.6.42/bin/mysqldump /usr/local/bin/`

            然后验证一下
            > `mysql --version`
            >> mysql  Ver 14.14 Distrib 5.6.42, for osx10.14 (x86_64) using  EditLine wrapper

            这个可能不一样，以真实安装为准

        5. 启动mysql
            `mysql.server start`

        6. 这个安装过程可能需要重复很多遍，参考[这个地方](https://www.cnblogs.com/liyunfei0103/p/8053223.html)来来回卸载MySql
            ```bash
             执行下列命令
             sudo rm /usr/local/mysql
             sudo rm -rf /usr/local/mysql*
             sudo rm -rf /Library/StartupItems/MySQLCOM
             sudo rm -rf /Library/PreferencePanes/My*
             rm -rf ~/Library/PreferencePanes/My*
             sudo rm -rf /Library/Receipts/mysql*
             sudo rm -rf /Library/Receipts/MySQL*
             sudo rm -rf /var/db/receipts/com.mysql.*
             其实不同的安装方式有些东西的存储位置不一样，删除完检查一下下面这些文件是否删除了，没有的话则删除掉：
             /usr/local/Cellar 里的mysql文件
             /usr/local/var 里的mysql文件
             /tmp 里的mysql.sock, mysql.sock.lock, my.cnf文件
             pid文件和err文件都在/usr/local/var/mysql里确保删除了
             brew安装的安装包存储在/usr/local/Library/Cache/Homebrew也可以一并删除
             执行brew cleanup

    7. 安装redis
        1. [官网下载](https://redis.io/download) stable版的安装包
        2. 解压缩然后挪到 /usr/local/
        `tar -vxf redis-5.0.0.tar -C /usr/local/`
        3. 进redis的目录编译一下，参考[这篇](https://blog.csdn.net/jason_m_ho/article/details/80007330)
           ```bash
           cd /usr/local/redis*
           sudo make test
           sudo make install
           ```
        4. 后台启动redis
            ```bash
            redis-server &
            ```

2. 各种开发工具，目前主流的是用IDEA和VSCode来进行开发，也欢迎用其他的
    1. IDEA
        1. 直接[官网下载](https://www.jetbrains.com/idea/download/#section=mac)
        2. 破解方法百度"lan yu"，支持正版从我做起：）
        3. 安装各种插件，比如"ruby"
    2. VSCode
        1. 直接[官网下载](https://code.visualstudio.com/download)
        2. 安装各种插件，比如"ruby"
    3. DB工具
        1. 我直接用的MySql自己的[MySQLWorkbench](https://dev.mysql.com/downloads/workbench/)
        2. 链接一下本地DB试试，记得改root密码 ：）

3. 运行容器
    1. 一般来说有三种方式运行程序
        1. 本地直接运行
        2. 虚拟机
        3. docker
        >三种方式各有优缺点可以自己找资料

    2. 安装docker
        1. [官网安装教程](https://docs.docker.com/docker-for-mac/install/)
        2. 下载普通安装略
        3. 版本验证
           ```bash
           chen1:llspay chenchen$ docker --version
           Docker version 18.06.1-ce, build e68fc7a
           ```
           ```bash
           chen1:llspay chenchen$ docker-compose --version
           docker-compose version 1.22.0, build f46880f
           ```
           ```bash
           chen1:llspay chenchen$ docker-machine --version
           docker-machine version 0.15.0, build b48dc28d
           ```
        4. 然后在状态栏小鲸鱼里面装Kubernetes

## ruby依赖库

1. bundler：Manage dependencies of an application
   >ref: <https://rubygems.org/gems/bundler>
2. pry: Ruby的debug工具。可能装了，如果没装：

   ```bash
   gem install pry
   ```

   >ref: https://github.com/pry/pry
3. Debug Ruby用的一些玩意儿
   1. debase

      ```bash
      gem install debase
      ```

      >ref: <https://rubygems.org/gems/debase/>
   1. ruby-debug-ide

      ```bash
      gem install ruby-debug-ide
      ```

      >ref: <https://rubygems.org/gems/ruby-debug-ide/>

## 关于开发

1. 教程
    * [Ruby on Rails Guides](https://guides.rubyonrails.org/)
      >官方文档，不过是英文的
    * [菜鸟教程](http://www.runoob.com/ruby/ruby-tutorial.html)
      >这里面从安装到基本语法都写的挺详细，而且是中文文档
1. CI/CD
    * 持续集成持续部署（大概这样翻译） Continuous Integration/Continuous Deployment
      >ref: https://blog.csdn.net/sinat_35930259/article/details/79429743
