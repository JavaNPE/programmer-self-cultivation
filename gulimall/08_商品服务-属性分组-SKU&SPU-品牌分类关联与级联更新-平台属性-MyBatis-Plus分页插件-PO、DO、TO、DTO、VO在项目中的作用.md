# 08_商品服务-属性分组-SKU&SPU-品牌分类关联与级联更新-平台属性-MyBatis-Plus分页插件-PO、DO、TO、DTO、VO介绍

# 70、商品服务-概念-SPU&SKU&规格参数&销售属性  | pms_attr属性表 | pms_attr_group属性分组表 | pms_attr_attrgroup_relation属性表和属性分组的关联表

对应的数据库表：

| pms_attr：属性表

| pms_attr_group：属性分组表

| pms_attr_attrgroup_relation：属性表和属性分组的关联表

| pms_product_attr_value：商品属性值表

可以理解为：SPU一个是名称，SKU一个是型号

## SPU: Standard Product Unit (标准化产品单元)

是商品信息聚合的最小单位，是一组可复用、易检索的标准化信息的集合，该集合描述了一个产品的特性。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

## SKU: Stock Keeping Unit (库存量单位)

即库存进出计量的基本单元，可以是以件，盒，托盘等为单位。SKU这是对于大型连锁超市DC (配送中心)物流管理的一个必要的方法。现在已经被引申为产品统一编号的简称，每种产品均对应有唯一一的SKU号。

## 基本属性[规格参数] 与销售属性

每个分类下的商品共享规格参数，与销售属性。只是有些商品不-定要用这个分类下全部的属性：

- 属性是以三级分 类组织起来的；
- 规格参数中有些是可以提供检索的；
- 规格参数也是基本属性，他们具有自己的分组；
- 属性的分组也是以三级分类组织起来的；
- 属性名确定的，但是值是每一个商品不同来决定的；

## [属性分组-规格参数-销售属性-3三级分类]关联关系

![image-20220530095610060](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220530095610060.png)

## SPU-SKU-属性表

![image-20220530095625301](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220530095625301.png)



## 属性分组-效果

![image-20220530095655800](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220530095655800.png)

# 71、商品服务API属性分组前端组件抽取&父子组件交互

后台：**商品系统/平台属性/属性分组，**现在想要实现点击菜单的左边，能够实现在右边展示数据。。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)

将谷粒商城雷神提供的那个SQL文件，打开，复制内容：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%207.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%208.png)

## 谷粒商城在线接口文档地址：（好宝贝）

[03、获取分类属性分组](https://easydoc.net/s/78237135/ZUqEdvA4/OXTgKobR)

按规则建文件夹：

根据其他的请求地址`http://localhost:8001/#/product-attrgroup`

所以应该有`product/attrgroup.vue`。我们之前写过`product/cateory.vue`，现在我们要抽象到`common/cateory.vue`（也就是左侧的tree单独成一个vue组件）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%209.png)

## 参考ElementUI：Layout 布局组件：分栏间隔

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/layout)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2010.png)

### 1）左侧内容：

要在左面显示菜单，右面显示表格。复制`<el-row :gutter="20">。。。`，放到**attrgroup.vue**的`<template>`。20表示列间距

```bash
<el-row :gutter="20">
  <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
</el-row>
```

分为2个模块，分别占6列和18列（分别是tree和当前spu等信息）

有了布局之后，要在里面放内容。接下来要抽象一个分类vue。新建common/category，生成vue模板。把之前写的el-tree放到<template>

```bash
<el-tree :data="menus" 
         :props="defaultProps" node-key="catId" ref="menuTree" @node-click="nodeClick"	></el-tree>
所以他把menus绑定到了菜单上，
所以我们应该在export default {中有menus的信息
该具体信息会随着点击等事件的发生会改变值（或比如created生命周期时），
tree也就同步变化了
```

common/category写好后，就可以在attrgroup.vue中导入使用了

```bash
<script>
import Category from "../common/category";
export default {
  //import引入的组件需要注入到对象中才能使用。组件名:自定义的名字，一致可以省略
  components: { Category},
```

导入了之后，就可以在attrgroup.vue中找合适位置放好

```bash
<!--  -->
<template>
  <el-row :gutter="20">
    <el-col :span="6">
      <category @tree-node-click="treenodeclick"></category>
    </el-col>
..........................
```

### 2）右侧表格内容：

开始填写属性分组页面右侧的表格

复制gulimall-product\src\main\resources\src\views\modules\product\attrgroup.vue中的部分内容div到attrgroup.vue

批量删除是弹窗add-or-update

导入data、结合components

### 父组件：

要实现功能：点击左侧，右侧表格对应内容显示。

父子组件传递数据：category.vue点击时，引用它的attgroup.vue能感知到， 然后通知到add-or-update

比如嵌套div，里层div有事件后冒泡到外层div（是指一次点击调用了两个div的点击函数）

1）子组件（category）给父组件（attrgroup）传递数据，事件机制；

去`element-ui`的`tree`  组件找event事件，看node-click()

在category中绑定node-click事件，

```bash
<el-tree :data="menus" :props="defaultProps" node-key="catId" ref="menuTree" 
         @node-click="nodeClick"	></el-tree>
```

### this.$emit()：

2）子组件给父组件发送一个事件，携带上数据；

```bash
nodeclick(data, node, component) {
      console.log("子组件category的节点被点击", data, node, component);
      //向父组件发送事件：
      this.$emit("tree-node-click", data, node, component);
    },

第一个参数事件名字随便写，
后面可以写任意多的东西，事件发生时都会传出去
```

父子组件传递数据：
子组件给父组件传递数据，使用事件机制
子组件被点击时要个父组件发送一个事件，事件携带数据，父组件感知上这个数据，就可以来触发
`this.$emit("事件名", 携带的数据);`

3）父组件中的获取发送的事件

```bash
<!-- 在attr-group中写 -->
<template>
  <el-row :gutter="20">
    <el-col :span="6">
      <category @tree-node-click="treenodeclick"></category>
    </el-col>
    <el-col :span="18">

表明他的子组件可能会传递过来点击事件，用自定义的函数接收传递过来的参数
```

```bash
//感知树节点被点击
    treenodeclick(data, node, component) {
      console.log(
        "attrgroup感知到了category的节点被点击：",
        data,
        node,
        component
      );
      console.log("刚才被点击的菜单id：", data.catId);
    },
```

### 这节课代码：

