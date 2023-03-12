 

GitHub 密钥

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

ASCII（发音是 “As-Key”

# Linux学习

1. 使用终端ssh连接主机：

```
ssh root@123.57.50.232
```

然后输入root用户的密码就行了。

2. 重启命令：reboot
3. 复制文件：
   1. 先进入所需拷贝文件所载地址，如：切换到桌面 命令输入为 cd 桌面
   2. 然后输入复制命令行 `cp -r code.zip /home/wgj/code`
   3. 利用此方法可以复制文件夹，也可以复制文件，当复制文件夹的时候，不需要加后缀

## 新建用户

### 新建可登录图形用户界面的用户

```bash
sudo su #切换为root用户为了获取创建用户的权限
adduser csdn #添加一个新用户（如用户名为csdn）
adduser csdn sudo #将用户添加到 sudo 组
123
```

然后根据系统提示进行密码和注释性描述的配置即可配置成功。

```bash
cat /etc/passwd #查看用户的属性
exit #退出当前用户
su csdn #切换到用户csdn
123
```

### 新建只能在控制台下登录的用户

```bash
sudo su #切换为root用户为了获取创建用户的权限
useradd csdn #添加一个新用户（如用户名为csdn）
passwd csdn #为该用户设定登录密码
usermod -s /bin/bash csdn #为该用户指定命令解释程序（通常为/bin/bash）
usermod -d /home/csdn csdn #为该用户指定用户主目录
cat /etc/passwd #查看用户的属性
123456
```

可以看到，已经存在csdn这个用户。/etc/passwd中一行记录对应着一个用户，每行记录又被冒号(:)分隔为7个字段，其格式和具体含义如下：

用户名:口令:用户标识号:组标识号:注释性描述:用户主目录:命令解释程序

```bash
su csdn #切换到用户csdn
1
```

可以看到登陆以后的用户csdn当前所在目录仍为用户的主目录。

这种方式只能在控制台中互相切换用户，一旦重启系统，用该用户还是无法登陆（只能用原来的用户或root登陆）。

### 二者命令的差别

两种方式最大的差别在于新建用户的命令不同，第一种是`adduser`, 第二种是useradd。
相对应的，如果要删除用户，第一种的命令为`deluser`, 第二种是userdel.

## 日常问题

### Vim出现E45: 'readonly' option is set (add ! to overrid）

1. 先使用 `:q!` 强制退出 
2. 使用 `sudo vim`

## MySQL 配置问题

### Mysql 安装

##### 1 通过apt 安装MySQL服务（推荐，会安装最新版）

1. 更新源

```python
sudo apt-get update
```

2. 安装mysql服务

```python
sudo apt-get install mysql-server
```

#####  2 初始化配置

```python
sudo mysql_secure_installation
```

##### 3 检查mysql服务状态

```python
systemctl status mysql.service
```

### 安全安装出现root 无效

问题：

```
Re-enter new password: 
 ... Failed! Error: SET PASSWORD has no significance for user 'root'@'localhost' as the authentication method used doesn't store authentication data in the MySQL server. Please consider using ALTER USER instead if you want to change authentication parameters.
```

解决：手动配置密码

```
root@LNMP:~# mysql

mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'mynewpassword';
Query OK, 0 rows affected (0.02 sec)
```

### 远程连接 MySQL

