## ImageMagick

### 图片转换

转换图片为指定分辨率。
```bash
convert -resize 1920x1080 src.jpg dst.jpg
```
又：按百分比转换大小。
```bash
convert -resize 50%x50% src.jpg dst.jpg
```
又：忽略原始宽高比。
```bash
convert -resize 300x300! src.jpg dst.jpg
```
又：多张图片合成 gif。
```bash
magick DSC_52{01..09}.JPG out.gif
magick -delay 10 *.jpg out.gif  # 指定每张持续 10ms
```
又：更改分辨率。
```bash
mogrify -resize 50%x50% *.jpg  # 所有 jpg 缩放至 50%，不加百分号默认单位 px（像素）
```
又，裁切 gif。
```bash
#
magick c.gif -coalesce -repage 0x0 -crop 600x600+175+0 +repage output.gif
```

```bash
convert input.gif -coalesce -repage 0x0 -crop WxH+X+Y +repage output.gif
```
- Animated gifs are often optimised to save space, but imagemagick doesn't seem to consider this when applying the crop command and treats each frame individually. -coalesce rebuilds the full frames.
- Other commands will take into consideration the offset information supplied in the original gif, so you need to force that to be reset with -repage 0x0.
- The crop itself is straightforward, with width, height, x offset and y offset supplied respectively. For example, a crop 40 wide and 30 high at an x offset of 50 = 40x30+50+0.
- Crop does not remove the canvas that it snipped from the image. Applying +repage after the crop will do this.

cf. https://stackoverflow.com/a/14036766