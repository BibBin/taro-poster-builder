# taro-vue3-poster-builder
基于taro3.x+vue3的小程序端海报生成器

## 概述
taro-vue3-poster-builder 基于taro3.x+vue3的微信小程序 canvas 绘图组件，封装了常用的操作, 通过配置的方式绘制海报。


## 生成效果
<img width="300" src="https://github.com/BibBin/taro-poster-builder/blob/main/src/image/test_poster.png"></img> 

## 安装使用

### 下载代码

直接通过 git 下载 taro-poster-builder 源代码，并将`src/component/PosterBuilder`目录拷贝到自己的项目的 `src/component`目录中

#### 使用组件

```javascript
import PosterBuilder from '../../components/PosterBuilder/index.vue';

```


### 使用注意事项

1. 图片的域名**务必**添加到 downloadFile 合法域名中（开发设置-服务器域名-downloadFile合法域名）
【P.s 开发时可 选中不校验合法域名、web-view（业务域名）、TLS 版本以及 HTTPS 证书】
【P.s 真机运行，可打开调试模式】

## 组件参数解释

### config字段

| 字段            | 类型                     | 必填 | 描述                                       |
| --------------- | ------------------------ | ---- | ------------------------------------------ |
| width           | Number(单位:rpx)         | 是   | 画布宽度                                   |
| height          | Number(单位:rpx)         | 是   | 画布高度                                   |
| backgroundColor | String                   | 否   | 画布颜色                                   |
| debug           | Boolean                  | 否   | false隐藏canvas，true显示canvas，默认false |
| blocks          | Object Array（对象数组） | 否   | 看下文                                     |
| texts           | Object Array（对象数组） | 否   | 看下文                                     |
| images          | Object Array（对象数组） | 否   | 看下文                                     |
| lines           | Object Array（对象数组） | 否   | 看下文   |
### blocks字段

| 字段名             | 类型             | 必填 | 描述                                   |
| ---------------   | ---------------- | ---- | -------------------------------------- |
| x                 | Number(单位:rpx) | 是   | 块的坐标                               |
| y                 | Number(单位:rpx) | 是   | 块的坐标                               |
| width             | Number(单位:rpx) | 否   | 如果内部有文字，由文字宽度和内边距决定 |
| height            | Number(单位:rpx) | 是   |                                        |
| paddingLeft       | Number(单位:rpx) | 否   | 内左边距                               |
| paddingRight      | Number(单位:rpx) | 否   | 内右边距                               |
| borderWidth       | Number(单位:rpx) | 否   | 边框宽度                               |
| borderColor       | String           | 否   | 边框颜色                               |
| backgroundColor   | String           | 否   | 背景颜色                               |
| borderRadius      | Number(单位:rpx) | 否   | 圆角                                   |
| borderRadiusGroup ｜ Number[]        | 否   | 圆角数组                                |
| text              | Object           | 否   | 块里面可以填充文字，参考texts字段解释  |
| zIndex            | Int              | 否   | 层级，越大越高                         |

### texts字段

| 字段名         | 类型             | 必填 | 描述                                                         |
| -------------- | ---------------- | ---- | ------------------------------------------------------------ |
| x              | Number(单位:rpx) | 是   | 坐标                                                         |
| y              | Number(单位:rpx) | 是   | 坐标                                                         |
| text           | String\|Object   | 是   | 当Object类型时，参数为text字段的参数，marginLeft、marginRight这两个字段可用（示例请看下文） |
| fontSize       | Number(单位:rpx) | 是   | 文字大小                                                     |
| color          | String           | 否   | 颜色                                                         |
| opacity        | Int              | 否   | 1为不透明，0为透明                                           |
| lineHeight     | Number(单位:rpx) | 否   | 行高                                                         |
| lineNum        | Int              | 否   | 根据宽度换行，最多的行数                                     |
| width          | Number(单位:rpx) | 否   | 没有指定为画布宽度                                           |
| marginLeft     | Number(单位:rpx) | 否   | 当text字段为Object可以使用，用来控制多行文字间距             |
| marginRight    | Number(单位:rpx) | 否   | 当text字段为Object可以使用，用来控制多行文字间距             |
| textDecoration | String           | 否   | 目前只支持 line-through（贯穿线），默认为none                |
| baseLine       | String           | 否   | top\| middle\|bottom基线对齐方式                             |
| textAlign      | String           | 否   | left\|center\|right对齐方式                                  |
| zIndex         | Int              | 否   | 层级，越大越高                                               |
| fontFamily     | String           | 否   | 小程序默认字体为'sans-serif', 请输入小程序支持的字体，例如：'STSong' |
| fontWeight     | String           | 否   | 'bold'加粗字体，目前小程序不支持 100 - 900 加粗            |
| fontStyle      | String           | 否   | 'italic'倾斜字体                                          |

### images字段

| 字段              | 类型             | 必填 | 描述                                      |
| ------------      | ---------------- | ---- | ----------------------------------------- |
| x                 | Number(单位:rpx) | 是   | 右上角的坐标                              |
| y                 | Number(单位:rpx) | 是   | 右上角的坐标                              |
| url               | String           | 是   | 图片url（**需要添加到下载白名单域名中**）也支持本地图片 |
| width             | Number(单位:rpx) | 是   | 宽度（**会根据图片的尺寸同比例缩放**）    |
| height            | Number(单位:rpx) | 是   | 高度（**会根据图片的尺寸同比例缩放**）    |
| borderRadius      | Number(单位:rpx) | 否   | 圆角，跟css一样                           |
| borderRadiusGroup ｜ Number[]        | 否   | 圆角数组                                |
| borderWidth       | Number(单位:rpx) | 否   | 边框宽度                                  |
| borderColor       | String           | 否   | 边框颜色                                  |
| zIndex            | Int              | 否   | 层级，越大越高                            |

### lines字段

| 字段   | 类型             | 必填 | 描述           |
| ------ | ---------------- | ---- | -------------- |
| startX | Number(单位:rpx) | 是   | 起始坐标       |
| startY | Number(单位:rpx) | 是   | 起始坐标       |
| endX   | Number(单位:rpx) | 是   | 终结坐标       |
| endY   | Number(单位:rpx) | 是   | 终结坐标       |
| width  | Number(单位:rpx) | 是   | 线的宽度       |
| color  | String           | 否   | 线的颜色       |
| zIndex | Int              | 否   | 层级，越大越高 |


### 问题反馈

有什么问题可以直接提issue


### 参考
- [taro-plugin-canvas](https://github.com/chuyun/taro-plugin-canvas)
- [wxa-plugin-canvas](https://github.com/jasondu/wxa-plugin-canvas)
- [轻松生成小程序分享海报](https://juejin.cn/post/6844903663840788493)
