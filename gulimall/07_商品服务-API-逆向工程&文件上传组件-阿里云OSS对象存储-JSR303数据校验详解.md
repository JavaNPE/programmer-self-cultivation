# 07_商品服务-API-品牌管理使用逆向工程的前后端代码 | 文件上传|阿里云OSS对象存储|JSR303数据校验

准备工作：

- [ ]  1、使用VSCode启动renren-fast-vue（`npm run dev`）记得启动虚拟机然后才能启动renren-fast
- [ ]  2、启动nacos客户端；
- [ ]  3、renren-fast，gulimall-product，gulimall-gateway等微服务

# 59、商品服务-API-品牌管理使用逆向工程的前后端代码 | pms_brand表

新增菜单：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142124.png)

将逆向生成的那两个文件通过复制粘贴到VSCode对应的目录层级下，

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142125.png)

在VSCode**终端面板**键入`Ctrl+C` 快捷键，停止当前服务，然后使用命令：`npm run dev`重启动**renren-fast-vue服务**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142127.png)

因为**新增**和**批量删除**功能需要权限才有，为了方便，我们先将权限问题暂时搁置一边（将权限关掉）调出**新增**和**批量删除**功能：`**index.js**` 位置 `src\utils\index.js` 先注释掉那一行代码。

```bash
/**
 * 是否有权限
 * @param {*} key
 */
export function isAuth (key) {
  // return JSON.parse(sessionStorage.getItem('permissions') || '[]').indexOf(key) !== -1 || false
  return true
}
```

![调出新增和批量删除功能之后的效果](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142128.png)

调出新增和批量删除功能之后的效果

# 60、商品服务-API-品牌管理-效果优化与快速显开关

## 关掉语法检查

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142129.png)

`webpack.base.conf.js` 文件的地址`build\webpack.base.conf.js` ，重启项目（Ctrl+C断开连接），`npm run dev` 启动项目。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142130.png)

## 参考的组件：自定义列模板

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/table)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142131.png)

```bash
<template slot-scope="scope">
        <i class="el-icon-time"></i>
        <span style="margin-left: 10px">{{ scope.row.date }}</span>
      </template>
```

## Switch 开关组件：

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/switch)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142132.png)

```bash
<el-switch
  v-model="value"
  active-color="#13ce66"
  inactive-color="#ff4949">
</el-switch>
```

## 文件上传组件：Upload 上传

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/upload)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142133.png)

```bash
<el-upload
  class="upload-demo"
  action="https://jsonplaceholder.typicode.com/posts/"
  :on-preview="handlePreview"
  :on-remove="handleRemove"
  :before-remove="beforeRemove"
  multiple
  :limit="3"
  :on-exceed="handleExceed"
  :file-list="fileList">
  <el-button size="small" type="primary">点击上传</el-button>
  <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
</el-upload>
```

Events：change switch 状态发生变化时的回调函数

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142134.png)

`scope.row` 代表是当前行的数据.

## 这节课代码：

- `brand.vue` ：`src\views\modules\product\brand.vue`
  
    ```bash
    <template>
      <div class="mod-config">
        <el-form
          :inline="true"
          :model="dataForm"
          @keyup.enter.native="getDataList()"
        >
          <el-form-item>
            <el-input
              v-model="dataForm.key"
              placeholder="参数名"
              clearable
            ></el-input>
          </el-form-item>
          <el-form-item>
            <el-button @click="getDataList()">查询</el-button>
            <el-button
              v-if="isAuth('product:brand:save')"
              type="primary"
              @click="addOrUpdateHandle()"
              >新增</el-button
            >
            <el-button
              v-if="isAuth('product:brand:delete')"
              type="danger"
              @click="deleteHandle()"
              :disabled="dataListSelections.length <= 0"
              >批量删除</el-button
            >
          </el-form-item>
        </el-form>
        <el-table
          :data="dataList"
          border
          v-loading="dataListLoading"
          @selection-change="selectionChangeHandle"
          style="width: 100%"
        >
          <el-table-column
            type="selection"
            header-align="center"
            align="center"
            width="50"
          >
          </el-table-column>
          <el-table-column
            prop="brandId"
            header-align="center"
            align="center"
            label="品牌id"
          >
          </el-table-column>
          <el-table-column
            prop="name"
            header-align="center"
            align="center"
            label="品牌名"
          >
          </el-table-column>
          <el-table-column
            prop="logo"
            header-align="center"
            align="center"
            label="品牌logo地址"
          >
          </el-table-column>
          <el-table-column
            prop="descript"
            header-align="center"
            align="center"
            label="介绍"
          >
          </el-table-column>
          <el-table-column
            prop="showStatus"
            header-align="center"
            align="center"
            label="显示状态"
          >
            <template slot-scope="scope">
              <el-switch
                v-model="scope.row.showStatus"
                active-color="#13ce66"
                inactive-color="#ff4949"
                :active-value="1"
                :inactive-value="0"
                @change="updateBrandStatus(scope.row)"
              >
              </el-switch>
            </template>
          </el-table-column>
          <el-table-column
            prop="firstLetter"
            header-align="center"
            align="center"
            label="检索首字母"
          >
          </el-table-column>
          <el-table-column
            prop="sort"
            header-align="center"
            align="center"
            label="排序"
          >
          </el-table-column>
          <el-table-column
            fixed="right"
            header-align="center"
            align="center"
            width="150"
            label="操作"
          >
            <template slot-scope="scope">
              <el-button
                type="text"
                size="small"
                @click="addOrUpdateHandle(scope.row.brandId)"
                >修改</el-button
              >
              <el-button
                type="text"
                size="small"
                @click="deleteHandle(scope.row.brandId)"
                >删除</el-button
              >
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
          @size-change="sizeChangeHandle"
          @current-change="currentChangeHandle"
          :current-page="pageIndex"
          :page-sizes="[10, 20, 50, 100]"
          :page-size="pageSize"
          :total="totalPage"
          layout="total, sizes, prev, pager, next, jumper"
        >
        </el-pagination>
        <!-- 弹窗, 新增 / 修改 -->
        <add-or-update
          v-if="addOrUpdateVisible"
          ref="addOrUpdate"
          @refreshDataList="getDataList"
        ></add-or-update>
      </div>
    </template>
    
    <script>
    import AddOrUpdate from "./brand-add-or-update";
    export default {
      data() {
        return {
          dataForm: {
            key: "",
          },
          dataList: [],
          pageIndex: 1,
          pageSize: 10,
          totalPage: 0,
          dataListLoading: false,
          dataListSelections: [],
          addOrUpdateVisible: false,
        };
      },
      components: {
        AddOrUpdate,
      },
      activated() {
        this.getDataList();
      },
      methods: {
        // 获取数据列表
        getDataList() {
          this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/brand/list"),
            method: "get",
            params: this.$http.adornParams({
              page: this.pageIndex,
              limit: this.pageSize,
              key: this.dataForm.key,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataList = data.page.list;
              this.totalPage = data.page.totalCount;
            } else {
              this.dataList = [];
              this.totalPage = 0;
            }
            this.dataListLoading = false;
          });
        },
        updateBrandStatus(data) {
          console.log("最新信息", data);
          let { brandId, showStatus } = data;
          //发送请求修改状态
          this.$http({
            url: this.$http.adornUrl("/product/brand/update"),
            method: "post",
            data: this.$http.adornData({ brandId, showStatus }, false),
          }).then(({ data }) => {
            this.$message({
              type: "success",
              message: "状态更新成功",
            });
          });
        },
        // 每页数
        sizeChangeHandle(val) {
          this.pageSize = val;
          this.pageIndex = 1;
          this.getDataList();
        },
        // 当前页
        currentChangeHandle(val) {
          this.pageIndex = val;
          this.getDataList();
        },
        // 多选
        selectionChangeHandle(val) {
          this.dataListSelections = val;
        },
        // 新增 / 修改
        addOrUpdateHandle(id) {
          this.addOrUpdateVisible = true;
          this.$nextTick(() => {
            this.$refs.addOrUpdate.init(id);
          });
        },
        // 删除
        deleteHandle(id) {
          var ids = id
            ? [id]
            : this.dataListSelections.map((item) => {
                return item.brandId;
              });
          this.$confirm(
            `确定对[id=${ids.join(",")}]进行[${id ? "删除" : "批量删除"}]操作?`,
            "提示",
            {
              confirmButtonText: "确定",
              cancelButtonText: "取消",
              type: "warning",
            }
          ).then(() => {
            this.$http({
              url: this.$http.adornUrl("/product/brand/delete"),
              method: "post",
              data: this.$http.adornData(ids, false),
            }).then(({ data }) => {
              if (data && data.code === 0) {
                this.$message({
                  message: "操作成功",
                  type: "success",
                  duration: 1500,
                  onClose: () => {
                    this.getDataList();
                  },
                });
              } else {
                this.$message.error(data.msg);
              }
            });
          });
        },
      },
    };
    </script>
    ```
    
- `brand-add-or-update.vue`：`src\views\modules\product\brand-add-or-update.vue`
  
    ```bash
    <template>
      <el-dialog
        :title="!dataForm.brandId ? '新增' : '修改'"
        :close-on-click-modal="false"
        :visible.sync="visible">
        <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="140px">
        <el-form-item label="品牌名" prop="name">
          <el-input v-model="dataForm.name" placeholder="品牌名"></el-input>
        </el-form-item>
        <el-form-item label="品牌logo地址" prop="logo">
          <el-input v-model="dataForm.logo" placeholder="品牌logo地址"></el-input>
        </el-form-item>
        <el-form-item label="介绍" prop="descript">
          <el-input v-model="dataForm.descript" placeholder="介绍"></el-input>
        </el-form-item>
        <el-form-item label="显示状态" prop="showStatus">
          <el-switch v-model="dataForm.showStatus" active-color="#13ce66" inactive-color="#ff4949"> </el-switch>
        </el-form-item>
        <el-form-item label="检索首字母" prop="firstLetter">
          <el-input v-model="dataForm.firstLetter" placeholder="检索首字母"></el-input>
        </el-form-item>
        <el-form-item label="排序" prop="sort">
          <el-input v-model="dataForm.sort" placeholder="排序"></el-input>
        </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="visible = false">取消</el-button>
          <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
        </span>
      </el-dialog>
    </template>
    
    <script>
      export default {
        data () {
          return {
            visible: false,
            dataForm: {
              brandId: 0,
              name: '',
              logo: '',
              descript: '',
              showStatus: '',
              firstLetter: '',
              sort: ''
            },
            dataRule: {
              name: [
                { required: true, message: '品牌名不能为空', trigger: 'blur' }
              ],
              logo: [
                { required: true, message: '品牌logo地址不能为空', trigger: 'blur' }
              ],
              descript: [
                { required: true, message: '介绍不能为空', trigger: 'blur' }
              ],
              showStatus: [
                { required: true, message: '显示状态[0-不显示；1-显示]不能为空', trigger: 'blur' }
              ],
              firstLetter: [
                { required: true, message: '检索首字母不能为空', trigger: 'blur' }
              ],
              sort: [
                { required: true, message: '排序不能为空', trigger: 'blur' }
              ]
            }
          }
        },
        methods: {
          init (id) {
            this.dataForm.brandId = id || 0
            this.visible = true
            this.$nextTick(() => {
              this.$refs['dataForm'].resetFields()
              if (this.dataForm.brandId) {
                this.$http({
                  url: this.$http.adornUrl(`/product/brand/info/${this.dataForm.brandId}`),
                  method: 'get',
                  params: this.$http.adornParams()
                }).then(({data}) => {
                  if (data && data.code === 0) {
                    this.dataForm.name = data.brand.name
                    this.dataForm.logo = data.brand.logo
                    this.dataForm.descript = data.brand.descript
                    this.dataForm.showStatus = data.brand.showStatus
                    this.dataForm.firstLetter = data.brand.firstLetter
                    this.dataForm.sort = data.brand.sort
                  }
                })
              }
            })
          },
          // 表单提交
          dataFormSubmit () {
            this.$refs['dataForm'].validate((valid) => {
              if (valid) {
                this.$http({
                  url: this.$http.adornUrl(`/product/brand/${!this.dataForm.brandId ? 'save' : 'update'}`),
                  method: 'post',
                  data: this.$http.adornData({
                    'brandId': this.dataForm.brandId || undefined,
                    'name': this.dataForm.name,
                    'logo': this.dataForm.logo,
                    'descript': this.dataForm.descript,
                    'showStatus': this.dataForm.showStatus,
                    'firstLetter': this.dataForm.firstLetter,
                    'sort': this.dataForm.sort
                  })
                }).then(({data}) => {
                  if (data && data.code === 0) {
                    this.$message({
                      message: '操作成功',
                      type: 'success',
                      duration: 1500,
                      onClose: () => {
                        this.visible = false
                        this.$emit('refreshDataList')
                      }
                    })
                  } else {
                    this.$message.error(data.msg)
                  }
                })
              }
            })
          }
        }
      }
    </script>
    ```
    

