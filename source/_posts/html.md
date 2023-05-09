---
title: HTML详解
date: 2017-01-04 09:51:59
tags: 总结文档
copyright: false
categories: 
- 前端
- HTML
---

### 1. HTML介绍&规范

#### 1.1 介绍

HTML 指的是超文本标记/标签语言 (Hyper Text Markup Language)专门制作网页的计算机语言<!--more-->

普通的文本就是英文单词，英文字母一样的存在。超文本的意思是有一些单词或字母，在网页浏览器的世界中被赋予了特殊的权利。

比如：我们都是普通人，但是有些人经过国家的选拔，穿上警服之后，他就被赋予了执法的特殊权利，普通人是没有执法权的。字母img只是普通的字母，没什么特殊的含义。而<img>被加上尖括号后，在网页的世界中，就具备了显示图片的作用。



#### 1.2 使用记事本开发第一个网页

在计算机任意的位置，创建一个空白的记事本

重点：文件的后缀名必须是 "htm" 或 "html"

鼠标右键文件，选择打开方式为"记事本"，输入下面代码

```html
<html>
    <head>
        <title>Jasper</title> 
    </head> 
    <body>木瓜煲鸡脚</body> 
</html>
```

保存文件，双击文件会以计算机默认的浏览器运行，你的第一个网页就制作完成了！

**注意：**

* `<html>`标签它代表当前页面是一个HTML
* `<head>`标签中可以声明HTML页面的相关信息
* `<body>`标签要有开始，有介绍，**成双成对**
* 开始标签与结束标签中的内容是标签的内容，如果没有标签内容，可以让标签自关闭`<br/>`
* 大多数标签它具有属性,属性值要使用引号引起来。
* HTML它本身是不区分大小写的。

#### 1.3 下载安装VSCode

记事本编写网页不方便，所以推荐一款编写网页的神器！

##### 1.3.1 下载地址

https://code.visualstudio.com/

##### 1.3.2 初始中文设置

安装vscode之后，在界面的左侧，选择安装中文插件。

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404102602.png)

点击安装后，右下角会弹出重启的窗口

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404102657.png)

重启就可以了

##### 1.3.3 创建项目

vscode本身没有新建项目的选项，所以要先创建一个空的文件夹，如: vscode目录

然后打开vscode软件，在vscode软件中选择 File -> Open Folder 打开刚才创建的文件夹

右键目录，创建子目录my-html，在my-html目录下创建文件，输入Test1.html

在代码编辑区，最上面输入！回车，就是见证奇迹的时刻

##### 1.3.4  运行页面

需要安装插件 “open in browser ”

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404102858.png)

安装完插件，右键想要运行的页面文件，或者 使用快捷键 alt+b

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404102926.png)

这里推荐使用chrome谷歌浏览器，精简，速度快。

先安装好chrome浏览器

修改默认的浏览器：首页的设置

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404103013.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404103032.png)

添加代码

```json
"open-in-browser.default":"chrome"
```

### 2. HTML的使用

#### 2.1 文件标签

`<html>` 标签： 代表当前书写的是一个HTML文档

`<head> `标签：存储的本页面的一些重要的信息，它不会显示

`<head>` 标签：有一个子标签`<title>`它是用于定义页面的标题的

`<body>` 标签：书写的内容会显示出来

`<body>`标签的属性：

* text 用于设置文字颜色
* bgcolor 用于设置页面的背景色
* background 用于设置页面的背景图片

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404103843.png)

#### 2.2 排版标签

##### 2.2.1 HTML注释

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404104219.png)

#####  2.2.2换行标签

`<br/>` 标签就是一个换行（回车）功能标签，标签中的 / 可有可无的。

有/是html语言的标准化，但是html语言是一门不那么严谨的语言

##### 2.2.3 段落标签

在`<p>`标签中的内容,会在开始与结束之间产生一个空白行，并且它会自动换行.

常用属性 `align` 它的作用是设置段落中的内容对齐方式 可取值有 `left` `right` `center`

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404104750.png)

##### 2.2.4 水平线标签

`<hr>`标签会在页面上产生一个水平线

对于`hr`标签它有常用属性:

* align：可取值有`left` `right` `center` 代表水平线位置

* size：代表水平线厚度（粗细）

* width：代表水平线宽度

* color：水平线的颜色

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404105621.png)

单位：size="5"，5是默认的单位，为"像素"/"像素点"，像素就是构成计算机图片的最小单位！也可以使用百分比，size="50%"