- category.vue ： src\views\modules\**common\category.vue**
  
    ```bash
    <!--  -->
    <template>
      <el-tree
        :data="menus"
        :props="defaultProps"
        node-key="catId"
        ref="menuTree"
        @node-click="nodeclick"
      >
      </el-tree>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      data() {
        //这里存放数据
        return {
          menus: [],
          expandedKey: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        nodeclick(data, node, component) {
          console.log("子组件category的节点被点击", data, node, component);
          //向父组件发送事件：
          this.$emit("tree-node-click", data, node, component);
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    
- attrgroup.vue : src\views\modules\**product\attrgroup-add-or-update.vue**
  
    ```bash
    <!--  -->
    <template>
      <el-row :gutter="20">
        <el-col :span="6">
          <category @tree-node-click="treenodeclick"></category>
        </el-col>
        <el-col :span="18">
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
                  v-if="isAuth('product:attrgroup:save')"
                  type="primary"
                  @click="addOrUpdateHandle()"
                  >新增</el-button
                >
                <el-button
                  v-if="isAuth('product:attrgroup:delete')"
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
                prop="attrGroupId"
                header-align="center"
                align="center"
                label="分组id"
              >
              </el-table-column>
              <el-table-column
                prop="attrGroupName"
                header-align="center"
                align="center"
                label="组名"
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
                prop="descript"
                header-align="center"
                align="center"
                label="描述"
              >
              </el-table-column>
              <el-table-column
                prop="icon"
                header-align="center"
                align="center"
                label="组图标"
              >
              </el-table-column>
              <el-table-column
                prop="catelogId"
                header-align="center"
                align="center"
                label="所属分类id"
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
                    @click="addOrUpdateHandle(scope.row.attrGroupId)"
                    >修改</el-button
                  >
                  <el-button
                    type="text"
                    size="small"
                    @click="deleteHandle(scope.row.attrGroupId)"
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
        </el-col>
      </el-row>
    </template>
    
    <script>
    /**
     * 父子组件传递数据
     * 1）、子组件给父组件传递数据，使用事件机制
     *      子组件被点击时要个父组件发送一个事件，事件携带数据，父组件感知上这个数据，就可以来触发
     *      this.$emit("事件名", 携带的数据);
     */
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    import Category from "../common/category.vue";
    import AddOrUpdate from "./attrgroup-add-or-update";
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: { Category, AddOrUpdate },
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
      activated() {
        this.getDataList();
      },
      methods: {
        //感知树节点被点击
        treenodeclick(data, node, component) {
          console.log(
            "attrgroup感知到了category的节点被点击：",
            data,
            node,
            component
          );
          console.log("刚才被点击的菜单id：", data.catId);
        },
        // 获取数据列表
        getDataList() {
          this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/attrgroup/list"),
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
                return item.attrGroupId;
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
              url: this.$http.adornUrl("/product/attrgroup/delete"),
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
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    
- attrgroup-add-or-update.vue ： src\views\modules**\product\attrgroup.vue**
  
    ```bash
    <template>
      <el-dialog
        :title="!dataForm.attrGroupId ? '新增' : '修改'"
        :close-on-click-modal="false"
        :visible.sync="visible">
        <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
        <el-form-item label="组名" prop="attrGroupName">
          <el-input v-model="dataForm.attrGroupName" placeholder="组名"></el-input>
        </el-form-item>
        <el-form-item label="排序" prop="sort">
          <el-input v-model="dataForm.sort" placeholder="排序"></el-input>
        </el-form-item>
        <el-form-item label="描述" prop="descript">
          <el-input v-model="dataForm.descript" placeholder="描述"></el-input>
        </el-form-item>
        <el-form-item label="组图标" prop="icon">
          <el-input v-model="dataForm.icon" placeholder="组图标"></el-input>
        </el-form-item>
        <el-form-item label="所属分类id" prop="catelogId">
          <el-input v-model="dataForm.catelogId" placeholder="所属分类id"></el-input>
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
              attrGroupId: 0,
              attrGroupName: '',
              sort: '',
              descript: '',
              icon: '',
              catelogId: ''
            },
            dataRule: {
              attrGroupName: [
                { required: true, message: '组名不能为空', trigger: 'blur' }
              ],
              sort: [
                { required: true, message: '排序不能为空', trigger: 'blur' }
              ],
              descript: [
                { required: true, message: '描述不能为空', trigger: 'blur' }
              ],
              icon: [
                { required: true, message: '组图标不能为空', trigger: 'blur' }
              ],
              catelogId: [
                { required: true, message: '所属分类id不能为空', trigger: 'blur' }
              ]
            }
          }
        },
        methods: {
          init (id) {
            this.dataForm.attrGroupId = id || 0
            this.visible = true
            this.$nextTick(() => {
              this.$refs['dataForm'].resetFields()
              if (this.dataForm.attrGroupId) {
                this.$http({
                  url: this.$http.adornUrl(`/product/attrgroup/info/${this.dataForm.attrGroupId}`),
                  method: 'get',
                  params: this.$http.adornParams()
                }).then(({data}) => {
                  if (data && data.code === 0) {
                    this.dataForm.attrGroupName = data.attrGroup.attrGroupName
                    this.dataForm.sort = data.attrGroup.sort
                    this.dataForm.descript = data.attrGroup.descript
                    this.dataForm.icon = data.attrGroup.icon
                    this.dataForm.catelogId = data.attrGroup.catelogId
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
                  url: this.$http.adornUrl(`/product/attrgroup/${!this.dataForm.attrGroupId ? 'save' : 'update'}`),
                  method: 'post',
                  data: this.$http.adornData({
                    'attrGroupId': this.dataForm.attrGroupId || undefined,
                    'attrGroupName': this.dataForm.attrGroupName,
                    'sort': this.dataForm.sort,
                    'descript': this.dataForm.descript,
                    'icon': this.dataForm.icon,
                    'catelogId': this.dataForm.catelogId
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
    

![本节课的：目录结构](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2011.png)

本节课的：目录结构

# 72、商品服务-API-属性分组获取分类属性分组模糊查询

## 谷粒商城对外接口文档地址：

[03、获取分类属性分组](https://easydoc.net/s/78237135/ZUqEdvA4/OXTgKobR)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2012.png)

## 03、获取分类属性分组

GET ：`/product/attrgroup/list/{catelogId}`

按照这个url，去gulimall-product项目下的AttrGroupController里修改

```java
/**
     * 列表
     * catelogId：三级分类的id
     */
    @RequestMapping("/list/{catelogId}")
    //@RequiresPermissions("product:attrgroup:list")
    public R list(@RequestParam Map<String, Object> params, @PathVariable("catelogId") Long catelogId) {
//        PageUtils page = attrGroupService.queryPage(params);
        PageUtils page = attrGroupService.queryPage(params, catelogId);

        return R.ok().put("page", page);
    }
```

增加接口与实现

> Query里面就有个方法getPage()，传入map，将map解析为mybatis-plus的IPage<T>对象；
自定义PageUtils类用于传入IPage对象，得到其中的分页信息；
AttrGroupServiceImpl extends ServiceImpl，其中ServiceImpl的父类中有方法page(IPage,Wrapper)。对于wrapper而言，没有条件的话就是查询所有；
queryPage()返回前还会return new PageUtils(page);，把page对象解析好页码信息，就封装为了响应数据；
> 

AttrGroupServiceImpl：

```java
package com.atguigu.gulimall.product.service.impl;

import org.apache.commons.lang.StringUtils;
import org.springframework.stereotype.Service;

import java.util.Map;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.atguigu.common.utils.PageUtils;
import com.atguigu.common.utils.Query;

import com.atguigu.gulimall.product.dao.AttrGroupDao;
import com.atguigu.gulimall.product.entity.AttrGroupEntity;
import com.atguigu.gulimall.product.service.AttrGroupService;

@Service("attrGroupService")
public class AttrGroupServiceImpl extends ServiceImpl<AttrGroupDao, AttrGroupEntity> implements AttrGroupService {

    @Override
    public PageUtils queryPage(Map<String, Object> params, Long catelogId) {
        if (catelogId == 0) {
            IPage<AttrGroupEntity> page = this.page(new Query<AttrGroupEntity>().getPage(params),
                    new QueryWrapper<AttrGroupEntity>());
            return new PageUtils(page);
        } else {
            String key = (String) params.get("key");
            //SELECT * from pms_attr_group WHERE catelog_id = ? AND (attr_group_id=key or attr_group_name LIKE %key%)
            QueryWrapper<AttrGroupEntity> wrapper = new QueryWrapper<AttrGroupEntity>().eq("catelog_id", catelogId);
            if (StringUtils.isNotEmpty(key)) {
                wrapper.and((obj) -> {
                    obj.eq("attr_group_id", key).or().like("attr_group_name", key);
                });
            }
            IPage<AttrGroupEntity> page = this.page(new Query<AttrGroupEntity>().getPage(params),
                    wrapper);
            return new PageUtils(page);
        }

    }
}
```

测试

查1分类的属性分组：`localhost:88/api/product/attrgroup/list/1`

查1分类的属性分组并且分页、关键字为aa：`localhost:88/api/product/attrgroup/list/1?page=1&key=aa`。结果当然查不到

```json
{
    "msg": "success",
    "code": 0,
    "page": {
        "totalCount": 0,
        "pageSize": 10,
        "totalPage": 0,
        "currPage": 1,
        "list": []
    }
}
```

然后调整前端

发送请求时url携带id信息，`${this.catId}`，get请求携带page信息

点击第3级分类时才查，修改`attr-group.vue`中的函数即可

`attrgroup.vue：`

```bash
//感知树节点被点击
    treenodeclick(data, node, component) {
      console.log(
        "attrgroup感知到了category的节点被点击：",
        data,
        node,
        component
      );
      console.log("刚才被点击的菜单id：", data.catId);
      **if (node.level == 3) {
        //说明是一个三级分类
        this.catId = data.catId;
        this.getDataList(); //重新查询
      }**
    },

.....................................................
// 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl(`/product/attrgroup/list/${this.catId}`),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          key: this.dataForm.key,
        }),
...........................
....................................................

