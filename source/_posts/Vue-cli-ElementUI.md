---
title: Vue-cli&ElementUI
date: 2021-03-06 20:17:44
tags: 总结文档
copyright: false
categories: 
- 记录文档
---

## 1. 什么是Vue-cli

* Vue cli是基于Vue的应用开发提供的一个标准的脚手架工具.为应用搭建基础的框架结构,提供插件、开发服务、Preset、构建打包功能
* Vue cli 背后集成了现代化开发的诸多功能,通过简单的命令就可以完成 "零配置"的项目环境搭建

## 2. 安装Vue-cli步骤
在安装vue-cli前，要确认自己的电脑是否安装了nodejs和npm.

### 2.1 安装Node.js
安装了node.js才有使用npm ，才能安装vue-cli

#### 2.1.1 什么是node.js
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416204738.png)
为什么会有node.js？
>传统意义上的 JavaScript 运行在浏览器上，Chrome 使用的 JavaScript 引擎是 V8，Node.js 是一个运行在服务端 的框架，它的底层就使用了 V8 引擎，这样就可以使用javascript去编写一些服务端的程序，这样也就实现了用 javaScript去开发 Apache + PHP 以及 Java Servlet所开发的服务端功能，这样做的好处就是前端和后端都采用 javascript，即开发一份js程序即可以运行在前端也可以运行的服务端，这样比一个应用使用多种语言在开发效率上 要高，不过node.js属于新兴产品，一些公司也在尝试使用node.js完成一些业务领域，node.js基于V8引擎，基于 事件驱动机制，在特定领域性能出色，比如用node.js实现消息推送、状态监控等的业务功能非常合适。

#### 2.1.2 安装node.js
* 下载对应你系统的Node.js版本:

https://nodejs.org/en/download/
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416204949.png)

* 选安装目录进行安装, 我选择安装在了E盘: E:\Program Files\nodejs

* 测试: 在命令提示符下输入命令

```powershell
node -v //会显示当前node的版本
```

### 2.2 安装NPM
npm全称Node Package Manager，他是node包管理和分发的工具，使用NPM可以对应用的依赖进行管理，NPM 的功能和服务端项目构建工具maven的依赖管理功能差不多，我们通过npm 可以很方便地下载js库，打包js文件。

#### 2.2.1 自动安装NPM
node.js已经集成了npm工具
在命令提示符输入 npm -v 可查看当前npm版本
```powershell
npm -v
```
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416205247.png)

#### 2.2.2 查看包管理路径
包路径就是npm从远程下载的js包所存放的路径。 使用 npm config ls 查询NPM管理包路径（NPM下载的依赖包所存放的路径）
```powershell
npm config ls
```
我们发现NPM默认的管理包路径在:
```powershell
C:\Users\hao\AppData\Roaming\npm
```
#### 2.2.3 设置包管理路径
依赖包放在C盘不太合适,为了方便对依赖包管理, 我们将管理包的路径设置在单独的地方:
* 我们选择一个路径,专门存放这些依赖包.我选择创建一个目录: H:\software\nodejs_package

* 在 H:\software\nodejs_package 下再创建 npm_modules 文件夹 和 npm_cache 文件夹:

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416205548.png)

* 执行下边的命令,设置为自定义的包管理路径：

```powershell
npm config set prefix "H:\software\nodejs_package\npm_modules" 
npm config set cache "H:\software\nodejs_package\npm_cache"
```
* 此时再使用 npm config ls 查询NPM管理包路径发现路径已更改

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416205653.png)

#### 2.2.4 NPM环境变量配置
* 查看npm的全局路径是什么

```powershell
npm config get prefix
```
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416210101.png)
* 配置PATH环境变量

#### 2.2.5 安装cnpm
npm默认会去国外的镜像去下载js包，在开发中通常我们使用国内镜像，这里我们使用淘宝镜像下边我们来安装cnpm： 有时我们使用npm下载资源会很慢，所以我们可以安装一个cnmp(淘宝镜像)来加快下载速度。
* 联网情况下, 输入命令，进行全局安装淘宝镜像:

```powershell
//安装 
npm install -g cnpm --registry=https://registry.npm.taobao.org 
//查看cnpm的版本 
cnpm -v
```

### 2.3 安装vue-cli
目前主流版本是 2.x 和 3.x 版本,安装3.x 以上的版本是因为该版本既可以创建2.x项目与3.x 项目
注意: 以管理员身份打开命令行
* 安装命令

