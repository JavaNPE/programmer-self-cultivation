# 05_前端基础技术栈学习笔记-ES6新特性-let&const-Vue总结-ElementUI-Vue脚手架进行模块化开发教程

## 用VSCode打开：人人开源项目clone的项目renren-fast-vue

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130249.png)

# 29、前端基础-ES6-let&const

## 29.1 ES6简介：

**ECMAScript6.0** (以下简称ES6，ECMAScript是一种由Ecma国际(前身为欧洲计算机制造商协会，英文名称是European Computer Manufacturers Association)通过ECMA-262标准化的脚本程序设计语言)是JavaScript 语言的下一代标准，已经在2015 年6月正式发布了，**并且从ECMAScript6开始，开始采用年号来做版本**。即ECMAScript 2015,就是ECMAScript6。**它的目标，是使得JavaScript 语言可以用来编写复杂的大型应用程序，成为企业级开发语言**。每年一个新版本。

- ES2015，就是ES6。
- ES2016，就是ES7。
- ES2017，就是ES8。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130250.png)

所以，**ECMAScript**是浏览器脚本语言的**规范**，而各种我们**熟知的js语言**，**如JavaScript则是
规范的具体实现。   比如：JDBC是规范，MySql是实现。**

## 29.2 ES6新特性：

VSCode中`shift+!` (英文输入法下)可以快速生成Html头模板；

`shift+alt+F`：代码格式化。

### 1、let 声明变量有严格局部作用域 | let变量只能声明一次 | let不存在变量提升

- let不会作用到{}外，var会越域跳到{}外
- var可以多次声明同一变量，let会报错
- var定义之前可以使用，let定义之前不可使用。（变量提升问题）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
        //var 声明变量往往会越域
        //let 声明变量有严格局部作用域

        // {
        //     var a = 1;
        //     let b = 2;
        // }
        // console.log(a); //输出 1  不受作用域限制
        // console.log(b); // Uncaught ReferenceError: b is not defined 受作用域限制
//-----------------------------------------------分开测试------------------------------------------------
        // var 变量可以声明多次
        // let 变量只能声明一次

        // var m = 1
        // var m = 2
        // let n = 3
        // let n = 4       //n 重复定义 浏览器控制台会报错
        // console.log(m)
        // console.log(n)  //Identifier 'n' has already been declared

//-----------------------------------------------分开测试------------------------------------------------

    // var 会变量提升
    // let 不存在变量提升
    console.log(x);     //undefined
    var x = 10;     // var 会变量提升
    console.log(y);     //Cannot access 'y' before initialization
    let y = 20;     //浏览器控制台报上述错误

    </script>
</body>
</html>
```

### 2、const 声明常量(只读变量)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <script>
  

        // const 声明常量(只读变量)
        // 1. const声明之后不允许改变
        // 2. 一但声明必须初始化，否则会报错
        const a = 1;
        a = 3; //Uncaught TypeError: Assignment to constant variable.

    </script>
</body>

</html>
```

### 3、解构表达式：数组解构 | 对象解构

> 数组结构：
> 

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <script>

        //数组解构前:
        
        // let arr = [1,2,3];
        // let a = arr[0];
        // let b = arr[1];
        // let c = arr[2];

        // console.log(a,b,c) //传统写法

        //-----------------------------------------------分开测试------------------------------------------------
        
			  //数组解构后:
        //以前我们想获取其中的值，只能通过角标。ES6可以这样:

        let arr = [1,2,3];
        let [a,b,c] = arr; //a,b,c 将与arr中的每一个位置对应来取值
        console.log(a,b,c) //然后打印
    </script>

</body>

</html>
```

> 对象结构：
> 

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <script>

        
        //对象结构：之前的写法
        // const person = {
        //     name: "jack",
        //     age: 21,
        //     language: ['java', 'js', 'css']
        // }
        // const name = person.name;
        // const age = person.age;
        // const language = person.language;
        // console.log(name, age, language)

        //-----------------------------------------------分开测试------------------------------------------------

        //对象结构：之后的写法
        const person = {
            name: "jack",
            age: 21,
            language: ['java', 'js', 'css']
        }

        // const { name, age, language } = person;
        // console.log(name, age, language)

        //对象解构 // 把name属性变为abc，声明了abc、age、language三个变量
        const { name: abc, age, language } = person;
        console.log(abc, age, language)

    </script>

</body>

</html>
```

### 4、字符串扩展：几个新的API | 字符串模板

> 1)、几个新的API
> 

ES6为字符串扩展了几个新的AP|:

- includes(): 返回布尔值，表示是否找到了参数字符串。
- startsWith() :返回布尔值，表示参数字符串是否在原字符串的头部。
- endsWith():返回布尔值，表示参数字符串是否在原字符串的尾部。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <script>

        //4、字符串扩展
        let str = "hello.vue";
        console.log(str.startsWith("hello"));//true
        console.log(str.endsWith(".vue"));//true
        console.log(str.includes("e"));//true
        console.log(str.includes("hello"));//true

    </script>

</body>

