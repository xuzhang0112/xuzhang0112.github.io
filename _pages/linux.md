---
permalink: /linux/
title: "Linux"
excerpt: ""
author_profile: true
toc: true
---


## Shell

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202410241649045.png" alt="image-20241024164920306" style="zoom: 25%;" />

Shell是用户和操作系统之间的媒介，它提供了丰富的接口，以帮助用户使用操作系统。

Shell命令的基本格式是“命令+参数”，参数可以缺省，这些参数随后会被执行该命令的解释器所解析。

命令本质上是可执行文件，可以通过路径来调用，但大部分时候是以别名的形式调用

```shell
pwd
sh *.sh
python main.py
```

当连续执行多条命令时，如果用"&&"连接，则多条命令依次执行，报错则终止；如果用“||”连接则当任意一条命令执行成功，后续命令都不执行

在命令结尾加"&"，可以使命令挂载到后台执行，从而可以在同一个Shell中并行执行多条命令

### 配置默认启动脚本

用户使用终端登录Linux服务器时，通常只会默认执行`~/.bash_profile.sh`，而conda的自启动一般写在`~/.bashrc`中，所以一般会在`~/.bash_profile.sh`同时运行`~/.bashrc`

```sh
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi
```

每当重新打开一个新的终端时，则会执行`~/.bashrc`

conda自启动

```sh
# >>> conda init >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$(CONDA_REPORT_ERRORS=false '/data/shaozhisun/anaconda3/bin/conda' shell.bash hook 2> /dev/null)"
if [ $? -eq 0 ]; then
    \eval "$__conda_setup"
else
    if [ -f "/data/shaozhisun/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/data/shaozhisun/anaconda3/etc/profile.d/conda.sh"
        CONDA_CHANGEPS1=false conda activate base
    else
        export PATH="/data/shaozhisun/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
```

### Oh My ZSH