```powershell
npm install -g @vue/cli
```
* 输入 vue命令

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416210439.png)
* 输入 vue -V 查看版本

```
vue -v
```
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416210527.png)

## 3. 快速构建Vue项目
### 3.1 步骤说明
我们使用vue-cli 快速构建项目,步骤如下:
* 桌面创建一个空的文件夹

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416210612.png)
* 以管理员身份运行cmd , 进入到vueTest文件夹

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416210642.png)
* 执行下面的命令

```powershell
//1.基于交互式命令方式,创建项目 
//文件名 不支持驼峰（含大写字母）使用短横线方式 
vue create my-project
```
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211212.png)
* 选择自定义安装,点击回车

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211302.png)
* 在这列表中,选择我们要安装的组件,使用空格键选择,选好后回车

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211333.png)

* 按回车之后,提示选择什么模式的路由,我们输入 n (表示选择hash模式)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211417.png)

* 选择项目配置文件单独存放

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211443.png)

* 是否保存模板,选择n 不创建

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211510.png)
* 安装完成,提示输入执行下面这两个命令

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211549.png)
* 首先进入项目目录

```powershell
cd my-project
```
* 启动项目

```powershell
npm run serve
```
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211652.png)
* 访问项目: http://localhost:8080/

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210416211757.png)
* 停止项目 ctr+c或者关闭命令行窗口就可以

### 3.2 导入Vue项目到VSCode

* VSCode中右键选择打开文件夹

* 选择桌面上的项目

* 打开项目,可以看到如下项目结构

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420193436.png)

### 3.3 项目结构介绍

```
|--- my-project 项目名称 
	|--- node_modules 存放依赖包的目录 
	|--- public 静态资源管理目录 
	|--- src 组件源码目录(我们写的代码) 
		|--- assets 存放静态图片资源(CSS也可以放在这里) 
		|--- components 存放各种组件(一个页面可以看做一个组件)，各个组件联系在一起组成一个 完整的项目
		|--- router 存放了项目路由文件 
		|--- views 放置的为公共组件(主要还是各个主要页面) 
		|--- App.vue app.vue可以当做是网站首页，是一个vue项目的主组件，页面入口文件 
		|--- main.js 打包运行的入口文件，引入了vue模块和app.vue组件以及路由route 	
	|--- babel.config.js babel配置文件, 对源代码进行转码(把es6=>es5) 
	|--- package.json 项目及工具的依赖配置文件 
	|--- paxkage-lock.json 依赖配置文件 
	|--- README.md 项目说明
```



### 3.4 Vue脚手架自定义配置

#### 3.4.1 package.js 介绍

每个项目的根目录下面，一般都有一个 package.json 文件，定义了这个项目所需要的各种模块，以及项目的配置信息（比如名称、版本、许可证等元数据）。 npm install 命令根据这个配置文件，自动下载所需的模块，也就是配置项目所需的运行和开发环境。
```json
{ 
    //1.项目基本信息 
    "name": "project3", 
    "version": "0.1.0", 
    "private": true, 
    
    //2.指定运行脚本命令 
    "scripts": { 
        "serve": "vue-cli-service serve", 
        "build": "vue-cli-service build" 
    },
    
    //4.生产环境所依赖模块的版本 
    "dependencies": { 
        "core-js": "^3.6.5", 
        "vue": "^2.6.11", 
        "vue-router": "^3.2.0" 
    },
    
    //5.本地环境开发所依赖的版本 
    "devDependencies": { 
        "@vue/cli-plugin-babel": "~4.4.0", 
        "@vue/cli-plugin-router": "~4.4.0", 
        "@vue/cli-service": "~4.4.0", 
        "vue-template-compiler": "^2.6.11" 
    } 
}
```

#### 3.4.2 通过package.json 配置项目
配置内容采用JSON格式,所有的内容都用双引号包裹
打开package.json,再最末端添加如下配置:
```json
"vue":{ 
    "devServer":{ 
        "port":"8888", 
        "open":true 
    } 
}
```
* 配置说明: 该配置设置打包时服务器相关的信息
  * port : 访问端口 
  * open true: 打包完成自动打开浏览器

**启动项目**

* 在VSCode中选择项目,右键在终端打开

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420194223.png)

* 输入命令

```powershell
npm run serve
```