</html>
```

> 2)、字符串模板
> 

模板字符串相当于加强版的字符串，用反引号``  ``，除了作为普通字符串，还可以用来定义多行字符串，还可以在字符串中加入变量和表达式。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <script>

        //-----------------------------------------------分开测试------------------------------------------------

        //对象结构：之后的写法
        const person = {
            name: "jack",
            age: 21,
            language: ['java', 'js', 'css']
        }

        // const { name, age, language } = person;
        // console.log(name, age, language)

        //对象解构 // 把name属性变为abc，声明了abc、age、language三个变量
        const { name: abc, age, language } = person;
        console.log(abc, age, language)

        //-----------------------------------------------分开测试------------------------------------------------

        //4、字符串扩展
        let str = "hello.vue";
        console.log(str.startsWith("hello"));//true
        console.log(str.endsWith(".vue"));//true
        console.log(str.includes("e"));//true
        console.log(str.includes("hello"));//true

        //-----------------------------------------------分开测试------------------------------------------------

        //字符串模板 ``可以定义多行字符串
        let ss = `<div>
                    <span>hello world<span>
                </div>`;
        console.log(ss);

        //-----------------------------------------------分开测试------------------------------------------------

        function fun() {
            return "这是一个函数"
        }

        // 2、字符串插入变量和表达式。变量名写在 ${} 中，${} 中可以放入 JavaScript 表达式。
        let info = `我是${abc}，今年${age + 10}了, 我想说： ${fun()}`;
        console.log(info);

    </script>

</body>

</html>
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130252.png)

### 5、函数优化：箭头函数 | 函数参数的默认值 | 不定参数

```html
支持函数形参默认值 function add2(a, b = 1) {
支持不定参数 function fun(...values) {
支持箭头函数 var print = obj => console.log(obj);
箭头函数+结构 var hello2 = ({name}) => console.log("hello," +name);，[本来应该是person.name](http://xn--person-2v1lo61c97bkq7523a.name/)

```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
         //在ES6以前，我们无法给一个函数参数设置默认值，只能采用变通写法：
         function add(a, b) {
            // 判断b是否为空，为空就给默认值1
            b = b || 1;
            return a + b;
        }
        // 传一个参数
        console.log(add(10));

        //现在可以这么写：直接给参数写上默认值，没传就会自动使用默认值
        function add2(a, b = 1) {
            return a + b;
        }
        console.log(add2(20));

        //2）、不定参数
        function fun(...values) {
            console.log(values.length)
        }
        fun(1, 2)      //2
        fun(1, 2, 3, 4)  //4

        //3）、箭头函数。lambda
        //以前声明一个方法
        // var print = function (obj) {
        //     console.log(obj);
        // }
        var print = obj => console.log(obj);
        print("hello");

        var sum = function (a, b) {
            c = a + b;
            return a + c;
        }

        var sum2 = (a, b) => a + b;
        console.log(sum2(11, 12));

        var sum3 = (a, b) => {
            c = a + b;
            return a + c;
        }
        console.log(sum3(10, 20))

        const person = {
            name: "jack",
            age: 21,
            language: ['java', 'js', 'css']
        }

        function hello(person) {
            console.log("hello," + person.name)
        }

        //箭头函数+解构
        var hello2 = ({name}) => console.log("hello," +name);
        hello2(person);
    </script>
    
</body>
</html>
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130253.png)

### 6、对象优化

对象简写很常见

可以获取map的键值对等`Object.keys()`、`values`、`entries`

`Object.assgn(target,source1,source2)` 合并

如果属性名和属性值的变量名相同，可以省略`const person2 = { age, name }` 代表age属性的值是变量age的值

`…`代表取出该对象所有属性拷贝到当前对象。`let someone = { …p1 }`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
         const person = {
            name: "jack",
            age: 21,
            language: ['java', 'js', 'css']
        }

        console.log(Object.keys(person));//["name", "age", "language"]
        console.log(Object.values(person));//["jack", 21, Array(3)]
        console.log(Object.entries(person));//[Array(2), Array(2), Array(2)]

        const target  = { a: 1 };
        const source1 = { b: 2 };
        const source2 = { c: 3 };

        // 合并
        //{a:1,b:2,c:3}
        Object.assign(target, source1, source2);

        console.log(target);//{a:1,b:2,c:3}
        
        //2）、声明对象简写
        const age = 23
        const name = "张三"
        const person1 = { age: age, name: name }
        // 等价于
        const person2 = { age, name }//声明对象简写
        console.log(person2);

        //3）、对象的函数属性简写
        let person3 = {
            name: "jack",
            // 以前：
            eat: function (food) {
                console.log(this.name + "在吃" + food);
            },
            //箭头函数this不能使用，要使用的话需要使用：对象.属性
            eat2: food => console.log(person3.name + "在吃" + food),
            eat3(food) {
                console.log(this.name + "在吃" + food);
            }
        }

        person3.eat("香蕉");
        person3.eat2("苹果")
        person3.eat3("橘子");

        //4）、对象拓展运算符

        // 1、拷贝对象（深拷贝）
        let p1 = { name: "Amy", age: 15 }
        let someone = { ...p1 }
        console.log(someone)  //{name: "Amy", age: 15}

        // 2、合并对象
        let age1 = { age: 15 }
        let name1 = { name: "Amy" }
        let p2 = {name:"zhangsan"}
        p2 = { ...age1, ...name1 } 
        console.log(p2)
    </script>
    
</body>
</html>
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130254.png)

### 7、map和reduce

map()：接收一个函数，将原数组中的所有元素用这个函数处理后放入新数组返回。
reduce() 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素，

```html
arr = arr.map(item=> item*2);

//[2, 40, -10, 6]
arr.reduce(callback,[initialValue])
/**
    1、previousValue （上一次调用回调返回的值，或者是提供的初始值（initialValue））
    2、currentValue （数组中当前被处理的元素）
    3、index （当前元素在数组中的索引）
    4、array （调用 reduce 的数组）*/
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    
    <script>
        //数组中新增了map和reduce方法。
         let arr = ['1', '20', '-5', '3'];
         
        //map()：接收一个函数，将原数组中的所有元素用这个函数处理后放入新数组返回。
        //  arr = arr.map((item)=>{
        //     return item*2
        //  });
         arr = arr.map(item=> item*2);

        

         console.log(arr);
        //reduce() 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素，
        //[2, 40, -10, 6]
        //arr.reduce(callback,[initialValue])
        /**
        1、previousValue （上一次调用回调返回的值，或者是提供的初始值（initialValue））
        2、currentValue （数组中当前被处理的元素）
        3、index （当前元素在数组中的索引）
        4、array （调用 reduce 的数组）*/
        let result = arr.reduce((a,b)=>{
            console.log("上一次处理后："+a);
            console.log("当前正在处理："+b);
            return a + b;
        },100);
        console.log(result)

    
    </script>
</body>
</html>
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130255.png)

### 8、Promise：优化异步操作。封装ajax

以前嵌套ajax的时候很繁琐。

- 把Ajax封装到Promise中，赋值给let p
- 在Ajax中成功使用resolve(data)，失败使用reject(err)
- p.then().catch()
- 6、promise.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js"></script>
    </head>
    <body>
        <script>
            //1、查出当前用户信息
            //2、按照当前用户的id查出他的课程
            //3、按照当前课程id查出分数
            // $.ajax({
            //     url: "mock/user.json",
            //     success(data) {
            //         console.log("查询用户：", data);
            //         $.ajax({
            //             url: `mock/user_corse_${data.id}.json`,
            //             success(data) {
            //                 console.log("查询到课程：", data);
            //                 $.ajax({
            //                     url: `mock/corse_score_${data.id}.json`,
            //                     success(data) {
            //                         console.log("查询到分数：", data);
            //                     },
            //                     error(error) {
            //                         console.log("出现异常了：" + error);
            //                     }
            //                 });
            //             },
            //             error(error) {
            //                 console.log("出现异常了：" + error);
            //             }
            //         });
            //     },
            //     error(error) {
            //         console.log("出现异常了：" + error);
            //     }
            // });
    
            //1、Promise可以封装异步操作
            // let p = new Promise((resolve, reject) => { //传入成功解析，失败拒绝
            //     //1、异步操作
            //     $.ajax({
            //         url: "mock/user.json",
            //         success: function (data) {
            //             console.log("查询用户成功:", data)
            //             resolve(data);
            //         },
            //         error: function (err) {
            //             reject(err);
            //         }
            //     });
            // });
    
            // p.then((obj) => { //成功以后做什么
            //     return new Promise((resolve, reject) => {
            //         $.ajax({
            //             url: `mock/user_corse_${obj.id}.json`,
            //             success: function (data) {
            //                 console.log("查询用户课程成功:", data)
            //                 resolve(data);
            //             },
            //             error: function (err) {
            //                 reject(err)
            //             }
            //         });
            //     })
            // }).then((data) => { //成功以后干什么
            //     console.log("上一步的结果", data)
            //     $.ajax({
            //         url: `mock/corse_score_${data.id}.json`,
            //         success: function (data) {
            //             console.log("查询课程得分成功:", data)
            //         },
            //         error: function (err) {
            //         }
            //     });
            // })
    
            function get(url, data) { //自己定义一个方法整合一下
                return new Promise((resolve, reject) => {
                    $.ajax({
                        url: url,
                        data: data,
                        success: function (data) {
                            resolve(data);
                        },
                        error: function (err) {
                            reject(err)
                        }
                    })
                });
            }
    
            get("mock/user.json")
                .then((data) => {
                    console.log("用户查询成功~~~:", data)
                    return get(`mock/user_corse_${data.id}.json`);
                })
                .then((data) => {
                    console.log("课程查询成功~~~:", data)
                    return get(`mock/corse_score_${data.id}.json`);
                })
                .then((data)=>{
                    console.log("课程成绩查询成功~~~:", data)
                })
                .catch((err)=>{ //失败的话catch
                    console.log("出现异常",err)
                });
    
        </script>
    </body>
    
    </html>
    ```
    
- corse_score_10.json
  
    ```html
    {
        "id": 100,
        "score": 90
    }
    ```
    
- user_corse_1.json
  
    ```html
    {
        "id": 10,
        "name": "chinese"
    }
    ```
    
- user.json
  
    ```html
    {
        "id": 1,
        "name": "zhangsan",
        "password": "123456"
    }
    ```
    

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130256.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130257.png)

### 9、模块化

模块化就是把代码进行拆分，方便重复利用。类似于java中的导包，而JS换了个概念，是导模块。

模块功能主要有两个命令构成 export 和import

- export用于规定模块的对外接口
- import用于导入其他模块提供的功能
- user.js
  
    ```html
    var name = "jack"
    var age = 21
    function add(a, b) {
        return a + b;
    }
    // 导出变量和函数
    export { name, age, add }
    ```
    
- hello.js
  
    ```html
    // export const util = {
    //     sum(a, b) {
    //         return a + b;
    //     }
    // }
    
    // 导出后可以重命名
    export default {
        sum(a, b) {
            return a + b;
        }
    }
    // export {util}
    
    //`export`不仅可以导出对象，一切JS变量都可以导出。比如：基本类型变量、函数、数组、对象。
    ```
    
- main.js
  
    ```html
    import abc from "./hello.js"
    import { name, add } from "./user.js"
    
    abc.sum(1, 2);
    console.log(name);
    add(1, 3);
    ```
    

# 36、 前端基础-Vue-介绍&demo案例

## Vue官网教程：

[介绍 - Vue.js](https://cn.vuejs.org/v2/guide/)

Vue官网教程

## MVVM思想

M：model 包括数据和一些基本操作

V：view 视图，页面渲染结果

VM：View-model，模型与视图间的双向操作（无需开发人员干涉）

视图和数据通过VM绑定起来，model里有变化会自动地通过Directives填写到视view中，视图表单中添加了内容也会自动地通过DOM Listeners保存到模型中。

在MWVM之前，开发人员从后端获取需要的数据模型，然后要通过DOM操作Model渲染
到View中。而后当用户操作视图，我们还需要通过DOM获取View中的数据，然后同步到
Model中。

而MVVM中的VM要做的事情就是把DOM操作完全封装起来，开发人员不用再关心Model
和View之间是如何互相影响的：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130258.png)

## 使用NPM的方式安装Vue：

在用 Vue 构建大型应用时推荐使用 NPM 安装。NPM 能很好地和诸如 webpack 或 Browserify 模块打包器配合使用。同时 Vue 也提供配套工具来开发单文件组件。

```bash
# 最新稳定版
$ npm install vue
```

下载`js`并用 `<script>` 标签引入：

`<script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>`

或者在VScode控制台使用`npm install vue` 命令导入。

### NPM的方式安装Vue 步骤：`cls` 清除控制台

> 第一步：
> 

先在VSCode终端窗口使用 `npm init -y` 命令初始化项目，生成了一个`package.json`文件，说明他是一个`npm`管理的项目，类似于`maven`的`pom.xml`

> 第二步：
> 

在VSCode终端窗口使用`npm install vue` 命令，安装后在项目node_modules里有vue，类似maven install拉取远程到本地。

![使用 `npm init -y` 命令初始化项目](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130259.png)

使用 `npm init -y` 命令初始化项目

![`npm install vue`](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130260.png)

`npm install vue`

### Vue声明式渲染案例：demo

使用：

- new Vue
- 在dom中{{name}}代表从模型中放到view中
- v-model实现双向绑定
- index.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <div id="app">
            <h1>{{name}} ,非常帅</h1>
        </div>
        <script src="./node_modules/vue/dist/vue.js"></script>
        <script>
            //1、Vue声明式渲染案例
            let vm = new Vue({
                el: "#app",
                data: {
                    name: "张三"
                }
            });
        </script>
    </body>
    
    </html>
    ```
    
    ![Vue实时渲染功能：](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130261.png)
    
    Vue实时渲染功能：
    

### Vue双向绑定：案例

- Vue双向绑定，模型变化，视图变化。反之亦然。
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <div id="app">
            <input type="test" v-model="num">
            <h1>{{name}} ,非常帅,有{{num}}人为他点赞</h1>
        </div>
    
        <script src="./node_modules/vue/dist/vue.js"></script>
    
        <script>
            //1、Vue声明式渲染案例
            let vm = new Vue({
                el: "#app",
                data: {
                    name: "张三",
                    num: 1      //初始值设为1
                }
            });
            //2、 双向绑定，模型变化，视图变化。反之亦然。
    
        </script>
    </body>
    
    </html>
    ```
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130262.png)
    
    ![录制_2021_08_16_22_57_19_454.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130263.gif)
    

# 37、前端基础-Vue基本语法&插件安装 | vue 2 snippets语法提示插件 | 谷歌浏览器中安装vue-devtool

在VSCode中安装`vue 2 snippets`语法提示插件，在谷歌浏览器中安装`vue-devtool` 插件。

## **1、v-text、v-html、v-ref**

- 1、v-test、v-html.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <div id="app">
            <!-- 前面的内容如果网速慢的话会先显示括号，然后才替换成数据。 -->
            {{msg}} {{1+1}} {{hello()}}<br />   
            {{link}}<br>
            <!-- 用v-html取内容 -->
            <span v-html="msg"></span>
            <br />
            <!-- 原样显示 -->
            <span v-text="msg"></span>
        </div>
        <script src="../node_modules/vue/dist/vue.js"></script>
        <script>
            new Vue({
                el: "#app",
                data: {
                    msg: "<h1>Hello</h1>",
                    link: "http://www.baidu.com"
                },
                methods: {
                    hello() {
                        return "World"
                    }
                }
            })
        </script>
    
    </body>
    
    </html>
    ```
    

### **插值表达式：**

这两个可以使用data数据。而`<div>123{{}}</div>`这种写法叫插值表达式，可以计算，可以取值，可以调用函数这里还介绍v-html v-text区别注意取的大多数不是请求域了，而是vue对象里的data

### 插值闪烁：

使用{{}}方式在网速较慢时会出现问题。在数据未加载完成时，页面会显示出原始的`{{}}`，加载完毕后才显示正确数据，我们称为插值闪烁。我们将网速调慢一些，然后刷新页面，试试看刚才的案例

## 2、单向绑定：v-bind.html

**数据修改页面修改，页面修改不会导致数据修改（就是vue实例中的data没有修改）**

问题：花括号只能写在标签体内（`<div 标签内> 标签体 </div>`），不能用在标签外。

**插值表达**式只能用在标签体里，如果我们这么用`<a href="{{}}">`是不起作用的，所以需要 `<a v-bind:href="link">跳转</a>`这种用法

解决：用`v-bind:`，简写为`:`。表示把model绑定到view。可以设置src、title、class等

在浏览器里`vm.link="[www.baidu.com](http://www.baidu.com/)"`，此处vue数据改了，dom里跳转链接也改了

class里有哪些内容可以通过vue数据的bool值添加删除，而在style中代表的是`k:v`值。

也可以把`v-bind:`简写成`:`

**{{}}必须有返回值**

### `v-bind` 缩写 `:`

```html
Vue.js 为两个最为常用的指令提供了特别的缩写：
v-bind 缩写

<!-- 完整语法 -->
<a v-bind:href="url"></a>
<!-- 缩写 -->
<a :href="url"></a>
```

- 2、单向绑定v-bind.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <!-- 给html标签的属性绑定 -->
        <div id="app">
            <a v-bind:href="link">gogogobaidu</a>
    
            <!-- 绑定class，根据true或false动态绑定class属性值 -->
            <span v-bind:class="{active:isActice,'text-danger':hasError}" 
            
            v-bind:style="{color: color1,fontSize: size}">你好</span>   
    <!-- class,style  {class名：vue值}-->
    
        </div>
        <script src="../node_modules/vue/dist/vue.js"></script>
        <script>
            let vm = new Vue({
                el: "#app",
                data: {
                    link: "http://www.baidu.com",
                    isActice: true,
                    hasError: true,
                    color1: 'red',
                    size: '36px'
                }
            })
        </script>
    </body>
    
    </html>
    ```
    
    ```html
    1、v-bind动态绑定属性值，不能使用插值表达式{{link}}
    	1）、绑定href
    		<a v-bind:href="link">gogogo</a>    绑定link
     		data:{
    			link: "http://www.baidu.com"
     		}
    
    	内部是对象形式
    	2）、绑定class，根据true或false动态绑定class属性值
    		<span v-bind:class="{active:isActive,'text-danger':hasError}"/> // 特殊字符加单引号
    		data:{
    			isActive:true,
        		hasError:true
    		}
    
    	3）、绑定style，这里的v-bind 可以省略
          	<span :style="{color: color1,fontSize: size}">你好</span> // 存在特殊字符也可以使用驼峰命名
    		data:{
            	color1:'red',
            	size:'36px'
            }
    ```
    

## 3、双向绑定v-model.html

**页面修改数据修改，数据修改页面修改：**

v-bind只能从model到view。v-model能从view到model

- 3、双向绑定v-model.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
          <!-- 表单项，自定义组件 -->
        <div id="app">
            精通的语言：<!--如果是多选框，那么会把每个value值赋值给vue数据 -->
            <input type="checkbox" v-model="language" value="Java"> java<br />
            <input type="checkbox" v-model="language" value="PHP"> PHP<br />
            <input type="checkbox" v-model="language" value="Python"> Python<br />
            选中了{{language.join(",")}}
        </div>
        <script src="../node_modules/vue/dist/vue.js"></script>
        <script>
            let vm = new Vue({
                el:"#app",
                data:{
                    language: []    //[]: 数组
                }
            })
        </script>
    
    </body>
    
    </html>
    ```
    

## 4、v-on事件.html （点击事件、按键事件）

本小节内容包括 **事件修饰符** 和 **按键修饰符。**

### 事件监听可以使用 v-on 指令：`v-on:click=` 可以简写成 `@click=`

`v-on:事件类型="方法"` ，可以简写成`@事件类型="方法"`

> **事件冒泡：**大小div都有单机事件，点了内部div相当于外部div也点击到了。
如果不想点击内部div冒泡到外部div，可以使用`.prevent`阻止事件冒泡
用法是`v-on:事件类型.事件修饰符="方法"`
还可以绑定按键修饰符
`v-on:keyup.up=“num+=2” @keyup.down=“num-=2” @click.ctrl=“num=10”`
> 

Vue.js 为 v-on 提供了事件修饰符来处理 DOM 事件细节，如：`event.preventDefault()` 或 `event.stopPropagation()`。

Vue.js 通过由点 `.` 表示的指令后缀来调用修饰符。

`.stop` - 阻止冒泡
`.prevent` - 阻止默认事件
`.capture` - 阻止捕获
`.self` - 只监听触发该元素的事件
`.once` - 只触发一次
`.left` - 左键事件
`.right` - 右键事件
`.middle` - 中间滚轮事件
————————————————

### `v-on:` 缩写 `@`

```html
<!-- 同上 -->
<input v-on:keyup.enter="submit">
<!-- 缩写语法 -->
<input @keyup.enter="submit">
```

- 4、v-on事件.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <div id="app">
    
            <!--事件中直接写js片段-->
            <button v-on:click="num++">点赞</button>
            <!--事件指定一个回调函数，必须是Vue实例中定义的函数-->
            <button @click="cancel">取消</button>
            <!--  -->
            <h1>有{{num}}个赞</h1>
    
            <!-- 事件修饰符 -->
            <div style="border: 1px solid red;padding: 20px;" v-on:click.once="hello">
                大div
                <div style="border: 1px solid blue;padding: 20px;" @click.stop="hello">
                    小div <br />
                    <a href="http://www.baidu.com" @click.prevent.stop="hello">去百度</a>
                </div>
            </div>
    
            <!-- 按键修饰符： -->
            <input type="text" v-model="num" v-on:keyup.up="num+=2" @keyup.down="num-=2" @click.ctrl="num=10"><br />
    
            提示：
    
        </div>
        <script src="../node_modules/vue/dist/vue.js"></script>
    
        <script>
            new Vue({
                el: "#app",
                data: {
                    num: 1
                },
                methods: {
                    cancel() {
                        this.num--;
                    },
                    hello() {
                        alert("点击了")
                    }
                }
            })
        </script>
    </body>
    
    </html>
    ```
    
    ```html
    1、v-on:
    <button v-on:click="num++">点赞</button>
    
    2、v-on可以简写
    <button @click="num++">点赞</button>
    
    <!-- 事件修饰符 -->
    3、事件修饰符：阻止事件冒泡【例如一个大div包含一个小div，点击小div触发两次事件】
    @click.stop:阻止事件冒泡到父元素:
    @click.prevent: 阻止默认事件发生，a跳转事件被阻止
    @click.capture: 使用事件捕获模式
    @click.self: 只有元素自身触发事件才执行。(冒泡或捕获的都不执行)
    @click.once : 只执行一次
    
    解析a标签：<a>标签会alert一次，不加stop会alert2次
    		 1、prevent阻止了跳转baidu.com的默认事件，
    		 2、stop阻止了事件冒泡
    		 3、="hello"是自己的事件
    <div style="border: 1px solid red;padding: 20px;" v-on:click.once="hello">
        大div
        <div style="border: 1px solid blue;padding: 20px;" @click.stop="hello">
            小div <br />
            <a href="http://www.baidu.com" @click.prevent.stop="hello">去百度</a>
        </div>
    </div>
    
    methods:{
        hello(){
        	alert("点击了")
        }
    }
        
    <!-- 按键修饰符 -->
    4、可以是组合键，@click.ctrl单击+ctrl键
    按键别名，也可以使用建值
    .enter
    .tab
    .delete (捕获“删除”和“退格”键)
    .esc
    .space
    .up
    .down
    .Ieft
    .right
    <!-- 按键修饰符： -->
    <input type="text" v-model="num" v-on:keyup.up="num+=2" @keyup.down="num-=2" @click.ctrl="num=10"><br />
    ```
    

## 5、v-for遍历.html

**遍历的时候都加上`:key`来区分不同数据，提高vue渲染效率**，key选择唯一项

可以遍历 `数组[]` `字典{}` 。对于字典`<li v-for="(value, key, index) in object">`

- 5、v-for遍历.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <div id="app">
            <ul>
                <li v-for="(user,index) in users" :key="user.name" v-if="user.gender == '女'">
                    <!-- 1、显示user信息：v-for="item in items" -->
                    当前索引：{{index}} ==> {{user.name}} ==> {{user.gender}} ==>{{user.age}} <br>
                    <!-- 2、获取数组下标：v-for="(item,index) in items" -->
                    <!-- 3、遍历对象：
                            v-for="value in object"
                            v-for="(value,key) in object"
                            v-for="(value,key,index) in object"
                    -->
                     对象信息：<!--v:值，k:键，i:索引 -->
                    <span v-for="(v,k,i) in user">{{k}}=={{v}}=={{i}}；</span> <br>
                    <span v-for="(v,k,i) in user">{{v}}=={{k}}=={{i}}；</span>
    
                    <!-- 4、遍历的时候都加上:key来区分不同数据，提高vue渲染效率 -->
                </li>
            </ul>
    
            <ul>
                <li v-for="(num,index) in nums" :key="index"></li>
            </ul>
        </div>
        <script src="../node_modules/vue/dist/vue.js"></script>
        <script>         
            let app = new Vue({
                el: "#app",
                data: {
                    users: [{ name: '柳岩', gender: '女', age: 21 },
                    { name: '张三', gender: '男', age: 18 },
                    { name: '范冰冰', gender: '女', age: 24 },
                    { name: '刘亦菲', gender: '女', age: 18 },
                    { name: '古力娜扎', gender: '女', age: 25 }],
                    nums: [1, 2, 3, 4, 4]
                },
            })
        </script>
    </body>
    
    </html>
    ```
    

## 6、v-if和v-show.html

在vue实例的`data`指定一个bool变量，然后`v-show`赋值即可。

show里的字符串也可以比较

`if`是根据表达式的真假，切换元素的显示和隐藏（操作dom元素）

区别：`show的标签F12一直都在，if的标签会移除`，

**if操作dom树对性能消耗大**

`v-if：是这个标签是否渲染`

`v-show：是display是否显示`

- 6、v-if和v-show.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    
    <body>
        <!-- 
            v-if，顾名思义，条件判断。当得到结果为true时，所在的元素才会被渲染。
            v-show，当得到结果为true时，所在的元素才会被显示。 
        -->
        <div id="app">
            <button v-on:click="show = !show">点我呀</button>
            <!-- 1、使用v-if显示 -->
            <h1 v-if="show">if=看到我....</h1>
            <!-- 2、使用v-show显示 -->
            <h1 v-show="show">show=看到我</h1>
        </div>
    
        <script src="../node_modules/vue/dist/vue.js"></script>
    
        <script>
            let app = new Vue({
                el: "#app",
                data: {
                    show: true
                }
            })
        </script>
    
    </body>
    
    </html>
    
    </html>
    ```
    
    ![录制_2021_08_17_19_12_00_885.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130264.gif)
    

## 1、计算属性和侦听器.html | **`computed` |** watch监听

所有需要动态计算的属性写在**`computed`**中:

### 1、计算属性：computed计算总价

### 2、侦听器，watch监听xyjNum，如果>=3修改msg和xyjNum的值【在computed之前执行】

- 1、计算属性和侦听器.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=
        , initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <!-- 某些结果是基于之前数据实时计算出来的，我们可以利用计算属性。来完成 -->
        <div id="app">
            <ul>
                <li>西游记；价格：{{xyjPrice}}, 数量：<input type="number" v-model="xyjNum"></li>
                <li>水浒传；价格：{{shzPrice}}, 数量：<input type="number" v-model="shzNum"></li>
                <li>总价：{{totalPrice}}</li>
                {{msg}}
            </ul>
        </div>
        <script src="../node_modules/vue/dist/vue.js"></script>
        <script>
            new Vue({
                el: "#app",
                data: {
                    xyjPrice: 99.98,
                    shzPrice: 98.00,
                    xyjNum: 1,
                    shzNum: 1,
                    msg: ""
                },
                //1、计算属性：computed计算总价
                computed: {
                    // totalPrice: function () {
                    //     return this.xyjPrice * this.xyjNum + this.shzPrice*this.shzNum
                    // }
    
                    // totalPrice : function(){} ES6后可以简化成下样式
                    totalPrice() {
                        return this.xyjPrice * this.xyjNum + this.shzPrice * this.shzNum
                    }
                },
                // 2、侦听器，watch监听xyjNum，如果>=3修改msg和xyjNum的值【在computed之前执行】
                watch: {
                    // xyjNum(newVal, oldVal) 新式语法 可以省略  ":function" 内容。
                    xyjNum: function (newVal, oldVal) {
                        // alert("newVal" + newVal + "==>" + "oldVal" + oldVal)
                        if (newVal >= 3) {
                            this.msg = "库存超出限制";
                            this.xyjNum = 3  //超出后 库存设置最大值：3
                        } else {
                            this.msg = "";
                        }
                    }
                }
    
            })
        </script>
    </body>
    
    </html>
    ```
    

## 2、过滤器.html | `filter` 定义全局过滤器 | `filters` 定义局部过滤器

定义filter组件后，管道符`|`后面跟具体过滤器`{{user.gender | gFilter}}`

- 2、过滤器.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <!-- 过滤器常用来处理文本格式化的操作，过滤器可以用来在两个地方：双花括号插值和v-bind 表达式 -->
    
        <div id="app">
            <ul>
                <li v-for="user in userList">
                    {{user.id}} ==> {{user.name}} ==> {{user.gender == 1?"男":"女"}} ==>
                    {{user.gender | genderFilter}} ==> {{user.gender | gFilter}}
                </li>
            </ul>
        </div>
        <script src="../node_modules/vue/dist/vue.js"></script>
        <script>
    
            //filter 定义全局过滤器
            Vue.filter("gFilter", function (val) {
                if (val == 1) {
                    return "男~~~~";
                } else {
                    return "女~~~~";
                }
            })
    
            let vm = new Vue({
                el: "#app",
                data: {
                    userList: [
                        { id: 1, name: 'jacky', gender: 1 },
                        { id: 2, name: 'peter', gender: 0 }
                    ]
                },
                filters: {
                    //filters 定义局部过滤器，只可以写在当前vue实例中使用
                    genderFilter(val) {
                        if (val == 1) {
                            return "男";
                        } else {
                            return "女";
                        }
                    }
                }
            })
    
        </script>
    </body>
    
    </html>
    ```
    

# 41、 前端基础-Vue-组件化基础 | 全局声明一个组件 | 局部声明一个组件

## 组件简介：

1、就是`view` 和`model` 整体包装成一个组件可以被复用

2、组件本身就是一个对象（Vue实例），其他的Vue实例内部可以包含组件实例【因为组件实例也是一个Vue实例，所以可以实现套娃，多重重复使用】

3、在一个Vue实例中，`template`（模板）和`components`（导入组件）**可以同时出现**，可以添加模板将自己声明成一个组件，也可以使用`components`导入其他组件【多重重复使用的实现】

在大型应用开发的时候，页面可以划分成很多部分。往往不同的页面，也会有相同的部分。例如可能会有相同的头部导航。

但是如果每个页面都自开发，这无疑增加了我们开发的成本。所以我们会把页面的不同分拆分成立的组件，然后在不同页面就可以共享这些组件，避免重复开发。

在vue里，所有的vue实例都是组件

- **组件其实也是一个vue实例**，因此它在定义时也会接收：data、methods、生命周期函数等。
- 不同的是组件不会与页面的元素绑定（所以不写el），否则就无法复用了，因此没有el属性。
- 但是组件渲染需要html模板，所以增加了template属性，值就是HTML模板
- **data必须是一个函数**，不再是一个对象。
- 全局组件定义完毕，任何vue实例都可以直接在HTML中通过组件名称来使用组件了。

下面的html内容是在说：

`<div id="app">`这个块跟下面的new Vue对象绑定

vue对象里指定了components属性，是指：标签里有子标签，`'button-counter': buttonCounter`代表有个**组件叫**`buttonCounter`，可以填充标签`<button-counter>`

- 1、组件化.html
  
    分为全局组件 和 局部组件
    **全局组件：**`Vue.component("counter"，  // counter`是组件名。我猜测就是静态的所以全局
    **局部组件：**定义一个普通对象就可以，然后在Vue实例内部添加`components`属性并设置组件名`（k:v形式）`
    
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    
    <body>
        <div id="app">
            <button v-on:click="count++">我被点击了 {{count}} 次</button>
            <!-- 如何使用全局声明组件 -->
            <count></count>
            <count></count>
            <count></count>
            <count></count>
    
            <!-- 如何使用局部声明的组件 -->
            <button-counter></button-counter>
        </div>
        <script src="../node_modules/vue/dist/vue.js"></script>
        <script>
            //1、全局声明一个组件
            Vue.component("count", {
                template: '<button v-on:click="count++">我被点击了 {{count}} 次</button>',
                data() {
                    return {
                        count: 1
                    }
                }
            });
    
            //2、局部声明一个组件
            const buttonCounter = {
                template: '<button v-on:click="count++">我被点击了 {{count}} 次~~~~~</button>',
                data() {
                    return {
                        count: 1
                    }
                }
            };
            new Vue({
                el: "#app",
                data: {
                    count: 1
                },
                components: {
                    //k:v 键值对   组件自定义名:组件
                    'button-counter': buttonCounter
                }
            })
        </script>
    </body>
    
    </html>
    ```
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130265.png)
    

# 42、前端基础-Vue-生命周期和钩子函数

每个vue实例在被创建时都要经过一系列的初始化过程：创建实例，装载模板、渲染模板等等。vue为生命周期中的每个状态都设置了钩子函数（监听函）。每当vue实列处于不同的生命周期时，对应的函数就会被触发调用。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130266.png)

- 1、生命周期.html
  
    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    
    <body>
        <div id="app">
            <span id="num">{{num}}</span>
            <button @click="num++">赞！</button>
            <h2>{{name}}，有{{num}}个人点赞</h2>
        </div>
    
        <script src="../node_modules/vue/dist/vue.js"></script>
        
        <script>
            let app = new Vue({
                el: "#app",
                data: {
                    name: "张三",
                    num: 100
                },
                methods: {
                    show() {
                        return this.name;
                    },
                    add() {
                        this.num++;
                    }
                },
                beforeCreate() {
                    console.log("=========beforeCreate=============");
                    console.log("数据模型未加载：" + this.name, this.num);
                    console.log("方法未加载：" + this.show());
                    console.log("html模板未加载：" + document.getElementById("num"));
                },
                created: function () {
                    console.log("=========created=============");
                    console.log("数据模型已加载：" + this.name, this.num);
                    console.log("方法已加载：" + this.show());
                    console.log("html模板已加载：" + document.getElementById("num"));
                    console.log("html模板未渲染：" + document.getElementById("num").innerText);
                },
                beforeMount() {
                    console.log("=========beforeMount=============");
                    console.log("html模板未渲染：" + document.getElementById("num").innerText);
                },
                mounted() {
                    console.log("=========mounted=============");
                    console.log("html模板已渲染：" + document.getElementById("num").innerText);
                },
                beforeUpdate() {
                    console.log("=========beforeUpdate=============");
                    console.log("数据模型已更新：" + this.num);
                    console.log("html模板未更新：" + document.getElementById("num").innerText);
                },
                updated() {
                    console.log("=========updated=============");
                    console.log("数据模型已更新：" + this.num);
                    console.log("html模板已更新：" + document.getElementById("num").innerText);
                }
            });
        </script>
    </body>
    </html>
    ```
    

# 43、前端基础-Vue-使用Vue脚手 架进行模块化开发 😍

## 1、全局安装webpack命令： `npm install webpack -g`

全局安装webpack命令：`npm install webpack -g` 【能把项目打包】；

注意细节：cmd右键**取消快速编辑模式**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130267.png)

## 2、全局安装vue脚手架命令：`npm install -g @vue/cli-init`

全局安装vue脚手架命令：`npm install -g @vue/cli-init` 【模块化项目】；

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130268.png)

**附：**

### 2.1、安装vue脚手架【模块化项目】如果没有问题不用看 【vue环境变量配置】

`npm install -g @vue/cli-init`【后面init 失败，选择下面一条语句成功】
解决方法一：`cnpm install -g vue-cli`

查找`vue.cmd`，将文件夹添加到`环境变量Path中` 。

推荐使用**EveryThing**工具查找`vue.cmd`文件。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130269.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130270.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130271.png)

