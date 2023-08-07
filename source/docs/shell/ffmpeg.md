Ffmpeg
======

转视频为 gif
-----------

```bash
ffmpeg -i input.mkv out.gif
```
又：加速播放。假设原视频 60 fps，输出降到 30 fps，丢掉一半的帧。
```sh
ffmpeg -r 60 -i input.mkv -r 30 out.gif
```
又：不想丢帧。将输入扩大一倍，输出保留原样。
```sh
ffmpeg -r 120 -i input.mkv -r 60 out.gif
```
又：不想全转。从视频的第 2 秒开始，截取 3 秒转化为 gif。
```sh
## 从视频中第二秒开始，截取时长为 3 秒的片段转化为 gif
ffmpeg -t 3 -ss 00:00:02 -i input.mkv out-clip.gif
```
又：控制转化质量。
```sh
## 默认转化是中等质量模式，若要转化出高质量的 gif，可以修改比特率
ffmpeg -i input.mkv -b 2048k out.gif
```

VOB 转 MP4
----------

cf. http://www.ruhuamtv.com/thread-9782-1-1.html
```bash
ffmpeg -i 源视频.vob -c:v libx264 -vf yadif -crf 18 目标视频.mp4
```
又：合并 VOB 文件。
```bash
cat VTS_01_1.VOB VTS_01_2.VOB | ffmpeg -y -i - -fflags genpts -vcodec copy -acodec copy ../output.VOB
```
又：合并 mp4 文件。
see: https://www.tais3.com/others/983.html
```bash
#! /bin/bash
# 将 mp4 文件封装为 ts 格式
ffmpeg -i a1.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 1.ts
ffmpeg -i a2.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 2.ts
ffmpeg -i a3.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 3.ts
ffmpeg -i a4.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 4.ts
# 拼接 ts 并导出最终 mp4 文件
ffmpeg -i "concat:1.ts|2.ts|3.ts|4.ts" -acodec copy -vcodec copy -absf aac_adtstoasc output.mp4
# 删除过程中生成的 ts 文件
rm *.ts
```

参考：[FFMPEG 合并视频文件（无损）](https://www.tais3.com/others/983.html)

TS 转 MP4
---------

```bash
ffmpeg -y -i your_file.ts -vcodec copy -acodec copy -map 0:v -map 0:a your_file.mp4
```
cf. 

1. https://www.reddit.com/r/ffmpeg/comments/ggmjep/comment/fq2m1ux/?utm_source=share&utm_medium=web2x&context=3
2. https://askubuntu.com/a/716457

分辨率、码率、帧率相关介绍，及相关压缩方法：

1. https://blog.csdn.net/weixin_30536861/article/details/114496746
2. https://zhuanlan.zhihu.com/p/255042580

写给新手的指令：https://gnu-linux.readthedocs.io/zh/latest/Chapter04/00_ffmpeg.basic.html

Reference
---------

- [linux 下视频转 gif](https://kxp55.coding.me/2017/11/23/Linux%E4%B8%8B%E8%A7%86%E9%A2%91%E8%BD%ACgif/)