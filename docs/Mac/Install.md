#### Mac 环境配置

>  最近换了新电脑进行工程，对程序员来说，最痛苦的莫过于迁移环境了，电脑数据的迁移，开发环境的安装，各种插件包括浏览器等插件，本地的运行App安装等。此次迁移完成，我以后决定要学习下Docker安装，配置下云端环境，一些环境的改进安装等。



# 目标

整理下，此次的迁移结果以及总结一些后续的快速迁移方式。



其中主要工作有：

1. Chrome浏览器的账号同步问题，以后需要同步账号与标签
2. 一些关键资料通过AirDrops传输与App安装
3. 环境的安装

## Homebrew安装

https://zhuanlan.zhihu.com/p/90508170

### 安装homebrew 403问题

由于GFW问题，DNS解析会有问题；

https://github.com/hawtim/blog/issues/10

https://githubusercontent.com.ipaddress.com/user-images.githubusercontent.com



在本机的 host 文件中添加，建议使用 utool 的hosts 方便 host 管理

199.232.68.133 raw.githubusercontent.com
199.232.68.133 user-images.githubusercontent.com
199.232.68.133 avatars2.githubusercontent.com
199.232.68.133 avatars1.githubusercontent.com



### Powerlevel10k

> https://medium.com/@howarder3/mac-%E5%AE%89%E8%A3%9D-homebrew-iterm2-oh-my-zsh-powerlevel10k-%E7%AD%86%E8%A8%98-bfa72ced659a
>
> https://suixinblog.cn/2019/09/beautify-terminal.html



https://blog.csdn.net/kk3909/article/details/105120234



## 安装zsh

虚拟终端,兼容bash

- macos
  mac默认已经安装了zsh,只需切换

```bash
chsh -s /bin/zsh
```

如果想还原回去执行`chsh -s /bin/bash`即可

- ubuntu

```bash
sudo apt install zsh
chsh -s /bin/zsh
```

#### 安装oh-my-zsh

基于zsh命令行，是对zsh的包装，提供了主题配置，插件机制.
curl

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

wget

```bash
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

#### 安装Powerlevel9k / Powerlevel10k主题

zsh使用最多的主题

```bash
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```

编辑 `~/.zshrc` 设置 `ZSH_THEME="powerlevel10k/powerlevel10k".`

再增加一行配置:`POWERLEVEL9K_MODE="awesome-patched"`

#### 安装字体

- ubuntu

```
apt-get install fonts-powerline
```

- macos

```
https://github.com/powerline/fonts/blob/master/SourceCodePro/Source%20Code%20Pro%20for%20Powerline.otf
https://github.com/Falkor/dotfiles/blob/master/fonts/SourceCodePro%2BPowerline%2BAwesome%2BRegular.ttf
12
```

打开下载的字体，然后按“安装字体”。
在iTerm2中设置字体（Preperence->Profiles->Text→Change Font）,选择`Source Code Pro + Font Awesome`,大小18，最好对“字体”和“非ASCII字体”都进行设置。重新启动iTerm2，以使所有更改生效.

#### zsh配置主题

```bash
source ~/.zshrc
```

或者执行下面的命令,重新配置

```
p10k configure
```

开始配置Powerlevel10k,根据提示(喜好)选择1234或yes、no

#### iterm2主题配置(针对mac)

下载主题:[https://iterm2colorschemes.com](https://github.com/mbadolato/iTerm2-Color-Schemes/archive/master.zip)
Preperence->Profiles->Colors->color Presets,点击import,选择刚解压的目录,点击目录下的schemes,选择你想要导入的主题(可多选全部导入),导入后选择自己喜欢的主题,这里我选择了ubuntu主题

### 一些常用的插件

#### zsh-autosuggestions

下载插件

```bash
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

编辑 `~/.zshrc` ,设置plugins

```bash
plugins=(zsh-autosuggestions git)
```

使插件生效

```bash
source ~/.zshrc
```

上次敲过的命令 ,会自动提示,按右方向键➡️确认填充上次的命令

#### zsh-syntax-highlighting

下载插件

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

写入到配置

```bash
echo "source $ZSH_CUSTOM/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
```

使插件生效

```bash
source ~/.zshrc
```

命令会自动高亮显示





### Git clone 加速问题

https://zhuanlan.zhihu.com/p/129257729



### 如何给git clone加速

> 此文仅适用于有梯子的朋友。

### Step1 查看 socks5 监听本地哪个端口

<img src="https://pic2.zhimg.com/80/v2-a24bf8cdbb2d30d783e1b42f7ef60ac9_1440w.jpg" alt="img" style="zoom:50%;" />



### Step2 设置代理

```bash
git config --global http.https://github.com.proxy socks5://127.0.0.1:1080
```

或者打开 `.gitconfig` 文件，输入下面配置

```bash
[http "https://github.com"]
    proxy = socks5://127.0.0.1:1086
```

### 有图有真相

![img](https://pic4.zhimg.com/80/v2-dfe76b6ac8b0ef4059c3fab11ee3689b_1440w.jpg)



## SSH KEYS

在GitHub中，同一账号的 ssh公钥问题，

cat ~/.ssh/id_rsa.pub

要明白 SHA256的公钥， 不要复制结尾的邮箱部分

看教程一定看懂啊



https://www.cnblogs.com/NyanKoSenSei/p/13381540.html



https://zhuanlan.zhihu.com/p/90508170