## 3、初始化vue项目命令：`vue init webpack appname`

`vue init webpack appname` ：vue 脚手架使用webpack模板初始化一个 appname项目。

vue脚手架初始化一个叫`vue-demo`以webpack为模板的应用`vue init webpack vue-demo`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130272.png)

**选用npm的方式进行管理 （视频默认选择的都是第一个）**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130273.png)

`cd vue-demo`

`npm run dev`启动，**`ctrl+c`**停止项目
*http://localhost:8080*就可以访问了

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130274.png)

浏览器访问：[http://localhost:8080](http://localhost:8080/)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130275.png)

## 4、VSCode中启动vue项目命令：`npm start = npm run dev`

项目的**package.json**中有**scripts，**代表我们能运行的命令

`npm start = npm run dev` ：启动项目

`npmrun build`:将项目打包

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130276.png)

## 5、模块化开发

### 5.1 vue项目目录结构：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130277.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130278.png)

- main.js
  
    ```html
    main.js
    
    import Vue from 'vue'
    import App from './App'
    import router from './router'	// 指定了路由规则
    
    Vue.config.productionTip = false
    
    /* eslint-disable no-new */
    new Vue({
      el: '#app',
      router,
      components: { App },	/* 导入的组件 */
      template: '<App/>' 	/* 单文件组件作为模板 */
    })
    解析：这里component其实已经导入了组件App，在index.html的div中直接使用<App></App>就可以了
    如果这里指定了template: '<App/>'，则div不用再写<App></App>了，表示以template作为模板渲染，否则以el指定的div作为模板
    
    在 Vue 构造器中有一个el 参数，它是 DOM 元素中的 id。
    这意味着我们接下来的改动全部在以上指定的 div 内，div 外部不受影响。
    ```
    