* 运行后发现端口号改为 8888,并且在打包完成后自动打开浏览器

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420194335.png)

注意: 不推荐这种方式,因为package.json 主要是用来管理包的配置信息.为了方便维护,我们将Vue脚手架相关的配置单独定义到 **vue.confifig.js** 配置文件中

#### 3.4.3 单独的配置文件配置项目
* 在项目的根目录创建文件 vue.config.js
* 删除掉package中新添加的配置项. 
* 在vue.config.js 文件中进行相关配置

```js
module.exports = { 
    devServer:{ 
        open:true port:8889 
    } 
}
```



### 3.5 Vue 组件化开发

#### 3.5.1 组件化开发

组件化是Vue的精髓，Vue项目就是由一个一个的组件构成的。 我们主要的工作就是开发的组件.

#### 3.5.2 组件介绍

1）我们用 vue-cli 脚手架搭建的项目，里面有很多，如 index.vue 或者 App.vue 这一类的文件.每一个**.vue 文件都是一个组件 ，是一个自定义的文件类型**, 比如 App.vue 就是整个项目的根组件。

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420194800.png)

2）常见的组件：
* 页面级别的组件
	* 页面级别的组件，通常是 views 目录下的.vue组件，是组成整个项目的各个主要页面
* 业务上可复用的基础组件
	* 这一类组件通常是在业务中被各个页面复用的组件，这一类组件通常都写到 components 目录下，然后通过import在各个页面中使用

3）组件的组成部分
* template : 组件的HTML部分
* script: 组件的JS脚本 (使用ES6语法编写)
* style: 组件的CSS样式

```js
<!-- 1.template 代表html结构, template中的内容必须有且只有一个根元素 编写页面静态部分 就是 view部分 --> 
<template> 
	<div>测试页面... </div> 
</template> 

<!-- 2.编写vue.js代码 --> 
<script> 
    //可以导入其组件 
    // import Header from '../components/header.vue' 
    //默认写法, 输出该组件 
    export default { 
        name:"Home", // 组件名称，用于以后路由跳转 
        data() {// 当前组件中需要使用的数据 
            return {} 
        }, 
        methods: {} 
    } 
</script> 

<!-- 3.编写当前组件的样式代码 --> 
<style scoped> 
    /* 页面样式 加上scoped 表示样式就只在当前组件有效*/ 
</style>
```



## 4. 项目运行流程

### 4.1 main.js

项目运行 会加载入口文件 main.js

```js
/* html文件中，通过script src = 'xxx'标签引入js文件。 而vue中，通过 import 变量名 from 文件路径 的方式导入文件，不光可以导入js文件。 
1.变量名: 指的是为导入的文件起一个名称，不是指导入的文件的名称，相当于变量名。 
2.文件路径: 指的是文件的相对路径 */

import Vue from 'vue' 
import App from './App.vue' 
import router from './router' 

//关闭启动提示 
Vue.config.productionTip = false 

//创建Vue实例 
new Vue({ 
    router, //为整个项目添加路由 
    render: h => h(App) //这是一个函数ES6语法,作用是生成模板: 
    App = App.vue 
}).$mount('#app') //挂载的是App.vue组件中的id为app的区域
```



### 4.2 App.vue

App.vue 是vue项目的主组件，是页面入口文件 ，所有页面都是在App.vue下进行切换的

```js
<template> 
	<div id="app"> <!--挂载的是这个div--> 
        <div id="nav"> <!--这里是两个路由导航链接--> 
            <!--1. to="/" 项目根路径 跳转的是首页--> 
            <router-link to="/">Home</router-link> | 
            <!--2. to="/about" 点击About按钮,跳转到about组件--> 
            <router-link to="/about">About</router-link> 
    </div> 
        <!--router-view 的作用是 根据访问的路径,渲染路径匹配到的视图组件--> 
        <router-view/> 
    </div> 
</template>
```



### 4.3 router 路由

找到路由文件,来看一下具体的路由配置

```js
// 引入所需文件 
import Vue from 'vue'
//vue库 
import VueRouter from 'vue-router'
//vue-router库 
import Home from '../views/Home.vue' //首页 

//使用路由功能 
Vue.use(VueRouter)

//创建路由规则 
const routes = [{
		path: '/', //路径 
		name: 'Home', //名称 
		component: Home //组件 
		Home.vue
	},
	{
		path: '/about',
		name: 'About',
		component: () =>
			import ( /* webpackChunkName: "about" */ '../views/About.vue')
	}
] //创建路由管理器,管理routes 
const router = new VueRouter({ 
    routes 
})
//export 用来导出模块 router就代表了整个路由文件 
export default router
```