##### 2.2.5 分区标签

div是一个**块**标签，用来进行布局的

普通的div并没有什么效果，肉眼也看不见，但div与CSS结合，就会更好对页面进行排版

div与span都是“容器”的作用，具体区别:

* div会自动换行，我们也叫这样的标签为块级元素

* span标签它不会自动换行，我们也叫它为行内元素

* div：整体划分区块

* span：局部划分

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210404110045902.png)

两个div的区别是显而易见的，后面我们再具体学习css语法

#### 2.3 字体标签

##### 2.3.1 字体标签

`<font>`标签可以设置字体，字的大小及颜色，常用属性：

* face:用于设置字体，例如 宋体 隶书 楷体

* size:用于设置字的大小（大小默认设置1-7，7最大，想更大，往后学css即可）

* color:用于设置字的颜色

注：

我们所看到的屏幕上所有的颜色都是由红、绿、蓝这三种基色调混合而成的。

每一种颜色的饱和度和透明度都是可以变化的，用0～255的数值来表示。如纯红色表示为（255，0， 0），十六进制表示为#FF0000。

按这种表达方式，理论上我们可以得到256 * 256 * 256 = 16777216种颜色。

* 使用十六进制方式，取值范围 #000000 ~ #FFFFFF （黑色到白色）。当颜色值为#cc3300 时，可简化成 #c30 这种方式

  ```html
  <body bgcolor="#666">
  ```
  
* RGB颜色表示法：RGB（x,y,z）。x、y、z是0 ～ 255之间的整数。rgb字母大小写无所谓

  ```html
  <body bgcolor="rgb(11,11,11)">
  ```

（在线颜色选择器： http://www.86y.org/code/colorpicker/color.html ）

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404112232.png)

查看电脑已有的字体才能显示，这里使用仿宋

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404112127.png)

也可下载别的，百度搜索：字魂网，下载一个免费的字体，并安装到计算机中。在我们的网页中显示出来！

##### 2.3.2 标题标记

给一段文字起一个标题

`<h1> ----- <h6>`

h1最大 h6最小，它们代表的是标题（和word文档是一样的）

自动换行，字体加粗，标题与标题之间产生一定的距离

注意:在HTML中允许标签进行嵌套的，但是一般都包裹嵌套，而不可以进行交叉嵌套

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404112805.png)

##### 2.3.3 格式化标签

* `<br>`：字体加粗

* `<i>`：字体倾斜

* `<del>`：删除线

* `<u>`：下划线

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404113707.png)

#### 2.4 列表标记

* **ol：有序列表**
  * type='A'：字母排序
  * type='I'：罗马排序
  * start=“3” 序列从几开始

* **ul：无序列表**
  * type="disc"：默认，实心圆
  * type="square"：方块
  * type="circle"：空心圆

* **li：列表项**

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210404114202855.png)

#### 2.5 图像标签

`<img>`它可以让我们在网页引入一张图片，常用属性：

* src 代表的图片的路径

* width 图片的宽度

* height 图片的高度

* border 用于设置图片的边框

* alt 如果图片不可以显示时，默认显示的文本信息

* title鼠标悬停图片上，默认显示的文本信息

* align 图片附件文字的对齐方式，可取值有
  * left：把图像对齐到左边
  * right：把图像对齐到右边

  * middle：把图像与中央对齐
  * top：把图像与顶部对齐
  * bottom：把图像与底部对齐（默认）

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/Snipaste_2021-04-04_11-55-42.png)

#### 2.6 超链接标签

`<a>`标签,可以实现跳转到其它页面操作.

超链接内容不仅可以是文本，也可以是图片等信息

常用属性: 

* href 代表的我们要跳转的路径
* target 这个属性规定在何处打开这个链接文档，可取值：
* _blank在**新窗口**中打开页面
* _self 默认。在**本窗口**打开页面

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/1.png)

功能性连接：

1.发邮件

```html
<a href="mailto:1839646816@qq.com">联系站长</a>
```

2.QQ聊天窗口

```html
<a href="tencent://message/?uin=1839646816&Menu=yes"> 
    <img border="0" src="http://wpa.qq.com/pa?p=1:615050000:7"/> 
</a>
```

#### 2.7 表格

`<table>`：定义一个表格

* `border`：边框，取值是像素为单位

* `width` 代表的表格的宽度

* `align` 代表表格的对齐方式；取值
  * `left` 左对齐表格
  * `right` 右对齐表格
  * `center` 居中对齐表格