- index.html
  
    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width,initial-scale=1.0">
        <title>vue-demo</title>
      </head>
      <body>
        <div id="app">
        	这里也可以直接使用<App></App>
        </div>
        <!-- built files will be auto injected -->
      </body>
    </html>
    ```
    

### 单文件组件 App.vue

【.vue文件就是一个全局的vue实例，不需要export语句】这一个文件就是一个组件。例如main.js中的Vue实例就使用了App.vue单文件组件作为模板

去官网看doc，三个组成部分

template ：html代码

script：js代码

style：css

- App.vue：单文件组件
  
    ```html
    1、App.vue 单文件组件，被引入到index.html的div模块
    template由img和<router-view/>组成，imag图片始终不变
    路由按照请求路径来显示不同的页面【路由规则查看main.js导入的路由组件具体路由】
    
    <template>
      <div id="app">
        <img src="./assets/logo.png">
        <router-view/>
      </div>
    </template>
    
    <script>
    export default {
      name: 'App'
    }
    </script>
    
    <style>
    #app {
      font-family: 'Avenir', Helvetica, Arial, sans-serif;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
      text-align: center;
      color: #2c3e50;
      margin-top: 60px;
    }
    </style>
    ```
    

### 路由视图：

路由视图会根据请求路径改变

- 路由视图：
  
    ```html
    1、App.vue
    
    <template>
      <div id="app">
        <!-- img图片不变 -->
        <img src="./assets/logo.png">
        <!-- 路由视图会根据请求路径改变 -->
        <router-view/>
      </div>
    </template>
    
    <script>
    export default {
      name: 'App'
    }
    </script>
    
    <style>
    #app {
      font-family: 'Avenir', Helvetica, Arial, sans-serif;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
      text-align: center;
      color: #2c3e50;
      margin-top: 60px;
    }
    </style>
    
    2、index.js【里面new了一个Router并且是default没有名字】
    规则是/请求会使用组件HelloWorld
    
    import Vue from 'vue'
    import Router from 'vue-router'
    import HelloWorld from '@/components/HelloWorld'
    
    Vue.use(Router)
    
    export default new Router({
      routes: [
        {
          path: '/',
          name: 'HelloWorld',
          component: HelloWorld
        }
      ]
    })
    
    3、所有在路由规则里面添加路由+组件的映射关系，就可以把自己写的组件也添加进去了【/hello】
    然后在写单页面组件hello.vue就可以了【.vue文件就是一个全局的vue实例，不需要export语句】
    
    import Vue from 'vue'
    import Router from 'vue-router'
    import HelloWorld from '@/components/HelloWorld'
    import Hello from '@/components/Hello'
    
    Vue.use(Router)
    
    export default new Router({
      routes: [
        {
          path: '/',
          name: 'HelloWorld',
          component: HelloWorld
        },{
          path: '/hello',
          name: 'Hello',
          component: Hello
        }
      ]
    })
    ```
    

# 😍前端Vue总结+梳理【重要，看这里】`.js`| `.vue`（单文件组件 | `.html`（渲染模板）

总结：`.js`（纯js代码，定义实例、变量）、`.vue`（单文件组件）、`.html`（渲染模板）【其中`.js`文件与`.vue`文件互相依赖进行渲染（`template`被作为模板渲染），`html`文件可以被作为模板进行渲染也可以在内部写js语句】

vue项目开放端口太麻烦了，其他主机访问不了。防火墙输入策略和host什么的修改过，nacos等其他服务能正常访问，vue项目别的主机访问不了，不知道哪里有限制

可以在`config/index.js`配置vue项目的ip和端口

```bash
vue实例：以下demo主要讲在<script>中new的Vue实例
		组成：任何vue实例都包含template、script、style三部分
	1、el：关联的html标签（一般是div标签的id）【表示该vue实例的template是el外部的html】
	2、router：路由，可以被当前vue实例依赖的组件相关联完成不同路由地址的渲染（在.vue文件< router-view>/使用路由）
	3、components：依赖的组件（组件也是一个vue实例），这里依赖了组件后，在div中就可以使用< 组件名/>来使用组件进行渲染了
	4、template：直接在这里渲染后添加到div标签中【表示该vue实例的template是指定的组件】
	5、data：数据，div标签内部可以使用插值表达式使用，但是标签属性上要使用指令 来使用data中的属性值
	6、methods：方法，跟data使用方式一样（插值表达式使用时不需要加括号）
	7、computed：计算属性，实际上一个方法，并且有返回值
	8、watch：监控data中的数据（先于computer被执行）
	9、过滤器：局部（只能在当前vue实例中使用）与全局过滤器，【用来对数据的过滤{{user.gender | gFilter}}】