### 4.4 Home.vue组件

* 默认访问的是Home.vue 首页

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420200637.png)

```js
< template>
	<div class = "home"> 首页的logo 
		< img alt = "Vue logo" src = "../assets/logo.png" > 
		< HelloWorld msg = "Welcome to Your Vue.js App" / > 
	</div> 
</template>

<script> 
//导入了一个组件 HelloWorld.vue @符号表示 src这个目录 
import HelloWorld from '@/components/HelloWorld.vue' 
export default { 
    name: 'Home', 
    components: { 
        HelloWorld 
    } 
}
</script>
```

HelloWorld.vue 组件页面

```js
<template>
	<div class="hello">
		<h1>{{ msg }}</h1>
		<p>
			For a guide and recipes on how to configure / customize this project,
			<br>
			check out the
			<a href="https://cli.vuejs.org" target="_blank" rel="noopener">
				vue-cli documentation
			</a>
		</p>
		<h3>
			Installed CLI Plugins
		</h3>
	</div>
</template>
<script>
	export default {
		name:
		'HelloWorld',
		props: {
			msg: String
		}
	}
</script>
```





## 5. 组件的使用案例
### 5.1 创建Header.vue组件

* 在components 目录下创建 Header.vue

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420201834.png)

* 编写Header.vue

```js
<template>
	<div class="header">
		{{msg}}
	</div>
</template>

<script>
	//JS 部分 
	export default {
		name: "Header", //组件的名称 
		data() { //data函数 
			return {
				msg: "这是一个Header组件"
			}
		},
	}
</script>

//scoped 表示当前的样式,只作用与当前组件中的 template 视图.
<style scoped>
	.header { 
      height: 100px; 
      line-height: 100px;
      background-color: #eee;text-align:
	  center; color: blue; 
    }
</style>
```



### 5.2 引入 Header组件

修改Home.vue

```html
<template>
	<div class="home">
		<img alt="Vue logo" src="../assets/logo.png">
		<!-- <HelloWorld msg="Welcome to Your Vue.js App"/>
		-->
		<Header/>
	</div>
</template>
<script>
	// @ is an alias to /src 
	//import HelloWorld from '@/components/HelloWorld.vue' 
	import Header from '@/components/Header.vue';
	export default {
		name: 'Home',
		components: { 
          //HelloWorld 
          Header 
        } 
    }
</script>
```



### 5.3 组件的传参

props : 是组件中的属性, 表示组件可以接受参数, 

```html
<template>
	<div id="Header" class="header">
		{{msg}}
	</div>
</template>
<script>
	export default {
		name: "Header", //组件名称,用于路由的跳转 
  		props:['msg'] 
	};	
</script>
// scoped 表示当前style的样式只作用于当前组件的template代码中，其他地方不会被影响
<style scoped>
	.header { height: 100px; line-height: 100px; background-color: #eee;text-align:
	center; color: blue; }
</style>
```



## 6. Element-UI介绍

element-ui 是饿了么前端出品的基于 Vue.js的 后台组件库，方便程序员进行页面快速布局和构建Element-UI官方站点：
https://element.eleme.cn/#/zh-CN

## 7.  Element-UI使用

### 7.1 命令行方式安装

* 创建 一个新的项目
* 当前项目下打开终端, 安装依赖包 ,执行下面的命令

```powershell
npm i element-ui -S
```
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420203004.png)

* 打开 main.js , 导入Element-UI 相关资源.
	* main.js是工程的入口文件，在此文件中加载了很多第三方组件，如：Element-UI、Base64、 VueRouter等。

```js
//导入组件库 
import ElementUI from 'element-ui' 

//导入组件相关样式 
import 'element-ui/lib/theme-chalk/index.css' 

//配置Vue插件 将El安装到Vue上 
Vue.use(ElementUI);
```

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420203205.png)

* 复制Element 按钮样式 到app.vue文件的 template下

