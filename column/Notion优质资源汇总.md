# Notion优质资源汇总-同步更新中

Notion这款笔记软件这两年可谓是非常的火爆，凭借着其独特的风格硬是在众多笔记软件中脱颖而出，甚至直接夺走了曾经笔记软件“教父”之称的**印象笔记**的光环。

即使目前Notion仍不支持中文版，也不影响它优秀的使用体验，也无法撼动这款软件在国人心中的地位，不过有消息称Notion中文界面已经在灰度测试了，相信在不久的将来就会和大家见面，如果英文界面对你来说用起来比较困难的话，那么还是有其他办法来汉化的。

## Notion汉化

### 方式一：网页版插件汉化（推荐）

这里优先推荐大家使用微软的Edge浏览器，当然对于动手能力比较强的小伙伴可以考虑谷歌Chrome浏览器，毕竟谷歌浏览器才是正统，谷歌Chrome应用商店对大部分小伙伴来说是无法正常使用的，小编这里以微软Edge浏览器为例，打开Microsoft插件市场直接搜索**Notion中文版**点击→**获取**即可。

![image-20220525220933570](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220525220933570.png)

当然你也可以先在浏览器中安装一个油猴Tamper monkey插件然后在油猴里面直接搜索notion汉化选择相应的汉化脚本点击安装即可。

### 方式二：Windows客户端汉化

1. **鼠标右键属性查看Notion 的安装目录：`C:\\Users\\用户名（以实际为准）\\AppData\\Local\\Programs\\Notion\\`**
2. **打开`C:\\Users\\用户名（以实际为准）\\AppData\\Local\\Programs\\Notion\\resources\\app\\renderer`文件夹**
3. **下载 `notion-zh_CN.js` （下载链接https://wwt.lanzouq.com/iy9vq059wz5c）到notion-zh_CN-main文件夹中的`notion-zh_CN.js`文件夹放入到（第2步中的renderer文件夹中）**
4. **打开renderer文件夹中的 `preload.js` 文件**
5. **在最后一行加上**

```java
//# sourceMappingURL=preload.js.map
 require("./notion-zh_CN")// 添加该行
```

其实小编觉得只要英语基础过关，不用汉化也不影响使用体验，总共也就那么几个单词用个几天就熟练了。

## Notion插件及工具推荐

1. **Save to Notion（插件）：网页裁剪。**
2. **Notion+Mark Manager（插件）：标注文章。**
3. **Notion PDF Export（插件）：导出pdf笔记。**
4. **[Snackthis.co](http://Snackthis.co)（网站）：类似幻灯片播放。**
5. **[indify.co](http://indify.co)（网站）：notion页面小组件，玩法非常丰富。**
6. **简悦（插件）：功能非常丰富，支持用户多种操作，比如，稍后阅读，收藏，个人感觉其剪藏功能比官方出品的剪藏功能还要强大，小伙伴可以试一试。**
7. **Notion Boost（插件）同样也是一款功能强大的插件，特别是它的右菜单栏悬浮功能，喜欢目录悬浮的小伙伴可以尝试一下这一款插件，当然还有其他自定义的功能，可以说功能非常的丰富。**
8. **super.so网站：将你的Notion页面直接转换成一个网站，以最低的技术成本让你有用一个属于自己的网站。**

如果小伙伴有比较推荐的Notion资源可以在评论区留言哦，好让更多的人看见并且用到你推荐的好产品，让更多的读者收益。毕竟该公众号的宗旨就是：免费分享，好东西不私藏！