// 全局过滤器（可以被其他vue使用）
Vue.filter("gFilter", function (val) {
    if (val == 1) {
    	return "男~~~";
    } else {
    	return "女~~~";
    }
})
	
new Vue({
  el: '#app',
  router,
  components: { App },// 名字与组件名相同可以简写
  data: {name: "zhangsan", age: 13},
  methods: {
  	hello(){return "World";}
  },
  computed: {
    totalPrice(){// totalPrice : function() ES6后可以简化成下样式
  		return this.xyjPrice*this.xyjNum + this.shzPrice*this.shzNum
    }
  },
  watch: {
  	xyjNum(newVal,oldVal){
        if(newVal>=3){
        	this.msg = "库存超出限制";
        	this.xyjNum = 3
        }else{
        	this.msg = "";
        }
  	}
  },
  filters: {
      // filters 定义局部过滤器，只可以在当前vue实例中使用
      genderFilter(val) {// 过滤器的名字
      	if (val == 1) {
      		return "男";
      	} else {
      		return "女";
      	}
      }
  },
  template: '<App/>'
})
```

```bash
component组件：
	实际上就是一个Vue实例，组件三要素：template、script、style
    组件的两种形式：1、声明式组件（下面demo中的全局组件、局部组件）；
    			 2、单文件组件（三要素，其实就是一个全局的vue实例，组件也是vue实例。例如App.vue）
    组件使用：在js代码中创建vue实例使用的组件的时候，要导入组
                1、导入单文件组件：import App from './App' （export default {name: 'App'}）
														 整个App.vue导出为一个组件实例对象
				2、导入声明式组件：import {buttonCounter} from './user.js' 
						  导出一个叫buttonCounter的组件实例（export {buttonCounter}）