# 61、商品服务-API-品牌管理-云存储开通与使用

## 文件存储的几种方式：1.自建服务器 FastDFS | 2.云存储：阿里云OSS

文件存储的几种方式：

1. **自建服务器：FastDFS** 和 **vsftpd**；特点：搭建复杂，维护成本高，前期费用高。
2. **云存储：[阿里云对象存储](https://www.aliyun.com/product/oss?spm=5176.19720258.J_8058803260.132.e9392c4aE8D7cU)** 和 **七牛云存储**； 特点：即开即用，无需维护，按量收费。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142135.png)

[简介](https://help.aliyun.com/document_detail/31947.html?spm=5176.8465980.help.dexternal.375b1450mA8zKv)

阿里云对象存储OSS相关介绍

### 阿里云对象存储OSS相关术语

一般推荐一个项目创建1个**Bucket**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142136.png)

### 阿里云对象存储OSS官网：

[Login](https://oss.console.aliyun.com/overview)

### 新建一个Bucket 列表：

本教程大致设置内容：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142137.png)

### 上传文件

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142138.png)

复制URL链接即可访问：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142139.png)

### 阿里云对象存储-普通上传方式 （不推荐）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142140.png)

### 阿里云对象存储-服务端签名后直传（推荐使用）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142141.png)

## 方式一：文件上传Java SDK方式（不推荐）

[安装](https://help.aliyun.com/document_detail/32009.html?spm=a2c4g.11186623.6.768.549d59aaWuZMGJ)

进行OSS各类操作前，您需要先安装Java SDK。本文提供了Java SDK的多种安装方式，请结合实际使用场景选用。

### 安装SDK：您可以通过以下三种方式安装SDK。

方式一：

方式一：在Maven项目中加入依赖项（推荐方式）

在Maven工程中使用OSS Java SDK，只需在pom.xml中加入相应依赖即可。以3.10.2版本为例，在<dependencies>中加入如下内容：

在本项目中gulimall-product服务的POM文件中添加以下内容

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.10.2</version>
</dependency>
```

如果使用的是Java 9及以上的版本，则需要添加jaxb相关依赖。添加jaxb相关依赖示例代码如下：

```xml
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.1</version>
</dependency>
<dependency>
    <groupId>javax.activation</groupId>
    <artifactId>activation</artifactId>
    <version>1.1.1</version>
</dependency>
<!-- no more than 2.3.3-->
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
    <version>2.3.3</version>
</dependency>
```

---

- 方式二：在Eclipse项目中导入JAR包
  
    以3.10.2版本为例，步骤如下：
    
    1. 下载[Java SDK 开发包](https://gosspublic.alicdn.com/sdks/java/aliyun_java_sdk_3.10.2.zip)。
    2. 解压该开发包。
    3. 将解压后文件夹中的文件aliyun-sdk-oss-3.10.2.jar以及lib文件夹下的所有文件拷贝到您的项目中。
    4. 在Eclipse中选择您的工程，右键选择**Properties > Java Build Path > Add JARs**。
    5. 选中拷贝的所有JAR文件，导入到Libraries中。
- 方式三：在IntelliJ IDEA项目中导入JAR包
  
    以3.10.2版本为例，步骤如下：
    
    1. 下载[Java SDK 开发包](https://gosspublic.alicdn.com/sdks/java/aliyun_java_sdk_3.10.2.zip)。
    2. 解压该开发包。
    3. 将解压后文件夹中的文件aliyun-sdk-oss-3.10.2.jar以及lib文件夹下的所有JAR文件拷贝到您的项目中。
    4. 在IntelliJ IDEA中选择您的工程，右键选择**File > Project Structure > Modules > Dependencies > + > JARs or directories** 。
    5. 选中拷贝的所有JAR文件，导入到External Libraries中。

### 上传文件流

以下代码用于将文件流上传到目标存储空间`examplebucket`中`exampledir`目录下的`exampleobject.txt`文件。

```java
// yourEndpoint填写Bucket所在地域对应的Endpoint。以华东1（杭州）为例，Endpoint填写为https://oss-cn-hangzhou.aliyuncs.com。
String endpoint = "[yourEndpoint](https://www.notion.so/07_-API-OSS-JSR303-ede1dc92894947919d72bd7e88623cbf)";
// 阿里云账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM用户进行API访问或日常运维，请登录RAM控制台创建RAM用户。
String accessKeyId = "yourAccessKeyId";
String accessKeySecret = "yourAccessKeySecret";

// 创建OSSClient实例。
OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

// 填写本地文件的完整路径。如果未指定本地路径，则默认从示例程序所属项目对应本地路径中上传文件流。
InputStream inputStream = new FileInputStream("D:\\localpath\\examplefile.txt");
// 依次填写Bucket名称（例如examplebucket）和Object完整路径（例如exampledir/exampleobject.txt）。Object完整路径中不能包含Bucket名称。
ossClient.putObject("examplebucket", "exampledir/exampleobject.txt", inputStream);

// 关闭OSSClient。
ossClient.shutdown();
```

Endpoint（地域节点）：[`oss-cn-beijing.aliyuncs.com`](http://oss-cn-beijing.aliyuncs.com/)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142142.png)

### 阿里云**创建**并使用RAM用户

阿里云账号AccessKey拥有所有API的访问权限，风险很高。**强烈建议**您**创建**并使用RAM用户进行API访问或日常运维，请登录RAM控制台创建RAM用户。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142143.png)

### 开始使用子用户AccessKey

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142144.png)

### 创建用户：开启Open API调用访问

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142145.png)

创建用户完毕后，会得到一个“AccessKey ID”和“AccessKeySecret”，然后复制这两个值到代码的`“AccessKey ID”`和`“AccessKeySecret”`。

另外还需要添加访问控制权限：

### AccessKey ID 和 AccessKey Secret

千万复制哦 要不然出去就没了。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142146.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142147.png)

下面代码的信息可以通过如下查找：

`endpoint的取值`：点击概览就可以看到你的endpoint信息，endpoint在这里就是上海等地区，如 [`oss-cn-qingdao.aliyuncs.com`](http://oss-cn-qingdao.aliyuncs.com/)
`bucket域名：`就是签名加上bucket，[如`gulimall-fermhan.oss-cn-qingdao.aliyuncs.com`](http://xn--gulimall-fermhan-rh03a.oss-cn-qingdao.aliyuncs.com/)

GulimallProductApplicationTests 单元测试

```java
package com.atguigu.gulimall.product;

import com.aliyun.oss.OSS;
import com.aliyun.oss.OSSClientBuilder;
import com.atguigu.gulimall.product.entity.BrandEntity;
import com.atguigu.gulimall.product.service.BrandService;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import sun.net.www.content.image.png;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.InputStream;
import java.util.List;

@RunWith(SpringRunner.class)
@SpringBootTest
public class GulimallProductApplicationTests {
    @Autowired
    BrandService brandService;

    @Test
    public void testUpload() throws FileNotFoundException {
        // yourEndpoint填写Bucket所在地域对应的Endpoint。以华东1（杭州）为例，Endpoint填写为https://oss-cn-hangzhou.aliyuncs.com。
        String endpoint = "oss-cn-beijing.aliyuncs.com";

        // 阿里云账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM用户进行API访问或日常运维，请登录RAM控制台创建RAM用户。
        String accessKeyId = "LTAI5tQJe43xkmKionyVWfkH";
        String accessKeySecret = "NYJesMLZJcVdFvhVx5bjEPw20vBe2z";

        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

        // 填写本地文件的完整路径。如果未指定本地路径，则默认从示例程序所属项目对应本地路径中上传文件流。
        InputStream inputStream = new FileInputStream("D:\\FFOutput\\2.png");
        // 依次填写Bucket名称（例如examplebucket）和Object完整路径（例如exampledir/exampleobject.txt）。Object完整路径中不能包含Bucket名称。
        ossClient.putObject("gulimall-dali", "2.png", inputStream);

        // 关闭OSSClient。
        ossClient.shutdown();

        System.out.println("上传完成...");
    }

    @Test
    public void contextLoads() {
        BrandEntity brandEntity = new BrandEntity();
//        brandEntity.setBrandId(6L);
//        brandEntity.setDescript("华为天下第一");
//        brandService.updateById(brandEntity);

        /*
        brandEntity.setName("华为");
        brandService.save(brandEntity);
        System.out.println("保存成功.....");
        */

        List<BrandEntity> list = brandService.list(new QueryWrapper<BrandEntity>().eq("brand_id", 1L));
//        for (BrandEntity entity : list) {
//            System.out.println(entity);
//        }
        list.forEach((item) -> {
            System.out.println(item);
        });
    }
}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142148.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142149.png)

测试运行，将本地2.png文件上传到阿里云

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142150.png)

## 方式二：文件上传使用SpringCloud Alibaba来管理OSS （推荐）

### Alibaba Cloud OSS Example官网案例：

[aliyun-spring-boot/aliyun-spring-boot-samples/aliyun-oss-spring-boot-sample at master · alibaba/aliyun-spring-boot](https://github.com/alibaba/aliyun-spring-boot/tree/master/aliyun-spring-boot-samples/aliyun-oss-spring-boot-sample)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142151.png)

1、在`gulimall-common` 项目中的`pom.xml` 添加以下内容：

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>aliyun-oss-spring-boot-starter</artifactId>
</dependency>
---------------------------------------------------------
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alicloud-oss</artifactId>
    <version>2.2.0.RELEASE</version>
</dependency>
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142152.png)

2、配置“AccessKey ID”和“AccessKeySecret”和endpoint

```yaml
// application.properties
alibaba.cloud.access-key=your-ak
alibaba.cloud.secret-key=your-sk
alibaba.cloud.oss.endpoint=***
```

`gulimall-product` 中的application.yml

```yaml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.56.10:3306/gulimall_pms
    driver-class-name: com.mysql.jdbc.Driver
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
    alicloud:
      access-key: LTAI5tQJe43xkmKionyVWfkH
      secret-key: NYJesMLZJcVdFvhVx5bjEPw20vBe2z
      oss:
        endpoint: oss-cn-beijing.aliyuncs.com

............................................
```

3、使用`OSSClient ossClient`

```java
@Service
 public class YourService {
 	**@Autowired
 	private OSSClient ossClient;**

 	public void saveFile() {
 		// download file to local
 		ossClient.getObject(new GetObjectRequest(bucketName, objectName), new File("pathOfYourLocalFile"));
 	}
 }
```

- 测试：gulimall-product：`GulimallProductApplicationTests`
  
    ```java
    package com.atguigu.gulimall.product;
    
    import com.aliyun.oss.OSS;
    import com.aliyun.oss.OSSClient;
    import com.aliyun.oss.OSSClientBuilder;
    import com.atguigu.gulimall.product.entity.BrandEntity;
    import com.atguigu.gulimall.product.service.BrandService;
    import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
    
    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.test.context.junit4.SpringRunner;
    import sun.net.www.content.image.png;
    
    import java.io.FileInputStream;
    import java.io.FileNotFoundException;
    import java.io.InputStream;
    import java.util.List;
    
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class GulimallProductApplicationTests {
        @Autowired
        BrandService brandService;
    
        **@Autowired
        OSSClient ossClient;**
        @Test
        public void testUpload() throws FileNotFoundException {
    
    */*        // yourEndpoint填写Bucket所在地域对应的Endpoint。以华东1（杭州）为例，Endpoint填写为https://oss-cn-hangzhou.aliyuncs.com。
            String endpoint = "oss-cn-beijing.aliyuncs.com";
    
            // 阿里云账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM用户进行API访问或日常运维，请登录RAM控制台创建RAM用户。
            String accessKeyId = "LTAI5tQJe43xkmKionyVWfkH";
            String accessKeySecret = "NYJesMLZJcVdFvhVx5bjEPw20vBe2z";
    
            // 创建OSSClient实例。
            OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);*/*
    
            // 填写本地文件的完整路径。如果未指定本地路径，则默认从示例程序所属项目对应本地路径中上传文件流。
            InputStream inputStream = new FileInputStream("D:\\FFOutput\\4.png");
            // 依次填写Bucket名称（例如examplebucket）和Object完整路径（例如exampledir/exampleobject.txt）。Object完整路径中不能包含Bucket名称。
            ossClient.putObject("gulimall-dali", "4.png", inputStream);
    
            // 关闭OSSClient。
            ossClient.shutdown();
    
            System.out.println("上传完成...");
        }
    
        @Test
        public void contextLoads() {
            BrandEntity brandEntity = new BrandEntity();
    //        brandEntity.setBrandId(6L);
    //        brandEntity.setDescript("华为天下第一");
    //        brandService.updateById(brandEntity);
    
            /*
            brandEntity.setName("华为");
            brandService.save(brandEntity);
            System.out.println("保存成功.....");
            */
    
            List<BrandEntity> list = brandService.list(new QueryWrapper<BrandEntity>().eq("brand_id", 1L));
    //        for (BrandEntity entity : list) {
    //            System.out.println(entity);
    //        }
            list.forEach((item) -> {
                System.out.println(item);
            });
        }
    }
    ```
    

