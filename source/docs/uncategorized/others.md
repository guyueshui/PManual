## Aria2c

[aria2c](https://aria2.github.io/) 是个好东西。支持连接，磁力，种子下载。轻量且强大，可直接使用，也可作为服务端，配合 WebUI 使用。

- 配置：参考 [aria2 配置示例](https://binux.blog/2012/12/aria2-examples/)
- WebUI:
  - [YAAW](http://binux.github.io/yaaw/demo/)
  - [ziahamza](https://ziahamza.github.io/webui-aria2/#)
  - [AriaNg](http://ariang.mayswind.net/latest/)

Note: jsonrpc 地址格式为 `http://token:<rpc-secret>@hostname:port/jsonrpc`
令牌填写自己设置的 `rpc-secret`
![](https://i.loli.net/2018/12/28/5c263327c4214.png)

`xxx` 替换为自己设置的 `rpc-secret`
![](https://i.loli.net/2018/12/28/5c263398854c3.png)

## MPV

[MPV](https://mpv.io) 是一个轻量、简约、跨平台的播放器。据我自己体验，在 Linux 下比 mplayer 播放效果要好，mplayer 倍速会掉帧，而 mpv 则不太明显。

## HTML

给网页添加 BGM。
```html
<embed src="bgm.mp3" autostart="true" loop="true" width="300" height="20" hidden="true">
```
又，添加可以控制播放的音乐。
```html
<audio autoplay="autoplay" controls="controls" loop="loop" preload="auto" src="music.mp3">Your browser doesn't support H5 audio flag!</audio>
```