//import引入的组件需要注入到对象中才能使用
  components: { Category, AddOrUpdate },
  data() {
    return {
      **catId: 0,**
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
...............................................
```

# 73、商品服务API-属性分组-分组新增&级联选择器

参考ElementUI 的Cascader 级联选择器

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/cascader)

```bash
<el-cascader
    v-model="value"
    :options="options"
    @change="handleChange"></el-cascader>
```

## 新增属性分组

上面演示了查询功能，下面写insert分类

但是想要下面这个效果：

因为分类可以对应多个属性分组，所以我们新增的属性分组时要指定分类

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2013.png)

下拉菜单应该是手机一级分类的，这个功能是级联选择器

### **级联选择器<el-cascader**

级联选择：https://element.eleme.cn/#/zh-CN/component/cascader

级联选择的下拉同样是个options数组，多级的话用children属性即可，只需为 Cascader 的options属性指定选项数组即可渲染出一个级联选择器。通过props.expandTrigger可以定义展开子级菜单的触发方式。

去**vue**里找src\views\modules\product\`attrgroup-add-or-update.vue`

修改对应的位置为`<el-cascader 。。。>` ，把data()里的数组`categorys`绑定到`options`上即可，更详细的设置可以用`props`绑定。

```bash
<el-form-item label="所属分类id" prop="catelogId">
        <!-- <el-input v-model="dataForm.catelogId" placeholder="所属分类id"></el-input> -->
        **<el-cascader
          v-model="dataForm.catelogIds"
          :options="categorys"
          :props="props"
        ></el-cascader>**
      </el-form-item>
```

```bash
data() {
    return {
      **props: {
        value: "catId",
        label: "name",
        children: "children"
      },**
      visible: false,
      **categorys: [],**
      dataForm: {
        attrGroupId: 0,
        attrGroupName: "",
        sort: "",
        descript: "",
        icon: "",
        catelogIds: [],
        catelogId: 0
      },
.......................................
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2014.png)

## @JsonInclude注解去空字段

优化：没有下级菜单时不要有下一级空菜单，在java端把children属性空值去掉，空集合时去掉children字段，

可以用`@JsonInclude(Inlcude.NON_EMPTY)`注解标注在实体类的属性上，

在CategoryEntity类中添加**@JsonInclude注解，**

```java
/**
	 * 当前菜单的子分类,
	 * 数据库表中并不存在children这个字段
	 */
	**@JsonInclude(JsonInclude.Include.NON_EMPTY) //children这个字段不为空的时候我们才返回**
    @TableField(exist = false)
    private List<CategoryEntity> children;
```

提交完后返回页面也刷新了，是用到了父子组件。在`$message`弹窗结束回调`$this.emit`

接下来要解决的问题是，修改了该vue后，新增是可以用，修改回显就有问题了，应该回显3级

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2015.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2016.png)

- 前端这节课修改的代码：`attrgroup-add-or-update.vue`
  
    ```bash
    <template>
      <el-dialog
        :title="!dataForm.attrGroupId ? '新增' : '修改'"
        :close-on-click-modal="false"
        :visible.sync="visible"
      >
        <el-form
          :model="dataForm"
          :rules="dataRule"
          ref="dataForm"
          @keyup.enter.native="dataFormSubmit()"
          label-width="80px"
        >
          <el-form-item label="组名" prop="attrGroupName">
            <el-input
              v-model="dataForm.attrGroupName"
              placeholder="组名"
            ></el-input>
          </el-form-item>
          <el-form-item label="排序" prop="sort">
            <el-input v-model="dataForm.sort" placeholder="排序"></el-input>
          </el-form-item>
          <el-form-item label="描述" prop="descript">
            <el-input v-model="dataForm.descript" placeholder="描述"></el-input>
          </el-form-item>
          <el-form-item label="组图标" prop="icon">
            <el-input v-model="dataForm.icon" placeholder="组图标"></el-input>
          </el-form-item>
          <el-form-item label="所属分类id" prop="catelogId">
            <!-- <el-input v-model="dataForm.catelogId" placeholder="所属分类id"></el-input> -->
            <el-cascader
              v-model="dataForm.catelogIds"
              :options="categorys"
              :props="props"
            ></el-cascader>
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
      data() {
        return {
          props: {
            value: "catId",
            label: "name",
            children: "children"
          },
          visible: false,
          categorys: [],
          dataForm: {
            attrGroupId: 0,
            attrGroupName: "",
            sort: "",
            descript: "",
            icon: "",
            catelogIds: [],
            catelogId: 0
          },
          dataRule: {
            attrGroupName: [
              { required: true, message: "组名不能为空", trigger: "blur" },
            ],
            sort: [{ required: true, message: "排序不能为空", trigger: "blur" }],
            descript: [
              { required: true, message: "描述不能为空", trigger: "blur" },
            ],
            icon: [{ required: true, message: "组图标不能为空", trigger: "blur" }],
            catelogId: [
              { required: true, message: "所属分类id不能为空", trigger: "blur" },
            ],
          },
        };
      },
      methods: {
        getCategorys() {
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            this.categorys = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        init(id) {
          this.dataForm.attrGroupId = id || 0;
          this.visible = true;
          this.$nextTick(() => {
            this.$refs["dataForm"].resetFields();
            if (this.dataForm.attrGroupId) {
              this.$http({
                url: this.$http.adornUrl(
                  `/product/attrgroup/info/${this.dataForm.attrGroupId}`
                ),
                method: "get",
                params: this.$http.adornParams(),
              }).then(({ data }) => {
                if (data && data.code === 0) {
                  this.dataForm.attrGroupName = data.attrGroup.attrGroupName;
                  this.dataForm.sort = data.attrGroup.sort;
                  this.dataForm.descript = data.attrGroup.descript;
                  this.dataForm.icon = data.attrGroup.icon;
                  this.dataForm.catelogId = data.attrGroup.catelogId;
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
                  `/product/attrgroup/${
                    !this.dataForm.attrGroupId ? "save" : "update"
                  }`
                ),
                method: "post",
                data: this.$http.adornData({
                  attrGroupId: this.dataForm.attrGroupId || undefined,
                  attrGroupName: this.dataForm.attrGroupName,
                  sort: this.dataForm.sort,
                  descript: this.dataForm.descript,
                  icon: this.dataForm.icon,
                  catelogId: this.dataForm.catelogIds[this.dataForm.catelogIds.length-1],
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
      created(){
        this.getCategorys();
      }
    };
    </script>
    ```
    
- 后端修改代码：
  
    ```java
    package com.atguigu.gulimall.product.entity;
    
    import com.baomidou.mybatisplus.annotation.TableField;
    import com.baomidou.mybatisplus.annotation.TableId;
    import com.baomidou.mybatisplus.annotation.TableLogic;
    import com.baomidou.mybatisplus.annotation.TableName;
    
    import java.io.Serializable;
    import java.util.Date;
    import java.util.List;
    
    import com.fasterxml.jackson.annotation.JsonInclude;
    import lombok.Data;
    
    /**
     * 商品三级分类
     *
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:21
     */
    @Data
    @TableName("pms_category")
    public class CategoryEntity implements Serializable {
        private static final long serialVersionUID = 1L;
    
        /**
         * 分类id
         */
        @TableId
        private Long catId;
        /**
         * 分类名称
         */
        private String name;
        /**
         * 父分类id
         */
        private Long parentCid;
        /**
         * 层级
         */
        private Integer catLevel;
        /**
         * 是否显示[0-不显示，1显示]
         */
        @TableLogic(value = "1",delval = "0")
        private Integer showStatus;
        /**
         * 排序
         */
        private Integer sort;
        /**
         * 图标地址
         */
        private String icon;
        /**
         * 计量单位
         */
        private String productUnit;
        /**
         * 商品数量
         */
        private Integer productCount;
    
    	/**
    	 * 当前菜单的子分类,
    	 * 数据库表中并不存在children这个字段
    	 */
    	**@JsonInclude(JsonInclude.Include.NON_EMPTY) //children这个字段不为空的时候我们才返回**
        @TableField(exist = false)
        private List<CategoryEntity> children;
    }
    ```
    

# 74、商品服务-API-属性分组-分组修改&级联选择器回显

上节课我们完成了新增操作，目前修改操作回显信息是不完整的

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2017.png)

## 这节课要实现的效果

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2018.png)

`attrgroup.vue` ：这里不做修改

```java
methods: {
    //感知树节点被点击
    treenodeclick(data, node, component) {
      console.log(
        "attrgroup感知到了category的节点被点击：",
        data,
    ...............................
    // 新增 / 修改
    addOrUpdateHandle(id) {
			// 先显示弹窗
      this.addOrUpdateVisible = true;
			// .$nextTick(代表渲染结束后再接着执行
      this.$nextTick(() => {
						// this是attrgroup.vue
            // $refs是它里面的所有组件。在本vue里使用的时候，标签里会些ref=""
            // addOrUpdate这个组件
            // 组件的init(id);方法
        this.$refs.addOrUpdate.init(id);
      });
    },
    // 删除
    deleteHandle(id) {
...........................
```

`attrgroup-add-or-update.vue`根据属性分组id查到属性分组后填充到页面

```java
init(id) {
      this.dataForm.attrGroupId = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.attrGroupId) {
          this.$http({
            url: this.$http.adornUrl(
              `/product/attrgroup/info/${this.dataForm.attrGroupId}`
            ),
            method: "get",
            params: this.$http.adornParams(),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.attrGroupName = data.attrGroup.attrGroupName;
              this.dataForm.sort = data.attrGroup.sort;
              this.dataForm.descript = data.attrGroup.descript;
              this.dataForm.icon = data.attrGroup.icon;
              this.dataForm.catelogId = data.attrGroup.catelogId;
              //查出catelogId的完整路径
              **this.dataForm.catelogPath = data.attrGroup.catelogPath;**
            }
          });
        }
      });
    },
```

将`attrgroup-add-or-update.vue`文件下的`catelogIds`全部替换成`catelogPath`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2019.png)

在 `AttrGroupEntity` 实体类中添加catelogPath字段

```java
/**
 * 三级分类修改的时候回显路径
 */
@TableField(exist = false)	//数据库中不存在
private Long[] catelogPath;
```

修改**AttrGroupController**，找到属性分组id对应的分类，然后把该分类下的所有属性分组都填充好。

```java
	
@Autowired
private CategoryService categoryService;

   /**
     * 信息
     */
    @RequestMapping("/info/{attrGroupId}")
    //@RequiresPermissions("product:attrgroup:info")
    public R info(@PathVariable("attrGroupId") Long attrGroupId) {
        AttrGroupEntity attrGroup = attrGroupService.getById(attrGroupId);

        Long catelogId = attrGroup.getCatelogId();
        Long[] path = categoryService.findCatelogPath(catelogId);

        attrGroup.setCatelogPath(path);
        return R.ok().put("attrGroup", attrGroup);
    }
```

**CategoryService**：

```java
   /**
     * 找到catelogId的完整路径；
     * [父路径/子路径/孙子路径]
     * @param catelogId
     * @return
     */
 Long[] findCatelogPath(Long catelogId);
```

**CategoryServiceImpl：**

```java
//[2,25,225]
    @Override
    public Long[] findCatelogPath(Long catelogId) {
        List<Long> paths = new ArrayList<>();
        List<Long> parentPath = findParentPath(catelogId, paths);//查出来的集合{225,34,2}是逆序的
        //使用Collections工具类将其逆序转换
        Collections.reverse(parentPath);    //完整路径：[2, 34, 225]

        return parentPath.toArray(new Long[parentPath.size()]); //将list集合转成long[]数组
    }

    //递归: 比如我们现在是查手机{225,34,2}  手机(2)/手机通讯(34)/手机(225)
    private List<Long> findParentPath(Long catelogId, List<Long> paths) {
        //1、收集当前节点id
        paths.add(catelogId);
        CategoryEntity byId = this.getById(catelogId);
        if (byId.getParentCid() != 0) { //如果当前节点有父id
            findParentPath(byId.getParentCid(), paths);
        }
        return paths;
    }
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2020.png)

优化：会话关闭时清空内容，防止下次开启还遗留数据

## 【优化】新增时不让其显示上一次修改时的【所属分类】

我们点击新增的时候{所属分类}还停留在上一次的状态，我们需要对其进行优化

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2021.png)

使用ElementUI中的Dialog对话框组件

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/dialog)

closed：Dialog 关闭动画结束时的回调；

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2022.png)

在 `attrgroup-add-or-update.vue` 下添加

```java
<template>
  <el-dialog
    :title="!dataForm.attrGroupId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
    **@closed="dialofClose"**
  >

。。。。。。。。。。。。。。。

methods: { 
    **dialofClose() {
      this.dataForm.catelogPath = [];
    },**
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2023.png)

# 75、商品服务-API-品牌管理-品牌分类关联与级联更新

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2024.png)

## MyBatis-Plus分页插件

[分页插件 | MyBatis-Plus](https://baomidou.com/guide/page.html)

MyBatis-Plus分页插件

## 实现分页功能

新建MybatisConfig类：重启gulimall-product该服务

```java
package com.atguigu.gulimall.product.config;

import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import com.baomidou.mybatisplus.extension.plugins.pagination.optimize.JsqlParserCountOptimize;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;

/**
 * @Author Dali
 * @Date 2021/8/30 13:51
 * @Version 1.0
 * @Description
 */
@Configuration
@EnableTransactionManagement    //开启事务功能
@MapperScan("com.atguigu.gulimall.product.dao")
public class MybatisConfig {
    //引入分页插件
    // 旧版
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        paginationInterceptor.setOverflow(true);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        paginationInterceptor.setLimit(1000);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}
```

分页显示效果正常

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2025.png)

 

## 修改查询的功能：

修改gulimall-product中的BrandServiceImpl类：

```java
package com.atguigu.gulimall.product.service.impl;

import org.apache.commons.lang.StringUtils;
import org.springframework.stereotype.Service;

import java.util.Map;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.atguigu.common.utils.PageUtils;
import com.atguigu.common.utils.Query;

import com.atguigu.gulimall.product.dao.BrandDao;
import com.atguigu.gulimall.product.entity.BrandEntity;
import com.atguigu.gulimall.product.service.BrandService;

@Service("brandService")
public class BrandServiceImpl extends ServiceImpl<BrandDao, BrandEntity> implements BrandService {

    @Override
    public PageUtils queryPage(Map<String, Object> params) {
        //1、获取key
        String key = (String) params.get("key");
        QueryWrapper<BrandEntity> queryWrapper = new QueryWrapper<>();

        if (!StringUtils.isEmpty(key)) {
            //brand_id 等于key 或者 name等于key
            queryWrapper.eq("brand_id", key).or().like("name", key);
        }
        IPage<BrandEntity> page = this.page(
                new Query<BrandEntity>().getPage(params),
                //new QueryWrapper<BrandEntity>()
                queryWrapper
        );

        return new PageUtils(page);
    }

}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2026.png)

## 显示关联分类功能

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2027.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2028.png)

接口文档：15、获取品牌关联的分类

[15、获取品牌关联的分类](https://easydoc.net/s/78237135/ZUqEdvA4/SxysgcEF)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2029.png)

### GET请求的几种不同的写法：`@RequestMapping(value ="/catelog/list", method = RequestMethod.GET)`等于`@GetMapping("/catelog/list")`

`@RequestMapping(value ="/catelog/list", method = RequestMethod.GET)`等于`@GetMapping("/catelog/list")`

```
@RequestMapping(value ="/catelog/list", method = RequestMethod.GET)
@GetMapping("/catelog/list")
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2030.png)

我们想要的效果：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2031.png)

BrandController：新增**updateDetail方法**

```java
/**
 * 修改
 */
@RequestMapping("/update")
//@RequiresPermissions("product:brand:update")
public R update(@Validated(UpdateGroup.class) @RequestBody BrandEntity brand) {
    //brandService.updateById(brand);
    **brandService.updateDetail(brand);**
    return R.ok();
}
```

- BrandService
  
    ```java
    package com.atguigu.gulimall.product.service;
    
    import com.baomidou.mybatisplus.extension.service.IService;
    import com.atguigu.common.utils.PageUtils;
    import com.atguigu.gulimall.product.entity.BrandEntity;
    
    import java.util.Map;
    
    /**
     * 品牌
     *
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:21
     */
    public interface BrandService extends IService<BrandEntity> {
    
        PageUtils queryPage(Map<String, Object> params);
    
        void updateDetail(BrandEntity brand);
    }
    ```
    
- BrandServiceImpl
  
    ```java
    package com.atguigu.gulimall.product.service.impl;
    
    import com.atguigu.gulimall.product.service.CategoryBrandRelationService;
    import org.apache.commons.lang.StringUtils;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    import java.util.Map;
    
    import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
    import com.baomidou.mybatisplus.core.metadata.IPage;
    import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
    import com.atguigu.common.utils.PageUtils;
    import com.atguigu.common.utils.Query;
    
    import com.atguigu.gulimall.product.dao.BrandDao;
    import com.atguigu.gulimall.product.entity.BrandEntity;
    import com.atguigu.gulimall.product.service.BrandService;
    import org.springframework.transaction.annotation.Transactional;
    
    @Service("brandService")
    public class BrandServiceImpl extends ServiceImpl<BrandDao, BrandEntity> implements BrandService {
    
        **@Autowired
        CategoryBrandRelationService categoryBrandRelationService;**
    
        @Override
        public PageUtils queryPage(Map<String, Object> params) {
            //1、获取key
            String key = (String) params.get("key");
            QueryWrapper<BrandEntity> queryWrapper = new QueryWrapper<>();
    
            if (!StringUtils.isEmpty(key)) {
                //brand_id 等于key 或者 name等于key
                queryWrapper.eq("brand_id", key).or().like("name", key);
            }
            IPage<BrandEntity> page = this.page(
                    new Query<BrandEntity>().getPage(params),
                    //new QueryWrapper<BrandEntity>()
                    queryWrapper
            );
    
            return new PageUtils(page);
        }
    
        **@Transactional  //事务注解=》 同时更新操作了不同的数据，保证数据的一致，所以加个事务
        @Override
        public void updateDetail(BrandEntity brand) {
            //保证冗余字段的数据一致
            this.updateById(brand);
            //如果品牌名不为空
            if(!StringUtils.isEmpty(brand.getName())){
                //同步更新其他关联表中的数据
                categoryBrandRelationService.updateBrand(brand.getBrandId(),brand.getName());
                //TODO 更新其他关联
            }
        }**
    
    }
    ```
    
- CategoryBrandRelationService
  
    ```java
    package com.atguigu.gulimall.product.service;
    
    import com.baomidou.mybatisplus.extension.service.IService;
    import com.atguigu.common.utils.PageUtils;
    import com.atguigu.gulimall.product.entity.CategoryBrandRelationEntity;
    
    import java.util.Map;
    
    /**
     * 品牌分类关联
     *
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:21
     */
    public interface CategoryBrandRelationService extends IService<CategoryBrandRelationEntity> {
    
        PageUtils queryPage(Map<String, Object> params);
    
        void saveDetail(CategoryBrandRelationEntity categoryBrandRelation);
    
        **void updateBrand(Long brandId, String name);**
    
        void updateCategory(Long catId, String name);
    }
    ```
    
- CategoryBrandRelationServiceImpl
  
    ```java
    @Override
    public void updateBrand(Long brandId, String name) {
        CategoryBrandRelationEntity relationEntity = new CategoryBrandRelationEntity();
        relationEntity.setBrandId(brandId);
        relationEntity.setBrandName(name);
        this.update(relationEntity, new UpdateWrapper<CategoryBrandRelationEntity>().eq("brand_id", brandId));
    }
    ```
    

### 修改分类维护使其在品牌管理中的关联分类里面正常读取数据

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2032.png)

我们需要实现的效果：先修改分类维护中的**手机111**将其改成**手机333**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2033.png)

在品牌管理中**关联分类**里面的**分类名**要同步读取到正常修改之后的数据。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2034.png)

- CategoryController
  
    ```java
       /**
         * 修改
         */
        @RequestMapping("/update")
        //@RequiresPermissions("product:category:update")
        public R update(@RequestBody CategoryEntity category) {
        // categoryService.updateById(category);
            categoryService.updateCascade(category);
    
            return R.ok();
        }
    ```
    
- CategoryService
  
    ```java
    package com.atguigu.gulimall.product.service;
    
    import com.baomidou.mybatisplus.extension.service.IService;
    import com.atguigu.common.utils.PageUtils;
    import com.atguigu.gulimall.product.entity.CategoryEntity;
    
    import java.util.List;
    import java.util.Map;
    
    /**
     * 商品三级分类
     *
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:21
     */
    public interface CategoryService extends IService<CategoryEntity> {
    
        PageUtils queryPage(Map<String, Object> params);
    
        List<CategoryEntity> listWithTree();
    
        void removeMenuByIds(List<Long> asList);
    
        /**
         * 找到catelogId的完整路径；
         * [父路径/子路径/孙子路径]
         * @param catelogId
         * @return
         */
        Long[] findCatelogPath(Long catelogId);
    
        **/**
         * 级联更新
         * @param category
         */
        void updateCascade(CategoryEntity category);**
    }
    ```
    
- CategoryServiceImpl
  
    ```java
    /**
         * 级联更新所有关联的数据
         *方式一：通过xml文件格式操作数据库写法：目前报错：{"msg":"参数格式校验失败","code":10001}
         * @param category
         */
        @Transactional  //这是一个事务= 更新自己还有更新级联的数据，所以加个事务
        @Override
        public void updateCascade(CategoryEntity category) {
            //首先更新自己:一般都是通过this的方式通过id更新
            this.updateById(category);    //视频中这么写的
    //        categoryService.updateById(category);
            //更新关联表中的数据
            categoryBrandRelationService.updateCategory(category.getCatId(), category.getName());
        }
    
    //    /**
    //     * 级联更新所有关联的数据
    //     * 方式二：通过mybatis-plus的方式直接修改数据库
    //     * @param category
    //     */
    //    @Transactional  //这是一个事务= 更新自己还有更新级联的数据，所以加个事务
    //    @Override
    //    public void updateCascade(CategoryEntity category) {
    //        //首先更新自己:一般都是通过this的方式通过id更新
    //        this.updateById(category);    //视频中这么写的
    ////        categoryService.updateById(category);
    //        //更新关联表中的数据
    //        if (!StringUtils.isEmpty(category.getName())) {
    //            categoryBrandRelationService.updateCategory(category.getCatId(), category.getName());
    //        }
    //    }
    ```
    
- CategoryBrandRelationService
  
    ```java
    package com.atguigu.gulimall.product.service;
    
    import com.baomidou.mybatisplus.extension.service.IService;
    import com.atguigu.common.utils.PageUtils;
    import com.atguigu.gulimall.product.entity.CategoryBrandRelationEntity;
    
    import java.util.Map;
    
    /**
     * 品牌分类关联
     *
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:21
     */
    public interface CategoryBrandRelationService extends IService<CategoryBrandRelationEntity> {
    
        PageUtils queryPage(Map<String, Object> params);
    
        void saveDetail(CategoryBrandRelationEntity categoryBrandRelation);
    
        void updateBrand(Long brandId, String name);
    
        **void updateCategory(Long catId, String name);**
    }
    ```
    
- CategoryBrandRelationServiceImpl
  
    ```java
    //方式一：
        @Override
        public void updateCategory(Long catId, String name) {
            this.baseMapper.updateCategory(catId, name);
        }
    
    //    /**
    //     * 方式二：
    //     * @param catId
    //     * @param name
    //     */
    //    @Override
    //    public void updateCategory(Long catId, String name) {
    //        CategoryBrandRelationEntity entity = new CategoryBrandRelationEntity();
    //        entity.setCatelogId(catId);
    //        entity.setCatelogName(name);
    //        // 将所有品牌id为 catId 的进行更新
    //        this.update(entity, new UpdateWrapper<CategoryBrandRelationEntity>().eq("catelog_id", catId));
    //    }
    ```
    
- CategoryBrandRelationDao
  
    ```java
    package com.atguigu.gulimall.product.dao;
    
    import com.atguigu.gulimall.product.entity.CategoryBrandRelationEntity;
    import com.baomidou.mybatisplus.core.mapper.BaseMapper;
    import org.apache.ibatis.annotations.Mapper;
    import org.apache.ibatis.annotations.Param;
    
    /**
     * 品牌分类关联
     *
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:21
     */
    @Mapper
    public interface CategoryBrandRelationDao extends BaseMapper<CategoryBrandRelationEntity> {
    
        void updateCategory(@Param("catId") Long catId, @Param("name") String name);
    }
    ```
    
- CategoryBrandRelationDao.xml
  
    ```java
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
    <mapper namespace="com.atguigu.gulimall.product.dao.CategoryBrandRelationDao">
    
        <!-- 可根据自己的需求，是否要使用 -->
        <resultMap type="com.atguigu.gulimall.product.entity.CategoryBrandRelationEntity" id="categoryBrandRelationMap">
            <result property="id" column="id"/>
            <result property="brandId" column="brand_id"/>
            <result property="catelogId" column="catelog_id"/>
            <result property="brandName" column="brand_name"/>
            <result property="catelogName" column="catelog_name"/>
        </resultMap>
        <update id="updateCategory">
           UPDATE `pms_category_brand_relation` SET catelog_name=#{name} WHERE catelog_id=#{catId}
        </update>
    
    </mapper>
    ```
    

### MyBatis-Plus SQL语句的正确写法：**是使用` `而不是单引号' '**

```java
**MyBatis-Plus SQL语句的正确写法 是使用``而不是单引号''**
UPDATE `pms_category_brand_relation` SET catelog_name = #{name} WHERE catelog_id = #{catId}
-----------------------------------------------------------------------

--          UPDATE 'pms_category_brand_relation' SET catelog_name=#{name} WHERE catelog_id=#{catId}   --错误写法
--          UPDATE pms_category_brand_relation SET catelog_name=#{name} WHERE catelog_id=#{catId}  --错误写法

		<!--动态SQL-->
    <!--    DELETE FROM `pms_attr_attrgroup_relation` WHERE(attr_id = 1 AND attr_group_id =1) OR (attr_id = 3 AND attr_group_id =2)-->
    <delete id="deleteBatchRelation">
        DELETE FROM `pms_attr_attrgroup_relation` WHERE
        <foreach collection="entities" item="item" separator=" OR ">
            (attr_id=#{item.attrId} AND attr_group_id=#{item.attrGroupId})
        </foreach>
    </delete>
```

使用了错误的写法容易出现下面的错误：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2035.png)

这节课完结撒花：最终实现下面的效果：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2036.png)

# 76、商品服务-API-平台属性规格参数新增与VO

## P76节课对本人代码进行如下勘误：

在**人人快速开发后台**界面中的**商品系统**→**平台属性**→**规格参数**→**新增**填写完信息之后对其**保存**，提示`{"msg":"参数格式校验失败","code":10001}` 报错信息。

在gulimall-product中将`AttrEntity` 实体类中的`//private Integer valueType;` 字段删除，就可以正常保存了。

当有新增字段时，我们往往会在entity实体类中新建一个字段，`@TableField(exist = false)`并标注数据库中不存在该字段

```java
如在一些Entity中，为了让mybatis-plus与知道某个字段不与数据库匹配，
    那么就加个
    @TableField(exist=false)
    private Long attrGroupId;
```

然而这种方式并不规范，比较规范的做法是，新建一个vo文件夹，将每种不同的对象，按照它的功能进行了划分。在java中，涉及到了这几种类型：

## Object划分：PO、DO、TO、DTO、VO

### 1.PO(persistant object)持久对象

PO就是对应数据库中某个表中的一条记录，多个记录可以用PO的集合。PO中应该不包含任何对数据的操作。

### [2.DO](http://2.do/) (Domain Object)领域对象

就是从现实世界中抽象出来的有形或无形的业务实体。

### [3.TO](http://3.to/)(Transfer Object)，数据传输对象

不同的应用程序之间传输的对象。微服务

### 4.DTO(Data Transfer Obiect)数据传输对象

这个概念来源于J2EE的设计模式，原来的目的是为了EJB 的分布式应用提供粗粒度的数据实体，以减少分布式调用的次数，从而提高分布式调用的性能和降低网络负载，但在这里，泛指用于**展示层与服务层**之间的数据传输对象。

### 5.VO(value object)值对象

通常用于业务层之间的数据传递，和PO一样也是仅仅包含数据而已。但应是抽象出的业务对象，可以和表对应,也可以不,这根据业务的需要。用new关键字创建，由GC回收的。

**VO也可以理解成：View object：视图对象   他的主要作用：**

接受页面传递来的对象，封装对象

将业务处理完成的对象，封装成页面要用的数据

不是entity的全量数据都可以封装vo。

Vo里面对应的都是接口文档里面的响应数据字段，比如 ：14、获取分类关联的品牌：[https://easydoc.net/s/78237135/ZUqEdvA4/HgVjlzWV](https://easydoc.net/s/78237135/ZUqEdvA4/HgVjlzWV)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2037.png)

### [6.BO](http://6.bo/)(business object)业务对象

从业务模型的角度看,见UML元件领域模型中的领域对象。封装业务逻辑的java对象,通过调用DAO方法,结合PO,VO进行业务操作。business object: 业务对象主要作用是把业务逻辑封装为一个对象。这个对象可以包括一一个或多个其它的对象。比如一个简历，有教育经历、工作经历、社会关系等等。我们可以把教育经历对应一个 PO，工作经历对应一个PO，社会关系对应一个PO。建立一个对应简历的BO对象处理简历，每个BO包含这些PO。这样处理业务逻辑时，我们就可以针对BO去处理。

### 7.POJO(plain ordinary java object)简单无规则java 对象

传统意义的java 对象。就是说在一些Object/Relation Mapping工具中，能够做到维护数据库表记录的persisent object完全是一一个符合Java Bean规范的纯Java 对象，没有增加别的属性和方法。我的理解就是最基本的java Bean， 只有属性字段及setter和getter方法!。

**POJO是DO/DTO/BO/VO的统称。**

### 8.DAO(data access object)数据访问对象

是一个sun的一个标准j2ee 设计模式，这个模式中有个接口就是DAO，它负持久层的操作。为业务层提供接口。此对象用于访问数据库。通常和PO结合使用，DAO中包含了各种数据库的操作方法。通过它的方法,结合PO对数据库进行相关的操作。夹在业务逻辑与数据库资源中间。配合Vo,提供数据库的CRUD操作。

![参考：[《Java_开发手册》嵩山版.pdf (amazonaws.com)](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2038.png) 3. 【参考】分层领域模型规约章节](08_%E5%95%86%E5%93%81%E6%9C%8D%E5%8A%A1-%E5%B1%9E%E6%80%A7%E5%88%86%E7%BB%84&%E5%85%B3%E8%81%94&%E7%A7%BB%E9%99%A4%20%E5%93%81%E7%89%8C%E7%AE%A1%E7%90%86-%E5%93%81%E7%89%8C%E5%88%86%E7%B1%BB%E5%85%B3%E8%81%94%E4%B8%8E%E7%BA%A7%E8%81%94%E6%9B%B4%E6%96%B0%20%E5%B9%B3%E5%8F%B0%E5%B1%9E%E6%80%A7-%E8%A7%84%E6%A0%BC%E4%BF%AE%E6%94%B9%208037f8da968b4ccca88fe7b1c1e72b6b/Untitled%2038.png)

参考：[《Java_开发手册》嵩山版.pdf (amazonaws.com)](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/b89734cb-4eb6-46cf-b08c-9c32beaa78d0/%E3%80%8AJava_%E5%BC%80%E5%8F%91%E6%89%8B%E5%86%8C%E3%80%8B%E5%B5%A9%E5%B1%B1%E7%89%88.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211215%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211215T011341Z&X-Amz-Expires=86400&X-Amz-Signature=7fd189bc9176f94f7c107bb0c5f58d926411e7f94e1609989887484c224bb27a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25E3%2580%258AJava%2520%25E5%25BC%2580%25E5%258F%2591%25E6%2589%258B%25E5%2586%258C%25E3%2580%258B%25E5%25B5%25A9%25E5%25B1%25B1%25E7%2589%2588.pdf%22&x-id=GetObject) 3. 【参考】分层领域模型规约章节

1. 【参考】分层领域模型规约：
• DO（Data Object）：此对象与数据库表结构一一对应，通过 DAO 层向上传输数据源对象。
• DTO（Data Transfer Object）：数据传输对象，Service 或 Manager 向外传输的对象。
• BO（Business Object）：业务对象，可以由 Service 层输出的封装业务逻辑的对象。
• Query：数据查询对象，各层接收上层的查询请求。注意超过 2 个参数的查询封装，禁止使用 Map 类
来传输。
• VO（View Object）：显示层对象，通常是 Web 向模板渲染引擎层传输的对象。
- 修改AttrGroupController控制层的AttrGroupServiceImpl
  
    ```java
    @Override
        public PageUtils queryPage(Map<String, Object> params, Long catelogId) {
            String key = (String) params.get("key");
            //SELECT * from pms_attr_group WHERE catelog_id = ? AND (attr_group_id=key or attr_group_name LIKE %key%)
            QueryWrapper<AttrGroupEntity> wrapper = new QueryWrapper<AttrGroupEntity>();
            if (StringUtils.isNotEmpty(key)) {
                wrapper.and((obj) -> {
                    obj.eq("attr_group_id", key).or().like("attr_group_name", key);
                });
            }
    
            if (catelogId == 0) {
                IPage<AttrGroupEntity> page = this.page(new Query<AttrGroupEntity>().getPage(params),
                        wrapper);
                return new PageUtils(page);
            } else {
                wrapper.eq("catelog_id", catelogId);
                IPage<AttrGroupEntity> page = this.page(new Query<AttrGroupEntity>().getPage(params),
                        wrapper);
                return new PageUtils(page);
            }
    
        }
    ```
    
- AttrVo：
  
    ```java
    package com.atguigu.gulimall.product.vo;
    
    import com.baomidou.mybatisplus.annotation.TableId;
    import lombok.Data;
    
    /**
     * @Author Dali
     * @Date 2021/8/31 17:36
     * @Version 1.0
     * @Description
     */
    @Data
    public class AttrVo {
        /**
         * 属性id
         */
    //    @TableId  //VO里面不需要标注跟数据库有关的注解了
        private Long attrId;
        /**
         * 属性名
         */
        private String attrName;
        /**
         * 是否需要检索[0-不需要，1-需要]
         */
        private Integer searchType;
        /**
         * 属性图标
         */
        private String icon;
        /**
         * 可选值列表[用逗号分隔]
         */
        private String valueSelect;
        /**
         * 属性类型[0-销售属性，1-基本属性
         */
        private Integer attrType;
        /**
         * 启用状态[0 - 禁用，1 - 启用]
         */
        private Long enable;
        /**
         * 所属分类
         */
        private Long catelogId;
        /**
         * 快速展示【是否展示在介绍上；0-否 1-是】，在sku中仍然可以调整
         */
        private Integer showDesc;
    
        /**
         * 分组的id
         */
        private Long attrGroupId;
    }
    ```
    
- AttrEntity
  
    ```java
    package com.atguigu.gulimall.product.entity;
    
    import com.baomidou.mybatisplus.annotation.TableField;
    import com.baomidou.mybatisplus.annotation.TableId;
    import com.baomidou.mybatisplus.annotation.TableName;
    
    import java.io.Serializable;
    import java.util.Date;
    import lombok.Data;
    
    /**
     * 商品属性
     * 
     * @author ÍõÈ½ê¿
     * @email daki9981@qq.com
     * @date 2021-08-13 19:53:20
     */
    @Data
    @TableName("pms_attr")
    public class AttrEntity implements Serializable {
    	private static final long serialVersionUID = 1L;
    
    	/**
    	 * 属性id
    	 */
    	@TableId
    	private Long attrId;
    	/**
    	 * 属性名
    	 */
    	private String attrName;
    	/**
    	 * 是否需要检索[0-不需要，1-需要]
    	 */
    	private Integer searchType;
    	/**
    	 * 属性图标
    	 */
    	private String icon;
    	/**
    	 * 可选值列表[用逗号分隔]
    	 */
    	private String valueSelect;
    	/**
    	 * 属性类型[0-销售属性，1-基本属性
    	 */
    	private Integer attrType;
    	/**
    	 * 启用状态[0 - 禁用，1 - 启用]
    	 */
    	private Long enable;
    	/**
    	 * 所属分类
    	 */
    	private Long catelogId;
    	/**
    	 * 快速展示【是否展示在介绍上；0-否 1-是】，在sku中仍然可以调整
    	 */
    	private Integer showDesc;
    
    //	@TableField(exist = false)	//不属于数据库表中的值使用的注解
    //	private Long attrGroupId;
    	/**
    	 *
    	 */
    	//private Integer valueType;
    
    }
    ```
    
- 修改gulimall-product中的AttrController
  
    ```java
    /**
         * 保存
         */
        @RequestMapping("/save")
        //@RequiresPermissions("product:attr:save")
        public R save(@RequestBody AttrVo attr){
    //		attrService.save(attr);
    		attrService.saveAttr(attr);
    
            return R.ok();
        }
    ```
    
- AttrServiceImpl
  
    ```java
    @Autowired
        AttrAttrgroupRelationDao relationDao;
    
    @Transactional  //开启事务注解
        @Override
        public void saveAttr(AttrVo attr) { //attr ： VO
            AttrEntity attrEntity = new AttrEntity();   //attrEntity : PO
    //        attrEntity.setAttrName(attr.getAttrName());
            BeanUtils.copyProperties(attr, attrEntity);
            //1、保存基本数据
            this.save(attrEntity);
            //2、保存关联关系
            AttrAttrgroupRelationEntity relationEntity = new AttrAttrgroupRelationEntity();
            relationEntity.setAttrGroupId(attr.getAttrGroupId());
            relationEntity.setAttrId(attrEntity.getAttrId());
            relationDao.insert(relationEntity);
        }
    ```
    

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2039.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2040.png)

# P77商品服务-API-平台属性规格参数列表

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2041.png)

## 谷粒商城接口文档地址：

[03、获取分类属性分组](https://easydoc.net/s/78237135/ZUqEdvA4/OXTgKobR)

对应的接口文档地址：05、获取分类规格参数：[https://easydoc.net/s/78237135/ZUqEdvA4/Ld1Vfkcd](https://easydoc.net/s/78237135/ZUqEdvA4/Ld1Vfkcd)

**AttrController：**

```java
//product/attr/base/list/{catelogId}
@GetMapping("/base/list/{catelogId}")
public R baseAttrList(@RequestParam Map<String, Object> params,
                      @PathVariable("catelogId") long catelogId) {
    //params:分页的条件，catelogId三级分类的id
    PageUtils page = attrService.queryBaseAttrPage(params, catelogId);
    return R.ok().put("page", page);
}
```

**AttrService：自动创建**

```java
PageUtils queryBaseAttrPage(Map<String, Object> params, long catelogId);
```

AttrServiceImpl：

```java
@Autowired
AttrGroupDao attrGroupDao;
@Autowired
CategoryDao categoryDao;

@Override
public PageUtils queryBaseAttrPage(Map<String, Object> params, long catelogId) {
    QueryWrapper<AttrEntity> queryWrapper = new QueryWrapper<>();
    if (catelogId != 0) {    //如果有分类， catelogId==0查询全部
        queryWrapper.eq("catelog_id", catelogId);   //如果分类id等于我们指定的值
    }
    String key = (String) params.get("key");    //按照key进行检索
    if (!StringUtils.isEmpty(key)) {   //如果检索条件存在
        //attr_id  attr_name
        queryWrapper.and((wrapper) -> {
            wrapper.eq("attr_id", key).or().like("attr_name", key);
        });
    }
    //分页方法
    IPage<AttrEntity> page = this.page(
            new Query<AttrEntity>().getPage(params), queryWrapper
    );
    PageUtils pageUtils = new PageUtils(page);
    List<AttrEntity> records = page.getRecords();
    List<AttrRespVo> respVos = records.stream().map(attrEntity -> {
        AttrRespVo attrRespVo = new AttrRespVo();
        BeanUtils.copyProperties(attrEntity, attrRespVo);    //把attrEntity中的属性拷贝到attrRespVo中
        //1、设置分类和分组的名字
        AttrAttrgroupRelationEntity attrId = relationDao.selectOne(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attrEntity.getAttrId()));
        //Long attrGroupId = attrId.getAttrGroupId();
        if (attrId != null) {
            AttrGroupEntity attrGroupEntity = attrGroupDao.selectById(attrId.getAttrGroupId());
            attrRespVo.setGroupName(attrGroupEntity.getAttrGroupName());
        }
        CategoryEntity categoryEntity = categoryDao.selectById(attrEntity.getCatelogId());  //返回分类的实体信息
        if (categoryEntity != null) {
            attrRespVo.setCatelogName(categoryEntity.getName());
        }
        return attrRespVo;
    }).collect(Collectors.toList());
    pageUtils.setList(respVos);
    return pageUtils;
}
```

### 后端启动以下服务：（nacos）虚拟机：VirtualBox（docker自启动）前端【npm run dev】

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2042.png)

### 人人快速开发平台测试地址：[http://localhost:8001/#/product-baseattr](http://localhost:8001/#/product-baseattr)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2043.png)

# P78-商品服务-API-平台属性-规格修改

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2044.png)

**07、查询属性详情**（接口文档）：[https://easydoc.net/s/78237135/ZUqEdvA4/7C3tMIuF](https://easydoc.net/s/78237135/ZUqEdvA4/7C3tMIuF)

mybatis-plus：

[简介 | MyBatis-Plus](https://mp.baomidou.com/guide/#%E7%89%B9%E6%80%A7)

AttrController：

```java
/**
 * 信息
 */
@RequestMapping("/info/{attrId}")
//@RequiresPermissions("product:attr:info")
public R info(@PathVariable("attrId") Long attrId) {
    //AttrEntity attr = attrService.getById(attrId);
    AttrRespVo attrRespVo = attrService.getAttrInfo(attrId);
    return R.ok().put("attr", attrRespVo);
}

/**
 * 修改
 */
@RequestMapping("/update")
//@RequiresPermissions("product:attr:update")
public R update(@RequestBody AttrVo attr) {
    attrService.updateAttr(attr);
    return R.ok();
}
```

AttrService：

```java
AttrRespVo getAttrInfo(Long attrId);

void updateAttr(AttrVo attr);
```

AttrServiceImpl：

```java
@Override
public AttrRespVo getAttrInfo(Long attrId) {
    AttrRespVo respVo = new AttrRespVo();
    AttrEntity attrEntity = this.getById(attrId);
    //将查出的attrEntity中的数据拷贝到respVo中
    BeanUtils.copyProperties(attrEntity, respVo);
      QueryWrapper:
      继承自 AbstractWrapper ,自身的内部属性 entity 也用于生成 where 条件
      及 LambdaQueryWrapper, 可以通过 new QueryWrapper().lambda() 方法获取
    //1、设置分组信息
    AttrAttrgroupRelationEntity attrgroupRelation = relationDao.selectOne(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attrId));
    if (attrgroupRelation != null) {
        respVo.setAttrGroupId(attrgroupRelation.getAttrGroupId());  //查询分组的id
        AttrGroupEntity attrGroupEntity = attrGroupDao.selectById(attrgroupRelation.getAttrGroupId());
        if (attrGroupEntity != null) {
            respVo.setGroupName(attrGroupEntity.getAttrGroupName());
        }
    }
    //2、设置分类信息
    Long catelogId = attrEntity.getCatelogId();
    Long[] catelogPath = categoryService.findCatelogPath(catelogId);
    respVo.setCatelogPath(catelogPath);    //分类完整路径
    CategoryEntity categoryEntity = categoryDao.selectById(catelogId);
    if (categoryEntity != null) {
        respVo.setCatelogName(categoryEntity.getName());
    }
    return respVo;
}

@Transactional//开启事务注解
@Override
public void updateAttr(AttrVo attr) {
    AttrEntity attrEntity = new AttrEntity();
    BeanUtils.copyProperties(attr, attrEntity);
    this.updateById(attrEntity);
    //1、修改分组关联
    AttrAttrgroupRelationEntity relationEntity = new AttrAttrgroupRelationEntity();
    relationEntity.setAttrGroupId(attr.getAttrGroupId());
    relationEntity.setAttrId(attr.getAttrId());
    // 根据 Wrapper 条件，查询总记录数
    Integer count = relationDao.selectCount(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attr.getAttrId()));
    if (count > 0) {    //修改
        relationDao.update(relationEntity, new UpdateWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attr.getAttrId()));
    } else { //新增
        relationDao.insert(relationEntity);
    }
}
```

# P79、商品服务-API-平台属性销售属性维护



![页面展示：](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2045.png)

页面展示：

F12：

```java
Request URL: http://localhost:88/api/product/attr/**sale**/list/0?t=1633163109495&page=1&limit=10&key=
```

此处的 **`sale` 是动态的，别地方可能会传入`base` 也可能是 `sale` 这时候就需要在** `@GetMapping("/{**attrType**}/list/{catelogId}")` 使用`{attrType}` 动态获取url路径地址。

**AttrController：**

```java
		//product/attr/base/list/{catelogId}
    //product/attr/sale/list/{catelogId}
    @GetMapping("/{**attrType**}/list/{catelogId}")
    public R baseAttrList(@RequestParam Map<String, Object> params,
                          @PathVariable("catelogId") long catelogId,
                          @PathVariable("attrType") String type) {
        //params:分页的条件，catelogId三级分类的id
        PageUtils page = attrService.queryBaseAttrPage(params, catelogId, type);
        return R.ok().put("page", page);
    }
```

**AttrService：自动生成**

```java
PageUtils queryBaseAttrPage(Map<String, Object> params, long catelogId, String type);
```

- **AttrServiceImpl：实现类**
  
    queryBaseAttrPage：
    
    ```java
    		@Override
        public PageUtils queryBaseAttrPage(Map<String, Object> params, long catelogId, String type) {
            **QueryWrapper<AttrEntity> queryWrapper = new QueryWrapper<AttrEntity>().eq("attr_type", "base".equalsIgnoreCase(type) ? ProductConstant.AttrEnum.ATTR_TYPE_BASE.getCode() : ProductConstant.AttrEnum.ATTR_TYPE_SALE.getCode());**
            if (catelogId != 0) {    //如果有分类， catelogId==0查询全部
                queryWrapper.eq("catelog_id", catelogId);   //如果分类id等于我们指定的值
            }
            String key = (String) params.get("key");    //按照key进行检索
            if (!StringUtils.isEmpty(key)) {   //如果检索条件存在
                //attr_id  attr_name
                queryWrapper.and((wrapper) -> {
                    wrapper.eq("attr_id", key).or().like("attr_name", key);
                });
            }
            //分页方法
            IPage<AttrEntity> page = this.page(
                    new Query<AttrEntity>().getPage(params), queryWrapper
            );
            PageUtils pageUtils = new PageUtils(page);
            List<AttrEntity> records = page.getRecords();
            List<AttrRespVo> respVos = records.stream().map(attrEntity -> {
                AttrRespVo attrRespVo = new AttrRespVo();
                BeanUtils.copyProperties(attrEntity, attrRespVo);    //把attrEntity中的属性拷贝到attrRespVo中
    
                **//1、设置分类和分组的名字
                if ("base".equalsIgnoreCase(type)) {
                    AttrAttrgroupRelationEntity attrId = relationDao.selectOne(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attrEntity.getAttrId()));
                    //Long attrGroupId = attrId.getAttrGroupId();
                    if (attrId != null) {
                        AttrGroupEntity attrGroupEntity = attrGroupDao.selectById(attrId.getAttrGroupId());
                        attrRespVo.setGroupName(attrGroupEntity.getAttrGroupName());
                    }
                }**
    
                CategoryEntity categoryEntity = categoryDao.selectById(attrEntity.getCatelogId());  //返回分类的实体信息
                if (categoryEntity != null) {
                    attrRespVo.setCatelogName(categoryEntity.getName());
                }
    
                return attrRespVo;
            }).collect(Collectors.toList());
            pageUtils.setList(respVos);
            return pageUtils;
        }
    ```
    
    saveAttr：
    
    ```java
    @Transactional  //开启事务注解
        @Override
        public void saveAttr(AttrVo attr) { //attr ： VO
            AttrEntity attrEntity = new AttrEntity();   //attrEntity : PO
    //        attrEntity.setAttrName(attr.getAttrName());
            BeanUtils.copyProperties(attr, attrEntity);
            //1、保存基本数据
            this.save(attrEntity);
            //2、保存关联关系
            **if (attr.getAttrType() == ProductConstant.AttrEnum.ATTR_TYPE_BASE.getCode()) {  //==1说明这是一个基本属性
                AttrAttrgroupRelationEntity relationEntity = new AttrAttrgroupRelationEntity();
                relationEntity.setAttrGroupId(attr.getAttrGroupId());
                relationEntity.setAttrId(attrEntity.getAttrId());
                relationDao.insert(relationEntity);
            }**
        }
    ```
    
    getAttrInfo：
    
    ```java
    @Override
        public AttrRespVo getAttrInfo(Long attrId) {
            AttrRespVo respVo = new AttrRespVo();
            AttrEntity attrEntity = this.getById(attrId);
            //将查出的attrEntity中的数据拷贝到respVo中
            BeanUtils.copyProperties(attrEntity, respVo);
    
    //        QueryWrapper:
    //        继承自 AbstractWrapper ,自身的内部属性 entity 也用于生成 where 条件
    //        及 LambdaQueryWrapper, 可以通过 new QueryWrapper().lambda() 方法获取
            **if (attrEntity.getAttrType() == ProductConstant.AttrEnum.ATTR_TYPE_BASE.getCode()) {
                //1、设置分组信息
                AttrAttrgroupRelationEntity attrgroupRelation = relationDao.selectOne(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attrId));
                if (attrgroupRelation != null) {
                    respVo.setAttrGroupId(attrgroupRelation.getAttrGroupId());  //查询分组的id
                    AttrGroupEntity attrGroupEntity = attrGroupDao.selectById(attrgroupRelation.getAttrGroupId());
                    if (attrGroupEntity != null) {
                        respVo.setGroupName(attrGroupEntity.getAttrGroupName());
                    }
                }
            }**
    
            //2、设置分类信息
            Long catelogId = attrEntity.getCatelogId();
            Long[] catelogPath = categoryService.findCatelogPath(catelogId);
            respVo.setCatelogPath(catelogPath);    //分类完整路径
    
            CategoryEntity categoryEntity = categoryDao.selectById(catelogId);
            if (categoryEntity != null) {
                respVo.setCatelogName(categoryEntity.getName());
            }
    
            return respVo;
        }
    ```
    
    updateAttr：
    
    ```java
    @Transactional
    @Override
    public void updateAttr(AttrVo attr) {
        AttrEntity attrEntity = new AttrEntity();
        BeanUtils.copyProperties(attr, attrEntity);
        this.updateById(attrEntity);
    
        **if (attrEntity.getAttrType() == ProductConstant.AttrEnum.ATTR_TYPE_BASE.getCode()) {
    			//1、修改分组关联
            AttrAttrgroupRelationEntity relationEntity = new AttrAttrgroupRelationEntity();
            relationEntity.setAttrGroupId(attr.getAttrGroupId());
            relationEntity.setAttrId(attr.getAttrId());
            // 根据 Wrapper 条件，查询总记录数
            Integer count = relationDao.selectCount(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attr.getAttrId()));
            if (count > 0) {    //修改
                relationDao.update(relationEntity, new UpdateWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attr.getAttrId()));
            } else { //新增
                relationDao.insert(relationEntity);
            }**
        }
    }
    ```
    

数据库表：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2046.png)

由于表：pms_attr中的attr_type字段是可选的（为了后期方便维护：比如增加一个2-[]）我们需要新建一个枚举类：

**ProductConstant：枚举类湘湖风情苑**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2047.png)

```java
package com.atguigu.common.constant;

/**
 * @Author Dali
 * @Date 2021/10/2 18:06
 * @Version 1.0
 * @Description: 数据库表：pms_attr 表中attr_type字段：属性类型[0-销售属性，1-基本属性]
 */
public class ProductConstant {
    public enum AttrEnum {
        ATTR_TYPE_BASE(1, "基本属性"), ATTR_TYPE_SALE(0, "销售属性");

        private int code;
        private String msg;

        AttrEnum(int code, String msg) {
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
}
```

然后测试新增与修改等功能

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2048.png)

# P80、商品服务API-平台属性查询分组关联属性&删除关联



## 1、平台属性查询分组关联属性：

页面原型：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2049.png)

**参考接口文档：**

10、获取属性分组的关联的所有属性：[https://easydoc.net/s/78237135/ZUqEdvA4/LnjzZHPj](https://easydoc.net/s/78237135/ZUqEdvA4/LnjzZHPj)

![10、获取属性分组的关联的所有属性](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2050.png)



AttrGroupController：

```java
		@Autowired
    AttrService attrService;

    ///product/attrgroup/{attrgroupId}/attr/relation    谷粒商城接口文档：10、获取属性分组的关联的所有属性
    @GetMapping("/{attrgroupId}/attr/relation")
    public R attrRelation(@PathVariable("attrgroupId") Long attrgroupId) {
        List<AttrEntity> entities = attrService.getRelationAttr(attrgroupId);
        return R.ok().put("data", entities);
    }
```

AttrService：alt+enter自动生成

```java
List<AttrEntity> getRelationAttr(Long attrgroupId);
```

AttrServiceImpl：

```java
/**
 * 根据分组id找到组内关联的所有属性（基本属性）
 *
 * @param attrgroupId
 * @return
 */
@Override
public List<AttrEntity> getRelationAttr(Long attrgroupId) {
//==>  Preparing: SELECT id,attr_id,attr_group_id,attr_sort FROM pms_attr_attrgroup_relation WHERE (attr_group_id = ?)
    **List<AttrAttrgroupRelationEntity> entities = relationDao.selectList(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_group_id", attrgroupId));
    List<Long> attrIds = entities.stream().map((attr) -> {
        return attr.getAttrId();
    }).collect(Collectors.toList());//最终把attrId收集成一个集合**

    Collection<AttrEntity> attrEntities = this.listByIds(attrIds);   //按照属性的id集合查出所有的属性信息
    return (List<AttrEntity>) attrEntities;
}
```

> 解释上述代码片段：
> 

`List<AttrAttrgroupRelationEntity> entities = relationDao.selectList(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_group_id", attrgroupId));` 

等于：==> Preparing: `SELECT **id,attr_id,attr_group_id,attr_sort** FROM pms_attr_attrgroup_relation WHERE (attr_group_id = ?)`

打开人人前台页面：[http://localhost:8001/#/product-attrgroup](http://localhost:8001/#/product-attrgroup) 发现所有的属性都已经被查到，并显示在前端页面。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2051.png)

## 2、删除关联：删除属性与分组的关联关系

### 谷粒商城接口文档地址：

12、删除属性与分组的关联关系：[https://easydoc.net/s/78237135/ZUqEdvA4/OXTgKobR](https://easydoc.net/s/78237135/ZUqEdvA4/OXTgKobR)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2052.png)

**AttrGroupController**：@PostMapping从前端携带JSON数据，必须加@RequestBody接收

```java
		//product/attrgroup/attr/relation/delete  : 删除属性与分组的关联关系
    //@PostMapping从前端携带JSON数据，必须加@RequestBody接收
    @PostMapping("/attr/relation/delete")
    public R deleteRelation(@RequestBody AttrGroupRelationVo[] vos) {
        attrService.deleteRelation(vos);
        return R.ok();
    }
```

**AttrService：** 个人感觉写在AttrGroupService更合适

```java
		/**
     * 删除属性与分组的关联关系
     * @param vos
     */
    void deleteRelation(AttrGroupRelationVo[] vos);
```

**AttrServiceImpl：**

```java
		/**
     * 12、删除属性与分组的关联关系
     *
     * @param vos
     */
    @Override
    public void deleteRelation(AttrGroupRelationVo[] vos) {
        //AttrGroupRelationVo[]是一个集合 如果我们选择了批量删除提交了很多数据，就会发送很多次删除请求；而我们希望只发送一次删除请求，也就不能这么写了
        //relationDao.delete(new QueryWrapper<>().eq("attr_id",1L).eq("attr_group_id",1L))

        //只发一次删除请求，来完成一个批量删除
        List<AttrAttrgroupRelationEntity> entities = Arrays.asList(vos).stream().map((item) -> {
            AttrAttrgroupRelationEntity relationEntity = new AttrAttrgroupRelationEntity();
            //进行属性对拷：将当前正在遍历的item里面的属性值复制到relationEntity
            BeanUtils.copyProperties(item, relationEntity);
            return relationEntity;
        }).collect(Collectors.toList());//将其(entities)收集成一个ArrayList集合
        relationDao.deleteBatchRelation(entities);
    }
```

**AttrAttrgroupRelationDao：**

```java
//@Param("entities") 标定一个自定义的属性
void deleteBatchRelation(@Param("entities") List<AttrAttrgroupRelationEntity> entities);
```

使用MybatisX插件自动生成：**AttrAttrgroupRelationDao.xml中**的**statement**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2053.png)

**AttrAttrgroupRelationDao.xml：动态SQL**

```sql
<!--动态SQL-->
    <!--DELETE FROM `pms_attr_attrgroup_relation` WHERE(attr_id = 1 AND attr_group_id =1) OR (attr_id = 3 AND attr_group_id =2)-->
    <delete id="deleteBatchRelation">
        DELETE FROM `pms_attr_attrgroup_relation` WHERE
        <foreach collection="entities" item="item" separator=" OR ">
            (attr_id=#{item.attrId} AND attr_group_id=#{item.attrGroupId})
        </foreach>
    </delete>
```

Idea控制台输出打印：

==> Preparing: DELETE FROM pms_attr_attrgroup_relation WHERE (attr_id=? AND attr_group_id=?)

测试关联属性之移除功能操作。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2054.png)

对应的`pms_attr_attrgroup_relation`数据库表：数据的变化

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2055.png)

# P81、商品服务-API-平台属性-查询分组未关联的属性



> 页面原型：
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2056.png)

> 难点：
> 

我们需要查出哪些属性是当前分组能关联进来的。

1. 当前分组能关联的，肯定是本分类下的（比如手机分类）而且是本分类下没有被其他分组关联的属性，所以这一步会发送一个请求（F12）看一下控制台

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2057.png)

1. 按照接口文档【13、获取属性分组没有关联的其他属性：[https://easydoc.net/s/78237135/ZUqEdvA4/d3EezLdO](https://easydoc.net/s/78237135/ZUqEdvA4/d3EezLdO)】

按照这些分组（catelog_id），找到这些分组已经关联了的属性，而且这些其他分组，必须还不是我们当前的分组，也就是三级分类id【catelog_id】等于我们指定的值【catelogId】并且我们分组的id【attr_group_id】不能是我们当前的分组id【attrgroupId】

```java
List<AttrGroupEntity> group = attrGroupDao.selectList(new QueryWrapper<AttrGroupEntity>().eq("catelog_id", catelogId).ne("attr_group_id",attrgroupId));
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2058.png)

由于一个三级分类（对应多个商品）下的所有关联的基本属性,而这些基本属性都被分成组别，此时要找到当前分类还能关联的基本属性， 必须找到其他分组中还没关联的基本属性.

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2059.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2060.png)

- AttrGroupController：
  
    ```java
    /**
         * 谷粒商城接口文档：13、获取属性分组没有关联的其他属性
         *
         * @param attrgroupId
         * @return
         */
        //product/attrgroup/{attrgroupId}/noattr/relation
        @GetMapping("/{attrgroupId}/noattr/relation")
        public R attrNoRelation(@PathVariable("attrgroupId") Long attrgroupId,
                                @RequestParam Map<String, Object> params) { //params是页面带来的分页参数
            PageUtils page = attrService.getNoRelationAttr(params, attrgroupId);
    //        return R.ok().put("data", page);    //将分页数据page返回 （前端没反应，一直转圈圈）
            return R.ok().put("page", page);    //将分页数据page返回  注意此处 与雷丰阳老师的返回的字段不一致 （本例改成page正常）
        }
    ```
    
- AttrService：
  
    ```java
    PageUtils getNoRelationAttr(Map<String, Object> params, Long attrgroupId);
    ```
    
- AttrServiceImpl：
  
    ```java
    /**
         * 获取当前分组没有关联的所有属性
         *
         * @param params
         * @param attrgroupId
         * @return
         */
        @Override
        public PageUtils getNoRelationAttr(Map<String, Object> params, Long attrgroupId) {
            //1、当前分组只能关联自己所属的分类里面的所有属性
            AttrGroupEntity attrGroupEntity = attrGroupDao.selectById(attrgroupId);     //查出当前分组的信息
            Long catelogId = attrGroupEntity.getCatelogId();    //获取当前分类的id （本分组的三级分类id）
    
            //2、当前分组只能关联别的分组没有引用的属性
            //2.1 找到当前分类下的其他分组
            List<AttrGroupEntity> group = attrGroupDao.selectList(new QueryWrapper<AttrGroupEntity>().eq("catelog_id", catelogId));
            List<Long> collect = group.stream().map((item -> {
                return item.getAttrGroupId();
            })).collect(Collectors.toList());  //将attrGroupId收集成一个集合
    
            //2.2 在找到这些分组关联的属性
            List<AttrAttrgroupRelationEntity> groupId = relationDao.selectList(new QueryWrapper<AttrAttrgroupRelationEntity>().in("attr_group_id", collect));
            List<Long> attrIds = groupId.stream().map(item -> {
                //获取到已经关联了的所有属性的id
                return item.getAttrId();
            }).collect(Collectors.toList());    //将attrId收集成一个集合
    
            //2.3 从当前分类的所有属性中移除这些属性，得到我们没有关联的属性
            //用当前这个service自己的dao：AttrDao   也可以通过this.获取     catelog_id：属性的三级分类必须是本分组下的三级分类id,而且attr_id不能再这个集合里面
    //        List<AttrEntity> attrEntities = this.baseMapper.selectList(new QueryWrapper<AttrEntity>().eq("catelog_id", catelogId).notIn("attr_id", attrIds));
            QueryWrapper<AttrEntity> wrapper = new QueryWrapper<AttrEntity>().eq("catelog_id", catelogId).eq("attr_type", ProductConstant.AttrEnum.ATTR_TYPE_BASE.getCode());
            if (attrIds != null && attrIds.size() > 0) {
                wrapper.notIn("attr_id", attrIds);
            }
            //拿到模糊查询条件
            String key = (String) params.get("key");
            if (!StringUtils.isEmpty(key)) {
                wrapper.and((w) -> {
                    w.eq("attr_id", key).or().like("attr_name", key);
                });
            }
            IPage<AttrEntity> page = this.page(new Query<AttrEntity>().getPage(params), wrapper);
            PageUtils pageUtils = new PageUtils(page);
            return pageUtils;
        }
    ```
    

测试：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2061.png)

# P82、商品服务API-平台属性新增分组与属性关联



编写添加分组与属性的关联关系功能：当我们点击关联的时候，先查出当前分组关联的所有属性，如果我们想关联新的属性，我们点击新建关联。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2062.png)

**11、添加属性与分组关联关系接口文档地址：**[https://easydoc.net/s/78237135/ZUqEdvA4/VhgnaedC](https://easydoc.net/s/78237135/ZUqEdvA4/VhgnaedC)

- AttrGroupController
  
    ```java
    @Autowired
        AttrAttrgroupRelationService relationService;
    
        /**
         * 11、添加属性与分组关联关系
         *
         * @return
         */
        //product/attrgroup/attr/relation
        @PostMapping("/attr/relation")
        public R addRelation(@RequestBody List<AttrGroupRelationVo> vos) {
    
            relationService.saveBatch(vos); //批量保存
            return R.ok();
        }
    ```
    
- AttrAttrgroupRelationService
  
    ```java
    void saveBatch(List<AttrGroupRelationVo> vos);
    ```
    
- AttrAttrgroupRelationServiceImpl
  
    ```java
    @Override
    public void saveBatch(List<AttrGroupRelationVo> vos) {
        List<AttrAttrgroupRelationEntity> collect = vos.stream().map(item -> {
            AttrAttrgroupRelationEntity relationEntity = new AttrAttrgroupRelationEntity();
            BeanUtils.copyProperties(item, relationEntity); //将item中的属性值对拷到relationEntity中
            return relationEntity;
        }).collect(Collectors.toList()); //将relationEntity收集成一个collect集合
        this.saveBatch(collect);
    }
    ```
    
- AttrServiceImpl：为了防止空指针异常，需要补充非空判断
  
    ```java
    @Transactional  //开启事务注解
        @Override
        public void saveAttr(AttrVo attr) { //attr ： VO
            AttrEntity attrEntity = new AttrEntity();   //attrEntity : PO
    //        attrEntity.setAttrName(attr.getAttrName());
            BeanUtils.copyProperties(attr, attrEntity);
            //1、保存基本数据
            this.save(attrEntity);
            //2、保存关联关系
            **if (attr.getAttrType() == ProductConstant.AttrEnum.ATTR_TYPE_BASE.getCode() && attr.getAttrGroupId() != null) {  //==1说明这是一个基本属性**
                AttrAttrgroupRelationEntity relationEntity = new AttrAttrgroupRelationEntity();
                relationEntity.setAttrGroupId(attr.getAttrGroupId());
                relationEntity.setAttrId(attrEntity.getAttrId());
                relationDao.insert(relationEntity);
            }
        }
    
    ---------
    
    						//1、设置分类和分组的名字
                if ("base".equalsIgnoreCase(type)) {
                    AttrAttrgroupRelationEntity attrId = relationDao.selectOne(new QueryWrapper<AttrAttrgroupRelationEntity>().eq("attr_id", attrEntity.getAttrId()));
                    //Long attrGroupId = attrId.getAttrGroupId();
                    **if (attrId != null && attrId.getAttrGroupId() != null) {**
                        AttrGroupEntity attrGroupEntity = attrGroupDao.selectById(attrId.getAttrGroupId());
                        attrRespVo.setGroupName(attrGroupEntity.getAttrGroupName());
                    }
                }
    
    ```