```html
<template>
	<div id="app">
		<!-- 测试elementUI -->
		<el-row>
			<el-button>默认按钮</el-button>
			<el-button type="primary">主要按钮</el-button>
			<el-button type="success">成功按钮</el-button>
			<el-button type="info">信息按钮</el-button>
			<el-button type="warning">警告按钮</el-button>
			<el-button type="danger">危险按钮</el-button>
		</el-row>
		
		<div id="nav">
			<router-link to="/">Home</router-link>|
			<router-link to="/about">About</router-link>
		</div>
		<router-view />
	</div>
</template>
```

* 启动项目 npm run serve, 查看页面

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420203522.png)



### 7.2 Vue-CLI工程改造
* 删除components 目录下的 HelloWord.vue组件
* 删除App.vue中的部分内容,只保留如下部分

```html
<template> 
	<div id="app"></div> 
</template> 

<style> 
</style>
```

* 删除router文件下的路由文件 index.js部分内容,只保留如下部分

```html
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter) 

const routes = [] 
const router = new VueRouter({
	routes
}) 
export default router
```

* 删除views目录下的 About.vue 与 Home.vue

### 7.3 安装axios

* npm安装：使用npm下载axios包 

```powershell
npm i axios
```

* 在main.js文件中导入axios 相关资源

```js
//引入axios 
import axios from 'axios' 

//Vue对象使用axios 
Vue.prototype.axios = axios;
```



## 8. 用户登录界面制作

### 8.1  Dialog对话框组件

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420204252.png)
**我们可以用Dialog制作一个登陆弹窗,选择自定义内容**
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420204323.png)

```html
<el-dialog title="收货地址" :visible.sync="dialogFormVisible">
	<el-form :model="form">
		<el-form-item label="活动名称" :label-width="formLabelWidth">
			<el-input v-model="form.name" autocomplete="off">
			</el-input>
		</el-form-item>
		<el-form-item label="活动区域" :label-width="formLabelWidth">
			<el-select v-model="form.region" placeholder="请选择活动区域">
				<el-option label="区域一" value="shanghai">
				</el-option>
				<el-option label="区域二" value="beijing">
				</el-option>
			</el-select>
		</el-form-item>
	</el-form>
	<div slot="footer" class="dialog-footer">
		<el-button @click="dialogFormVisible = false">
			取 消
		</el-button>
		<el-button type="primary" @click="dialogFormVisible = false">
			确 定
			</el-button>
	</div>
</el-dialog>
```



### 8.2 创建login.vue 组件

1. 在components 下创建login.vue

2. 将Diglog组件的内容,拷贝到login.vue,进行修改:

```html
<template>
	<el-dialog title="登录" :visible.sync="dialogFormVisible">
		<el-form>
			<el-form-item label="用户名称" :label-width="formLabelWidth">
				<el-input autocomplete="off">
				</el-input>
			</el-form-item>
			<el-form-item label="用户密码" :label-width="formLabelWidth">
				<el-input autocomplete="off">
				</el-input>
			</el-form-item>
		</el-form>
		<div slot="footer" class="dialog-footer">
			<el-button type="primary" @click="dialogFormVisible = false">
				登录
				</el-button>
		</div>
	</el-dialog>
</template>
<script>
	export default {
		data() {
			return {
				formLabelWidth: "120px", //宽度 
                dialogFormVisible: true 
            }; 
        } 
	};			
</script>
<style scoped>
</style>
```



### 8.3 配置路由

```js
import Vue from "vue";
import VueRouter from "vue-router";
import Login from "@/components/Login.vue"

Vue.use(VueRouter);
const routes = [
//访问 /,也跳转到login 
{
	path: '/',
	redirect: 'login' //重定向都login 
},
//登录 
{
	path: '/login',
	name: 'login',
	component: Login
}];
const router = new VueRouter({
	routes,
});
export default router;
```



### 8.4 修改App.vue

```html
<template> 
	<div id="app"> 
        <!-- router-view 的作用是根据访问的路径,渲染路径匹配到的视图组件 --> 
        <router-view></router-view> 
    </div> 
</template> 

<style> </style>
```



### 8.5 编写登录功能

* 去掉关闭按钮, 添加一个属性 **:show-close="false"** 

```html
<el-dialog title="登录" :show-close="false" :visible.sync="dialogFormVisible">
```

* 修改登陆触发事件

```vue
<el-button type="primary" @click="login">登录</el-button>
```

* 双向数据绑定
  * data 中定义数据

