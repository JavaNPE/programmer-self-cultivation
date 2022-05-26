# 如何提高写作效率？请拿好这份指南！基于腾讯云COS+PicGo图床搭建教程

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/%E5%9B%BE%E5%BA%8A%E6%95%99%E7%A8%8B.png)

> 什么是图床？

图：图片，床：睡觉的地方。单从字面上来说可以简单的理解成图片睡觉的地方，但是这张床要比传统的床要舒服的多。

传统的方式我们是将图片保存在电脑本地也好，OneDrive也罢，通过图床我们可以在创作的过程中将用到的图片素材直接通过PicGo软件上传到我们自己的服务器上面，使用体验极好。图片不在依赖于本地文件，而是以网络连接的方式呈现出跨平台的特点。

> 图床可以解决什么创作痛点？

工欲善其事，必先利其器，传统的写作模式一般都是将创作过程中使用到的图片存储在电脑本地，然后将图片从本地复制粘贴到Word也好，Typora也罢，或者是Notion，那么当你在网络其他平台上分发文章的时候也需要重复这类操作，简直就是在浪费宝贵的时间。

在一个良好的写作工具体系的加持下，不仅增加你写作的体验，还可以提高写作效率，如果你有过多平台网络创作的经历，特别是写一些技术博客的时候，比如CSDN（PS：正经人谁在CSDN写作，都是分发）在各个平台分发文章的时候需要重新导入图片，可以说是一件非常繁琐的事情。那么今天这期教程将会手把手教你从0开始搭建你的写作工具体系。

笔者目前使用的是Typora这款最基础的写作软件，平时会写一些技术类的文章分发到github平台。

> 写作工具推荐

排名不分先后，根据个人喜欢择优选择，笔者这里使用VS Code和Notion 为主，Typora辅而助之。

1. VS Code：编程人士必备Coding软件
2. Notion【关联上一篇文章】：上手难度比较大
3. Obsidian：上手难度比较大
4. Typora：熟悉Markdown语法，上手比较简单
5. FlowUS：一般般
6. Wolai：国产版的Notion

> 需要用到的软件

1. cosbrowser
2. PicGo
3. Typora

> 整体流程

注册腾讯云账号→配置对象存储功能→下载并配置PicGo→下载并配置Typora。

# 1、注册腾讯云账号

注册腾讯云账号已经有的可以忽略这一步，选择云产品，基础存储服务中的对象存储功能，点击进入。

![image-20220526163019780](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526163019780.png)

在「存储桶列表」里「创建存储桶」，新用户还有50GB的免费存储服务。由于是第一次登录腾讯云账号，所以有些配置还需要重新设置一下，下面我们需要点击不得不同意否则用不了按钮。同意协议之后点击进入控制台。

![image-20220526163120568](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526163120568.png)

点击「创建存储桶」，这个呢就是用来存放我们的文件的，并设置访问权限，可说是非常的重要，关乎这你的存储对象的安全，自己保存好就可以了，千万不要对外公布。

![image-20220526163536136](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526163536136.png)



这里需要自己设置一个名称，访问权限这里推荐选择「公有读私有写」，默认警告，点击「下一步」创建，其他配置默认即可。

![image-20220526163743215](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526163743215.png)

创建完成后，点击「配置管理」，因为后面添加配置信息的时候需要里面的数据。

![image-20220526163911723](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526163911723.png)

记录下这里的「**存储桶名称**」和「**访问域名**」。

存储桶名称

```sql
hedi****a-1312***060
```

访问域名

```sql
https://hedi****a-1312***060.cos.ap-shanghai.myqcloud.com
```

接着在「云产品」「API 密钥管理」里新建密钥，并记录下此时的密钥 id 和 key。

![image-20220526164254970](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526164254970.png)

下载COSBrowers工具：

[COSBrowser - 常用工具 - 对象存储 - 控制台 (tencent.com)](https://console.cloud.tencent.com/cos/cosbrowser)

![image-20220526164348401](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526164348401.png)



# 2、下载并配置**PicGo**

由于国内下载PicGo速度非常慢，笔者给大家推荐一个镜像地址下载速度相当可以，可以尝试一下。

下载地址：[Index of /github-release/Molunerfinn_PicGo/v2.3.0/ (sdu.edu.cn)](https://mirrors.sdu.edu.cn/github-release/Molunerfinn_PicGo/v2.3.0/)

如笔者这里使用的是Windows 64位系统，点击`[PicGo-Setup-2.3.0-x64.exe](https://mirrors.sdu.edu.cn/github-release/Molunerfinn_PicGo/v2.3.0/PicGo-Setup-2.3.0-x64.exe)` 即可，下载安装完成之后打开PicGo，点击PicGo设置，根据个人需要进行一些简单设置，也可以和我设置一样。

![image-20220526164558760](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526164558760.png)





腾讯云配置对照表，在「API 密钥管理」中填写记录下的密钥 **APPID，SecretId** 和 **SecretKey**。非常重要。

```json
{
  "secretId": "",
  "secretKey": "",
  "bucket": "", // 存储桶名称（也就是PicGO中的「设定存储空间名」），v4和v5版本不一样
  "appId": "",
  "area": "", // 存储区域，例如ap-beijing-1
  "path": "", // 自定义存储路径，比如img/
  "customUrl": "", // 自定义域名，注意要加http://或者https://
  "version": "v5" | "v4" // COS版本，v4或者v5
}
```

注意选择COS的版本一点要和自己注册的腾讯云COS的版本一直，否则无法登录，笔者这里推荐选择V5版本，点击确定按钮之前记得将腾讯云COS设为默认图床。如果有小伙伴设置完之后提示上传失败的提示信息，记得核对COS的版本，一下配置信息是否和腾讯云中的配置是否一致。

![image-20220526164652031](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526164652031.png)

至此，我们专属的图床算是搭建好了，PicGo支持很多中常见的图床，感兴趣的可以多次尝试以下，然后选择一个你个人用起来比较舒服的那个就可以了。

# 3、Typora配置PicGo

笔者这里使用的是Typora这款Markdown文本编辑软件，颜值简约美观，使用便携，可以说非常的经典，码起字来相当舒服，Markdown语法也是非常的简单，用起来不知道比Word强多少！如果你还没使用过这款软件，强烈建议下载一个，体验一段时间，保证你会爱不释手。

打开下载好的「Typora」点击左上角「文件」「图像」插入图片时选择「上传图片」并应用对应规则，然后在「上传服务」选择「PicGo（app）」，并选择本地电脑PicGo的安装位置，如下图，配置好之后，点击「验证图片上传选项」稍等一会提示验证成功。

![image-20220526164717825](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220526164717825.png)



那么我们以后在Typora中写作使用到的图片就会通过PicGo直接同步到你的腾讯云的对象存储中了，这样你在其他平台使用图片素材等就非常的简单了，直接复制粘贴就可以了，从此告别繁琐的图片导入工作，最后在配合Github代码托管平台，我们的写作体系就算是搭建好了。