Mysql8.0设置允许[远程连接](httpss://so.csdn.net/so/search?q=远程连接&spm=1001.2101.3001.7020)配置绑定地址

1. mysql配置绑定的地址是127.0.0.1，只允许本机连接。为使其他主机可以访问mysql服务，需要绑定非本地ip，或0.0.0.0即可。

```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf #找到 bind-address 修改值为 0.0.0.0(如果需要远程访问)

sudo /etc/init.d/mysql restart #重启mysql
```

![image.png](https://img.herrluk.icu/typoraPicture/3e2f26c7002efe89b24f7f2b434d07c8.png)

1. 登录mysql

```css
mysql -uroot -p
```

2. 选择mysql数据库

```sql
use mysql;
select host,user,plugin from user;
```

3. 修改user表使其root用户可以通过远程连接

```sql
update user set host = '%' where user = 'root'
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码'; #使用mysql_native_password修改加密规则
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '密码' PASSWORD EXPIRE NEVER; #更新一下用户的密码
mysql> UPDATE user SET host = '%' WHERE user = 'root'; #允许远程访问

```

4. 刷新权限

```lua
flush privileges;
```



如果无法更改密码使用flush privileges;然后再进行更改密码，修改加密规则操作。

其中root@localhost，localhost就是本地访问，配置成 % 就是所有主机都可连接；

第二个’密码’为你给新增权限用户设置的密码，%代表所有主机，也可以是具体的ip；
注意不要直接更新密码的编码格式，而不加密码，这样会把加密密码跟新了，需要携带密码

FLUSH PRIVILEGES;作用是：

将当前user和privilige表中的用户信息/权限设置从mysql库(MySQL数据库的内置库)中提取到内存里。
MySQL用户数据和权限有修改后，希望在"不重启MySQL服务"的情况下直接生效，那么就需要执行这个命令。
通常是在修改ROOT帐号的设置后，怕重启后无法再登录进来，那么直接flush之后就可以看权限设置是否生效。
而不必冒太大风险。

修改密码

```python
alter user 'root'@'%' identified with mysql_native_password by '密码';
1
```

##### 新增用户赋权并设置远程访问

*mysql8*和原来的版本有点不一样，8的安全级别更高，所以在创建远程连接用户的时候，不能用原来的命令（同时创建用户和赋权）:
*必须先创建用户*（密码规则：mysql8.0以上密码策略限制必须要大小写加数字特殊符号）

```python
mysql> CREATE USER 'sammy'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```

##### 赋权

```python
mysql> GRANT ALL PRIVILEGES ON *.* TO 'sammy'@'%' WITH GRANT OPTION;
```

##### 修改加密方式：

```python
mysql8.0 引入了新特性 caching_sha2_password；这种密码加密方式Navicat 12以下客户端不支持；
Navicat 12以下客户端支持的是mysql_native_password这种加密方式；
update user set plugin='mysql_native_password' where user='root'
```



user   = debian-sys-maint

password = wfdgvK1qZeMzNDKS

# MacOS 学习

## 常用命令

1. 刷新 DNS

   ```
   sudo killall -HUP mDNSResponder
   ```

   

2. 访达通过转到访问隐藏文件：`command+shift+G`

3. Mac显示“隐藏文件”命令：

   ```
   defaults write com.apple.finder AppleShowAllFiles -bool true//改成 false 取消显示
   killall Finder
   ```

   快捷键：`shift+command+.`

4. 查看端口被哪个程序占用

   ` lsof -i tcp:port`

   port可以替换成你要查询的端口号

     如： `lsof -i tcp:8888`

   看到进程的PID，可以将进程杀死。

   ` kill -9 PID`

    如：`kill -9 10999`

5. 使用一下组合快捷键，可直接展示or关闭隐藏文件&隐藏文件夹

   ```shell
   shift+command+。
   ```

6. 最小化窗口
   1. 使用快捷键 `Command+M`，可以实现快速最小化当前窗口的目的。
   2. 使用快捷键 `Command+Option+M`，可以实现快速最小化当前应用程序所有窗口的目的。比如你想一下子最小化多个 Finder 窗口，就可以用该快捷键。
   3. 使用快捷键 `Command+H`，可以实现快速隐藏当前应用程序所有窗口的目的。
   4. 使用快捷键 `Command+Option+H`，可以实现快速隐藏除当前应用程序之外所有程序窗口的目的。
   5. 使用快捷键 `Command+Option+M+H`，可以实现快速隐藏所有应用程序窗口的目的。
   6. 你还可以在「系统偏好设置——通用」中勾选”连按窗口的标题栏以将窗口最小化”，然后双击窗口标题栏就可以最小化当前窗口。

7. 进程挂起与恢复

当按下Ctrl + Z组合键后，就停止进程并转入后台。刷新当前执行命令行。程序并没有结束，而是被挂起了。此时我们没有必要通过PID杀掉这个进程。相反我们可以通过一下命令，使这个进程继续执行下去：

1. 使用 jobs 命令，可以查看当前被挂起的进程已经对应的号码。

```
@ubuntu: ~/data/scritps/test$jobs
[1]+ Stopped				./runTest
12
```

1. 使用命令 fg 1 可以恢复进程到前台执行。其中 fg 后面是jobs中查询到的数字。
2. 使用命令 bg 1 可以恢复进程到后台执行。其中 fg 后面是jobs中查询到的数字。

9. control+C ctrl+Z ctrl+D的区别

ctrl+c和ctrl+z都是中断命令,但是他们的作用却不一样.

 ctrl+c是强制中断程序的执行,进程已经终止。

ctrl+z的是将任务暂停，挂起

*ctrl-d 不是发送信号，而是表示一个特殊的二进制值，表示 EOF。*

注：在shell中，ctrl-d表示推出当前shell.

9. 修改 shell

   在 Mac 上的“终端” App  中，选取“终端”>“偏好设置”，然后点按“通用”。

   在“Shell 的打开方式”下方，选择“命令（完整的路径）”，然后输入您想要使用的 shell 的路径。
   
10. 打开滚轮滑动显示 APP 所有打开的窗口

Mac默认该功能是处于关闭状态,我们需要在终端中将其激活,指令如下:

```
defaults write com.apple.dock scroll-to-open -bool true && killall Dock
```

然后我们再Dock中随意App图标上用双指向上滑动,就可以打开该App显示的所有窗口啦.

要关闭这一特性也很容易,同样是在终端中输入如下命令:

```
defaults write com.apple.dock scroll-to-open -bool false && killall Dock
```

要想查看当前该特性是否打开,可以输入如下命令:

```
defaults read com.apple.dock scroll-to-open
```

返回0表示特性处于关闭状态,反之处于打开状态

## Mac使用sftp

1. shell 新建远程连接

   ![image-20221030110909848](https://typorapictureload.oss-cn-shanghai.aliyuncs.com/uPic/image-20221030110909848.png)

2. 选择 sftp和服务器

   ![image-20221030110949444](https://typorapictureload.oss-cn-shanghai.aliyuncs.com/uPic/image-20221030110949444.png)

3. 连接完成

   ![image-20221030111026798](https://typorapictureload.oss-cn-shanghai.aliyuncs.com/uPic/image-20221030111026798.png)

4. 使用 lcd 操作本机目录 put 上传

   ![image-20221030111111860](https://typorapictureload.oss-cn-shanghai.aliyuncs.com/uPic/image-20221030111111860.png)

## Mac 设置公钥私钥免密登录远程服务器

![image-20221030111224308](https://typorapictureload.oss-cn-shanghai.aliyuncs.com/uPic/image-20221030111224308.png)

```
// 切换到本机 ~/.ssh文件夹
$ ssh-keygen -t rsa -C “herrluk@ustc.com” 
```

生成后将公钥存到目标主机 .ssh 文件夹。



# 一些插件

## cgit

> cgit是一个github快速下载器，使用国内镜像，clone速度可达10M/s。

### 安装方法

目前，已提供了Ubuntu、Mac、Window的预编译程序，如果使用的是其他系统，可以采用源码编译安装。

**linux下安装**

```
sudo wget https://cgit.killf.info/cgit_linux_latest -O /usr/local/bin/cgit && sudo chmod 755 /usr/local/bin/cgit
```

**mac下安装**

```
sudo wget https://cgit.killf.info/cgit_mac_latest -O /usr/local/bin/cgit && sudo chmod 755 /usr/local/bin/cgit
```

**arm下安装**

```
sudo wget https://cgit.killf.info/cgit_arm_latest -O /usr/local/bin/cgit && sudo chmod 755 /usr/local/bin/cgit
```

**window下安装**

点击[下载](httpss://gitee.com/link?target=https%3A%2F%2Fcgit.killf.info%2Fcgit.exe)链接，将程序放到你喜欢的目录下，然后将该路径配置`PATH`变量下

**编译安装**

```
git clone httpss://github.com/killf/cgit.git && sudo ./cgit/install.sh
```

### 使用方法

cgit运行时与需要使用git，因此需要先安装git。 linux平台下GIT的默认地址为`/usr/bin/git`，window平台下GIT的默认地址为`C:\\Program Files\\Git\\bin\\git.exe`， 如果需要使用其他路径的git，可配置环境变量`GIT`，如：

```
export GIT=/usr/bin/git
```

也可以通过设置环境变量`CGIT_MIRROR`来修改默认的镜像地址，如：

```
export CGIT_MIRROR=httpss://hub.fastgit.org
```

cgit的使用方法git与一样，事实上内部使用的也是git，使用时只需将命令替换成cgit即可，如下：

```
cgit clone httpss://github.com/killf/cgit.git
```



# Jetbrains使用

## IDEA

### 快捷键

`variable.for`:快速生成 `for (int i = 0; i < variable; i++)`

`3.forr`:快速生成 `for (int i = 3; i > 0; i--)`

`command+option+v`：表达式结果生成变量

`CTRL+ALT+T `可以把代码包在一个块内，例如 if else 等

# Jetbrains的坑

## 远程连接 linux 服务器

### Linux 上找不到头文件

有时候我们使用clion远程连接服务器时找不到比如`<unistd.h>` 或者`arpa/inet.h`这种头文件，没有提示很恼火，只需要在菜单栏点击`工具-> 与远程主机重新同步`就可以解决了
![img](https://typorapictureload.oss-cn-shanghai.aliyuncs.com/uPic/2379061-20220317164705738-1736853135.png)

## Jetbrains 快捷键



- 重新格式化代码：`command+option+L`

# VSCode学习

## 快捷键

### 工作区快捷键

- 显示、隐藏侧边栏：cmd+B
- Mac: `Cmd` + `J` (使用 `Control` + \` 也可以，但是有时候不生效)



### 命令面板

- 显示命令面板：`Cmd` + `Shift` + `P`
- 打开 setting.json文件：需要改 VS Code 配置文件，可以直接通过命令面板打开。在命令面板下输入 `setting` ，然后选择 `Open Settings` 
- 打开用户设置： `Cmd` + `,`

### 光标操作

- 在单词之间移动光标: `Option` + 左右方向键（同时按 `Shift` 还可以进行选中）
- 在郑航之间移动光标：: `Cmd` + 左右方向键
- 光标定位到第一行/最后一行: `Cmd` + ↑/ `Cmd` + ↓

### 代码相关

- 在当前行的下方新增一行（即使光标不在行尾，也能快速向下插入一行）: `Cmd` + `Enter`
- 删除整行: `Cmd` + `Shift` + `K`
- 代码格式化: `Option` + `Shift` + `F`

### 搜索

- 搜索全局代码: `Cmd` + `Shift` + `F`
- 搜索文件名: `Cmd` + `P`
- 在当前文件中搜索代码: `Cmd` + `F`



```
sudo sed -i "s@https://.*archive.ubuntu.com@httpss://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
sudo sed -i "s@https://.*security.ubuntu.com@httpss://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
```

## 快速生成golang代码片段

1. `pkgm`：生成main包+main主函数

   ```go
   package main
   func main() {
   } 
   123
   ```

2. `ff`：格式化输出

   ```go
   fmt.Printf("", var)
   1
   ```

3. `fp`：Println换行输出

   ```go
   fmt.Println("")
   1
   ```

4. `a.Print!`(输入 `a.p`第一个就是,直接回车即可)：格式化输出变量 `a`:

   ```go
   a := 1
   fmt.Printf("a: %v\n", a)
   12
   ```

5. `forr`：for range

   ```go
   for _, v := range v {
   }
   12
   ```

6. `tys`：快捷构建结构体

   ```go
   type name struct {
   }
   ```

## 编辑功能

### 多光标

Visual Studio Code支持在多光标的情况下，对代码进行快速编辑。通过多个光标，你可以同时编辑多处文本。有以下几种方式可以添加多个光标。

- Alt+Click: 按住Alt快捷键，然后单击鼠标左键，就能方便地增加一个新的光标。

-  Ctrl+Alt+Down: 按下此快捷键，会在当前光标的下方，添加一个新的光标。
-  Ctrl+Alt+Up: 按下此快捷键，会在当前光标的上方，添加一个新的光标。
- Ctrl+D: 第一次按下CtrI+D快捷键，会选择当前光标处的单词。再次按下Ctrl+D快捷键，会在下一一个相同单词的位置添加一个新的光标。
- Ctrl+Shift+L: 按下此快捷键，会在当前光标处的单词所有出现的位置，都添加新的光标。

### 列选择

把光标放在要选择的区域的左上角，按住Shift+Alt 快捷键，然后把光标拖至右下角，就完成了对文字的列选择。



## vscode远程



# 中文排版

## 空格

「有研究显示，打字的时候不喜欢在中文和英文之间加空格的人，感情路都走得很辛苦，有七成的比例会在 34 岁的时候跟自己不爱的人结婚，而其余三成的人最后只能把遗产留给自己的猫。毕竟爱情跟书写都需要适时地留白。

与大家共勉之。」——[vinta/paranoid-auto-spacing](https://github.com/vinta/pangu.js)

### 中英文之间需要增加空格

正确：

> 在 LeanCloud 上，数据存储是围绕 `AVObject` 进行的。

错误：

> 在LeanCloud上，数据存储是围绕`AVObject`进行的。

> 在 LeanCloud上，数据存储是围绕`AVObject` 进行的。

完整的正确用法：

> 在 LeanCloud 上，数据存储是围绕 `AVObject` 进行的。每个 `AVObject` 都包含了与 JSON 兼容的 key-value 对应的数据。数据是 schema-free 的，你不需要在每个 `AVObject` 上提前指定存在哪些键，只要直接设定对应的 key-value 即可。

例外：「豆瓣FM」等产品名词，按照官方所定义的格式书写。

### 中文与数字之间需要增加空格

正确：

> 今天出去买菜花了 5000 元。

错误：

> 今天出去买菜花了 5000元。

> 今天出去买菜花了5000元。

### 数字与单位之间无需增加空格

正确：

> 我家的光纤入户宽带有 10Gbps，SSD 一共有 10TB。

错误：

> 我家的光纤入户宽带有 10 Gbps，SSD 一共有 20 TB。

另外，度／百分比与数字之间不需要增加空格：

正确：

> 今天是 233° 的高温。

> 新 MacBook Pro 有 15% 的 CPU 性能提升。

错误：

> 今天是 233 ° 的高温。

> 新 MacBook Pro 有 15 % 的 CPU 性能提升。

### 全角标点与其他字符之间不加空格

正确：

> 刚刚买了一部 iPhone，好开心！

错误：

> 刚刚买了一部 iPhone ，好开心！

### `-ms-text-autospace` to the rescue?

Microsoft 有个 [`-ms-text-autospace`](http://msdn.microsoft.com/en-us/library/ie/ms531164(v=vs.85).aspx) 的 CSS 属性可以实现自动为中英文之间增加空白。不过目前并未普及，另外在其他应用场景，例如 OS X、iOS 的用户界面目前并不存在这个特性，所以请继续保持随手加空格的习惯。

## 标点符号

### 不重复使用标点符号

正确：

> 德国队竟然战胜了巴西队！

> 她竟然对你说「喵」？！

错误：

> 德国队竟然战胜了巴西队！！

> 德国队竟然战胜了巴西队！！！！！！！！

> 她竟然对你说「喵」？？！！

> 她竟然对你说「喵」？！？！？？！！

## 全角和半角

不明白什么是全角（全形）与半角（半形）符号？请查看维基百科词条『[全角和半角](http://zh.wikipedia.org/wiki/全形和半形)』。

### 使用全角中文标点

正确：

> 嗨！你知道嘛？今天前台的小妹跟我说「喵」了哎！

> 核磁共振成像（NMRI）是什么原理都不知道？JFGI！

错误：

> 嗨! 你知道嘛? 今天前台的小妹跟我说 "喵" 了哎!

> 嗨!你知道嘛?今天前台的小妹跟我说"喵"了哎!

> 核磁共振成像 (NMRI) 是什么原理都不知道? JFGI!

> 核磁共振成像(NMRI)是什么原理都不知道?JFGI!

### 数字使用半角字符

正确：

> 这件蛋糕只卖 1000 元。

错误：

> 这件蛋糕只卖 １０００ 元。

例外：在设计稿、宣传海报中如出现极少量数字的情形时，为方便文字对齐，是可以使用全角数字的。

### 遇到完整的英文整句、特殊名词，其內容使用半角标点

正确：

> 乔布斯那句话是怎么说的？「Stay hungry, stay foolish.」

> 推荐你阅读《Hackers & Painters: Big Ideas from the Computer Age》，非常的有趣。

错误：

> 乔布斯那句话是怎么说的？「Stay hungry，stay foolish。」

> 推荐你阅读《Hackers＆Painters：Big Ideas from the Computer Age》，非常的有趣。

## 名词

### 专有名词使用正确的大小写

大小写相关用法原属于英文书写范畴，不属于本 wiki 讨论內容，在这里只对部分易错用法进行简述。

正确：

> 使用 GitHub 登录

> 我们的客户有 GitHub、Foursquare、Microsoft Corporation、Google、Facebook, Inc.。

错误：

> 使用 github 登录

> 使用 GITHUB 登录

> 使用 Github 登录

> 使用 gitHub 登录

> 使用 gｲんĤЦ8 登录

> 我们的客户有 github、foursquare、microsoft corporation、google、facebook, inc.。

> 我们的客户有 GITHUB、FOURSQUARE、MICROSOFT CORPORATION、GOOGLE、FACEBOOK, INC.。

> 我们的客户有 Github、FourSquare、MicroSoft Corporation、Google、FaceBook, Inc.。

> 我们的客户有 gitHub、fourSquare、microSoft Corporation、google、faceBook, Inc.。

> 我们的客户有 gｲんĤЦ8、ｷouЯƧquﾑгє、๓เςг๏ร๏Ŧt ς๏гק๏гคtเ๏ภn、900913、ƒ4ᄃëв๏๏к, IПᄃ.。

注意：当网页中需要配合整体视觉风格而出现全部大写／小写的情形，HTML 中请使用标准的大小写规范进行书写；并通过 `text-transform: uppercase;`／`text-transform: lowercase;` 对表现形式进行定义。

### 不要使用不地道的缩写

正确：

> 我们需要一位熟悉 JavaScript、HTML5，至少理解一种框架（如 Backbone.js、AngularJS、React 等）的前端开发者。

错误：

> 我们需要一位熟悉 Js、h5，至少理解一种框架（如 backbone、angular、RJS 等）的 FED。

## 争议

以下用法略带有个人色彩，即：无论是否遵循下述规则，从语法的角度来讲都是**正确**的。

### 链接之间增加空格

用法：

> 请 [提交一个 issue](https://github.com/mzlogin/chinese-copywriting-guidelines/blob/Simplified/README.md#) 并分配给相关同事。

> 访问我们网站的最新动态，请 [点击这里](https://github.com/mzlogin/chinese-copywriting-guidelines/blob/Simplified/README.md#) 进行订阅！

对比用法：

> 请[提交一个 issue](https://github.com/mzlogin/chinese-copywriting-guidelines/blob/Simplified/README.md#) 并分配给相关同事。

> 访问我们网站的最新动态，请[点击这里](https://github.com/mzlogin/chinese-copywriting-guidelines/blob/Simplified/README.md#)进行订阅！

### 简体中文使用直角引号

用法：

> 「老师，『有条不紊』的『紊』是什么意思？」

对比用法：

> “老师，‘有条不紊’的‘紊’是什么意思？”

# 常用软件与网站

## 下载纯净 windows

【下载电脑系统的网站】

1.Hellowindows：http://t.cn/A6xmqCOS，从 Windows 11，到 Windows 10、Windows 8、Windows 7、Windows XP 都有，除了系统镜像外，也提供Office/WPS下载。

2.微软官方下载：Win10下载 http://t.cn/RLCviQ1，Win11下载 http://t.cn/A6MJXGGT

3.xitongku：http://t.cn/A6S5ahW1，免费提供原版win11，win10，win8/8.1，win7系统下载，原版office全系列下载与安装等。选择对于系统，会给出对应版本和发布时间及位数，并附带安装教程。