* cellspacing：单元格间距（通常设置0表示单线表格）

`<tr>`：表格中的行 （Table Row）

* align 代表表格的对齐方式；取值

`<td>`：表格中的数据单元格 （Table DataCell）

* colspan 指示列的合并

* rowspan 指示行的合并

<table border="1" width="400px" align="center" cellspacing="0"> 
<tr align="center"> 
<td colspan="3">计划统计表</td> 
</tr> 
<tr>
<td rowspan="3">收入金额</td> 
<td>5</td> 
<td>6</td> 
</tr> 
<tr>
<td>8</td> 
<td>9</td> 
</tr> 
<tr>
<td>11</td> 
<td>12</td> 
</tr> 
</table>

```html
<table border="1" width="400px" align="center" cellspacing="0"> 
    <tr align="center"> 
        <td colspan="3">计划统计表</td>
    </tr> 
    
    <tr>
        <td rowspan="3">收入金额</td> 
        <td>5</td> 
        <td>6</td> 
    </tr> 
    
    <tr>
    	<td>8</td> 
    	<td>9</td> 
    </tr> 
    
    <tr>
        <td>11</td> 
        <td>12</td> 
    </tr> 
</table>
```

#### 2.8 表单标签

表单可以让我们将录入信息携带到服务器端。

简单说，通过表单可以将要提交的数据提交到指定的位置。

但一个一个的提交，不方便。表单正好解决了这个问题，将所有的数据形成一个整体，一起提交给服务器。

常见的 登录页面、注册页面 都离不开表单的应用

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404124024.png)



##### 2.8.1 form属性

action：整个表单提交的目的地

method：表单提交的方式

* get：提交时，传输数据量少（传递普通文字信息，传递照片会失败），明文提交（在浏览器的url后面会显示提交的数据，不适合用于登录）

* post：提交时，传输数据量大（传递文字和图片都行），密文提交（浏览器的url后面看不到提交的数据）

##### 2.8.2 表单中的元素（空间）

`<input>`元素的type属性

* text：默认值，普通的文本输入框
  * placeholder属性：提示文本
  * maxlength属性：最多能输入字符数量
* password：密码输入框
* checkbox：多选框/复选框
  * checked：被选中
* radio：单选按钮
* file：上传文件
* reset：重置按钮
* submit：提交按钮
* button：普通按钮

`<select>`：下拉列表/下拉框

* `<option>`：列表中的项
  * selected：被选中

`<textarea>`：文本域（多行文本框）

* 可以通过 cols 和 rows 属性来规定 textarea 的尺寸，不过更好的办法是使用 CSS 的height和 width 属性。

`<button>`：按钮

* 在form表单中，作用和submit一样
* 不在form表单中，就是普通按钮（配合后期的javascript，可扩展性更高）

```html
<form action="baidu" method="GET"> 
    <p>帐号：<input name="a" type="text" placeholder="请输入帐号..." maxlength="5" ></p> 
    <p>
        密码：
        <input name="b" type="password">
    </p> 
    <p>
        爱好： 
        <input name="hobby" type="checkbox"> 抽烟 
        <input name="hobby" type="checkbox" checked="checked"> 喝酒 
        <input name="hobby" type="checkbox"> 烫头 
        <input name="hobby" type="checkbox"> 泡澡 
    </p> 
    <p>
        性别： 
        <input type="radio" name="sex"> 男 
        <input type="radio" name="sex" checked="checked"> 女 
    </p> 
    <p>
        身份： 
        <input type="radio" name="role"> ceo
        <input type="radio" name="role"> cto 
        <input type="radio" name="role"> coo 
        <input type="radio" name="role" checked="checked"> ufo 
    </p> 
    <p>
        头像： 
        <input type="file"> 
    </p> 
    <p>
        血型： 
        <select> 
            <option>A型</option> 
            <option>B型</option> 
            <option>C型</option> 
            <option selected="selected">O型</option> 
        </select> 
    </p> 
    <p>
        个人简介： 
        <textarea cols="10" rows="5"></textarea> 
    </p> 
    <p> 
        <input type="reset" value="清空"> 
        <input type="submit" value="提交"> 
        <input type="button" value="取消"> 
        <button>保存</button> 
    </p> 
</form> 
<button>测试</button>
```

注意事项：

* 所有表单中的元素都要具有名称（否则提交到服务器之后，服务器无法区识别多个元素之间的不同）

