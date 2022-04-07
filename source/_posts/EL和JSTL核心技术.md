---
title: EL和JSTL核心技术
date: 2020-01-11 10:54:11
tags: 总结文档
copyright: false
---

## 1. EL表达式（熟悉）

### 1.1 基本概念

* EL（Expression Language）表达式提供了在JSP中简化表达式的方法，可以方便地访问各种数据并输出。

### 1.2 主要功能

* 依次访问pageContext、request、session和application作用域对象存储的数据。
* 获取请求参数值。
* 访问Bean对象的属性。
* 访问集合中的数据。
* 输出简单的运算结果。

### 1.3 访问内置对象的数据

**（1）访问方式**

- <%=request.getAttribute(“ varName”)%>
- 用EL实现: ${ varName }

**（2）执行流程**

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210411110437.png)

### 1.4 访问请求参数的数据

* 在EL之前使用下列方式访问请求参数的数据
  * request.getParameter(name);
  * request.getParameterValues(name);

* 在EL中使用下列方式访问请求参数的数据
  * param：接收的参数只有一个值。
  * paramValues：接受的参数有多个值。

```jsp
<!-- 获取指定参数的数值 --> 
${param.name} 
<!-- 获取指定参数中指定下标的数值 --> 
${paramValues.hobby[0]}
```



### 1.5 访问Bean对象的熟悉

**（1）访问方式**

* 方式一： $ { 对象名 . 属性名 }，例如：`${user.name}`
* 方式二： $ { 对象名 [“属性名”] }，例如：`${user["name"]}`

**（2）主要区别**

* 当要存取的属性名中包含一些特殊字符，如： . 或 ,等并非字母或数字的符号，就一定要使用 [ ]而不是.的方式
* 使用[]的方式可以动态取值，具体方式如下：

```jsp
<% request.setAttribute(“prop”,“age”); %>
<!-- 相当于表达式中写一个变量 --> 
${ user[prop] }
```



### 1.6 访问集合中的数据

```jsp
<!-- student为ArrayList类型的对象 --> 
${student[0].name}
```



### 1.7 常用的内置对象

<table>
	<tr>
	    <th>类别</th>
	    <th>标识符</th>
	    <th>描述</th>  
	</tr >
	<tr >
	    <td>JSP</td>
	    <td>pageContext</td>
        <td>PageContext 处理当前页面</td>
	</tr>
	<tr>
        <td rowspan="4">作用域</td>
	    <td>pageScope</td>
	    <td>同页面作用域属性名称和值有关的Map类</td>
	</tr>
	<tr>
	    <td>requestScope</td>
	    <td>同请求作用域属性的名称和值有关的Map类</td>
	</tr>
	<tr>
	    <td>sessionScope</td>
	    <td>同会话作用域属性的名称和值有关的Map类</td>
	</tr>
	<tr><td>applicationScope</td>
	    <td>同应用程序作用域属性的名称和值有关的Map类</td>
	</tr>
	<tr>
        <td rowspan="2">请求参数</td>
	    <td>param</td>
	    <td>根据名称存储请求参数的值的Map类</td>
	</tr>
	<tr>
	    <td>paramValues</td>
	    <td>把请求参数的所有值作为一个String数组来存储的Map类</td>
	</tr>
	<tr>
        <td rowspan="2">请求头</td>
	    <td>header</td>
	    <td>根据名称存储请求头主要值的Map类</td>
	</tr>
	<tr>
	    <td >headerValues</td>
	    <td>把请求头的所有值作为一个String数组来存储的Map类</td>
	</tr>
	<tr>
	    <td >Cookie</td>
	    <td>cookie</td>
	    <td>根据名称存储请求附带的cookie的Map类</td>
	</tr>
	<tr>
	    <td >初始化参数</td>
	    <td >initParam</td>
	    <td >根据名称存储Web应用程序上下文初始化参数的Map类</td>
	</tr>
</table>

### 1.8 常用的运算符

**（1）常用的算术运算符**

<table>
    <tr>
		<th>算术运算符</th>
        <th>说 明</th>
        <th>范 例</th>
        <th>运算结果</th>
    </tr>
    <tr>
		<td>+</td>
        <td>加</td>
        <td>${1+2}</td>
        <td>3</td>
    </tr>
    <tr>
		<td>－</td>
        <td>减</td>
        <td>${2-1}</td>
        <td>1</td>
    </tr>
    <tr>
		<td>*</td>
        <td>乘</td>
        <td>${2*3}</td>
        <td>6</td>
    </tr>
    <tr>
		<td>/ 或 div</td>
        <td>除</td>
        <td>${16/5}或${16div5}</td>
        <td>3.2</td>
    </tr>
    <tr>
		<td>% 或 mod</td>
        <td>取余</td>
        <td>${16%5}或${16mod5}</td>
        <td>1</td>
    </tr>  
</table>



**（2）常用的关系运算符**

