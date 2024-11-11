美化篇
=====

安装字体
-------

参考[这里](https://guyueshui.github.io/post/%E8%AE%B0%E4%B8%80%E6%AC%A1%E9%87%8D%E8%A3%85linux/#%E5%AD%97%E4%BD%93)。

如何在 Linux 上安装喜欢的[字体](https://fonts.google.com/)呢？只需要将对应的字体文件复制到相应的字体目录，再执行几个命令就 OK 了。

**下载字体**

英文字体的话，[Google Fonts](https://fonts.google.com/) 基本可以完全搞定。

关于中文字体，目前推荐的只有两个：[文泉驿](wenq.org)，[文鼎](http://www.arphic.com.tw/)。

例如要将下载好的 Alegreya.zip 字体安装到系统，只需要将其解压：
![](https://s2.ax1x.com/2019/01/18/kpjOAO.png)

可以看到里面的字体文件。挑几个解释一下
```
Alegreya-Regular.ttf    # 常规字体
Alegreya-Italic.ttf    # 斜体
Alegreya-Bold.ttf    # 粗体
Alegreya-BoldItalic.ttf    # 粗斜体
```
一般而言，虽然一个字体包里面可能有很多字体文件，很多变体（粗体，特粗，细，特细等），我们只需要安装以上四种变体基本上就足够了。在 TeXLive 中，如果指定了字体族，那么其中的 `\emph` 和 `\textbf`命令会自动寻找相应的变体来排版。

**复制到指定目录**

将这四个文件复制到系统字体文件夹
```
# 用户字体目录
~/.local/share/fonts

# 系统字体目录
/usr/share/fonts
```
为了便于管理，可以在系统字体目录下新建一个文件夹存放用户后续安装的字体，例如
```sh
mkdir /usr/share/fonts/userfonts
```
将需要安装的字体文件复制到上述文件夹，然后

**执行安装命令**

```sh
mkfontdir
mkfontscale

fc-cache -fv  #刷新系统字体缓存

```

**确认安装是否成功**

```sh
fc-list | grep userfonts
```
![](https://s2.ax1x.com/2019/01/18/kpvPDP.png)

如图所示，可以看到在指定目录下的字体已经安装成功！

**卸载字体**

删除相应字体文件，刷新系统字体缓存即可

**查看已安装字体**
```sh
fc-list :lang=zh # 查看支持中文的字体
```