* 单选框要想可以一次只选择一个，要具有相同的name值 

* 所有的复选框以组为单位，组内的每个复选框都应该具有相同的name值

#### 2.9 框架标签

通过`<frameset>`和`<frame>`框架标签可以定制HTML页面布局。可以理解为：用多个页面拼装成一个页面。

注意：框架标签和body标签不共存。“有你没我，有我没你”

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404125757.png)

```html
<!DOCTYPE html> 
<html lang="en"> 
    <head>
        <meta charset="UTF-8"> 
        <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
        <meta http-equiv="X-UA-Compatible" content="ie=edge"> 
        <title>Document</title> 
    </head> 
    
    <frameset rows="10%,*,13%">
        
        <frame src="top.html"></frame>
        
    	<frameset cols="15%,*">
    		<frame src="left.html"></frame>
    		<frame src="right.html"></frime>
    	</frameset>
    
    	<frame src="foot.html"></frame>	
    
    </frameset>
</html>
```

top.html、left.html、right.html、foot.html 四个页面内容一样，稍微改下文字而已，以top.html为例

```html
<!DOCTYPE html> 
<html lang="en"> 
    <head>
        <meta charset="UTF-8"> 
        <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
        <meta http-equiv="X-UA-Compatible" content="ie=edge"> 
        <title></title> 
    </head> 
    <body>
        <h1>顶部导航区域</h1> 
    </body> 
</html>
```

#### 2.10 其他标签与特殊字符

##### 2.10.1 `<meta>`标签

`<meta>`标签必须写在`<head>`标签之间. 

```html
<meta charset="UTF-8"> 
<meta name="viewport" content="width=device-width, initial-scale=1.0"> 
<meta http-equiv="X-UA-Compatible" content="ie=edge">
```

* 当前页面的字符编码 gbk：中文简体
* 这里 的 名字 是 viewport （显示窗口）数据 是 文本 内容 content="width=device-width, initial-scale=1.0"也就是 显示窗口 宽度 是 客户端的 屏幕 宽度 （就是 满屏 ！），显示的文字和图形的初始比例 是1.0
* 每个电脑内置的IE版本是不一样的，为了兼容所有的版本以最高级模式渲染文档，也就是任何IE版本都以当前版本所支持的最高级标准模式渲染

```html
<meta http-equiv="refresh" content="5; url=http://yournotes.cn">
```

注意：在html中如果跳转的互联网上的网站资源，那么我们在书写路径时，一定要带协议的路径。

##### 2.10.2 `<link>`标签

后面我们会使用link标签来导入css

注意:link标签也必须写在`<head>`标签中。

##### 2.10.3 特殊字符

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404134411.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404134428.png)

### 3. HTML5 新特性

#### 3.1 HTML5与HTML4的区别

H5包含H4

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404134555.png)

1.大小写不敏感

* 标签
* 属性
* 属性的值

```html
<inPUT tYPe="pasSWord"/>
```

2.引号可以省略

```html
<input type="password"> 
<input type=password>
```

3.结尾标签也可省略

```html
<p>哈哈哈哈哈哈哈 
<p>哈哈哈哈哈哈哈
```

#### 3.2 新增语义化标签

新增许多语义化标签，让div“见名知意”

* section标签：表示页面中的内容区域，部分，页面的主体部分

* article标签：文章

* aside标签：文章内容之外的，标题

* header标签：头部，页眉，页面的顶部

* hgroup标签：内容与标题的组合

* nav标签：导航

* figure标签：图文并茂

* foot：页脚，页面的底部

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210404135030.png)



#### 3.3 媒体标签

想在网页上播放视频，就要使用`<video>`，属性有：

* src：媒体资源文件的位置

* controls：控制面板

* autoplay：自动播放（谷歌失效，360浏览器可以）

* loop：循环播放

```html
<video src="img/html-css-js之间的关系.mp4" controls loop autoplay></video>
```

#### 3.4 新增表单空间

表单的控件更加丰富了

* `<input>` 增加了type属性：
  * color：调色板
  * date：日历
  * month：月历
  * week：周历
  * number：数值域
    * min：最小值（默认值是1）
    * max：最大值（默认值无上限）
    * step：递增量
  * range：滑块
  * search：搜索框（带×号，可一键删除框中内容）

* 进度条`<progress/>`

* 高亮`<mark>`

* 联想输入框`<datalist>` （模糊查询）
  * 选项`<option>`