```js
data() { 
    return { 
        formLabelWidth: "120px", //宽度 
        dialogFormVisible: true, //是否关闭对话框 
        user: { username: "", password: "" }, //登录数据 
    }; 
},
```

* 使用 v-model, 将视图与模型进行绑定

```html
<el-form> 
    <el-form-item label="用户名称" :label-width="formLabelWidth"> 
        <el-input v-model="user.username" autocomplete="off"></el-input> 
    </el-form-item> 
    <el-form-item label="用户密码" :label-width="formLabelWidth"> 
        <el-input v-model="user.password" autocomplete="off"></el-input> 
    </el-form-item> 
</el-form>
```

* 编写login方法

```js
methods: {
	login() {
		//定义常量保存 
		url const url = "http";

		//发送请求 
		this.axios.get(url, {
			//携带参数 
			params: {
				username: this.user.username,
				password: this.user.password,
			},
		}).then((res) = >{
			console.log();
			//成功就将对话框关闭
			this.dialogFormVisible = false;
		}).
		catch((error) = >{
			//出现错误使用ElementUI提供的消息提示 
			this.$message.error("对不起! 登录错误!");
		});
	},
},

```



### 8.6 Postman搭建mock server

* Mock server就是模拟一个服务器，我们使用Mock server可以模拟后台接口,对请求进行响应.
* 在前后端分离的开发中 前端利用mockeserver模拟出对应接口，拿到返回数据来调试，无需等后端开发人员完成工作。

postman模拟出一个server 步骤: 

* 使用postman模拟出一个server

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420205719.png)

* 打开如下窗体，创建一个伪服务

第一步

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420205758.png)

第二步

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210420205932.png)

第三步

```js
const url = "复制上面的地址/login"; 
```



### 8.7 登录成功后跳转

* 在js中设置跳转，常用的一种方法是 **this.$router.push**

```js
methods: {
	login() {
		//定义常量保存 
		url const url = "https://33284b33-e976-4124-a3a0-17044addc1e1.mock.pstmn.io/login";
		//发送请求 
		this.axios.get(url, {
			//携带参数 
			params: {
				username: this.user.username,
				password: this.user.password,
			},
		}).then((res) = >{
			console.log(res.data);
			alert("登录成功!");
			//成功就将对话框关闭 
			this.dialogFormVisible = false;
			//跳转页面,前端跳转页面必须使用路由,使用$router对象中的push方法
			this.$router.push('/index');
		}).
		catch((error) = >{
			//出现错误使用ElementUI提供的消息提示 
			this.$message.error("对不起! 登录错误!");
		});
	},
},

```



## 9. 跨域问题

### 9.1 出现跨域问题

当我们在前端项目中,向后端发送请求的获取课程数据的时候,出现了跨域问题:

* 已被CORS策略阻止：请求的资源上没有' Access-Control-Allow-Origin'标头（跨域请求失败）

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210423112514.png)

```
Access to XMLHttpRequest at 'http://localhost:8080/lagou_edu_home/course? methodName=findCourseList' from origin 'http://localhost:8088' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

### 9.2 什么是跨域

跨域是指通过JS在不同的域之间进行数据传输或通信，比如用ajax向一个不同的域请求数据，只要协议、域名、端口有任何一个不同，都被当作是不同的域,浏览器就不允许跨域请求。

* 跨域的几种常见情

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210423112637.png)

### 9.3 解决跨域问题

跨域的允许主要由服务器端控制。服务器端通过在响应的 header 中设置 Access-Control-Allow- Origin 及相关一系列参数，提供跨域访问的允许策略

* 设置响应头中的参数来允许跨域域请求:
* Access-Control-Allow-Credentials
* Access-Control-Allow-Origin 标识允许跨域的请求有哪些

**1）在POM文件中引入依赖**
```xml
<!-- 解决跨域问题所需依赖 --> 
<dependency> 
    <groupId>com.thetransactioncompany</groupId> 
    <artifactId>cors-filter</artifactId> 
    <version>2.5</version> 
</dependency>
```
**1）在web.xml中 配置跨域 filter**
```xml
<!--配置跨域过滤器--> 
<filter> 
    <filter-name>corsFilter</filter-name> 
    <filter-class>com.thetransactioncompany.cors.CORSFilter</filter-class> </filter> 

<filter-mapping> 
    <filter-name>corsFilter</filter-name> 
    <url-pattern>/*</url-pattern> 
</filter-mapping>
```