1、声明式组件：全局组件 && 局部组件
	使用：在vue实例中定义components属性指定局部属性，全局属性不需要指定
	demo：组件化.html
    <div id="app">
        <button v-on:click="count++">我被点击了 {{count}} 次</button>
        <counter></counter>
        <button-counter></button-counter>
    </div>
    <script src="../node_modules/vue/dist/vue.js"></script>
    <script>
        //1、全局声明注册一个组件
        Vue.component("counter", {
            template: `<button v-on:click="count++">我被点击了 {{count}} 次</button>`,
            data() {
                return {
                    count: 1
                }
            }
        });

        //2、局部声明一个组件
        const buttonCounter = {
            template: `<button v-on:click="count++">我被点击了 {{count}} 次~~~</button>`,
            data() {
                return {
                    count: 1
                }
            }
        };

        new Vue({
            el: "#app",
            data: {
                count: 1
            },
            components: {
                'button-counter': buttonCounter
            }
        })
    </script>

2、单文件组件，使用的时候要import导入【因为组件本身就是一个vue实例，一个js要使用另一个js的实例、变量就要导入】
demo：main.js
import Vue from 'vue'
import App from './App'    这里要导入App.vue单文件组件
import router from './router'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})

demo：App.vue
<template>
  <div id="app">
    <!-- img图片不变 -->
    <img src="./assets/logo.png">
    <!-- 路由视图会根据请求路径改变 -->
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