但是这样来做还是比较麻烦，如果以后的上传任务都交给gulimall-product来完成，显然耦合度高。最好单独新建一个Module来完成文件上传任务。

# 63、商品服务-API-品牌管理- OSS获取服务端签名 | 新建gulimall-third-party微服务

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142153.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142154.png)

添加依赖，将原来`gulimall-common`中的“`spring-cloud-starter-alicloud-oss`”依赖移动到`gulimall-third-party`该项目中pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.atguigu.gulimall</groupId>
    <artifactId>gulimall-third-party</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>gulimall-third-party</name>
    <description>第三方服务</description>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>2020.0.3</spring-cloud.version>
    </properties>
    <dependencies>
        **<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alicloud-oss</artifactId>
            <version>2.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>com.atguigu.gulimall</groupId>
            <artifactId>gulimall-common</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>**
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            **<dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>**
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

`gulimall-third-party` 新建`bootstrap.properties`

1、在nacos创建命名空间“ `third-party`”

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142156.png)

在配置列表中`third-party` 命名空间下新建如下配置

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142157.png)

```yaml
-------------------------------------oss.yml-注意缩进-----------------------------------
spring:
  cloud:
    alicloud:
      access-key: LTAI5tQJe43xkmKionyVWfkH
      secret-key: NYJesMLZJcVdFvhVx5bjEPw20vBe2z
      oss:
        endpoint: oss-cn-beijing.aliyuncs.com
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142158.png)

在 `gulimall-third-party` 服务下新建`application.yml` 文件，配置nacos服务注册与发现,，将gulimall-third-party注册到nacos中

```yaml
spring:
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
  application:
    name: gulimall-third-party
server:
  port: 30000
```

同时在`gulimall-third-party` 新建`bootstrap.properties` 文件，读取nacos配置列表中`third-party`命名空间下的`oss.yml`文件

```yaml
spring.application.name=gulimall-third-party
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
spring.cloud.nacos.config.namespace=ca79b464-08a0-4cc9-b26e-4491d80b493b

spring.cloud.nacos.config.ext-config[0].data-id=oss.yml
spring.cloud.nacos.config.ext-config[0].group=DEFAULT_GROUP
spring.cloud.nacos.config.ext-config[0].refresh=true