<table>
    <tr>
		<th>关系运算符</th>
        <th>说 明</th>
        <th>范 例</th>
        <th>运算结果</th>
    </tr>
    <tr>
		<td>==或eq</td>
        <td>等于</td>
        <td>${1==2}或${1 eq 2}</td>
        <td>false</td>
    </tr>
    <tr>
		<td>!= 或ne</td>
        <td>不等于</td>
        <td>${2!=1}或${1 ne 2}</td>
        <td>true</td>
    </tr>
    <tr>
		<td>< 或lt</td>
        <td>小于</td>
        <td>${2<3}或${2 lt 3 }</td>
        <td>true</td>
    </tr>
    <tr>
		<td>> 或 gt</td>
        <td>大于</td>
        <td>${16>5}或${16 gt 5}</td>
        <td>true</td>
    </tr>
    <tr>
		<td><= 或 le</td>
        <td>小于等于</td>
        <td>${16<=5}或${16 le 5}</td>
        <td>false</td>
    </tr>  
    <tr>
		<td>>= 或 ge</td>
        <td>大于等于</td>
        <td>${16>=5}或${16 ge 5}</td>
        <td>true</td>
    </tr> 
</table>
**（3）常用的逻辑运算符**

<table>
    <tr>
		<th>逻辑运算符</th>
        <th>说 明</th>
        <th>范 例</th>
        <th>运算结果</th>
    </tr>
    <tr>
		<td>&& 或 and</td>
        <td>与运算</td>
        <td>${true&&true}或${true and true}</td>
        <td>true</td>
    </tr>
    <tr>
		<td>|| 或or</td>
        <td>或运算</td>
        <td>${true||false}或${true or false}</td>
        <td>true</td>
    </tr>
    <tr>
		<td>! 或not</td>
        <td>非运算</td>
        <td>${!true}或${not true }</td>
        <td>false</td>
    </tr>
</table>
**（4）条件运算符**

```jsp
${条件表达式? 语句1 : 语句2}
```

**（5）验证运算符**

```jsp
${empty 表达式} 
返回布尔值判断表达式是否为"空"值，null值、无元素的集合或数组、长度为零的String被认为是空值。
```



## 2. JSTL标签（熟悉）

### 2.1 基本概念

- JSTL( JSP Standard Tag Library ) 被称为JSP标准标签库。
- 开发人员可以利用这些标签取代JSP页面上的Java代码，从而提高程序的可读性，降低程序的维护难度。

### 2.2 使用方式
* 下载JSTL的jar包并添加到项目中，下载地址为：https://tomcat.apache.org/download/taglibs.cgi
* 在JSP页面中使用taglib指定引入jstl标签库，方式为：

```jsp
<!-- prefix属性用于指定库前缀 --> 
<!-- uri属性用于指定库的标识 --> 
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %
```



### 2.3 常用核心标签

**（1）输出标签**

```jsp
<c:out></c:out> 用来将指定内容输出的标签
```

**（2）设置标签**

```jsp
<c:set></c:set> 用来设置属性范围值的标签
```

**（3）删除标签**

```jsp
<c:remove></c:remove> 用来删除指定数据的标签
```

**（4）单条件标签**

```jsp
<c:if test =“EL条件表达式”> 
    满足条件执行 
</c:if >
```

**（5）多条件判断标签**

```jsp
<c:choose > 
    <c:when test =“EL表达式”> 
    满足条件执行 
    </c:when> …
    <c:otherwise> 
        不满足上述when条件时执行 
    </c:otherwise> 
</c:choose >
```

**（6）循环标签**

```jsp
<c:forEach var=“循环变量” items=“集合”> 
    … 
</c:forEach>
```

### 2.4 常用函数标签

```jsp
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```

### 2.5 常用格式化标签

```jsp
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
```

### 2.6 自定义标签

* 如果上面几个标签不能满足需求，程序员也可以自定义标签，步骤如下：
* 编写标签类继承SimpleTagSupport类或TagSupport类并重写doTag方法或doStartTag方法。

```java
public class HelloTag extends SimpleTagSupport { 
    private String name; 
    public String getName() { 
        return name; 
    }
    public void setName(String name) { 
        this.name = name; 
    }
    @Override public void doTag() throws JspException, IOException {
        JspWriter out = this.getJspContext().getOut(); 
        out.println("自定义标签的参数为：" + name); 
    } 
}
```

* 定义标签库文件（tld标签库文件）并配置标签说明文件到到WEB-INF下：

```jsp
<tag>
    <name>helloTag</name> 
    <tag-class>com.hao.tag.HelloTag</tag-class> 
    <body-content>empty</body-content> 
    <attribute> 
        <name>name</name> 
        <required>true</required> 
    </attribute> 
</tag>
```

* 在JSP中添加taglib指令引入标签库使用：

```jsp
<%@ taglib prefix="hello" uri="/WEB-INF/mytag.tld" %>
```