3、组件内部属性template
单文件组件：App.vue在<template>标签内部可以使用<router-view/>标签指定该标签的渲染由路由指定
声明式组件：在组件的template属性中 `<router-view/>` 同样可以指定路由渲染标签

而路由标签的实际路由由依赖该组件的vue实例决定，所以<router-view/>就像一个钩子函数，由实际vue实例决定
```

```bash
router.js：路由实例，与vue实例搭配使用，在组件的template中可以使用<router-view/>来指定不同路由显示不同的组件
解析router.js内部：
1、导入 vue 和 vue-router
2、导入依赖的组件
3、new Router实例并export导出
4、在routes属性内部指定一个数组
5、数组元素三个属性
	1）path：路由地址【当访问/hello时，App.vue组件的template中的<router-view/>就会渲染Hello组件】
    2）name：组件名
    3）component：组件
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Hello from '@/components/Hello'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },{
      path: '/hello',
      name: 'Hello',
      component: Hello
    }
  ]
})
```

```bash
<router-view>和<router-link>
1、<router-view>：会根据访问路径决定渲染组件
2、<router-link>：相当于a标签，点击后渲染路由指定的组件
<template>
  <div id="app">
    <!-- img图片不变 -->
    <img src="./assets/logo.png">
    <router-link to="/hello">去hello</router-link>
    <router-link to="/">去首页</router-link>
    <!-- 路由视图会根据请求路径改变 -->
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