#spring.cloud.nacos.config.name=gulimall-third-party
```

在`gulimall-third-party` 的test测试类中进行测试：

- GulimallThirdPartyApplicationTests
  
    ```java
    package com.atguigu.gulimall.thirdparty;
    
    import com.aliyun.oss.OSSClient;
    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    import org.springframework.test.context.junit4.SpringRunner;
    
    import java.io.FileInputStream;
    import java.io.FileNotFoundException;
    import java.io.InputStream;
    
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class GulimallThirdPartyApplicationTests {
    
        @Test
        public void contextLoads() {
        }
    
        @Autowired
        OSSClient ossClient;
    
        @Test
        public void testUpload() throws FileNotFoundException {
    
    /*        // yourEndpoint填写Bucket所在地域对应的Endpoint。以华东1（杭州）为例，Endpoint填写为https://oss-cn-hangzhou.aliyuncs.com。
            String endpoint = "oss-cn-beijing.aliyuncs.com";
    
            // 阿里云账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM用户进行API访问或日常运维，请登录RAM控制台创建RAM用户。
            String accessKeyId = "LTAI5tQJe43xkmKionyVWfkH";
            String accessKeySecret = "NYJesMLZJcVdFvhVx5bjEPw20vBe2z";
    
            // 创建OSSClient实例。
            OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);*/
    
            // 填写本地文件的完整路径。如果未指定本地路径，则默认从示例程序所属项目对应本地路径中上传文件流。
            InputStream inputStream = new FileInputStream("D:\\FFOutput\\8.png");
            // 依次填写Bucket名称（例如examplebucket）和Object完整路径（例如exampledir/exampleobject.txt）。Object完整路径中不能包含Bucket名称。
            ossClient.putObject("gulimall-dali", "8.png", inputStream);
    
            // 关闭OSSClient。
            ossClient.shutdown();
    
            System.out.println("上传完成...");
        }
    }
    ```
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142159.png)
    

上面的逻辑中，我们的想法是先把字节流给服务器，服务器给阿里云，还是传到了服务器。我们需要一些前端代码完成这个功能，字节流就别来服务器了

## （文件上传）谷粒商城项目中使用-阿里云对象存储-服务端签名后直传的方式

### 使用Web端上传数据至OSS——对象存储 OSS

[Web端上传数据至OSS](https://help.aliyun.com/document_detail/31924.html)

Web端上传数据至OSS

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142160.png)

### 服务端签名后直传——对象存储 OSS

[服务端签名后直传](https://help.aliyun.com/document_detail/31926.html)

阿里云对象存储-服务端签名后直传

![image-20220606214407913](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062144069.png)

流程介绍：流程如下图所示：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142162.png)

## 以Java语言为例，讲解在服务端通过Java代码完成签名，并且设置上传回调，然后通过表单直传数据到OSS。

教程地址：

[Java](https://help.aliyun.com/document_detail/91868.htm?spm=a2c4g.11186623.0.0.16072214CFZKd7#concept-ahk-rfz-2fb)

### **应用服务器核心代码解析**

应用服务器源码包含了签名直传服务以及上传回调服务的完整示例代码。以下仅提供核心代码片段，如需了解这两个功能的完整实现，请参见[应用服务器源码（Java版本）](https://gosspublic.alicdn.com/doc/91868/aliyun-oss-appserver-java-master.zip)。

- 签名直传服务
  
    签名直传服务响应客户端发送给应用服务器的GET消息，代码片段如下：
    
- 以下是代码片段
  
    ```java
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
    
            String accessId = "<yourAccessKeyId>"; // 请填写您的AccessKeyId。
            String accessKey = "<yourAccessKeySecret>"; // 请填写您的AccessKeySecret。
            String endpoint = "oss-cn-hangzhou.aliyuncs.com"; // 请填写您的 endpoint。
            String bucket = "bucket-name"; // 请填写您的 bucketname 。
            String host = "https://" + bucket + "." + endpoint; // host的格式为 bucketname.endpoint
            // callbackUrl为上传回调服务器的URL，请将下面的IP和Port配置为您自己的真实信息。
            String callbackUrl = "http://88.88.88.88:8888";
            String dir = "user-dir-prefix/"; // 用户上传文件时指定的前缀。
    
            // 创建OSSClient实例。
            OSS ossClient = new OSSClientBuilder().build(endpoint, accessId, accessKey);
            try {
                long expireTime = 30;
                long expireEndTime = System.currentTimeMillis() + expireTime * 1000;
                Date expiration = new Date(expireEndTime);
                // PostObject请求最大可支持的文件大小为5 GB，即CONTENT_LENGTH_RANGE为5*1024*1024*1024。
                PolicyConditions policyConds = new PolicyConditions();
                policyConds.addConditionItem(PolicyConditions.COND_CONTENT_LENGTH_RANGE, 0, 1048576000);
                policyConds.addConditionItem(MatchMode.StartWith, PolicyConditions.COND_KEY, dir);
    
                String postPolicy = ossClient.generatePostPolicy(expiration, policyConds);
                byte[] binaryData = postPolicy.getBytes("utf-8");
                String encodedPolicy = BinaryUtil.toBase64String(binaryData);
                String postSignature = ossClient.calculatePostSignature(postPolicy);
    
                Map<String, String> respMap = new LinkedHashMap<String, String>();
                respMap.put("accessid", accessId);
                respMap.put("policy", encodedPolicy);
                respMap.put("signature", postSignature);
                respMap.put("dir", dir);
                respMap.put("host", host);
                respMap.put("expire", String.valueOf(expireEndTime / 1000));
                // respMap.put("expire", formatISO8601Date(expiration));
    
                JSONObject jasonCallback = new JSONObject();
                jasonCallback.put("callbackUrl", callbackUrl);
                jasonCallback.put("callbackBody",
                        "filename=${object}&size=${size}&mimeType=${mimeType}&height=${imageInfo.height}&width=${imageInfo.width}");
                jasonCallback.put("callbackBodyType", "application/x-www-form-urlencoded");
                String base64CallbackBody = BinaryUtil.toBase64String(jasonCallback.toString().getBytes());
                respMap.put("callback", base64CallbackBody);
    
                JSONObject ja1 = JSONObject.fromObject(respMap);
                // System.out.println(ja1.toString());
                response.setHeader("Access-Control-Allow-Origin", "*");
                response.setHeader("Access-Control-Allow-Methods", "GET, POST");
                response(request, response, ja1.toString());
    
            } catch (Exception e) {
                // Assert.fail(e.getMessage());
                System.out.println(e.getMessage());
            } finally { 
                ossClient.shutdown();
            }
        }
    ```
    

参照上面的代码片段案例，来编写我们本地“`com.atguigu.gulimall.gulimall-third-party.controller.OssController`”类：

- `[OssController.java](http://osscontroller.java)`
  
    ```java
    package com.atguigu.gulimall.thirdparty.controller;
    
    import com.aliyun.oss.OSS;
    import com.aliyun.oss.OSSClient;
    import com.aliyun.oss.OSSClientBuilder;
    import com.aliyun.oss.common.utils.BinaryUtil;
    import com.aliyun.oss.model.MatchMode;
    import com.aliyun.oss.model.PolicyConditions;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.text.SimpleDateFormat;
    import java.util.Date;
    import java.util.LinkedHashMap;
    import java.util.Map;
    
    /**
     * @Author Dali
     * @Date 2021/8/25 12:54
     * @Version 1.0
     * @Description
     */
    @RestController
    public class OssController {
        @Autowired
        OSS ossClient;
    
        @Value("${spring.cloud.alicloud.oss.endpoint}")
        private String endpoint;
    
        @Value("${spring.cloud.alicloud.oss.bucket}")
        private String bucket;
    
        @Value("${spring.cloud.alicloud.access-key}")
        private String accessId;
    
        @RequestMapping("/oss/policy")
        public Map<String, String> policy() {
           /* String accessId = "<yourAccessKeyId>"; // 请填写您的AccessKeyId。
            String accessKey = "<yourAccessKeySecret>"; // 请填写您的AccessKeySecret。
            String endpoint = "oss-cn-hangzhou.aliyuncs.com"; // 请填写您的 endpoint。*/
    
            //参考阿里云OSS文件地址格式：https://gulimall-dali.oss-cn-beijing.aliyuncs.com/2.png
    //        String bucket = "gulimall-dali"; // 请填写您的 bucketname 。
    
            String host = "https://" + bucket + "." + endpoint; // host的格式为 bucketname.endpoint
            // callbackUrl为上传回调服务器的URL，请将下面的IP和Port配置为您自己的真实信息。
    //        String callbackUrl = "http://88.88.88.88:8888";
    
            //阿里云OSS对象存储//Bucket列表//gulimall-dali//文件管理上传文件时我们以日期的方式创建文件夹供用户上传
            String format = new SimpleDateFormat("yyyy-MM-dd").format(new Date());
    //        String dir = "user-dir-prefix/"; // 用户上传文件时指定的前缀。
            String dir = format + "/"; // 用户上传文件时指定的前缀。
    
    //        // 创建OSSClient实例。
    //        OSS ossClient = new OSSClientBuilder().build(endpoint, accessId, accessKey);
            Map<String, String> respMap = null;
            try {
                long expireTime = 30;
                long expireEndTime = System.currentTimeMillis() + expireTime * 1000;
                Date expiration = new Date(expireEndTime);
                // PostObject请求最大可支持的文件大小为5 GB，即CONTENT_LENGTH_RANGE为5*1024*1024*1024。
                PolicyConditions policyConds = new PolicyConditions();
                policyConds.addConditionItem(PolicyConditions.COND_CONTENT_LENGTH_RANGE, 0, 1048576000);
                policyConds.addConditionItem(MatchMode.StartWith, PolicyConditions.COND_KEY, dir);
    
                String postPolicy = ossClient.generatePostPolicy(expiration, policyConds);
                byte[] binaryData = postPolicy.getBytes("utf-8");
                String encodedPolicy = BinaryUtil.toBase64String(binaryData);
                String postSignature = ossClient.calculatePostSignature(postPolicy);
    
                respMap = new LinkedHashMap<String, String>();
                respMap.put("accessid", accessId);
                respMap.put("policy", encodedPolicy);
                respMap.put("signature", postSignature);
                respMap.put("dir", dir);
                respMap.put("host", host);
                respMap.put("expire", String.valueOf(expireEndTime / 1000));
                // respMap.put("expire", formatISO8601Date(expiration));
    
    //            JSONObject jasonCallback = new JSONObject();
    //            jasonCallback.put("callbackUrl", callbackUrl);
    //            jasonCallback.put("callbackBody",
    //                    "filename=${object}&size=${size}&mimeType=${mimeType}&height=${imageInfo.height}&width=${imageInfo.width}");
    //            jasonCallback.put("callbackBodyType", "application/x-www-form-urlencoded");
    //            String base64CallbackBody = BinaryUtil.toBase64String(jasonCallback.toString().getBytes());
    //            respMap.put("callback", base64CallbackBody);
    //
    //            JSONObject ja1 = JSONObject.fromObject(respMap);
    //            // System.out.println(ja1.toString());
    //            response.setHeader("Access-Control-Allow-Origin", "*");
    //            response.setHeader("Access-Control-Allow-Methods", "GET, POST");
    //            response(request, response, ja1.toString());
    
            } catch (Exception e) {
                // Assert.fail(e.getMessage());
                System.out.println(e.getMessage());
            } finally {
                ossClient.shutdown();
            }
            return respMap;
        }
    }
    ```
    

上面的意思是说用户通过url请求得到一个policy，要拿这个东西直接传到阿里云，不要去服务器了

- `application.yml：`gulimall-third-party
  
    ```yaml
    spring:
      cloud:
        nacos:
          discovery:
            server-addr: 127.0.0.1:8848
        alicloud:
          access-key: LTAI5tQJe43xkmKionyVWfkH
          secret-key: NYJesMLZJcVdFvhVx5bjEPw20vBe2z
          oss:
            endpoint: oss-cn-beijing.aliyuncs.com
            bucket: gulimall-dali
      application:
        name: gulimall-third-party
    server:
      port: 30000
    ```
    
- 测试： [http://localhost:30000/oss/policy](http://localhost:30000/oss/policy) 返回签名
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142163.png)
    

在该微服务中测试通过，但是我们不能对外暴露端口或者说为了统一管理，我们还是让用户请求网关然后转发过来，以后在上传文件时的访问路径为[http://localhost:88/api/thirdparty/oss/policy](http://localhost:88/api/thirdparty/oss/policy)

在“**gulimall-gateway**”中`application.yml`配置路由规则：

将[http://localhost:30000/oss/policy](http://localhost:30000/oss/policy) 转换为： [http://localhost:88/api/thirdparty/oss/policy](http://localhost:88/api/thirdparty/oss/policy)

```yaml
- id: third_party_route
          uri: lb://gulimall-third-party
          predicates:
            - Path=/api/thirdparty/**     #只要是/api/thirdparty这些前缀的 就自动路由给第三方服务gulimall-third-party
          filters:
            - RewritePath=/api/thirdparty/(?<segment>/?.*),/$\{segment}
```

测试是否能够正常跳转： [http://localhost:88/api/thirdparty/oss/policy](http://localhost:88/api/thirdparty/oss/policy)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142164.png)

# 64、商品服务-API-品牌管理-OSS前后端联调测试**文件上传**

放置项目提供的upload文件夹到`components/`目录下，**一个是单文件上传**，**另外一个是多文件上传**

`policy.js`封装一个`Promise`，发送`/thirdparty/oss/policy`请求。vue项目会自动加上api前缀
`multiUpload.vue`多文件上传。要改，改方式如下
`singleUpload.vue`单文件上传。要替换里面的action中的内容。action=“[http://gulimall-fermhan.oss-cn-qingdao.aliyuncs.com](http://gulimall-fermhan.oss-cn-qingdao.aliyuncs.com/)”
![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142165.png)

`multiUpload.vue`多文件上传和`singleUpload.vue`单文件上传中均更改：

```bash
action="http://gulimall-dali.oss-cn-beijing.aliyuncs.com"
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142166.png)

要修改vue项目中心品牌logo地址，要改成下面形式：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142167.png)

brand-add-or-update.vue中，修改**el-form-item label="品牌logo地址"**内容。
要使用文件上传组件，先导入`import SingleUpload from “@/components/upload/singleUpload”;`
填入`<single-upload v-model="dataForm.logo"></single-upload>`
写明要使用的组件`components: { SingleUpload },`

- brand-add-or-update.vue
  
    ```yaml
    <template>
      <el-dialog
        :title="!dataForm.brandId ? '新增' : '修改'"
        :close-on-click-modal="false"
        :visible.sync="visible"
      >
        <el-form
          :model="dataForm"
          :rules="dataRule"
          ref="dataForm"
          @keyup.enter.native="dataFormSubmit()"
          label-width="140px"
        >
          <el-form-item label="品牌名" prop="name">
            <el-input v-model="dataForm.name" placeholder="品牌名"></el-input>
          </el-form-item>
          **<el-form-item label="品牌logo地址" prop="logo">
            <!-- <el-input v-model="dataForm.logo" placeholder="品牌logo地址"></el-input> -->
            <single-upload v-model="dataForm.logo"></single-upload>
          </el-form-item>**
          <el-form-item label="介绍" prop="descript">
            <el-input v-model="dataForm.descript" placeholder="介绍"></el-input>
          </el-form-item>
          <el-form-item label="显示状态" prop="showStatus">
            <el-switch
              v-model="dataForm.showStatus"
              active-color="#13ce66"
              inactive-color="#ff4949"
            >
            </el-switch>
          </el-form-item>
          <el-form-item label="检索首字母" prop="firstLetter">
            <el-input
              v-model="dataForm.firstLetter"
              placeholder="检索首字母"
            ></el-input>
          </el-form-item>
          <el-form-item label="排序" prop="sort">
            <el-input v-model="dataForm.sort" placeholder="排序"></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="visible = false">取消</el-button>
          <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
        </span>
      </el-dialog>
    </template>
    
    <script>
    **import SingleUpload from "@/components/upload/singleUpload";**
    // import singleUpload from '../../../components/upload/singleUpload.vue';
      export default {
      **components: { SingleUpload },**
        data () {
          return {
            visible: false,
            dataForm: {
              brandId: 0,
              name: '',
              logo: '',
              descript: '',
              showStatus: '',
              firstLetter: '',
              sort: ''
            },
            dataRule: {
              name: [
                { required: true, message: '品牌名不能为空', trigger: 'blur' }
              ],
              logo: [
                { required: true, message: '品牌logo地址不能为空', trigger: 'blur' }
              ],
              descript: [
                { required: true, message: '介绍不能为空', trigger: 'blur' }
              ],
              showStatus: [
                { required: true, message: '显示状态[0-不显示；1-显示]不能为空', trigger: 'blur' }
              ],
              firstLetter: [
                { required: true, message: '检索首字母不能为空', trigger: 'blur' }
              ],
              sort: [
                { required: true, message: '排序不能为空', trigger: 'blur' }
              ]
            }
          }
        },
        methods: {
          init (id) {
            this.dataForm.brandId = id || 0
            this.visible = true
            this.$nextTick(() => {
              this.$refs['dataForm'].resetFields()
              if (this.dataForm.brandId) {
                this.$http({
                  url: this.$http.adornUrl(`/product/brand/info/${this.dataForm.brandId}`),
                  method: 'get',
                  params: this.$http.adornParams()
                }).then(({data}) => {
                  if (data && data.code === 0) {
                    this.dataForm.name = data.brand.name
                    this.dataForm.logo = data.brand.logo
                    this.dataForm.descript = data.brand.descript
                    this.dataForm.showStatus = data.brand.showStatus
                    this.dataForm.firstLetter = data.brand.firstLetter
                    this.dataForm.sort = data.brand.sort
                  }
                })
              }
            })
          },
          // 表单提交
          dataFormSubmit () {
            this.$refs['dataForm'].validate((valid) => {
              if (valid) {
                this.$http({
                  url: this.$http.adornUrl(`/product/brand/${!this.dataForm.brandId ? 'save' : 'update'}`),
                  method: 'post',
                  data: this.$http.adornData({
                    'brandId': this.dataForm.brandId || undefined,
                    'name': this.dataForm.name,
                    'logo': this.dataForm.logo,
                    'descript': this.dataForm.descript,
                    'showStatus': this.dataForm.showStatus,
                    'firstLetter': this.dataForm.firstLetter,
                    'sort': this.dataForm.sort
                  })
                }).then(({data}) => {
                  if (data && data.code === 0) {
                    this.$message({
                      message: '操作成功',
                      type: 'success',
                      duration: 1500,
                      onClose: () => {
                        this.visible = false
                        this.$emit('refreshDataList')
                      }
                    })
                  } else {
                    this.$message.error(data.msg)
                  }
                })
              }
            })
          }
        }
      }
    </script>
    ```
    
- singleUpload.vue
  
    ```bash
    <template> 
      <div>
        <el-upload
          **action="http://gulimall-dali.oss-cn-beijing.aliyuncs.com"**
          :data="dataObj"
          list-type="picture"
          :multiple="false" :show-file-list="showFileList"
          :file-list="fileList"
          :before-upload="beforeUpload"
          :on-remove="handleRemove"
          :on-success="handleUploadSuccess"
          :on-preview="handlePreview">
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过10MB</div>
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="fileList[0].url" alt="">
        </el-dialog>
      </div>
    </template>
    <script>
       import {policy} from './policy'
       import { getUUID } from '@/utils'
    
      export default {
        name: 'singleUpload',
        props: {
          value: String
        },
        computed: {
          imageUrl() {
            return this.value;
          },
          imageName() {
            if (this.value != null && this.value !== '') {
              return this.value.substr(this.value.lastIndexOf("/") + 1);
            } else {
              return null;
            }
          },
          fileList() {
            return [{
              name: this.imageName,
              url: this.imageUrl
            }]
          },
          showFileList: {
            get: function () {
              return this.value !== null && this.value !== ''&& this.value!==undefined;
            },
            set: function (newValue) {
            }
          }
        },
        data() {
          return {
            dataObj: {
              policy: '',
              signature: '',
              key: '',
              ossaccessKeyId: '',
              dir: '',
              host: '',
              // callback:'',
            },
            dialogVisible: false
          };
        },
        methods: {
          emitInput(val) {
            this.$emit('input', val)
          },
          handleRemove(file, fileList) {
            this.emitInput('');
          },
          handlePreview(file) {
            this.dialogVisible = true;
          },
          beforeUpload(file) {
            let _self = this;
            return new Promise((resolve, reject) => {
              policy().then(response => {
                **console.log("响应的数据",response);**
                _self.dataObj.policy = response.data.policy;
                _self.dataObj.signature = response.data.signature;
                _self.dataObj.ossaccessKeyId = response.data.accessid;
                **_self.dataObj.key = response.data.dir +getUUID()+'_${filename}';**
                _self.dataObj.dir = response.data.dir;
                _self.dataObj.host = response.data.host;
                **console.log("响应的数据222...",_self.dataObj  );**
                resolve(true)
              }).catch(err => {
                reject(false)
              })
            })
          },
          handleUploadSuccess(res, file) {
            console.log("上传成功...")
            this.showFileList = true;
            this.fileList.pop();
            this.fileList.push({name: file.name, url: this.dataObj.host + '/' + this.dataObj.key.replace("${filename}",file.name) });
            this.emitInput(this.fileList[0].url);
          }
        }
      }
    </script>
    <style>
    
    </style>
    ```
    

启动VSCode：renren-fast-vue（`npm run dev`）启动idea：gulimall-gateway；gulimall-product；gulimall-third-party；renren-fast。启动nacos和Oracle VM VirtualBox虚拟机

点击一下文件上传，发现发送了两个请求

`localhost:88/api/thirdparty/oss/policy?t=1613300654238`
————————————————

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142168.png)

在vue中看是`response.data.policy`，在控制台看`response.policy`。所以去java里面改返回值为R。

- OssController：修改返回值代码
  
    ```java
    package com.atguigu.gulimall.thirdparty.controller;
    
    import com.aliyun.oss.OSS;
    import com.aliyun.oss.OSSClient;
    import com.aliyun.oss.OSSClientBuilder;
    import com.aliyun.oss.common.utils.BinaryUtil;
    import com.aliyun.oss.model.MatchMode;
    import com.aliyun.oss.model.PolicyConditions;
    import com.atguigu.common.utils.R;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.text.SimpleDateFormat;
    import java.util.Date;
    import java.util.LinkedHashMap;
    import java.util.Map;
    
    /**
     * @Author Dali
     * @Date 2021/8/25 12:54
     * @Version 1.0
     * @Description
     */
    @RestController
    public class OssController {
        @Autowired
        OSS ossClient;
    
        @Value("${spring.cloud.alicloud.oss.endpoint}")
        private String endpoint;
    
        @Value("${spring.cloud.alicloud.oss.bucket}")
        private String bucket;
    
        @Value("${spring.cloud.alicloud.access-key}")
        private String accessId;
    
        @RequestMapping("/oss/policy")
    //    public Map<String, String> policy() {
        **public R policy() {**
           /* String accessId = "<yourAccessKeyId>"; // 请填写您的AccessKeyId。
            String accessKey = "<yourAccessKeySecret>"; // 请填写您的AccessKeySecret。
            String endpoint = "oss-cn-hangzhou.aliyuncs.com"; // 请填写您的 endpoint。*/
    
            //参考阿里云OSS文件地址格式：https://gulimall-dali.oss-cn-beijing.aliyuncs.com/2.png
    //        String bucket = "gulimall-dali"; // 请填写您的 bucketname 。
    
            String host = "https://" + bucket + "." + endpoint; // host的格式为 bucketname.endpoint
            // callbackUrl为上传回调服务器的URL，请将下面的IP和Port配置为您自己的真实信息。
    //        String callbackUrl = "http://88.88.88.88:8888";
    
            //阿里云OSS对象存储//Bucket列表//gulimall-dali//文件管理上传文件时我们以日期的方式创建文件夹供用户上传
            String format = new SimpleDateFormat("yyyy-MM-dd").format(new Date());
    //        String dir = "user-dir-prefix/"; // 用户上传文件时指定的前缀。
            String dir = format + "/"; // 用户上传文件时指定的前缀。
    
    //        // 创建OSSClient实例。
    //        OSS ossClient = new OSSClientBuilder().build(endpoint, accessId, accessKey);
            Map<String, String> respMap = null;
            try {
                long expireTime = 30;
                long expireEndTime = System.currentTimeMillis() + expireTime * 1000;
                Date expiration = new Date(expireEndTime);
                // PostObject请求最大可支持的文件大小为5 GB，即CONTENT_LENGTH_RANGE为5*1024*1024*1024。
                PolicyConditions policyConds = new PolicyConditions();
                policyConds.addConditionItem(PolicyConditions.COND_CONTENT_LENGTH_RANGE, 0, 1048576000);
                policyConds.addConditionItem(MatchMode.StartWith, PolicyConditions.COND_KEY, dir);
    
                String postPolicy = ossClient.generatePostPolicy(expiration, policyConds);
                byte[] binaryData = postPolicy.getBytes("utf-8");
                String encodedPolicy = BinaryUtil.toBase64String(binaryData);
                String postSignature = ossClient.calculatePostSignature(postPolicy);
    
                respMap = new LinkedHashMap<String, String>();
                respMap.put("accessid", accessId);
                respMap.put("policy", encodedPolicy);
                respMap.put("signature", postSignature);
                respMap.put("dir", dir);
                respMap.put("host", host);
                respMap.put("expire", String.valueOf(expireEndTime / 1000));
                // respMap.put("expire", formatISO8601Date(expiration));
    
    //            JSONObject jasonCallback = new JSONObject();
    //            jasonCallback.put("callbackUrl", callbackUrl);
    //            jasonCallback.put("callbackBody",
    //                    "filename=${object}&size=${size}&mimeType=${mimeType}&height=${imageInfo.height}&width=${imageInfo.width}");
    //            jasonCallback.put("callbackBodyType", "application/x-www-form-urlencoded");
    //            String base64CallbackBody = BinaryUtil.toBase64String(jasonCallback.toString().getBytes());
    //            respMap.put("callback", base64CallbackBody);
    //
    //            JSONObject ja1 = JSONObject.fromObject(respMap);
    //            // System.out.println(ja1.toString());
    //            response.setHeader("Access-Control-Allow-Origin", "*");
    //            response.setHeader("Access-Control-Allow-Methods", "GET, POST");
    //            response(request, response, ja1.toString());
    
            } catch (Exception e) {
                // Assert.fail(e.getMessage());
                System.out.println(e.getMessage());
            } finally {
                ossClient.shutdown();
            }
    //        return respMap;
            **return R.ok().put("data",respMap);**
        }
    }
    ```
    

## 阿里云OSS开启跨域设置

开始执行上传，但是在上传过程中，出现了跨域请求问题：（从我们的服务去请求oss服务，我们前面说过了，跨域不是浏览器限制了你，而是新的服务器限制的问题，所以得去阿里云设置）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142169.png)

再次执行文件上传。

注意上传时他的key变成了response.data.dir +getUUID()+"_${filename}";

优化：上传后显示图片地址

显示图片：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142170.png)

去阿里云查看，成功上传！！！

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142171.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142172.png)

# 65、商品服务-API-品牌管理表单校验&自定义校验器 ——前端校验

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142173.png)

## 品牌Logo以图片的形式显示

**品牌logo地址栏**我们需要给他做成缩略图的形式，更直观。参照ElementUI的Table组件和Image 图片组件

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/table)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142174.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142175.png)

```bash
<template slot-scope="scope">
        <i class="el-icon-time"></i>
        <span style="margin-left: 10px">{{ scope.row.date }}</span>
</template>
```

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/image)

Image 图片组件

```bash
<el-image
      style="width: 100px; height: 100px"
      :src="url"
      :fit="fit"></el-image>
```

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/quickstart)

- 修改vue项目的element-ui脚手架的问题，没有导入element-ui的image组件：src\element-ui\`index.js`
  
    ```bash
    /**
     * UI组件, 统一使用饿了么桌面端组件库(https://github.com/ElemeFE/element）
     *
     * 使用:
     *  1. 项目中需要的组件进行释放(解开注释)
     *
     * 注意:
     *  1. 打包只会包含释放(解开注释)的组件, 减少打包文件大小
     */
    import Vue from 'vue'
    import {
      Pagination,
      Dialog,
      Autocomplete,
      Dropdown,
      DropdownMenu,
      DropdownItem,
      Menu,
      Submenu,
      MenuItem,
      MenuItemGroup,
      Input,
      InputNumber,
      Radio,
      RadioGroup,
      RadioButton,
      Checkbox,
      CheckboxButton,
      CheckboxGroup,
      Switch,
      Select,
      Option,
      OptionGroup,
      Button,
      ButtonGroup,
      Table,
      TableColumn,
      DatePicker,
      TimeSelect,
      TimePicker,
      Popover,
      Tooltip,
      Breadcrumb,
      BreadcrumbItem,
      Form,
      FormItem,
      Tabs,
      TabPane,
      Tag,
      Tree,
      Alert,
      Slider,
      Icon,
      Row,
      Col,
      Upload,
      Progress,
      Spinner,
      Badge,
      Card,
      Rate,
      Steps,
      Step,
      Carousel,
      CarouselItem,
      Collapse,
      CollapseItem,
      Cascader,
      ColorPicker,
      Transfer,
      Container,
      Header,
      Aside,
      Main,
      Footer,
      Timeline,
      TimelineItem,
      Link,
      Divider,
      Image,
      Calendar,
    
      Loading,
      MessageBox,
      Message,
      Notification
    } from 'element-ui';
    
    Vue.use(Pagination);
    Vue.use(Dialog);
    Vue.use(Autocomplete);
    Vue.use(Dropdown);
    Vue.use(DropdownMenu);
    Vue.use(DropdownItem);
    Vue.use(Menu);
    Vue.use(Submenu);
    Vue.use(MenuItem);
    Vue.use(MenuItemGroup);
    Vue.use(Input);
    Vue.use(InputNumber);
    Vue.use(Radio);
    Vue.use(RadioGroup);
    Vue.use(RadioButton);
    Vue.use(Checkbox);
    Vue.use(CheckboxButton);
    Vue.use(CheckboxGroup);
    Vue.use(Switch);
    Vue.use(Select);
    Vue.use(Option);
    Vue.use(OptionGroup);
    Vue.use(Button);
    Vue.use(ButtonGroup);
    Vue.use(Table);
    Vue.use(TableColumn);
    Vue.use(DatePicker);
    Vue.use(TimeSelect);
    Vue.use(TimePicker);
    Vue.use(Popover);
    Vue.use(Tooltip);
    Vue.use(Breadcrumb);
    Vue.use(BreadcrumbItem);
    Vue.use(Form);
    Vue.use(FormItem);
    Vue.use(Tabs);
    Vue.use(TabPane);
    Vue.use(Tag);
    Vue.use(Tree);
    Vue.use(Alert);
    Vue.use(Slider);
    Vue.use(Icon);
    Vue.use(Row);
    Vue.use(Col);
    Vue.use(Upload);
    Vue.use(Progress);
    Vue.use(Spinner);
    Vue.use(Badge);
    Vue.use(Card);
    Vue.use(Rate);
    Vue.use(Steps);
    Vue.use(Step);
    Vue.use(Carousel);
    Vue.use(CarouselItem);
    Vue.use(Collapse);
    Vue.use(CollapseItem);
    Vue.use(Cascader);
    Vue.use(ColorPicker);
    Vue.use(Transfer);
    Vue.use(Container);
    Vue.use(Header);
    Vue.use(Aside);
    Vue.use(Main);
    Vue.use(Footer);
    Vue.use(Timeline);
    Vue.use(TimelineItem);
    Vue.use(Link);
    Vue.use(Divider);
    Vue.use(Image);
    Vue.use(Calendar);
    
    Vue.use(Loading.directive)
    
    Vue.prototype.$loading = Loading.service
    Vue.prototype.$msgbox = MessageBox
    Vue.prototype.$alert = MessageBox.alert
    Vue.prototype.$confirm = MessageBox.confirm
    Vue.prototype.$prompt = MessageBox.prompt
    Vue.prototype.$notify = Notification
    Vue.prototype.$message = Message
    
    Vue.prototype.$ELEMENT = { size: 'medium' }
    ```
    
- 自定义表格+自定义图片
  
    ```bash
    <el-table-column
            prop="logo"
            header-align="center"
            align="center"
            label="品牌logo地址">
            <template slot-scope="scope">
              <!-- <el-image style="width: 100px; height: 80px" :fit="contain"></el-image> -->
              
               **<!-- 自定义表格+自定义图片 -->
            <img :src="scope.row.logo" style="width: 100px; height: 80px" />**
            </template>
          </el-table-column>
    ```
    

## 表单校验&自定义校验器 ——前端进行校验

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142176.png)

**前端的校验是element-ui表单验证**https://element.eleme.cn/#/zh-CN/component/form

Form 组件提供了表单验证的功能，只需要通过 rules 属性传入约定的验证规则，并将 Form-Item 的 prop 属性设置为需校验的字段名即可。校验规则参见 async-validator

- `brand-add-or-update.vue` **使用自定义校验规则可以解决字母限制的问题**
  
    ```bash
    <template>
      <el-dialog
        :title="!dataForm.brandId ? '新增' : '修改'"
        :close-on-click-modal="false"
        :visible.sync="visible"
      >
        <el-form
          :model="dataForm"
          :rules="dataRule"
          ref="dataForm"
          @keyup.enter.native="dataFormSubmit()"
          label-width="140px"
        >
          <el-form-item label="品牌名" prop="name">
            <el-input v-model="dataForm.name" placeholder="品牌名"></el-input>
          </el-form-item>
          <el-form-item label="品牌logo地址" prop="logo">
            <!-- <el-input v-model="dataForm.logo" placeholder="品牌logo地址"></el-input> -->
            <single-upload v-model="dataForm.logo"></single-upload>
          </el-form-item>
          <el-form-item label="介绍" prop="descript">
            <el-input v-model="dataForm.descript" placeholder="介绍"></el-input>
          </el-form-item>
          <el-form-item label="显示状态" prop="showStatus">
            <el-switch
              v-model="dataForm.showStatus"
              active-color="#13ce66"
              inactive-color="#ff4949"
              **:active-value="1"
              :inactive-value="0"**
            >
            </el-switch>
          </el-form-item>
          <el-form-item label="检索首字母" prop="firstLetter">
            <el-input
              v-model="dataForm.firstLetter"
              placeholder="检索首字母"
            ></el-input>
          </el-form-item>
          <el-form-item label="排序" prop="sort">
            **<el-input v-model.number="dataForm.sort" placeholder="排序"></el-input>**
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="visible = false">取消</el-button>
          <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
        </span>
      </el-dialog>
    </template>
    
    <script>
    import SingleUpload from "@/components/upload/singleUpload";
    // import singleUpload from '../../../components/upload/singleUpload.vue';
    export default {
      components: { SingleUpload },
      data() {
        return {
          visible: false,
          dataForm: {
            brandId: 0,
            name: "",
            logo: "",
            descript: "",
            **showStatus: 1, //显示默认1**
            firstLetter: "",
            **sort: 0, //排序默认0**
          },
          dataRule: {
            name: [{ required: true, message: "品牌名不能为空", trigger: "blur" }],
            logo: [
              { required: true, message: "品牌logo地址不能为空", trigger: "blur" },
            ],
            descript: [
              { required: true, message: "介绍不能为空", trigger: "blur" },
            ],
            showStatus: [
              {
                required: true,
                message: "显示状态[0-不显示；1-显示]不能为空",
                trigger: "blur",
              },
            ],
            **firstLetter: [
              {
                validator: (rule, value, callback) => {
                  if (value == "") {
                    callback(new Error("首字母必须填写"));
                  } else if (!/^[a-zA-Z]$/.test(value)) {
                    callback(new Error("首字母必须a-z或者A-Z之间"));
                  } else {
                    callback();
                  }
                },
                trigger: "blur",
              },
            ],
            sort: [
              {
                validator: (rule, value, callback) => {
                  if (value === "") { //value == "" 这样写有问题，因为 ‘’==0为true, 正确的写法用3个=，value===""
                    callback(new Error("排序字段必须填写"));
                  } else if (!Number.isInteger(value) || value < 0) {
                    callback(new Error("排序必须是一个大于等于0的整数"));
                  } else {
                    callback();
                  }
                },
                trigger: "blur",
              },
            ],**
          },
        };
      },
      methods: {
        init(id) {
          this.dataForm.brandId = id || 0;
          this.visible = true;
          this.$nextTick(() => {
            this.$refs["dataForm"].resetFields();
            if (this.dataForm.brandId) {
              this.$http({
                url: this.$http.adornUrl(
                  `/product/brand/info/${this.dataForm.brandId}`
                ),
                method: "get",
                params: this.$http.adornParams(),
              }).then(({ data }) => {
                if (data && data.code === 0) {
                  this.dataForm.name = data.brand.name;
                  this.dataForm.logo = data.brand.logo;
                  this.dataForm.descript = data.brand.descript;
                  this.dataForm.showStatus = data.brand.showStatus;
                  this.dataForm.firstLetter = data.brand.firstLetter;
                  this.dataForm.sort = data.brand.sort;
                }
              });
            }
          });
        },
        // 表单提交
        dataFormSubmit() {
          this.$refs["dataForm"].validate((valid) => {
            if (valid) {
              this.$http({
                url: this.$http.adornUrl(
                  `/product/brand/${!this.dataForm.brandId ? "save" : "update"}`
                ),
                method: "post",
                data: this.$http.adornData({
                  brandId: this.dataForm.brandId || undefined,
                  name: this.dataForm.name,
                  logo: this.dataForm.logo,
                  descript: this.dataForm.descript,
                  showStatus: this.dataForm.showStatus,
                  firstLetter: this.dataForm.firstLetter,
                  sort: this.dataForm.sort,
                }),
              }).then(({ data }) => {
                if (data && data.code === 0) {
                  this.$message({
                    message: "操作成功",
                    type: "success",
                    duration: 1500,
                    onClose: () => {
                      this.visible = false;
                      this.$emit("refreshDataList");
                    },
                  });
                } else {
                  this.$message.error(data.msg);
                }
              });
            }
          });
        },
      },
    };
    </script>
    ```
    

# 66、商品服务-API-品牌管理- JSR303数据校验——后端校验

问题引入：填写form时应该有前端校验，后端也应该有校验

## ISR303校验步骤

1)、给Bean添加校验注解: javax. val idation. constraints,并定义自己的message提示

2)、开启校验功能`@Valid`

效果:校验错误以后会有默认的响应;

3)、给校验的bean后紧跟一个BindingResult, 就可以获取到校验的结果

## jsr3参数校验器maven依赖

```xml
<!--jsr3参数校验器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
里面依赖了hibernate-validator
```

在非空处理方式上提供了`@NotNull`，`@NotBlank`和`@NotEmpty`

（1）`@NotNull` 该属性**不能为null**

（2）`@NotEmpty` 该字段**不能为null或`""`**

比如后端校验注解：`@NotNull` 关于校验的注解信息都在`package io.renren.modules.oss.cloud;`包中
在IDEA中BrandController类中的save方法添加注解 `@Valid:`告诉SpringMVC我们需要校验

```java
/**
     * 保存
     * @Valid:告诉SpringMVC我们需要校验
     */
    @RequestMapping("/save")
    //@RequiresPermissions("product:brand:save")
    public R save(**@Valid** @RequestBody BrandEntity brand){
		brandService.save(brand);

        return R.ok();
    }
```

BrandEntity实体类中name字段添加**`@NotBlank`注解**

```java
/**
	 * 品牌名
	 */
	**@NotBlank**
	private String name;
```

使用PostMan测试：[http://localhost:88/api/product/brand/save](http://localhost:88/api/product/brand/save)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142177.png)

- 双击Shift：查找 `validationmessages.properties` 定义了发生错误该怎么提示——英文版提示
  
    ```java
    javax.validation.constraints.AssertFalse.message     = must be false
    javax.validation.constraints.AssertTrue.message      = must be true
    javax.validation.constraints.DecimalMax.message      = must be less than ${inclusive == true ? 'or equal to ' : ''}{value}
    javax.validation.constraints.DecimalMin.message      = must be greater than ${inclusive == true ? 'or equal to ' : ''}{value}
    javax.validation.constraints.Digits.message          = numeric value out of bounds (<{integer} digits>.<{fraction} digits> expected)
    javax.validation.constraints.Email.message           = must be a well-formed email address
    javax.validation.constraints.Future.message          = must be a future date
    javax.validation.constraints.FutureOrPresent.message = must be a date in the present or in the future
    javax.validation.constraints.Max.message             = must be less than or equal to {value}
    javax.validation.constraints.Min.message             = must be greater than or equal to {value}
    javax.validation.constraints.Negative.message        = must be less than 0
    javax.validation.constraints.NegativeOrZero.message  = must be less than or equal to 0
    javax.validation.constraints.NotBlank.message        = must not be blank
    javax.validation.constraints.NotEmpty.message        = must not be empty
    javax.validation.constraints.NotNull.message         = must not be null
    javax.validation.constraints.Null.message            = must be null
    javax.validation.constraints.Past.message            = must be a past date
    javax.validation.constraints.PastOrPresent.message   = must be a date in the past or in the present
    javax.validation.constraints.Pattern.message         = must match "{regexp}"
    javax.validation.constraints.Positive.message        = must be greater than 0
    javax.validation.constraints.PositiveOrZero.message  = must be greater than or equal to 0
    javax.validation.constraints.Size.message            = size must be between {min} and {max}
    
    org.hibernate.validator.constraints.CreditCardNumber.message        = invalid credit card number
    org.hibernate.validator.constraints.Currency.message                = invalid currency (must be one of {value})
    org.hibernate.validator.constraints.EAN.message                     = invalid {type} barcode
    org.hibernate.validator.constraints.Email.message                   = not a well-formed email address
    org.hibernate.validator.constraints.ISBN.message                    = invalid ISBN
    org.hibernate.validator.constraints.Length.message                  = length must be between {min} and {max}
    org.hibernate.validator.constraints.CodePointLength.message         = length must be between {min} and {max}
    org.hibernate.validator.constraints.LuhnCheck.message               = the check digit for ${validatedValue} is invalid, Luhn Modulo 10 checksum failed
    org.hibernate.validator.constraints.Mod10Check.message              = the check digit for ${validatedValue} is invalid, Modulo 10 checksum failed
    org.hibernate.validator.constraints.Mod11Check.message              = the check digit for ${validatedValue} is invalid, Modulo 11 checksum failed
    org.hibernate.validator.constraints.ModCheck.message                = the check digit for ${validatedValue} is invalid, ${modType} checksum failed
    org.hibernate.validator.constraints.NotBlank.message                = may not be empty
    org.hibernate.validator.constraints.NotEmpty.message                = may not be empty
    org.hibernate.validator.constraints.ParametersScriptAssert.message  = script expression "{script}" didn't evaluate to true
    org.hibernate.validator.constraints.Range.message                   = must be between {min} and {max}
    org.hibernate.validator.constraints.SafeHtml.message                = may have unsafe html content
    org.hibernate.validator.constraints.ScriptAssert.message            = script expression "{script}" didn't evaluate to true
    org.hibernate.validator.constraints.UniqueElements.message          = must only contain unique elements
    org.hibernate.validator.constraints.URL.message                     = must be a valid URL
    
    org.hibernate.validator.constraints.br.CNPJ.message                 = invalid Brazilian corporate taxpayer registry number (CNPJ)
    org.hibernate.validator.constraints.br.CPF.message                  = invalid Brazilian individual taxpayer registry number (CPF)
    org.hibernate.validator.constraints.br.TituloEleitoral.message      = invalid Brazilian Voter ID card number
    
    org.hibernate.validator.constraints.pl.REGON.message                = invalid Polish Taxpayer Identification Number (REGON)
    org.hibernate.validator.constraints.pl.NIP.message                  = invalid VAT Identification Number (NIP)
    org.hibernate.validator.constraints.pl.PESEL.message                = invalid Polish National Identification Number (PESEL)
    
    org.hibernate.validator.constraints.time.DurationMax.message        = must be shorter than${inclusive == true ? ' or equal to' : ''}${days == 0 ? '' : days == 1 ? ' 1 day' : ' ' += days += ' days'}${hours == 0 ? '' : hours == 1 ? ' 1 hour' : ' ' += hours += ' hours'}${minutes == 0 ? '' : minutes == 1 ? ' 1 minute' : ' ' += minutes += ' minutes'}${seconds == 0 ? '' : seconds == 1 ? ' 1 second' : ' ' += seconds += ' seconds'}${millis == 0 ? '' : millis == 1 ? ' 1 milli' : ' ' += millis += ' millis'}${nanos == 0 ? '' : nanos == 1 ? ' 1 nano' : ' ' += nanos += ' nanos'}
    org.hibernate.validator.constraints.time.DurationMin.message        = must be longer than${inclusive == true ? ' or equal to' : ''}${days == 0 ? '' : days == 1 ? ' 1 day' : ' ' += days += ' days'}${hours == 0 ? '' : hours == 1 ? ' 1 hour' : ' ' += hours += ' hours'}${minutes == 0 ? '' : minutes == 1 ? ' 1 minute' : ' ' += minutes += ' minutes'}${seconds == 0 ? '' : seconds == 1 ? ' 1 second' : ' ' += seconds += ' seconds'}${millis == 0 ? '' : millis == 1 ? ' 1 milli' : ' ' += millis += ' millis'}${nanos == 0 ? '' : nanos == 1 ? ' 1 nano' : ' ' += nanos += ' nanos'}
    ```
    
- 双击Shift：搜索`ValidationMessages_zh_CN.properties` 查找发生错误了中文版提示
  
    ```java
    javax.validation.constraints.AssertFalse.message     = 只能为false
    javax.validation.constraints.AssertTrue.message      = 只能为true
    javax.validation.constraints.DecimalMax.message      = 必须小于或等于{value}
    javax.validation.constraints.DecimalMin.message      = 必须大于或等于{value}
    javax.validation.constraints.Digits.message          = 数字的值超出了允许范围(只允许在{integer}位整数和{fraction}位小数范围内)
    javax.validation.constraints.Email.message           = 不是一个合法的电子邮件地址
    javax.validation.constraints.Future.message          = 需要是一个将来的时间
    javax.validation.constraints.FutureOrPresent.message = 需要是一个将来或现在的时间
    javax.validation.constraints.Max.message             = 最大不能超过{value}
    javax.validation.constraints.Min.message             = 最小不能小于{value}
    javax.validation.constraints.Negative.message        = 必须是负数
    javax.validation.constraints.NegativeOrZero.message  = 必须是负数或零
    javax.validation.constraints.NotBlank.message        = 不能为空
    javax.validation.constraints.NotEmpty.message        = 不能为空
    javax.validation.constraints.NotNull.message         = 不能为null
    javax.validation.constraints.Null.message            = 必须为null
    javax.validation.constraints.Past.message            = 需要是一个过去的时间
    javax.validation.constraints.PastOrPresent.message   = 需要是一个过去或现在的时间
    javax.validation.constraints.Pattern.message         = 需要匹配正则表达式"{regexp}"
    javax.validation.constraints.Positive.message        = 必须是正数
    javax.validation.constraints.PositiveOrZero.message  = 必须是正数或零
    javax.validation.constraints.Size.message            = 个数必须在{min}和{max}之间
    
    org.hibernate.validator.constraints.CreditCardNumber.message        = 不合法的信用卡号码
    org.hibernate.validator.constraints.Currency.message                = 不合法的货币 (必须是{value}其中之一)
    org.hibernate.validator.constraints.EAN.message                     = 不合法的{type}条形码
    org.hibernate.validator.constraints.Email.message                   = 不是一个合法的电子邮件地址
    org.hibernate.validator.constraints.Length.message                  = 长度需要在{min}和{max}之间
    org.hibernate.validator.constraints.CodePointLength.message         = 长度需要在{min}和{max}之间
    org.hibernate.validator.constraints.LuhnCheck.message               = ${validatedValue}的校验码不合法, Luhn模10校验和不匹配
    org.hibernate.validator.constraints.Mod10Check.message              = ${validatedValue}的校验码不合法, 模10校验和不匹配
    org.hibernate.validator.constraints.Mod11Check.message              = ${validatedValue}的校验码不合法, 模11校验和不匹配
    org.hibernate.validator.constraints.ModCheck.message                = ${validatedValue}的校验码不合法, ${modType}校验和不匹配
    org.hibernate.validator.constraints.NotBlank.message                = 不能为空
    org.hibernate.validator.constraints.NotEmpty.message                = 不能为空
    org.hibernate.validator.constraints.ParametersScriptAssert.message  = 执行脚本表达式"{script}"没有返回期望结果
    org.hibernate.validator.constraints.Range.message                   = 需要在{min}和{max}之间
    org.hibernate.validator.constraints.SafeHtml.message                = 可能有不安全的HTML内容
    org.hibernate.validator.constraints.ScriptAssert.message            = 执行脚本表达式"{script}"没有返回期望结果
    org.hibernate.validator.constraints.URL.message                     = 需要是一个合法的URL
    
    org.hibernate.validator.constraints.time.DurationMax.message        = 必须小于${inclusive == true ? '或等于' : ''}${days == 0 ? '' : days += '天'}${hours == 0 ? '' : hours += '小时'}${minutes == 0 ? '' : minutes += '分钟'}${seconds == 0 ? '' : seconds += '秒'}${millis == 0 ? '' : millis += '毫秒'}${nanos == 0 ? '' : nanos += '纳秒'}
    org.hibernate.validator.constraints.time.DurationMin.message        = 必须大于${inclusive == true ? '或等于' : ''}${days == 0 ? '' : days += '天'}${hours == 0 ? '' : hours += '小时'}${minutes == 0 ? '' : minutes += '分钟'}${seconds == 0 ? '' : seconds += '秒'}${millis == 0 ? '' : millis += '毫秒'}${nanos == 0 ? '' : nanos += '纳秒'}
    ```
    

我们修改response提示信息，使其更加符合我们的规则。

修改gulimall-product服务中的`BrandController` 局部异常处理`BindingResult` 

```java
/**
  * 保存
  *
  * @Valid:告诉SpringMVC我们需要校验
  */
 @RequestMapping("/save")
 //@RequiresPermissions("product:brand:save")
 public R save(@Valid @RequestBody BrandEntity brand, BindingResult result) {
     HashMap<String, String> map = new HashMap<>();
     if (result.hasErrors()) {
         //1、获取校验的错误结果
         result.getFieldErrors().forEach((item) -> {
             //FieldError 获取到的错误提示
             String message = item.getDefaultMessage();
             //获取错误的属性的名字
             String field = item.getField();
             map.put(field, message);
         });
         return R.error(400, "提交的数据不合法").put("data", map);
     } else {
         brandService.save(brand);
     }
     return R.ok();
 }
```

修改`gulimall-product`中的`BrandEntity` `name`字段，自定义错误提示

```java
/**
 *品牌名
*/
@NotBlank(message ="品牌名必须提交")
privateStringname;
```

使用postman测试：[http://localhost:88/api/product/brand/save](http://localhost:88/api/product/brand/save)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142178.png)

- `BrandEntity`：gulimall-product 这节课结束时的代码
  
    ```java
    package com.atguigu.gulimall.product.entity;
    
    import com.baomidou.mybatisplus.annotation.TableId;
    import com.baomidou.mybatisplus.annotation.TableName;
    
    import java.io.Serializable;
    import java.util.Date;
    
    import lombok.Data;
    import org.hibernate.validator.constraints.URL;
    
    import javax.validation.constraints.*;
    
    /**
     * 品牌
     *
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:21
     */
    @Data
    @TableName("pms_brand")
    public class BrandEntity implements Serializable {
        private static final long serialVersionUID = 1L;
    
        /**
         * 品牌id
         */
        @TableId
        private Long brandId;
        /**
         * 品牌名
         */
        @NotBlank(message = "品牌名必须提交")
        private String name;
        /**
         * 品牌logo地址
         */
        @NotEmpty
        @URL(message = "logo必须是一个合法的url地址")
        private String logo;
        /**
         * 介绍
         */
        private String descript;
        /**
         * 显示状态[0-不显示；1-显示]
         */
        private Integer showStatus;
        /**
         * 检索首字母[a-zA-Z]
         */
        @NotEmpty
        **@Pattern(regexp = "^[a-zA-Z]$", message = "检索首字母必须是一个字母")**
        private String firstLetter;
        /**
         * 排序
         */
        @NotNull
        @Min(value = 0, message = "排序必须大于等于0")
        private Integer sort;
    
    }
    ```
    
    postman测试：request请求字段
    
    ```json
    {
      "brandId": 1,
      "name": "赫昕",
      "logo": "https://gulimall-dali.oss-cn-beijing.jpg",
      "descript": "demoData",
      "showStatus": 1,
      "firstLetter": "h",
      "sort": 1
    }
    ```
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142179.png)
    

# 67、商品服务-API-品牌管理统一异常处理



## 统一异常处理`@ExceptionHandler`

上文说到 `@ ExceptionHandler` 需要进行异常处理的方法必须与出错的方法在同一个Controller里面。那么当代码加入了 @ControllerAdvice，则不需要必须在同一个 controller 中了。这也是 Spring 3.2 带来的新特性。从名字上可以看出大体意思是控制器增强。 也就是说，`@controlleradvice + @ ExceptionHandler` 也可以实现全局的异常捕捉。

```
4、统一的异常处理
@ControllerAdvice
1)、编写异常处理类，使用@ControllerAdvice。
2)、使用@ExceptionHandler标注方法可以处理的异常。
```

（1）抽取一个异常处理类

- `@ControllerAdvice`标注在类上，通过“basePackages”能够说明处理哪些路径下的异常。
- `@ExceptionHandler(value = 异常类型.class)`标注在方法上

`GulimallExceptionControllerAdvice` 位置：`com.atguigu.gulimall.product**.exception.GulimallExceptionControllerAdvice**`

```java
package com.atguigu.gulimall.product.exception;

import com.atguigu.common.exception.BizCodeEnume;
import com.atguigu.common.utils.R;
import lombok.extern.slf4j.Slf4j;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import org.springframework.web.servlet.ModelAndView;

import java.util.HashMap;
import java.util.Map;

/**
 * @Author Dali
 * @Date 2021/8/26 11:32
 * @Version 1.0
 * @Description: 67、商品服务-API-品牌管理统一异常处理 ： 集中处理所有异常 | 校验异常统一处理
 */
@Slf4j
//@ResponseBody
//@ControllerAdvice(basePackages = "com.atguigu.gulimall.product.controller")
@RestControllerAdvice(basePackages = "com.atguigu.gulimall.product.controller")
//@RestControllerAdvice = @ControllerAdvice + @ResponseBody
public class GulimallExceptionControllerAdvice {

    @ExceptionHandler(value = MethodArgumentNotValidException.class)
    public R handleVaildException(MethodArgumentNotValidException e) {
        log.error("数据校验出现问题{}，异常类型：{}", e.getMessage(), e.getClass());
        BindingResult bindingResult = e.getBindingResult();

        Map<String, String> errorMap = new HashMap<>();
        bindingResult.getFieldErrors().forEach((fieldError) -> {
            errorMap.put(fieldError.getField(), fieldError.getDefaultMessage());
        });
        return R.error(BizCodeEnume.VALID_EXCEPTION.getCode(), BizCodeEnume.VALID_EXCEPTION.getMsg()).put("data", errorMap);
    }

    /**
     * @return 处理任意类型的异常
     */
    @ExceptionHandler(value = Throwable.class)
    public R handleException(Throwable throwable) {

        return R.error(BizCodeEnume.VALID_EXCEPTION.getCode(), BizCodeEnume.VALID_EXCEPTION.getMsg());
    }
}
```

`BrandController`：`gulimall-product` 服务下将之间自定义异常处理恢复为原来的样子

```java
/**
  * 保存
  *
  * @Valid:告诉SpringMVC我们需要校验
  */
 @RequestMapping("/save")
 //@RequiresPermissions("product:brand:save")
 public R save(@Valid @RequestBody BrandEntity brand) {
     brandService.save(brand);
     return R.ok();
 }
```

上面的用法主要是通过`@Controller+@ExceptionHandler`来进行异常拦截处理`BizCodeEnum ，`为了定义这些错误状态码，我们可以单独定义一个常量类，用来存储这些错误状态码

```java
package com.atguigu.common.exception;

/**
 * @Author Dali
 * @Date 2021/8/26 12:19
 * @Version 1.0
 * @Description 错误码和错误信息定义类
 * 1.错误码定义规则为5为数字
 * 2.前两位表示业务场景，最后三位表示错误码。例如: 100001。 10:通 用001:系统未知
 * 异常
 * 3.维护错误码后需要维护错误描述，将他们定义为枚举形式
 * 错误码列表:
 * 10:通用
 * -----001:参数格式校验I
 * 11:商品
 * 12:订单
 * 13:购物车
 * 14:物流
 */
public enum BizCodeEnume {
    UNKNOW_EXCEPTION(10000, "系统未知异常"),
    VALID_EXCEPTION(10001, "参数格式校验失败");

    private int code;
    private String msg;

    BizCodeEnume(int code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    public int getCode() {
        return code;
    }

    public String getMsg() {
        return msg;
    }
}
```

## 错误码和错误信息自定义类

```java
错误码和错误信息定义类
 1.错误码定义规则为5为数字
 2.前两位表示业务场景，最后三位表示错误码。例如: 100001。 10:通 用001:系统未知
异常
 3.维护错误码后需要维护错误描述，将他们定义为枚举形式
 错误码列表:
 10:通用
	001:参数格式校验I
 11:商品
 12:订单
 13:购物车
 14:物流

```

测试：[http://localhost:88/api/product/brand/save](http://localhost:88/api/product/brand/save)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142180.png)

# 68、商品服务-API-品牌管理-JSR303分组校验 | @Validated注解

## 为社么需要JSR303分组校验

新增时我们某些字段的校验规则可能与修改时这些字段的规则校验方式可能不一样，比如，新增的时候我们的品牌Id（brandId）是自增的，新增的时候我们不用携带品牌Id（brandId），但是修改的时候我们必须携带品牌id，否则我们不知道修改哪个品牌。我们又不可能写两个vo来满足不同的校验规则。所以就需要用到分组校验来实现。

### 步骤：

在gulimall-common中创建valid包并且在valid包下创建分组接口`AddGroup`  和`UpdateGroup` **接口。**
在gulimall-product服务中的 `entity` 实体类中`BrandEntity`的属性中标注`@NotBlank`等注解，并指定要使用的分组。如：

`@NotBlank(message = "品牌名必须提交", groups = {AddGroup.class, UpdateGroup.class})`

controller的方法上或者方法参数上写要处理的分组的接口信息，如`@Validated(AddGroup.class)`

1、创建空接口：AddGroup 和 UpdateGroup

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142181.png)

2、给校验注解标注什么情况需要进行校验，比如新增时创建

```java
package com.atguigu.gulimall.product.entity;

import com.atguigu.common.valid.AddGroup;
import com.atguigu.common.valid.UpdateGroup;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;

import java.io.Serializable;
import java.util.Date;

import lombok.Data;
import org.hibernate.validator.constraints.URL;

import javax.validation.constraints.*;

/**
 * 品牌
 *
 * @author ÍõÈ½ê¿
 * @email daki9981@qq.com
 * @date 2021-08-13 19:53:21
 */
@Data
@TableName("pms_brand")
public class BrandEntity implements Serializable {
    private static final long serialVersionUID = 1L;

    /**
     * 品牌id
     */
    **@NotNull(message = "修改时必须指定品牌id", groups = {UpdateGroup.class})
    @Null(message = "新增时不能指定id", groups = {AddGroup.class})**
    @TableId
    private Long brandId;
    /**
     * 品牌名
     */
    **@NotBlank(message = "品牌名必须提交", groups = {AddGroup.class, UpdateGroup.class})**
    private String name;
    /**
     * 品牌logo地址
     */
    @NotEmpty
    @URL(message = "logo必须是一个合法的url地址")
    private String logo;
    /**
     * 介绍
     */
    private String descript;
    /**
     * 显示状态[0-不显示；1-显示]
     */
    private Integer showStatus;
    /**
     * 检索首字母[a-zA-Z]
     */
    @NotEmpty
    @Pattern(regexp = "^[a-zA-Z]$", message = "检索首字母必须是一个字母")
    private String firstLetter;
    /**
     * 排序
     */
    @NotNull
    @Min(value = 0, message = "排序必须大于等于0")
    private Integer sort;

}
```

3、在controller中添加注解`@Validated({AddGroup.class}` 按需添加

```java
/**
     * 保存
     *
     * @Valid:告诉SpringMVC我们需要校验
     */
    @RequestMapping("/save")
    //@RequiresPermissions("product:brand:save")
//  public R save(@Valid @RequestBody BrandEntity brand) {   //没有使用ISR303分组校验使用的注解@Valid
    public R save(**@Validated({AddGroup.class}**) @RequestBody BrandEntity brand) {  //ISR303分组校验

        brandService.save(brand);

        return R.ok();
    }
```

4、PostMan填写brandId测试

```json
{
  "brandId": 1,
  "name": "王昕昕",
  "logo": "abc"
}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142182.png)

5、PostMan不填写brandId测试

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142183.png)

修改：BrandEntity

```java
		/**
     * 品牌logo地址
     */
    @NotEmpty(groups = {AddGroup.class})
    @URL(message = "logo必须是一个合法的url地址", groups = {AddGroup.class, UpdateGroup.class})
    private String logo;

```

不添加brandId再次使用Postman进行测试

```json
{
  "name": "王昕昕",
  "logo": "abc"
}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142184.png)

测试BrandController修改update()方法的情况：update(`@Validated(UpdateGroup.class`)

```java
/**
 * 修改
 */
@RequestMapping("/update")
//@RequiresPermissions("product:brand:update")
public R update(@Validated(UpdateGroup.class) @RequestBody BrandEntity brand) {
    brandService.updateById(brand);
    return R.ok();
}
```

换用**@NotBlank注解**

```java
    /**
     * 品牌logo地址
     */
    **@NotBlank**(groups = {AddGroup.class})
    @URL(message = "logo必须是一个合法的url地址", groups = {AddGroup.class, UpdateGroup.class})
    private String logo;
```

postman测试：[http://localhost:88/api/product/brand/update](http://localhost:88/api/product/brand/update)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142185.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062142186.png)

这节课总的代码

- BrandController
  
    ```java
    /**
         * 保存
         *
         * @Valid:告诉SpringMVC我们需要校验
         */
        @RequestMapping("/save")
        //@RequiresPermissions("product:brand:save")
    //    public R save(@Valid @RequestBody BrandEntity brand) {
        public R save(@Validated({AddGroup.class}) @RequestBody BrandEntity brand) {  //ISR303分组校验
    
            brandService.save(brand);
    
            return R.ok();
        }
    
        /**
         * 修改
         */
        @RequestMapping("/update")
        //@RequiresPermissions("product:brand:update")
        public R update(@Validated(UpdateGroup.class) @RequestBody BrandEntity brand) {
            brandService.updateById(brand);
    
            return R.ok();
        }
    ```
    
- BrandEntity
  
    ```java
    package com.atguigu.gulimall.product.entity;
    
    import com.atguigu.common.valid.AddGroup;
    import com.atguigu.common.valid.UpdateGroup;
    import com.baomidou.mybatisplus.annotation.TableId;
    import com.baomidou.mybatisplus.annotation.TableName;
    
    import java.io.Serializable;
    import java.util.Date;
    
    import lombok.Data;
    import org.hibernate.validator.constraints.URL;
    
    import javax.validation.constraints.*;
    
    /**
     * 品牌
     *
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:21
     */
    @Data
    @TableName("pms_brand")
    public class BrandEntity implements Serializable {
        private static final long serialVersionUID = 1L;
    
        /**
         * 品牌id
         */
        @NotNull(message = "修改时必须指定品牌id", groups = {UpdateGroup.class})
        @Null(message = "新增时不能指定id", groups = {AddGroup.class})
        @TableId
        private Long brandId;
        /**
         * 品牌名
         */
        @NotBlank(message = "品牌名必须提交", groups = {AddGroup.class, UpdateGroup.class})
        private String name;
        /**
         * 品牌logo地址
         */
        @NotBlank(groups = {AddGroup.class})
        @URL(message = "logo必须是一个合法的url地址", groups = {AddGroup.class, UpdateGroup.class})
        private String logo;
        /**
         * 介绍
         */
        private String descript;
        /**
         * 显示状态[0-不显示；1-显示]
         */
        private Integer showStatus;
        /**
         * 检索首字母[a-zA-Z]
         */
        @NotEmpty(groups = {AddGroup.class})
        @Pattern(regexp = "^[a-zA-Z]$", message = "检索首字母必须是一个字母", groups = {AddGroup.class, UpdateGroup.class})
        private String firstLetter;
        /**
         * 排序
         */
        @NotNull(groups = {AddGroup.class})
        @Min(value = 0, message = "排序必须大于等于0", groups = {AddGroup.class, UpdateGroup.class})
        private Integer sort;
    
    }
    ```
    

# 69、商品服务-API-品牌管理- JSR303自定义校验注解

## 自定义校验步骤：

[1)、编写一个自定义的校验注解](https://www.notion.so/07_-API-OSS-JSR303-ede1dc92894947919d72bd7e88623cbf)

[2)、编写一个自定义的校验器](https://www.notion.so/07_-API-OSS-JSR303-ede1dc92894947919d72bd7e88623cbf)

[3)、关联自定义的校验器和自定义的校验注解](https://www.notion.so/07_-API-OSS-JSR303-ede1dc92894947919d72bd7e88623cbf)：@Constraint(validatedBy= {ListValueConstraintValidator.class})

```java
@Documented
@Constraint(validatedBy= {ListValueConstraintValidator.class})【可以指定多个不同的校验器，适配不同类型的校验】
@Target({METHOD,FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE})
@Retention(RUNTIME)
```

### 1)、编写一个自定义的校验注解：ListValue

场景：要校验**showStatus**的**0/1**状态，可以用正则，但我们可以利用其他方式解决复杂场景。比如我们想要下面的场景：gulimall-product服务下的BrandEntity实体类的showStatus字段

```java
    /**
     * 显示状态[0-不显示；1-显示]
     * 编写一个@ListValue()自定义的校验注解
     */
    @NotNull(groups = {AddGroup.class, UpdateStatusGroup.class})
    **@ListValue(vals = {0, 1}, groups = {AddGroup.class, UpdateStatusGroup.class})**
    private Integer showStatus;
```

在gulimall-common服务中的valid包下新建**`UpdateStatusGroup`接口；**

自定义的校验注解必须有3个属性：

- message()错误信息
- groups()分组校验
- payload()自定义负载信息

```java
package com.atguigu.common.valid;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.TYPE_USE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

/**
 * @Author Dali
 * @Date 2021/8/26 14:26
 * @Version 1.0
 * @Description: 编写一个自定义@ListValue的校验注解 这里校验的默认只能是Integer类型的
 */
@Documented
[**@Constraint(validatedBy = {ListValueConstraintValidator.class}) //可以指定多个不同的校验器，适配不同类型的校验】**](https://www.notion.so/07_-API-OSS-JSR303-ede1dc92894947919d72bd7e88623cbf)
@Target({METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE})
@Retention(RUNTIME)
public @interface ListValue {
    String message() default "{com.atguigu.common.valid.ListValue.message}";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};

    int[] vals() default {};
}
```

因为上面的`message`值对应的最终字符串需要去`ValidationMessages.properties`中获得，所以我们在gulimall-common中`resources`文件夹下新建文件`ValidationMessages.properties` 文件内容如下：

```java
com.atguigu.common.valid.ListValue.message=必须提交指定的值[0,1]
```

添加以下依赖：

```xml
----------------------------------视频中只添加了这一个注解-------------------------------

**<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>2.0.1.Final</version>
</dependency>**

------------------------------------------其他博客-------------------------------------------
<!--校验-->
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>2.1.0.Final</version>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.4.1.Final</version>
</dependency>
<!--高版本需要javax.el-->
<dependency>
    <groupId>org.glassfish</groupId>
    <artifactId>javax.el</artifactId>
    <version>3.0.1-b08</version>
</dependency>
```

### 2)、编写一个自定义的校验器：ListValueConstraintValidator

```java
package com.atguigu.common.valid;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;
import java.util.HashSet;
import java.util.Set;

/**
 * @Author Dali
 * @Date 2021/8/26 14:44
 * @Version 1.0
 * @Description: 自定义校验器
 */
public class ListValueConstraintValidator implements ConstraintValidator<ListValue, Integer> {

    // 存储所有可能的值
    private Set<Integer> set = new HashSet<>();

    //初始化方法:你可以获取注解上的内容并进行处理
    @Override
    public void initialize(ListValue constraintAnnotation) {
        // 获取后端写好的限制
        // 这个vals就是ListValue注解里的vals，我们写的注解是@ListValue(vals={0,1})
        int[] vals = constraintAnnotation.vals();
        for (int val : vals) {
            set.add(val);
        }
    }

    //判断是否校验成功

    /**
     * @param value   需要校验的值
     * @param context
     * @return
     */
    @Override
    public boolean isValid(Integer value, ConstraintValidatorContext context) {

        return set.contains(value);
    }
}
```

### 3)、关联自定义的校验器和自定义的校验注解：`@Constraint(validatedBy = {ListValueConstraintValidator.class})`

`@Constraint(validatedBy = {ListValueConstraintValidator.class})` //可以指定多个不同的校验器，适配不同类型的校验】一个校验注解可以匹配多个校验器

因为我们在gulimall-product服务中BrandController里面新增了updateStatus()方法，前端`brand.vue`注意修改请求路径：

```java
updateBrandStatus(data) {
      console.log("最新信息", data);
      let { brandId, showStatus } = data;
      //发送请求修改状态
      this.$http({
        **url: this.$http.adornUrl("/product/brand/update/status"),**
        method: "post",
        data: this.$http.adornData({ brandId, showStatus }, false),
      }).then(({ data }) => {
        this.$message({
          type: "success",
          message: "状态更新成功",
        });
      });
    },
```

重启测试