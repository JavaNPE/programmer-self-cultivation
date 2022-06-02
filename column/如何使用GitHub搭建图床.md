# 如何使用GitHub搭建图床

昨天，看了我公众号分享图床搭建教程文章的一个小绿妹突然发消息给我说

“茶哥，你之前分享的腾讯云COS竟然是收费的呀，第一天竟然扣了我0.04个软妹子，这一个月下来岂不要扣我大几十块，这一年下来岂不一套眼霜就没有了~~呜呜”

不得不说这小绿妹好会过日子，毕竟对大多数的我们来说，有免费的一律不付费才是处世哲学。

那么今天给大家分享一款超级nice而且免费的图床管理工具——GitHub，号称全球最大同性交友网站，这也是目前国内为数不多的能与世界各地的老外交流互动的一个平台。而且强烈建议阅读这篇文章的理工科的男同学们，特别是学计算机相关专业的，一定要学会使用GitHub，毕竟也是国内为数不多拥有良好社区氛围的开源社区，里面有非常多优质的开源项目和资源。

当然有些同学会认为自己搭建非常繁琐，不就自己写写文章及记笔记用不着这么麻烦。其实不然，如果你只是本地写写文章，记录一下课堂笔记的话Notion完全够用，但是如果你有写公众号或写博客的需求，图床一定非常适合你，因为这套写作系统可以节省写作导入图片的时间。

茶哥，一般都是用Typora写好文章，直接复制粘贴到公众号中，通过图床可以将图片随着文字粘贴到公众号中，不用再一一导入图片。

> 前提条件

需要注册一个GitHub账号，然后下载Typora和PicGo，可以参考上期文章【附链接】

账号注册好之后，鼠标选中右上角的”加号“点击『New repository』新建一个仓库，然后设置一个自己仓库的名字，添加仓库描述，选择Public，需要的话添加一个Readme文件，用好了可以对自己的这个仓库加分哦，基础信息和笔者一样就可以了，其他默认即可，然后鼠标点击下方绿色『Create repository』按钮。

![image-20220601131108131](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220601131108131.png)

这样我们的仓库就算是创建好了，接着也就是最重要的一步，创建并生成一个唯一的tokens，可以简单的理解成这是一把仓库的钥匙，点击右上角头像，选择并进入一下层级目录Settings/Developer settings/Personal access tokens，笔者之前有创建过，第一次创建的小伙伴点击图片中的Generate new token直接选择生成一个新的token即可。不过要记得随机复制一份到自己本地文件中保存，否则token一闪而过，将不在显示，为了方便起见，动动发财的小手保存一下生成的tokens。

![image-20220601132643865](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220601132643865.png)



![image-20220601143530430](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220601143530430.png)



![image-20220601143726381](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220601143726381.png)

```java
<![pic1](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220601132643865.png),![pic2](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220601143530430.png),![pic3](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220601143726381.png)>
```



由于众所周知的原因GitHub在国内的访问速度并不是很快，不过没关系，只需要简单的配置cdn加速即可解决访问速度的问题，可以在设置自定义域名这一栏中添加加速域名”**https://cdn.jsdelivr.net/gh/用户名/仓库名**“提高响应速度。



![image-20220601192751710](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220601192751710.png)



GitHub图床到这一步算是搭建好了，可以参考上一篇文章【文章链接】打开Typora进行验证，以上就是使用GitHub配置图床的教程了，是不是很简单，最重要的使用Github是全程免费的。