## 区分.js文件、.html文件、.vue文件

```bash
区分.js文件、.html文件、.vue文件
.js文件：
1、js多用于绑定vue实例与div的绑定关系（用el:'#app'）
2、js文件可以import和export变量、函数、实例（vue、component、router）
3、js文件其实就是<script>标签内部代码单独抽离出来了
4、vue实例可以选择html作为模板渲染，也可以使用template属性指定的作为模板渲染

.html文件
1、多用作模板，模板与vue实例结合才能完成渲染。
2、内部可以指定<script>标签，内部代码跟.js文件的一样

.vue文件
1、一个单文件组件，一个vue实例，并在<script>以export default{name:App}的形式向外提供组件实例
1、包含template、script、style，是一个完整组件
```

# 44、前端基础-Vue- 整合ElementUI快速开发

## ElementUI官网：

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/installation)

[https://element.eleme.cn/#/zh-CN/component/installation](https://element.eleme.cn/#/zh-CN/component/installation)

## npm 安装ElementUI命令：`npm i element-ui -S`

推荐使用 npm 的方式安装，它能更好地和 webpack 打包工具配合使用。

```bash
npm i element-ui -S
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130279.png)

> vue项目开放端口太麻烦了，其他主机访问不了。防火墙输入策略和host什么的修改过，nacos等其他服务能正常访问，vue项目别的主机访问不了，不知道哪里有限制可以在`onfig/index.js`配置vue项目的ip和端口
> 

如果有启动项目先将其停止：`ctrl+c`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062130280.png)

## 快速上手：完整引入 Element 在 main.js 中写入以下内容：【参照官网】

```bash
import Vue from 'vue';
**import ElementUI from 'element-ui';**
**import 'element-ui/lib/theme-chalk/index.css';**
import App from './App.vue';

Vue.use(ElementUI);

new Vue({
  el: '#app',
  render: h => h(App)
});
```

### 快捷键模板设置

[VSCode 快速生成 .vue 基本模板、发送http请求模板 - 农夫三拳有点疼~ - 博客园](https://www.notion.so/VSCode-vue-http-3f202317b6a241649716387f7fbeeaa4) 

```html
{
	"Print to console": {
		"prefix": "vue",
		"body": [
			"<!-- $1 -->",
			"<template>",
			"<div class='$2'>$5</div>",
			"</template>",
			"",
			"<script>",
			"//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）",
			"//例如：import 《组件名称》 from '《组件路径》';",
			"",
			"export default {",
			"//import引入的组件需要注入到对象中才能使用",
			"components: {},",
			"data() {",
			"//这里存放数据",
			"return {",
			"",
			"};",
			"},",
			"//监听属性 类似于data概念",
			"computed: {},",
			"//监控data中的数据变化",
			"watch: {},",
			"//方法集合",
			"methods: {",
			"",
			"},",
			"//生命周期 - 创建完成（可以访问当前this实例）",
			"created() {",
			"",
			"},",
			"//生命周期 - 挂载完成（可以访问DOM元素）",
			"mounted() {",
			"",
			"},",
			"beforeCreate() {}, //生命周期 - 创建之前",
			"beforeMount() {}, //生命周期 - 挂载之前",
			"beforeUpdate() {}, //生命周期 - 更新之前",
			"updated() {}, //生命周期 - 更新之后",
			"beforeDestroy() {}, //生命周期 - 销毁之前",
			"destroyed() {}, //生命周期 - 销毁完成",
			"activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发",
			"}",
			"</script>",
			"<style scoped>",
			"/* @import url(); 引入公共css类 */",
			"$4",
			"</style>"
		],
		"description": "生成vue模板"
	},
	"http-get请求": {
		"prefix": "httpget",
		"body": [
			"this.\\$http({",
			"url: this.\\$http.adornUrl(''),",
			"method: 'get',",
			"params: this.\\$http.adornParams({})",
			"}).then(({ data }) => {",
			"})"
		],
		"description": "httpGET请求"
	},
	"http-post请求": {
		"prefix": "httppost",
		"body": [
			"this.\\$http({",
			"url: this.\\$http.adornUrl(''),",
			"method: 'post',",
			"data: this.\\$http.adornData(data, false)",
			"}).then(({ data }) => { });"
		],
		"description": "httpPOST请求"
	}
}
```