[配置代码高亮和自动补全插件](https://kirigaya.cn/blog/article?seq=66)

### Shell脚本

尽管Linux系统中已经实现了大量的Shell脚本供用户调用，但有些时候我们仍然需要自己编写Shell脚本，以实现定制化的需求。

如果希望shell脚本可以作为可执行文件调用，则需要在第一行指定解释器

```sh
#!/bin/bash 当作为
```

否则，只需要在调用该脚本时，显式地指定解释器即可；bash和zsh的语法比sh更全

```shell
sh *.sh
bash *.sh
zsh *.sh
```

#### 变量的定义和使用

变量通过`=`定义，通过`${}`调用，通过`echo`可以将变量输出到控制台

Shell中有两类特殊的变量，字符串和数组。

字符串区分单引号和双引号，单引号无视一切转义字符和变量引用，双引号则支持转义字符和变量；多个字符串可以拼接，中间不加空格即可

数组通过`()`定义，通过`${数组名[下标]}`访问，`${数组名[@]}`可获取数组中全部元素

${#变量名}可以获取指定变量的长度

环境变量通常是大写，一般定义在`~/.bashrc`中，每次启动shell时都会导入；`export`可以显式环境变量，也可以定义临时的环境变量

| 定义环境变量的方式                         | 作用域                          |
| ------------------------------------------ | ------------------------------- |
| 写在～/.bash_rc里                          | 全局有效，每次启动shell都会导入 |
| `export PATH=$PATH:/data/xuzhang/bin`      | 仅当前shell和子进程有效         |
| `CUDA_VISIBLE_DEVICES=1,3 python train.py` | 仅子进程有效                    |

#### 运算符

算数运算符一般通过`expr`命令或者`$(())$`来实现，运算符的两边需要有空格，双括号中的变量可以不使用$符号前缀

逻辑运算符一般通过`test`命令或者`[[]]`来实现，中括号和运算符的两边需要有空格，双中括号支持更多的逻辑运算符

#### 流程控制

和其他语言类似，包括分支和循环

分支包括`if`和`case`

`if`的结束词是`fi`，即`if`倒过来

```shell
if condition1
then
	command1
elif condition2
then
	command2
else
	commandN
fi
```

此外也有`case`，结束词同样是倒过来`esac`

```
case 变量 in
	1)
	command1
	...
	commandN
	;;
	2)
	...
esac
```

循环包括`for`和`while`，两者都支持`break`和`continue`

for

```shell
for 变量 in 数组
do
	command
done
```

while

```shell
while condition
do
done
```

#### 输入输出

输出有`echo`和`printf`，其中`echo`默认不开启转义，需要通过`-e`开启转义，而`printf`则支持转义

两者的输出都可以通过`>`（覆盖写）或者`>>`（追加写）重定向到指定的文件

可以通过反引号来获取指定命令的输出，以字符串的形式返回，

### ssh：安全的shell

|          |                          |      |
| -------- | ------------------------ | ---- |
| 建立连接 | `ssh 用户名@服务器IP`    |      |
| 断开连接 | `ctrl+d`或`exit`断开连接 |      |


内网免密登录

1. 本地生成公钥`ssh-keygen -t rsa`

2. 上传公钥到服务器的授权列表

   - [SSH配置公钥后仍需要输入密码](https://blog.csdn.net/qq_19922839/article/details/117488663)

     跳板机登录

     方式1：两步登录

     首先通过ssh连接跳板机（如果端口号不是ssh的默认端口22，则需要手动指定），然后在跳板机上选择要登录的服务器

     方式2：一步登录

     `ssh xuzhang@xuzhang@172.16.120.1@bacteria.tech -p2222`

     local和跳板机之间的免密是通过跳板机的用户界面设置，跳板机和服务器之间的免密是由跳板机的管理员设置。

## Mac/Linux OS

### 文件系统

路径分为绝对路径和相对路径，相对路径是相对于当前***工作路径***，可通过`pwd`查看

|          |                   | 备注         |
| -------: | :---------------- | ------------ |
| 绝对路径 | /data/xuzhang/... |              |
| 相对路径 | `~/...`           | 用户根目录   |
|          | `./`              | 当前目录     |
|          | `../`             | 上一级目录   |
|          | `../../`          | 上上一级目录 |

不涉及文件内容的查看

```shell
ls dir # 查看dir内的文件
du -sh # 查看dir的磁盘占用情况
df -h # 查看分区情况
find -name filename # 搜索

```

对文件的增删改查

```shell
touch filename # 创建文件
mv raw_file new_dir/new_file # 移动/重命名
cp file1 file2 # 复制，文件夹需要-r
rm file/dir # 删除
chmod +x filename # 修改文件的读写权限r (只读)w (写入)x (执行)
```

对文件内容的查看和修改

```shell
cat file # 本义是“concatenate and print files”，连接和查看多个文件内容，实际多用于查看单个文件内容
head -n 10 # 前10行
tail -n 10 # 后10行
grep -c [exp] # 与正则表达式匹配的行，-c返回计数
wc file # 字数
```

### 进程管理

[杀死僵尸进程](https://blog.csdn.net/weixin_47700137/article/details/134363063)

```sh
ps aux | grep user_id | grep python | grep Rsl # 搜索某一用户运行的python进程
kill -9 PID # 杀死进程
```

挂载后台
`nohup command &` 

显卡占用情况

````sh
gpustat -i # 需要pip安装
````

端口占用情况

```sh
netstat -tuln | grep 6006 # 查看6006端口的占用情况
ssh -L 8008:127.0.0.1:8008 xuzhang@172.16.120.1 #将指定端口映射到某个ip
```

### 用户管理

|          |                                          |                                                              |
| -------- | ---------------------------------------- | ------------------------------------------------------------ |
| 用户名   | `whoami``echo $USER`                     |                                                              |
| root权限 | `sudo apt install gimp` `sudo cd /root/` | 安装软件或编辑用户主目录以外的文件                           |
| 修改权限 | `chmod [num][num][num]`                  | 6表示读写，7表示读写和执行；三个数字分别对应用户、用户组、其他用户 |
| 修改密码 | `passwd`                                 |                                                              |
| 退出登录 | `exit`                                   | `Ctrl+D`                                                     |
| 关机     | `shutdown`                               |                                                              |
| sudo权限 | `su - [user]`                            |                                                              |

## 常用软件

### Zip

|               | 压缩 | 解压                                  |
| ------------- | ---- | ------------------------------------- |
| zip & unzip   |      |                                       |
| gzip & gunzip |      | gunzip -r dir 解压文件夹下所有.gz文件 |

### Tmux

[Tmux 使用教程](https://www.ruanyifeng.com/blog/2019/10/tmux.html)

将会话（Section）和窗口（Terminal Window）解绑

- 会话可以随时解绑（不随窗口的关闭而终止）和重新接入
- 可以同时发起多个会话，并进行任意的垂直和水平拆分

#### 安装

```sh
# Linux
sudo apt-get install tmux

# Mac
brew install tmux
```

#### CLI使用

```sh
tmux #新建会话
tmux new -s <session-name> #新建会话
tmux detach # 分离会话

tmux ls # 查看所有会话
tmux attach -t 0 #接入会话

tmux rename-session -t 0 <new-name> # 重命名会话
tmux kill-session -t <session-name> # 杀死会话

```

#### 快捷键

`Ctrl+b`是前缀键，用于表示使用快捷键

| `Ctrl+b+` |         |                                                           |
| --------- | ------- | --------------------------------------------------------- |
| d         | detatch | 分离当前会话                                              |
| s         |         | 列出所有会话                                              |
| $         |         | 重命名当前会话                                            |
| %         |         | 左右分屏（百分号的左和右）                                |
| ""        |         | 上下分屏（上引号和下引号）                                |
| o         |         | 切换分屏                                                  |
| [         |         | 上下滚动(q退出)                                           |
| x         |         | 关闭分屏窗口                                              |
| :         |         | 命令模式：<br />输入select-layout even-horizontal垂直平分 |

### VIM

```sh
vim filename # 我只会这一条
```

1. 正常模式（Normal-mode) 
   - 狂按`<Esc>`进入
   - `dd`剪切一行；`yy`复制一行；`p`粘贴；`u`撤销；
2. 插入模式（Insert-mode)
   - 按 `i` 键会进行插入模式，进入编辑状态，通过键盘输入内容。

3. 命令模式（Command-mode)
   - 先切正常模式，然后按`：`或`/` 进入
   - 未编辑，按q退出；编辑后，wq保存退出，q!不保存强制退出

### CUDA

[CUDA 是什么 | Chenglu’s Log](https://chenglu.me/blogs/what-is-cuda)

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202406052252187.png" alt="image-20240605225234182" style="zoom:67%;" />

Driver：可通过`nvidia-smi`查看显卡的驱动和CUDA驱动版本，驱动版本需要高于Runtime版本

Runtime: 可通过`nvcc -V`查看，表示CUDA Toolkit的runtime版本，通过CUDA Toolkit installer安装，见`usr/local/cuda/include/cuda.h`），一些软件会调用Runtime提供的API

不同应用对CUDA各个层的依赖要求是不同的

- PyTorch会打包所需要的Runtime依赖，不调用本机的CUDA Toolkit，只需要驱动版本满足要求即可。
- Tensorflow需要安装相应的Library，例如CuDNN，也会对Runtime有要求

### Git

[Git 基本操作 | 菜鸟教程 (runoob.com)](https://www.runoob.com/git/git-basic-operations.html)

#### 初始化

基本配置

```sh
git config --global user.name [name]
git config --global user.email [email]
```

从本地仓库初始化

```sh
git init # 创建git仓库
touch .gitignore # 修改.gitignore文件在其中写入需要忽略的文件和目录
# "/"开头，从.gitignore所在目录开始匹配
# "/"结尾，只匹配目录，不匹配文件
# "/../"匹配多级目录
# *.filetype，忽略指定类型的文件
# !filetype，不忽略指定类型的文件（如果文件夹已经被忽略，则文件必然被忽略）
```

- [一键生成gitignore](https://www.toptal.com/developers/gitignore)

  克隆远程仓库

```sh
git clone [url] # 复制仓库  
```

#### 分支管理

查看分支

```SH
git branch # 查看本地分支
git branch -r # 查看远程分支
git branch -a # 查看所有分支
```

切换分支

如果是创建新分支，则会保持当前文件状态

```sh
git checkout -b <branchname> # 创建并切换到分支
```

如果是切换到已有的分支，则必须先提交，切换到已有分支后，文件会变成目标分支的最后一次提交

```sh
git add . && git commit -m [msg] # 切换分支前，首先提交

git checkout [branchname] # 切换分支
git add . && git commit -m [msg] # 提交分支修改

git checkout main # 切换到主分支
git merge [branch_name] # 合并分支，并手动解决冲突
git add <conflict-file>
git commit -m [msg] # 合并与修改应当视为一次新的提交
```

#### 远程仓库

```sh
git remote add [origin_name] [http/ssh] # 添加远程仓库
git remote -v # 查看远程仓库

git fetch && git merge [alias]/[branch] # 拉取远程仓库，将远程仓库的指定分支合并到当前分支
git pull [alias] [branch] #将远程仓库的[branch]拉取并合并到当前分支 

git add <conflict-file> #解决冲突
git commit -m [msg] # 并提交

git push -u [alias] [branch] # 第一次需要指定远程仓库
git push # 将[branch]推送到[alias]的[branch]

```

#### 私有仓库

在deploy key中上传服务器的公钥 / 在编辑器里上传Github Token

上传失败可能是服务器的网络问题，挂代理即可

### GDown

``````shell
gdown [url]
``````

[Gdown下载权限错误](https://cloud.tencent.com/developer/ask/sof/108261926)

### Hugo

[Hugo官方文档](https://gohugo.io/documentation/)

#### 部署

将Hugo服务器部署到局域网

```sh
hugo server --bind "0.0.0.0" # 将hugo服务器端口向局域网内所有ip开放
ifconfig | grep 192 # 查看本机在局域网的ip
```

[将静态网站部署到Github Pages](https://simumis.com/posts/deploy-to-github/)

```sh
hugo && cd public && git add . && git commit -m 'update' && git push && cd ..
```

#### 主题

| Theme                                                        |                      |
| ------------------------------------------------------------ | -------------------- |
| [PaperMod](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-installation/) | 配置简单，但功能较少 |
| [Blowfish](https://blowfish.page/docs/)                      | 推荐                 |

#### 定制化功能

|          |                                                              |
| -------- | ------------------------------------------------------------ |
| 公式渲染 | 在layouts/partials中重写主题的某些文件，以blowfish为例，需要在vendor.html中重写katex部分的js脚本 |
| 社交图标 | [Social Icons SVG](https://www.iconfont.cn/)                 |
| 站内搜索 | [Search Tools](https://gohugo.io/tools/search/)<br/>[原理](https://www.beizigen.com/post/hugo-implements-simple-on-site-search/) |

### iTerm2

[修改键盘映射](https://medium.com/@kwhsiung/在-iterm2-中新增慣用的刪行-刪字-跳字熱鍵-1c9a642aa22f)

### Vscode

服务器连接失败

`.vscode-sever/bin`删除多余文件夹即可

#### 自用配置

|        |                                 |      |
| ------ | ------------------------------- | ---- |
| Theme  | One Dark Pro                    |      |
| Python | 编译器+格式化（black, pip安装） |      |
| Matlab | 编译器+gb2312                   |      |
| Latex  | xelatex                         |      |

### Docker

![image-20241010144153061](https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202410101442972.png)

|                |                                            |      |
| -------------- | ------------------------------------------ | ---- |
| Image/镜像     | 通过Dockerfile将需要部署的应用打包成Image  |      |
| Container/容器 | Docker运行在OS上，Contrainer运行在Docker上 |      |
| Docker         | 在相应地OS上安装对应的Docker               |      |
| Dockerfile     | 配置脚本                                   |      |

## 网络

获取ip

```shell
ifconfig
```

检查连通性

```shell
ping [ip]
ping [url]
```

### 下载

```shell
wget ``http://www.ipcmen.com/testfile.zip``
wget -O wordpress.zip [url]
```

### 内网传输

```shell
# sftp
sftp user@ip
put 文件名
get 文件名

# scp
scp src/... tgt/...
```

## 硬件

### 键盘

键盘失灵怎么办？

1. [修复轴体](https://blog.csdn.net/binbinczsohu/article/details/114951773)
2. 更换轴体