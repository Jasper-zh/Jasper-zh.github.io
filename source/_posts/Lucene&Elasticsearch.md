---
title: Elasticsearch详解
date: 2022-03-22 11:58:17
tags: 总结文档
---

{% note warning simple %}
超长警告！！！善用目录！！！
{% endnote %}

[TOC]

# 一、什么是全文检索

## 1.1 数据分类

我们生活中的数据总体分为两种：结构化数据和非结构化数据。

* **结构化数据：**指具有固定格式或有限长度的数据，如数据库，元数据等。

* **非结构化数据：**指不定长或无固定格式的数据，如邮件，word 文档等磁盘上的文件

## 1.2 结构化数据搜索

常见的结构化数据也就是**数据库中的数据**。

在数据库中搜索很容易实现，通常都是使用 sql语句进行查询，而且能很快的得到查询结果

![image-20220316100525323](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316100525323-16481072835511.png)

为什么数据库搜索很容易？

* 因为数据库中的数据存储是有规律的，有行有列而且数据格式、数据长度都是固定的

## 1.3 非结构化数据查询方法

### 1.3.1 顺序扫描法（Serial Scanning）

用户搜索 → 文件

所谓顺序扫描，比如要找内容包含某一个字符串的文件，就是一个文档一个文档的看，对于每一个文档，从头看到尾，如果此文档包含此字符串，则此文档为我们要找的文件，接着看下一个文件，直到扫描完所有的文件。如利用 windows 的搜索也可以搜索文件内容，只是相当的慢。

![image-20220316101249074](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316101249074.png)

### 1.3.2 全文检索（Full-text Search）

用户通过查询索引库 → 生成索引 → 文档

全文检索是指计算机索引程序通过扫描文章中的每一个词，对每一个词建立一个索引，指明该词在文章中出现的次数和位置，当用户查询时，检索程序就根据事先建立的索引进行查找，并将查找的结果反馈给用户的检索方法。这个过程类似于通过字典的目录查字的过程。

将非结构化数据中的一部分信息提取出来，重新组织，使其变得有一定结构，然后对此有一定结构的数据进行搜索，从而达到搜索相对较快的目的。这部分从非结构化数据中提取出的然后重新组织的信息，我们称之**索引**。

例如：字典。字典的拼音表和部首检字表就相当于字典的索引，对每一个字的解释是非结构化的，如果字典没有音节表和部首检字表，在茫茫辞海中找一个字只能顺序扫描。然而字的某些信息可以提取出来进行结构化处理，比如读音，就比较结构化，分声母和韵母，分别只有几种可以一一列举，于是将读音拿出来按一定的顺序排列，每一项读音都指向此字的详细解释的页数。我们搜索时按结构化的拼音搜到读音，然后按其指向的页数，便可找到我们的非结构化数据——也即对字的解释。

这种先建立索引，再对索引进行搜索的过程就叫**全文检索(Full-Text Search)** 。虽然创建索引的过程也是非常耗时的，但是索引一旦创建就可以多次使用，全文检索主要处理的是查询，所以耗时间创建索引是值得的。

建立索引

检索索引

## 1.4 如何实现全文索引

可以使用 **Lucene** 实现全文检索。Lucene 是 apache 下的一个开放源代码的全文检索引擎工具包。提供了完整的查询引擎和索引引擎，部分文本分析引擎（英文与德文两种西方语言）。Lucene 的目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能。

**Lucene适用场景：**

* 在应用中为数据库中的数据提供全文检索实现。

* 开发独立的搜索引擎服务、系统

**Lucene的特性：**

**1）稳定、索引性能高**

* 每小时能够索引150GB以上的数据
* 对内存的要求小，只需要1MB的堆内存
* 增量索引和批量索引一样快
* 索引的大小约为索引文本大小的20%~30%

**2）高效、准确、高性能的搜索算法**

* 良好的搜索排序
* 强大的查询方式支持：短语查询、通配符查询、临近查询、范围查询等
* 支持字段搜索（如标题、作者、内容）
* 可根据任意字段排序
* 支持多个索引查询结果合并
* 支持更新操作和查询操作同时进行
* 支持高亮、join、分组结果功能
* 速度快
* 可扩展排序模块，内置包含向量空间模型、BM25模型可选
* 可配置存储引擎

**3）跨平台**

* 纯java编写
* 作为Apache开源许可下的开源项目，你可以在商业或开源项目中使用
* Lucene有多种语言实现版（如C，C++、Python等），不仅仅是JAVA



**Lucene架构：**

![image-20220316102654969](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316102654969-16481073622166.png)



![image-20220316102659981](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316102659981-164810743086511.png)

![](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316102705798.png)



## 1.5 全文检索的应用场景

对于数据量大、数据结构不固定的数据可采用全文检索方式搜索，
* 单机软件的搜索：word、markdown
* 站内搜索：京东、淘宝、拉勾，索引源是数据库
* 搜索引擎：百度、Google，索引源是爬虫程序抓取的数据



# 二、Lucence实现全文检索的流程说明

## 2.1 索引和搜索流程图

![](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316103027700.png)

1、绿色表示索引过程，对要搜索的原始内容进行索引构建一个索引库，索引过程包括：
	确定原始内容即要搜索的内容-->采集文档-->创建文档-->分析文档-->索引文档

2、红色表示搜索过程，从索引库中搜索内容，搜索过程包括：
	用户通过搜索界面-->创建查询-->执行搜索，从索引库搜索-->渲染搜索结果



## 2.2 创建索引

### 2.2.1 核心概念

**Document：**

* 用户提供的源是一条条记录，它们可以是文本文件、字符串或者数据库表的一条记录等等。一条记录经过索引之后，就是以一个Document的形式存储在索引文件中的。用户进行搜索，也是以Document列表的形式返回。

**Field：**

* 一个Document可以包含多个信息域，例如一篇文章可以包含“标题”、“正文”、“最后修改时间”等信息域，这些信息域就是通过Field在Document中存储的。

* Field有两个属性可选：存储和索引。通过存储属性你可以控制是否对这个Field进行存储；通过索引属性你可以控制是否对该Field进行索引。

* 如果对标题和正文进行全文搜索，所以我们要把索引属性设置为真，同时我们希望能直接从搜索结果中提取文章标题，所以我们把标题域的存储属性设置为真，但是由于正文域太大了，我们为了缩小索引文件大小，将正文域的存储属性设置为假，当需要时再直接读取文件；我们只是希望能从搜索解果中提取最后修改时间，不需要对它进行搜索，所以我们把最后修改时间域的存储属性设置为真，索引属性设置为假。上面的三个域涵盖了两个属性的三种组合，还有一种全为假的没有用到，事实上Field不允许你那么设置，因为既不存储又不索引的域是没有意义的。

**Term：** 

* Term是搜索的最小单位，它表示文档的一个词语，Term由两部分组成：它表示的词语和这个词语所出现的Field的名称。

### 2.2.2 大致步骤

以我的博客网站 **木瓜煲鸡脚‘blog** 的搜索为例，在网站上输入关键字搜索显示的内容不是直接从数据库中来的，而是从索引库中获取的，网站的索引数据需要提前创建的。以下是创建的过程：

**第一步**：获得原始文档：就是从mysql数据库中通过sql语句查询需要创建索引的数据

**第二步**：创建文档对象（Document），把查询的内容构建成lucene能识别的Document对象，获取原始内容的目的是为了索引，在索引前需要将原始内容创建成文档，文档中包括一个一个的域（Field），这个域对应就是表中的列。 

注意：每个 Document 可以有多个 Field，不同的 Document 可以有不同的 Field，同一个Document可以有相同的 Field（域名和域值都相同）。每个文档都有一个唯一的编号，就是文档 id。

**第三步**：分析文档

将原始内容创建为包含域（Field）的文档（document），**需要再对域中的内容进行分析**，分析的过程是经过对原始文档提取单词、将字母转为小写、去除标点符号、去除停用词等过程生成最终的**语汇单元**，可以将语汇单元理解为一个一个的单词。

```
木瓜煲鸡脚’blogs  -》 木瓜
                    鸡脚
                    blogs
```

分好的词会组成索引库中最小的单元：term，一个term由域名和词组成

**第四步：**创建索引

对所有文档分析得出的语汇单元进行索引，索引的目的是为了搜索，最终要实现只搜索被索引的语汇单元从而找到 Document（文档）。

`注意：**创建索引是对语汇单元索引**，通过词语找文档，这种索引的结构叫 **倒排索引结构**。倒排索引结构是根据内容（词语）找文档，倒排索引结构也叫反向索引结构，包括索引和文档两部分，索引即词汇表，它的规模较小，而文档集合较大。`



## 2.3 倒排索引

倒排索引记录每个词条出现在哪些文档，及在文档中的位置，可以根据词条快速定位到包含这个词条的文档及出现的位置。

文档：索引库中的每一条原始数据，例如一个商品信息、一个职位信息

词条：原始数据按照分词算法进行分词，得到的每一个词

创建倒排索引，分为以下几步：

**1）创建文档列表**

lucene首先对原始文档数据进行编号（DocID），形成列表，就是一个文档列表

![image-20220316105029246](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316105029246.png)

**2）创建倒排索引列表**

对文档中数据进行分词，得到词条（分词后的一个又一个词）。对词条进行编号，以词条创建索引。然后记录下包含该词条的所有文档编号（及其它信息）。

![image-20220316105112156](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316105112156.png)

搜索的过程：

当用户输入任意的词条时，首先对用户输入的数据进行分词，得到用户要搜索的所有词条，然后拿着这些词条去倒排索引列表中进行匹配。找到这些词条就能找到包含这些词条的所有文档的编号。然后根据这些编号去文档列表中找到文档



## 2.4 查询条件

查询索引也是搜索的过程。搜索就是用户输入关键字，从索引（index）中进行搜索的过程。根据关键字搜索索引，根据索引找到对应的文档

第一步：创建用户接口（用户输入关键字的地方）

第二步：创建查询 指定查询的域名和关键字

第三步：执行查询

第四步：渲染结果 （结果内容显示到页面上 关键字需要高亮）

![image-20220316111709233](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316111709233.png)



# 三、Lucence实战

## 3.1 需求说明

生成职位信息索引库，从索引库检索数据

基础数据：创建数据库导入job-info.sql

https://www.aliyundrive.com/s/wtZQfJYqYHi



## 3.2 准备开发环境

**1）引入依赖**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.hao</groupId>
    <artifactId>ES</artifactId>
    <version>1.0</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
    </parent>

    <dependencies>
        <!--web模块-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--测试模块-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.4</version>
            <scope>provided</scope>
        </dependency>
        <!--mybatis plus-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.3.2</version>
        </dependency>
        <!--pojo持久化-->
        <dependency>
            <groupId>javax.persistence</groupId>
            <artifactId>javax.persistence-api</artifactId>
            <version>2.2</version>
        </dependency>
        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.27</version>
        </dependency>
        <!--Lucene核心包以及分词包-->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-core</artifactId>
            <version>4.10.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-analyzers-common</artifactId>
            <version>4.10.3</version>
        </dependency>
    </dependencies>

</project>
```

**2）application配置**

```yaml
server:
  port: 8080

spring:
  application:
    name: es
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://xxx.xxx.xxx:3306/es?useUnicode=true&characterEncoding=utf8&serverTimezone=UTC
    username: root
    password: 1234

mybatis-plus:
  configuration:
    map-underscore-to-camel-case: true
```

**3）创建实体类**

```java
import lombok.Data;

import javax.persistence.Id;
import javax.persistence.Table;

/**
 * @author hao
 * @date 2022/3/16 12:01
 */
@Data
@Table(name = "job_info")
public class JobInfo {
    @Id
    private long id;
    private String companyName;
    private String companyAddr;
    private String companyInfo;
    private String jobName;
    private String jobAddr;
    private String jobInfo;
    private long salaryMin;
    private long salaryMax;
    private String url;
    private String time;
}
```

**4）创建mapper**

```java
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.hao.pojo.JobInfo;
import org.apache.ibatis.annotations.Mapper;

/**
 * @author hao
 * @date 2022/3/16 12:02
 */
@Mapper
public interface JobInfoMapper extends BaseMapper<JobInfo> {
}
```

**5）创建service**

```java
@Service
public class JobInfoServiceImpl implements JobInfoService {
    @Autowired
    JobInfoMapper jobInfoMapper;

    @Override
    public JobInfo selectById(Long id) {
        return jobInfoMapper.selectById(id);
    }

    @Override
    public List<JobInfo> selectAll() {
        QueryWrapper<JobInfo> query = new QueryWrapper<>();
        return jobInfoMapper.selectList(query);
    }
}
```

**6）进行测试**

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = EsApplication.class)
public class TestApplication {
    @Autowired
    JobInfoService jobInfoService;

    @Test
    public void test(){
        List<JobInfo> jobInfos = jobInfoService.selectAll();
        System.out.println(jobInfos);
    }
}
```

![image-20220316132527196](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316132527196.png)



## 3.3 创建索引

在单元测试当中，进行Lucene的使用测试

```java
@Test
public void testCreateIndex() throws Exception {
    // 1、指定索引文件存储的位置 D:\class\index
    Directory directory = FSDirectory.open(new File("D:\\class\\index"));
    // 2、配置版本和分词器
    Analyzer analyzer = new StandardAnalyzer();
    //标准分词器
    IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, analyzer);
    // 3、创建一个用来创建索引的对象
    IndexWriter indexWriter = new IndexWriter(directory, config);
    indexWriter.deleteAll(); //先删除索引
    // 4、获取原始数据
    List<JobInfo> jobInfoList = jobInfoService.selectAll();
    // 有多少的数据就应该构建多少lucene的文档对象document
    for (JobInfo jobInfo : jobInfoList) {
        Document document = new Document();
        // 域名 值 源数据是否存储
        document.add(new LongField("id", jobInfo.getId(), Field.Store.YES));
        document.add(new TextField("companyName", jobInfo.getCompanyName(), Field.Store.YES));
        document.add(new TextField("companyAddr", jobInfo.getCompanyAddr(), Field.Store.YES));
        document.add(new TextField("jobName", jobInfo.getJobName(), Field.Store.YES));
        document.add(new TextField("jobAddr", jobInfo.getJobAddr(), Field.Store.YES));
        document.add(new LongField("salary", jobInfo.getSalaryMax(), Field.Store.YES));
        document.add(new StringField("url", jobInfo.getUrl(), Field.Store.YES));
        // StringField 不需要分词时使用 举例：url 、电话号码、身份证号
        indexWriter.addDocument(document);
    }
    // 关闭资源
    indexWriter.close();
}
```

**生成的索引目录：**`D:\class\index`

* 索引(Index)： 
  * 在Lucene中一个索引是放在一个文件夹中的。  
  * 如下图，同一文件夹中的所有的文件构成一个Lucene索引。

* 段(Segment)：
  * 按层次保存了从索引一直到词的包含关系：索引(Index) –> 段(segment) –> 文档(Document) –> 域(Field) –> 词(Term)
  * 也即此索引包含了那些段，每个段包含了那些文档，每个文档包含了那些域，每个域包含了那些词。
  * 一个索引可以包含多个段，段与段之间是独立的，添加新文档可以生成新的段，不同的段可以合并。
  * 如上图，具有相同前缀文件的属同一个段，图中共一个段 "_0" 。
  * segments.gen和segments_1是段的元数据文件，也即它们保存了段的属性信息。

![image-20220316135140165](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316135140165.png)

**Field的特性：**

Document(文档)是Field(域)的承载体, 一个Document由多个Field组成. Field由名称和值两部分组成,**Field的值是要索引的内容,也是要搜索的内容.**

* 是否分词(tokenized)
  * 是: 将Field的值进行分词处理, **分词的目的是为了索引**. 如: 商品名称, 商品描述. 这些内容用户会通过输入关键词进行查询, 由于内容多样, 需要进行分词处理建立索引. 
  * 否: 不做分词处理. 如: 订单编号, 身份证号, 是一个整体, 分词以后就失去了意义, 故不需要分词.
* 是否索引
  * 是: 将Field内容进行分词处理后得到的词(或整体Field内容)建立索引, 存储到索引域. **索引的目的是为了搜索**. 如: 商品名称, 商品描述需要分词建立索引. 订单编号, 身份证号作为整体建立索引. **只要可能作为用户查询条件的词, 都需要索引.** 
  * 否: 不索引. 如: 商品图片路径, 不会作为查询条件, 不需要建立索引.
* 是否存储
  * 是: 将Field值保存到Document中. 如: 商品名称, 商品价格. **凡是将来在搜索结果页面展现给用户的内容,都需要存储.** 
  * 否: 不存储. 如: 商品描述. 内容多格式大, 不需要直接在搜索结果页面展现, 不做存储. 需要的时候可以从关系数据库取.

**常用的Field类型**

![image-20220316140249457](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316140249457.png)

## 3.4 查询索引

测试查询

```java
@Test
public void testQueryIndex() throws Exception {
    // 1、指定索引文件存储的位置 D:\class\index
    Directory directory = FSDirectory.open(new File("D:\\class\\index"));
    // 2、 创建一个用来加载索引的对象
    IndexReader indexReader = DirectoryReader.open(directory);
    // 3、 创建一个用来查询索引的对象
    IndexSearcher indexSearcher = new IndexSearcher(indexReader);
    // 使用term查询：指定查询的域名对应值的关键字
    Query query = new TermQuery(new Term("companyName", "北京"));
    TopDocs topDocs = indexSearcher.search(query, 100); //第二个参数：最多显示多 少条数据
    int totalHits = topDocs.totalHits;
    //查询的总数量
    System.out.println("符合条件的总数:" + totalHits);
    ScoreDoc[] scoreDocs = topDocs.scoreDocs;
    //获取命中的文档 存储的是文档的id
    for (ScoreDoc scoreDoc : scoreDocs) {
        int docID = scoreDoc.doc; // 根据id查询文档
        Document document = indexSearcher.doc(docID);
        System.out.println("id:" + document.get("id"));
        System.out.println("companyName:" + document.get("companyName"));
        System.out.println("companyAddr:" + document.get("companyAddr"));
        System.out.println("salary:" + document.get("salary"));
        System.out.println("url:" + document.get("url"));
        System.out.println("***********************************************************");
    }
}
```

查看结果你会发现，居然没有数据，如果把查询的关键字“北京”那里改为“北”或“京”就可以，原因是因为中文会一个字一个字的分词（索引里面没有“北京”这个词条），显然是不合适的，所以我们需要使用可以合理分词的分词器，其中最有名的IKAnalyzer分词器



## 3.5 中文分词器的使用

**1）导入依赖**

```xml
<dependency>
    <groupId>com.janeluo</groupId>
    <artifactId>ikanalyzer</artifactId>
    <version>2012_u6</version>
</dependency>
```



**2）添加配置文件**



**3）创建索引时使用IKanalyzer**

![image-20220316142055731](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316142055731.png)

把原来的索引数据删除，将标准分词器换成IK分词器再重新生产索引文件，再使用关键字“北京”就可以查询到结果了

![image-20220316142628993](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316142628993.png)

{% note info simple  %}

考虑一个问题：一个大型网站中的索引数据会很庞大的，所以使用lucene这种原生的写代码的方式就不合适了，所以需要借助一个成熟的项目或软件来实现，目前比较有名是solr和elasticSearch，所以接下来我们学习elasticSearch的使用。

{% endnote %}



# 四、Elasticsearch介绍与安装

Elasticsearch是一个需要安装配置的软件。

**ELK**技术栈说明

Elastic有一条完整的产品线：Elasticsearch、Logstash、Kibana等，前面说的三个就是大家常说的ELK技术栈（开源实时日志分析平台）。

![image-20220316142819109](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316142819109.png)

Logstash 的作用就是一个数据收集器，将各种格式各种渠道的数据通过它收集解析之后格式化输出到Elasticsearch ，最后再由 Kibana 提供的比较友好的 Web 界面进行汇总、分析、搜索。

ELK 内部实际就是个管道结构，数据从 Logstash 到 Elasticsearch 再到 Kibana 做可视化展示。这三个组件各自也可以单独使用，比如 Logstash 不仅可以将数据输出到Elasticsearch ，也可以到数据库、缓存等

## 4.1 简介

### 4.1.1 Elastic

Elastic官网：https://www.elastic.co/cn/

![image-20220316143113179](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316143113179.png)

Elastic公司有一条完整的产品线：Elasticsearch、Logstash、Kibana等，前面说的三个就是大家常说的ELK技术栈。

![image-20220316143130020](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316143130020.png)

### 4.1.2 Elasticsearch

Elasticsearch官网：https://www.elastic.co/cn/products/elasticsearch

![image-20220316143328346](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316143328346.png)

**功能：**

* 分布式的搜索引擎：百度、Google、站内搜索
* 全文检索：提供模糊搜索等自动度很高的查询方式，并进行相关性排名，高亮等功能
* 数据分析引擎（分组聚合）：电商网站—一周内手机销量Top10
* 对海量数据进行近乎实时处理：水平扩展，每秒钟可处理海量事件，同时能够自动管理索引和查询在集群中的分布方式，以实现极其流畅的操作。

如上所述，Elasticsearch具备以下特点：

高速、扩展性、最相关的搜索结果

* 分布式：节点对外表现对等，每个节点都可以作为入门，加入节点自动负载均衡
* JSON：输入输出格式是JSON
* Restful风格，一切API都遵循Rest原则，容易上手
* 近实时搜索，数据更新在Elasticsearch中几乎是完全同步的，数据检索近乎实时
* 安装方便：没有其它依赖，下载后安装很方便，简单修改几个参数就可以搭建集群
* 支持超大数据：可以扩展到PB级别的结构化和非结构化数据

### 4.1.3 版本
我这边直接用比较新的8.0.0
https://www.elastic.co/cn/downloads/past-releases/elasticsearch-8-0-0

![image-20220316143855837](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316143855837.png)



## 4.2 安装和配置

为了快速看到效果我们直接在本地window下安装Elasticsearch。环境要求：JDK11及以上版本

**1）下载解压**

![image-20220316144839161](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316144839161.png)



它这个版本还自带了JDK，如果电脑配置JAVA_HOME的环境变量那就使用JAVA_HOME的。如果没配那就使用自带的。如果有JAVA_HOME 也可以再配置个ES_JAVA_HOME（es优先使用）指向自带这个（自带的JDK版本是最适配的）

**2）修改配置**

* 修改索引数据和日志数据存储的路径

  ![image-20220316144938526](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316144938526.png)

  ![image-20220316145053516](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316145053516.png)

  打开注释 修改索引数据存储路径，以及日志路径

  ![image-20220316145308973](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316145308973.png)

**3）进入bin目录启动bat**

![image-20220316145614613](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316145614613.png)

如果启动的失败的话，很大可能是需要修改虚拟机内存的大小，不过新版本应该不会出现这个问题默认的就可以。`-Xms启动时分配的内存越大相对来说快当然也要结合你自身机器的内存情况，-Xmx运行期间最大也只给它分配的空间，实际超过了就会内存溢出`

到config中找到jvm.options进行修改

![image-20220316150519205](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316150519205.png)





## 4.3 访问

![image-20220316151058887](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316151058887.png)

可以看到绑定了两个端口:

9300：集群节点间通讯接口，接收tcp协议

9200：客户端访问接口，接收Http协议

我们在浏览器中访问：http://127.0.0.1:9200

然后发现一直访问不了localhost:9200，是因为首次启动会开启一些配置。如下是从官网截图，那么我们可以去关掉或者使用https访问。

![image-20220316160945363](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316160945363.png)

```powershell
received plaintext http traffic on an https channel, closing connection Netty4HttpChannel{localAddress=/[0:0:0:0:0:0:0:1]:9200, remoteAddress=/[0:0:0:0:0:0:0:1]:59093}
```

![image-20220316155002908](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316155002908.png)

第二就是elastic的密码在控制台需要找到，然后发现死活找不到（因为只有第一次才会有）。

![image-20220316165241998](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316165241998.png)

访问 https://127.0.0.1:9200 用户名：elastic 密码：ma9Uxd7nRUycKSBxFGbC （控制台上的）

![image-20220316165651719](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316165651719.png)



## 4.4 安装Kibana

### 4.4.1 什么是Kibana

https://www.elastic.co/cn/kibana/

![image-20220316170804295](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316170804295.png)

Kibana是一个基于Node.js的Elasticsearch索引库数据统计工具，可以利用Elasticsearch的聚合功能，生成各种图表，如柱形图，线状图，饼图等。

而且还提供了操作Elasticsearch索引数据的控制台，并且提供了一定的API提示，非常有利于我们学习Elasticsearch的语法。

### 4.4.2 安装

因为Kibana依赖于node，需要在windows下先安装Node.js。我自己的node版本16.13.0

![image-20220316170958366](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316170958366.png)

然后下载kibana，版本和elasticsearch保持一致。我就用8.0.0

https://www.elastic.co/cn/downloads/past-releases#kibana

### 4.4.3 配置运行

进入安装目录下的config目录，配置kibana.yml 当中的 elasticsearch.hosts 为自己的elasticsearch服务地址

![image-20220316172942208](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316172942208.png)

在安装目录bin下，使用kibana.bat启动。

![image-20220316173151894](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316173151894.png)



### 4.4.4 控制台

控制台可见默认端口是5601

去访问看看 http://localhost:5601

![image-20220316173310244](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316173310244.png)

访问进去之后，它是要进行一个与elasticsearch的认证的配置，要输入token，也就是首次启动elasticsearch终端生成的

![image-20220316173419653](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316173419653.png)

填入token确定之后，弹出个验证码。这时这个验证码会在kibana的控制台显示输出，也可以向上面提示的使用 `kibana-verification-code.bat`获取

![image-20220316173611495](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316173611495.png)

![image-20220316174242888](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316174242888.png)

我这边有个是token失效了，所以就通过手动配置登录

![image-20220316181115312](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316181115312.png)

![image-20220316181214276](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316181214276.png)

需要先通过elasticsearch生成一个kibnana_system的密码

```powershell
bin/elasticsearch-reset-password -u kibana_system
```

![image-20220316181403644](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316181403644.png)

可以将这个密码配置到，kibana当中

![image-20220316181543254](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316181543254.png)

填入密码之后就可以配置通过了，然后正式进入到了登录界面，终于可以使用管理员elastic用户进行登录了。

![image-20220316184543654](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316184543654.png)

![image-20220316182835326](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316182835326.png)

这时也可以把elastic的密码改掉了

![image-20220316183546969](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220316183546969.png)



现在这个版本确实和以前界面差距挺大



##  4.5 安装ik分词器

Lucene的IK分词器早在2012年已经没有维护了，现在我们要使用的是在其基础上维护升级的版本，并且开发为Elasticsearch的集成插件了，与Elasticsearch一起维护升级，版本也保持一致，所以我这里下载8.0.0的

https://github.com/medcl/elasticsearch-analysis-ik/releases

下载解压到elasticsearch的plugs当中并重命名为ik

![image-20220317094452296](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317094452296.png)

![image-20220317094511770](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317094511770.png)

重新启动elasticsearch即可

**测试**

先不管这个查询语法，先测试下

在kibana控制台输入下面的请求：

```json
GET /_analyze
{
    "analyzer": "ik_max_word",
    "text": "我是中国人"
}
```

运行得到结果如下：

```json
{
  "tokens" : [
    {
      "token" : "我",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "CN_CHAR",
      "position" : 0
    },
    {
      "token" : "是",
      "start_offset" : 1,
      "end_offset" : 2,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "中国人",
      "start_offset" : 2,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "中国",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "CN_WORD",
      "position" : 3
    },
    {
      "token" : "国人",
      "start_offset" : 3,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 4
    }
  ]
}
```

上面使用了 ik_max_word，对内容的分词是最大化。再测试下 ik_smart

```json
GET /_analyze
{
    "analyzer": "ik_smart",
    "text": "我是中国人"
}
```

结果：

```java
{
  "tokens" : [
    {
      "token" : "我",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "CN_CHAR",
      "position" : 0
    },
    {
      "token" : "是",
      "start_offset" : 1,
      "end_offset" : 2,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "中国人",
      "start_offset" : 2,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 2
    }
  ]
}
```

ik_smart模式是一个智能的识别，不会将语义中的词因为恰好其中的字和词前或者后的字也好像能组成词而去生成这个词条

## 4.6 Head插件

### 4.6.1 elasticsearch-head简介

elasticsearch-head是一个界面化的集群操作和管理工具，可以对集群进行傻瓜式操作。你可以通过插件把它集成到es（首选方式）,也可以安装成一个独立webapp。

es-head主要有三个方面的操作：

* 显示集群的拓扑,并且能够执行索引和节点级别操作
* 搜索接口能够查询集群中原始json或表格格式的检索数据
* 能够快速访问并显示集群的状态

官方的文档： <https://github.com/mobz/elasticsearch-head>

### 4.6.2 安装

这个就不用 多说，谷歌浏览器或者火狐都可以在谷歌插件商店搜索名称安装即可（科学上网）

https://chrome.google.com/webstore/category/extensions

![image-20220317100742395](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317100742395.png)

**界面如下**

![image-20220317101005283](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317101005283.png)

![image-20220317101128771](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317101128771.png)

总之和kibana一样都是作为Elasticsearch的控制面板，不过这个可以更方便的查看集群状态。



# 五、使用Kibana对索引库操作

## 5.1 基本概念

### 5.1.1 节点、集群、分片及副本

**1）节点（node）**

一个节点是一个Elasticsearch的实例

在服务器上启动Elasticsearch之后，就拥有了一个节点。如果在另一台服务器上启动Elasticsearch，这就是另一个节点。甚至可以通过启动多个Elasticsearch进程，在同一台服务器上拥有多个节点。

**2）集群（cluster）**

多个协同工作的Elasticsearch节点的集合被称为集群。

在多节点的集群上，同样的数据可以在多台服务器上传播。这有助于性能。这同样有助于稳定性，如果每个分片至少有一个副本分片，那么任何一个节点宕机后，Elasticsearch依然可以进行服务，返回所有数据。

但是它也有缺点：必须确定节点之间能够足够快速地通信，并且不会产生脑裂效应（集群的2个部分不能彼此交流，都认为对方宕机了）。

**3）分片（shard）**

索引可能会存储大量数据，这些数据可能超过单个节点的硬件限制。例如，十亿个文档的单个索引占用了1TB的磁盘空间，可能不适合单个节点的磁盘，或者可能太慢而无法单独满足来自单个节点的搜索请求。

为了解决此问题，Elasticsearch提供了将索引细分为多个碎片的功能。创建索引时，只需定义所需的分片数量即可。每个分片本身就是一个功能齐全且独立的“索引”，可以托管在群集中的任何节点上。

分片很重要，主要有两个原因：

* 它允许您水平分割/缩放内容量
* 它允许您跨碎片（可能在多个节点上）分布和并行化操作，从而提高性能/吞吐量

分片如何分布以及其文档如何聚合回到搜索请求中的机制完全由Elasticsearch管理，并且对您作为用户是透明的。

在随时可能发生故障的网络/云环境中，非常有用，强烈建议您使用故障转移机制，以防碎片/节点因某种原因脱机或消失。为此，Elasticsearch允许您将索引分片的一个或多个副本制作为所谓的副本分片（简称副本）。

**4）副本（replica）**

分片处理允许用户推送超过单机容量的数据至Elasticsearch集群。副本则解决了访问压力过大时单机无法处理所有请求的问题。

分片可以是主分片，也可以是副本分片，其中副本分片是主分片的完整副本。副本分片用于搜索，或者是在原有的主分片丢失后成为新的主分片。

注意：可以在任何时候改变每个分片的副本分片的数量，因为副本分片总是可以被创建和移除的。这并不适用于索引划分为主分片的数量，在创建索引之前，必须决定主分片的数量。过少的分片将限制可扩展性，但是过多的分片会影响性能。默认设置的5份是一个不错的开始。

### 5.1.2 文档、类型、索引及映射

**1）文档（document）**

Elasticsearch是面向文档的，这意味着索引和搜索数据的最小单位是文档。

在Elasticsearch中文档有几个重要的属性。

* 它是自我包含的。一篇文档同时包含字段和它们的取值。
* 它可以是层次的。文档中还包含新的文档，字段还可以包含其他字段和取值。例如，“location”字段可以同时包含“city”和“street“两个字段。
* 它拥有灵活的结构。文档不依赖于预先定义的模式。并非所有的文档都需要拥有相同的字段，它们不受限于同一个模式。

**2）类型（type）**

类型是文档的逻辑容器，类似于表格是行的容器。在不同的类型中，最好放入不同结构的文档。例如，可以用一个类型定义聚会时的分组，而另一个类型定义人们参加的活动。

**3）索引（index）**

索引是映射类型的容器。一个Elasticsearch索引是独立的大量的文档集合。 每个索引存储在磁盘上的同组文件中，索引存储了所有映射类型的字段，还有一些设置。

**4）映射（mapping）**

所有文档在写入索引前都将被分析，用户可以设置一些参数，决定如何将输入文本分割为词条，哪些词条应该被过滤掉，或哪些附加处理有必要被调用（比如移除HTML标签）。这就是映射扮演的角色：存储分析链所需的所有信息。

Elasticsearch也是基于Lucene的全文检索库，本质也是存储数据，很多概念与MySQL类似的。

对比关系：

<table>
    <tr>
        <th>全文检索</th>
        <th>关系型数据库</th>
    </tr>
    <tr>
        <td>索引库（indices）</td>
        <td>Database 数据库</td>
    </tr>
    <tr>
        <td>类型（type）</td>
        <td>Table 数据表</td>
    </tr>
    <tr>
        <td>文档（document）</td>
        <td>Row 行</td>
    </tr>
    <tr>
        <td>域字段（Field）</td>
        <td>Columns 列</td>
    </tr>
    <tr>
        <td>映射配置（mappings）</td>
        <td>每个列的约束（类型、长度）</td>
    </tr>
</table>

详细说明：

<table>
    <tr>
        <th>概念</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>索引库（indices）</td>
        <td>indices是index的复数，代表许多索引</td>
    </tr>
    <tr>
        <td>索引/类型（type）</td>
        <td>类型是模拟mysql中的table概念，一个索引库下可以有不同类型的索引（目前 6.X以后的版本只能有一个类型），类似数据库中的表概念。数据库表中有表 结构，也就是表中每个字段的约束信息；索引库的类型中对应表结构的叫做映射(mapping) ，用来定义每个字段的约束。</td>
    </tr>
    <tr>
        <td>文档（document）</td>
        <td>存入索引库原始的数据。比如每一条商品信息，就是一个文档</td>
    </tr>
    <tr>
        <td>域字段（Field）</td>
        <td>文档中的属性</td>
    </tr>
    <tr>
        <td>映射配置（mappings）</td>
        <td>字段的数据类型、属性、是否索引、是否存储等特性</td>
    </tr>
</table>



## 5.2 创建索引

### 5.2.1 语法

Elasticsearch采用Rest风格API，因此其API就是一次http请求，你可以用任何工具发起http请求

创建索引的请求格式：

* 请求方式：PUT

* 请求路径：/索引名

* 请求参数：json格式

  ```json
  { 
      "settings": { 
          "属性名": "属性值" 
      } 
  }
  ```

  settings：就是索引库设置，其中可以定义索引库的各种属性，目前我们可以不设置，都走默认。

### 5.2.3 使用Kibana创建

kibana的控制台，可以对http请求进行简化，示例：

![image-20220317105016689](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317105016689.png)

相当于是省去了elasticsearch的服务器地址，而且还有语法提示，非常舒服。

## 5.3 查看索引库

> 语法

Get请求可以帮我们查看索引信息，格式：

```
GET /索引名
```

![image-20220317105438324](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317105438324.png)

## 5.4 删除索引

> 语法

```
DELETE /索引名
```

![image-20220317105708155](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317105708155.png)

再次查看就找不到了

![image-20220317105931939](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317105931939.png)

我们也可以用head插件来发个请求玩下

![image-20220317110202182](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317110202182.png)

这个查询索引的接口是GET请求，我们直接用浏览器都可以发送

![image-20220317110408425](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317110408425.png)



## 六、使用Kibana对类型及映射操作

有了 索引库 ，等于有了数据库中的 database 。接下来就需要索引库中的 类型 了，也就是数据库中的表 。创建数据库表需要设置字段约束，索引库也一样，在创建索引库的类型时，需要知道这个类型下有哪些字段，每个字段有哪些**约束**信息，这就叫做 字段映射(mapping)

注意：Elasticsearch7.x取消了索引type类型的设置，不允许指定类型，默认为_doc，但字段仍然是有的，我们需要设置字段的约束信息，叫做字段映射（mapping）

字段的约束我们在学习Lucene中我们都见到过，包括到不限于：

* 字段的数据类型
* 是否要存储
* 是否要索引
* 是否分词
* 分词器是什么

一起来看下创建的语法

## 6.1 创建字段映射

> 语法

请求方式依然是PUT

![image-20220317112623310](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317112623310.png)

如果版本是6版本，那么如上图，给指定索引创建一个类型，且定义类型当中有哪些字段，字段的约束等等。其实在6版本当中虽然可以指定类型名，但一个索引也只能有一个类型。在7版本之后将这个类型完全取消。即使是古老版本多类型映射字段也是属于索引（因为第二个类型不能创建已经存在的字段），也就是索引相当于表，类型只不过是按列分表而已，完全的不同结构的实体应分别创建索引。 

现在我使用的是8版本，没有类型名指定。在**创建索引的时候**直接指定索引的字段映射即可。goods索引下就是mapping

```json
PUT /goods
{
	"mappings": {
		"properties": {
			"good_id": {
				"type": "long"
			},
			"good_name": {
				"type": "text",
				"index": true,
				"analyzer":"ik_max_word"
			},
			"good_img": {
				"type": "keyword",
				"index": false
			}
		}
	}
}
```

![image-20220317132827616](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317132827616.png)



**已经创建了索引，怎么添加字段呢**

上面我们是直接在创建一个索引时指定了字段映射，但如果已经创建了索引，现在要去给已存在的索引添加字段可通过如下接口进行

```json
PUT /goods/_mapping
{
	"properties": {
		"price": {
			"type": "float"
		}
	}
}
```

![image-20220317133455903](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317133455903.png)

![image-20220317134237387](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317134237387.png)

![image-20220317133919215](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317133919215.png)

## 6.2 查询映射关系

> 语法

```
GET /索引名/_mapping
```

如果是6版本及以下版本还可以指定类型名

```
GET /索引名/_mapping/类型名
```

> 示例

```
GET /jasper/_mapping
```

![image-20220317134622781](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317134622781.png)

## 6.3 映射属性详解

### 6.3.1 type

Elasticsearch中支持的数据类型非常丰富：

下面链接为官网的全部类型介绍

https://www.elastic.co/guide/en/elasticsearch/reference/8.0/mapping-types.html

<table>
    <tr>
        <th>一级分类</th>
        <th>二级分类</th>
        <th>具体类型</th>
        <th>介绍</th>
    </tr>
    <tr>
        <td rowspan="7">核心类型</td>
        <td>字符串类型</td>
        <td>text,keyword</td>
        <td>结构化搜索，全文文本搜索、聚合、排序等</td>
    </tr>
    <tr>
        <td>整数类型</td>
        <td>interger,long,short,byte</td>
        <td>字段的长度越短，索引和搜索的效率越高</td>
    </tr>
    <tr>
        <td>浮点型</td>
        <td>double,float.half_float,scaled_float</td>
        <td></td>
    </tr>
    <tr>
        <td>布尔型</td>
        <td>boolean</td>
        <td></td>
    </tr>
    <tr>
        <td>日期型</td>
        <td>date</td>
        <td></td>
    </tr>
    <tr>
        <td>范围型</td>
        <td>range</td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td>二进制类型</td>
        <td>binary</td>
        <td>该binary类型接受二进制值作为Base64编码的字符串。该字段默认的情况下不存储（store）,并且不可搜索</td>
    </tr>
    <tr>
        <td rowspan="3">复合类型</td>
        <td>数组类型</td>
        <td>array</td>
        <td></td>
    </tr>
    <tr>
        <td>对象类型</td>
        <td>object</td>
        <td>用于单个JSON对象</td>
    </tr>
    <tr>
        <td>嵌套类型</td>
        <td>nested</td>
        <td>用于JSON对象数组</td>
    </tr>
    <tr>
        <td rowspan="2">地理类型</td>
        <td>地理坐标类型</td>
        <td>geo_point</td>
        <td>经纬度</td>
    </tr>
    <tr>
        <td>地理地图</td>
        <td>geo_shape</td>
        <td>用于多边形等复杂形状</td>
    </tr>
    <tr>
        <td rowspan="3">特殊类型</td>
        <td>IP类型</td>
        <td>ip</td>
        <td>用于IPv4和IPv6</td>
    </tr>
    <tr>
        <td>地理地图</td>
        <td>geo_shape</td>
        <td>用于多边形等复杂形状</td>
    </tr>
    <tr>
        <td>地理地图</td>
        <td>geo_shape</td>
        <td>用于多边形等复杂形状</td>
    </tr>
</table>

提几个关键的：

* 字符串类型又分两种：

  * text：使用文本数据类型的字段，它们会被分词，文本字段不用于排序，很少用于聚合，如文章标题、正文。
  * keyword：关键字数据类型，用于索引结构化内容的字段，不会被分词，必须完整匹配的内容，如邮箱，身份证号。支持聚合

  这两种类型都是比较常用的，但有的时候，对于一个字符串字段，我们可能希望他两种都支持，此时，可以利用其多字段特性

  ```json
  "properties":{
      "title":{
          "type":"text",
          "analyzer":"ik_max_word",
          "fields":{
              "sort":{
                  "type":"keyword"
              }
          },
          "index":true
      }
  }
  ```
  
* Numerical：数值类型，分两类

  * 基本数据类型：long、interger、short、byte、double、float、half_float

  * double 双精度64位

  * float 单精度32位

  * half_float 半精度16位

  * 浮点数的高精度类型：scaled_float

    * 带有缩放因子的缩放类型浮点数，依靠一个 long 数字类型通过一个固定的( double 类 型)缩放因数进行缩放.
    * 需要指定一个精度因子，比如10或100。elasticsearch会把真实值乘以这个因子后存储，取出时再还原。

  * Date：日期类型

    elasticsearch可以对日期格式化为字符串存储，但是建议我们存储为毫秒值，存储为long，节省空间。

* Array数组类型

  * 进行匹配时，任意一个元素满足，都认为满足
  * 排序时，如果升序则用数组中的最小值来排序，如果降序则用数组中的最大值来排序
  
* Object：对象



### 6.3.1 index

index影响字段的索引情况。

* true：字段会被索引，则可以用来进行搜索过滤。默认值就是true，只有当某一个字段的index值设置为true时，检索ES才可以作为条件去检索。
* false：字段不会被索引，不能用来搜索

index的默认值就是true，也就是说你不进行任何配置，所有字段都会被索引。

但是有些字段是我们不希望被索引的，比如商品的图片信息（URL），就需要手动设置index为false。 



### 6.3.2 store

是否将数据进行**额外**存储。

在学习lucene时，我们知道如果一个字段的store设置为false，那么在文档列表中就不会有这个字段的值，用户的搜索结果中不会显示出来。

但是在Elasticsearch中，即便store设置为false，也可以搜索到结果。

原因是Elasticsearch在创建文档索引时，会将文档中的原始数据备份，保存到一个叫做 _source 的属性中。而且我们可以通过过滤 _source 来选择哪些要显示，哪些不显示。

而如果设置store为true，就会在 _source 以外额外存储一份数据，多余，因此一般我们都会将store设置为false，事实上，**store的默认值就是false。**

在某些情况下，这对 store 某个领域可能是有意义的。例如，如果您的文档包含一个 title ，一个date 和一个非常大的 content 字段，则可能只想检索the title 和the date 而不必从一个大 _source字段中提取这些字段：

```json
PUT my_index
{
    "mappings":{
        "_doc":{
            "properties":{
                "title":{
                    "type":"text",
                    "store":true
                },
                "date":{
                    "type":"date",
                    "store":true
                },
                "content":{
                    "type":"text"
                }
            }
        }
    }
}
```

### 6.3.3 boost

网站权重:网站权重是指搜索引擎给网站（包括网页）赋予一定的权威值，对网站（含网页）权威的评估评价。一 个网站权重越高，在搜索引擎所占的份量越大，在搜索引擎排名就越好。提高网站权重，不但利于网站（包括网 页）在搜索引擎的排名更靠前，还能提高整站的流量，提高网站信任度。所以提高网站的权重具有相当重要的意 义。 权重即网站在SEO中的重要性，权威性。英文：Page Strength。1、权重不等于排名 2、权重对排名有着 非常大的影响 3、整站权重的提高有利于内页的排名。

权重，新增数据时，可以指定该数据的权重，权重越高，得分越高，排名越靠前。

```json
PUT my_index
{
    "mappings":{
        "_doc":{
            "properties":{
                "title":{
                    "type":"text",
                    "boost":2
                },
                "content":{
                    "type":"text"
                }
            }
        }
    }
}
```

title 字段上的匹配项的权重是字段上的匹配项的权重的两倍 content ，默认 boost 值为 1.0 。提升仅适用于Term查询（不提升prefix，range和模糊查询）。



# 七、使用Kibana对文档操作

文档，即索引库中某个类型下的数据，会根据规则创建索引，将来用来搜索。可以类比做数据库中的每一行数据。

## 7.1 新增文档

### 7.1.1 新增并随机生成id

通过POST请求，可以向一个已经存在的索引库中添加文档数据。

> 语法：如果是6版本把_doc换成类型名（在取消了类型的情况下，都是用 _doc 代替相当于默认类型）

```json
POST /索引名/_doc
{
    “key":"value"
}
```

![image-20220317150552716](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317150552716.png)

## 7.2 查看文档

根据rest风格，新增是post，查询应该是get，不过查询一般都需要条件，这里我们把刚刚生成数据的id带上。

通过kibana查看数据：

```
GET /索引/_doc/文档id
```

![image-20220317150940527](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317150940527.png)

* _source ：源文档信息，所有的数据都在里面。
* _id ：这条文档的唯一标示
* 自动生成的id,长度为20个字符，URL安全，base64编码，GUID（全局唯一标识符）,分布式系统并行生成时不可能会发生冲突
* 在实际开发中不建议使用ES生成的ID，太长且为字符串类型，检索时效率低。建议：将数据表中唯一的ID，作为ES的文档ID

## 7.3 新增文档并自定义id

如果我们想要自己新增的时候指定id，可以这么做：

```
POST /索引名/_doc/文档id
{
   ...
}
```

**示例：**

![image-20220317151447589](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317151447589.png)

## 7.4 修改数据

PUT：修改文档

POST：新增文档

把刚才新增的请求方式改为PUT，就是修改了。不过修改必须指定文档id

* id对应文档存在，则修改
* id对应文档不存在，则新增

**示例1：**

指定文档id 002不存在，则PUT会新增

![image-20220317151812383](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317151812383.png)

**示例2：**

指定的002存在则是修改

![image-20220317152019602](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317152019602.png)



## 7.5 删除数据

> 语法

```
DELETE /索引名/类型名/id值
```

**示例**

![image-20220317152209986](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317152209986.png)

## 7.6 智能判断

刚刚我们在新增数据时，添加的字段都是提前在类型中定义过的，如果我们添加的字段并没有提前定义过，能够成功吗？

事实上Elasticsearch非常智能，你不需要给索引库设置任何mapping映射，它也可以根据你输入的数据来判断类型，动态添加数据映射。

**测试一下：**

![image-20220317152617955](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317152617955.png)

desc与date都是没有在映射里配置过的

看下现在goods索引的映射情况

![image-20220317152920099](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317152920099.png)

添加了两条映射，且连类型都自动识别了。一般会选择同类型中最大的，比如你的字段值是整型，那么自动添加映射时会配置成long类型

当是字符串类型时，ES无法智能判断就会两个类型都设置

```json
"desc" : {
    "type" : "text",
    "fields" : {
        "keyword" : {
            "type" : "keyword",
            "ignore_above" : 256
        }
    }
},
```

这种智能映射，底层原理是动态模板映射，如果我们想修改这种智能映射的规则，其实只要修改动态模板即可！



## 7.7 动态映射模板

![image-20220317153459219](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317153459219.png)

1）模板名称，随便起

2）匹配条件，凡是符合条件的未定义字段，都会按照这个规则来映射

3）映射规则，匹配成功后的映射规则

举例，我们可以把所有未映射的string类型数据自动映射为keyword类型：

![image-20220317154410407](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317154410407.png)

```json
PUT /goods/_mapping
{
  "dynamic_templates":[
    {
        "strings":{
            "match_mapping_type":"string",
            "mapping":{
                "type":"keyword",
                "index":false,
                "store":true
            }
        }
    }
  ]
}
```

如图给goods索引的映射配置了名为strings的动态映射模板，规则是只要是字符串类型，就将其设置为keyword类型且继续额外存储并不需要索引

**测试**

![image-20220317154844714](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317154844714.png)

![image-20220317155003557](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317155003557.png)

新增文档产生的新字段，配自动添加映射且和我们设定的一样而不是默认的text与keyword共存

# 八、查询（重点）

## 8.1 基础查询

> 基本语法

```json
GET /索引名/_search 
{ 
    "query":{ 
        "查询类型":{ 
            "查询条件":"查询条件值" 
        } 
    } 
}
```

这里的query代表一个查询对象，里面可以有不同的查询属性

* 查询类型：
  * 例如： match_all ， match ， term ， range 等等
* 查询条件：查询条件会根据类型的不同，写法也有差异，后面详细讲解

### 8.1.1 查询所有（match_all）

> 示例

```json
GET /goods/_search
{
  "query": {
    "match_all": {}
  }
}
```

* query：代表查询对象
* match_all：代表查询所有

![image-20220317160841370](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317160841370.png)



**took：**查询花费时间，单位是毫秒

**time_out：**是否超时

**_shards：**分片信息

**hits：**搜索结果总览对象

* **total：**搜索到的总条数

* **max_score：**所有结果中文档得分的最高分

* **hits：**搜索结果的文档对象数组，每个元素是一条搜索到的文档信息

  * **_index：**索引库

  * **_type：**文档类型

  * **_id：**文档id

  * **_score：**文档得分

  * **_source：**文档的源数据

文档得分：使用ES时，对于查询出的文档无疑会有文档相似度之别。而理想的排序是和查询条件相关性越高排序越靠前，而这个排序的依据就是_score

### 8.1.2 匹配查询（match）

**or关系**

match 类型查询，会把查询条件进行分词，然后进行查询,多个词条之间是or的关系

```json
GET /goods/_search
{
  "query": {
    "match": {
      "good_name": "小米"
    }
  }
}
```

![image-20220317162052485](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317162052485.png)

匹配good_name”小米“很显然是可以匹配到之前的两条数据。但不能体系分词匹配最后在or的关系。我们这次匹配`小米pro`看效果

![image-20220317162405009](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317162405009.png)

小米匹配到了两条，pro匹配到了一条。一共三条数据

**and关系**

某些情况下，我们需要更精确查找：比如在电商平台精确搜索商品时，我们希望这个关系（查询条件切分词之后的关系）变成 and （既要满足你，又要满足我），可以这样做：

```json
GET /goods/_search
{
  "query": {
    "match": {
      "good_name": {
        "query": "小米pro"
        , "operator": "and"
      }
    }
  }
}
```

这样也就是一条文档的good_name字段的值分词后得同时拥有`小米`以及`pro`词条才能被检索，当前是没有的

![image-20220317163020705](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317163020705.png)

### 8.1.3 词条匹配（term）

term 查询被用于精确值匹配，这些精确值可能是数字、时间、布尔或者那些**未分词**的字符串.

反正一句话，不对搜索词进行分词，就用完整的搜索词匹配索引词条

如果搜索小米就用小米去匹配，预计匹配2条，如果搜索手机小米，就去匹配`手机小米`这个词条很显然查不到。因为文档的属性值即使包含`手机小米`但也会变成`手机` `小米` 两个词条。

```json
GET /goods/_search
{
  "query": {
    "term": {
      "good_name": "手机小米"
    }
  }
}
```

![image-20220317165154794](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317165154794.png)

![image-20220317165239689](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317165239689.png)

因此如果我用term查询是不分词的，那么文档的字段内容是分词的就没有意义。所以才说一般用这个查的字段都是不分词的比如数字或者keyword这种字段

### 8.1.4 布尔组合（bool）

`bool` 把各种其它查询通过 `must （与）`、 `must_not （非）`、 `should （或）`的方式进行组合

```json
GET /goods/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "good_name": "小米"
        }}
      ],
      "must_not": [
        {"match": {
          "good_name": "手机"
        }}
      ],
      "should": [
        {"match": {
          "good_name": "pro"
        }}
      ]
    }
  }
}
```

![image-20220317170144150](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317170144150.png)

匹配到`小米`的是必须的，匹配`手机`词条的必须没有，`pro` 有和没有都可以。最后得到的就是`数码小米12`而不会有`手机小米`

### 8.1.5 范围查询（range）

`range`查询找出那些落在指定区间内的数字或者时间

```json
GET /goods/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 4000,
        "lt": 5000
      }
    }
  }
}
```

![image-20220317170930624](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317170930624.png)

range查询允许的操作符

<table>
    <tr>
    	<th>操作符</th>
        <th>说明</th>
    </tr>
    <tr>
    	<td>gt</td>
        <td>大于</td>
    </tr>
    <tr>
    	<td>gte</td>
        <td>大于等于</td>
    </tr>
    <tr>
    	<td>lt</td>
        <td>小于</td>
    </tr>
    <tr>
    	<td>lte</td>
        <td>小于等于</td>
    </tr>
</table>

### 8.1.6 模糊查询（fuzzy）

fuzzy查询是term查询的模糊等价，很少直接使用它

它允许用户搜索词条与实际词条的拼写出现偏差，但是偏差的编辑距离不得超过1：

```json
GET /goods/_search
{
  "query": {
    "fuzzy": {
      "good_name": "小米1"
    }
  }
}
```

和term一样不分词查询，也就是查`小米1`，词条里面没有但最相似的是`小米`,误差是1可以算，那就是匹配到具有`小米`的document

![image-20220317171813378](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317171813378.png)



## 8.2 结果过滤

默认情况下，elasticsearch在搜索的结果中，会把文档中保存在 _source 的所有字段都返回。

如果我们只想获取其中的部分字段，我们可以添加 _source 的过滤

### 8.2.1 直接指定字段

**示例**

```json
GET /goods/_search
{
  "_source": ["good_name","price"], 
  "query": {
    "match_all": {}
  }
}
```

![image-20220317172202745](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317172202745.png)

### 8.2.2 指定includes和excludes

我们也可以通过

* includes：来指定想要显示的字段
* excludes：来指定不想要显示的字段

```json
GET /goods/_search
{
  "_source": {
    "includes": ["good_name"]
  }, 
  "query": {
    "match_all": {}
  }
}
```



## 8.3 过滤

Elasticsearch 使用的查询语言（DSL）拥有一套查询组件，这些组件可以以无限组合的方式进行搭配。这套组件可以在以下两种情况下使用：过滤情况（filtering context）和查询情况（query context）。

**如何选择查询与过滤**

通常的规则是，使用查询（query）语句来进行 全文 搜索或者其它任何需要影响 相关性得分 的搜索。除此以外的情况都使用过滤（filters)。

### 8.3.1 条件查询中进行过滤

所有的查询都会影响到文档的评分及排名。如果我们需要在查询结果中进行过滤，并且不希望过滤条件影响评分，那么就不要把过滤条件作为查询条件来用。而是使用 filter 方式：

```json
GET /goods/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "good_name": "小米"
        }}
      ],
      "filter": [
        {
          "range": {
            "price": {
              "gte": 4000,
              "lte": 5000
            }
          }
        }
      ]
    }
  }
}
```

![image-20220317173935474](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317173935474.png)

### 8.3.2 无条件查询，直接过滤

如果一次查询只有过滤，没有查询条件，不希望进行评分，我们可以使用 constant_score 取代只有filter 语句的 bool 查询。在性能上是完全相同的，但对于提高查询简洁性和清晰度有很大帮助。

```json
GET /goods/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "term": {
          "good_name": "小米"
        }
      }
    }
  }
}
```

![image-20220317174342467](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317174342467.png)

## 8.4 排序

### 8.4.1 单个字段排序

`sort` 可以让我们按照不同的字段进行排序，并且通过 order 指定排序的方式

```json
GET /goods/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    { "price": { "order": "asc"} }
  ]
}
```

![image-20220317175027854](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317175027854.png)

### 8.4.2 多字段排序

假定我们想要结合使用 price和 _score（得分） 进行查询，并且匹配的结果首先按照价格排序，然后按照相关性得分排序：（安装先后顺序进行以price排序，在price相同的先后顺序再由 _score决定）

```
GET /goods/_search
{
  "query": {
    "match": {
      "good_name": "小米手机pro"
    }
  },
  "sort": [
    {"price": { "order": "asc" }},
    {"_score": { "order": "desc" }}
  ]
}
```

![image-20220317180410609](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317180410609.png)

## 8.5 分页

Elasticsearch中数据都存储在分片中，当执行搜索时每个分片独立搜索后，数据再经过整合返回。那么，如果要实现分页查询该怎么办呢？

elasticsearch的分页与mysql数据库非常相似，都是指定两个值：

* from：目标数据的偏移值（开始位置），默认from为0
* size：每页大小

```json
GET /goods/_search
{
  "query": {
    "match_all": {}
  }, 
  "from": 0,
  "size": 2
}
```

![image-20220317180822159](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317180822159.png)

## 8.6 高亮

高亮原理：

	* 服务端搜索数据，得到搜索结果
	* 把搜索结果中，搜索关键字都加上约定好的标签
	* 前端页面提前写好标签的CSS样式，即可高亮

elasticsearch中实现高亮的语法比较简单：

![image-20220317181552809](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220317181552809.png)

在使用match查询的同时，在查询外加上一个highlight属性：

* pre_tags：前置标签
* post_tags：后置标签
* fields：需要高亮的字段



# 九、聚合aggregations

聚合可以让我们极其方便的实现对数据的统计、分析。例如：

* 什么品牌的手机最受欢迎？
* 这些手机的平均价格、最高价格、最低价格？
* 这些手机每月的销售情况如何？

实现这些统计功能的比数据库的sql要方便的多，而且查询速度非常快，可以实现近实时搜索效果。

## 9.1 基本概念

Elasticsearch中的聚合，包含多种类型，最常用的两种，一个叫 桶 ，一个叫`度量`：

> 桶（bucket）类似于 group by

桶的作用，是按照某种方式对数据进行分组，每一组数据在ES中称为一个 桶 ，例如我们根据国籍对人划分，可以得到 中国桶 、 英国桶 ， 日本桶 ……或者我们按照年龄段对人进行划分：0~10,10~20,20~30,30~40等。

Elasticsearch中提供的划分桶的方式有很多：

* Date Histogram Aggregation：根据日期阶梯分组，例如给定阶梯为周，会自动每周分为一组
* Histogram Aggregation：根据数值阶梯分组，与日期类似，需要知道分组的间隔（interval）
* Terms Aggregation：根据词条内容分组，词条内容完全匹配的为一组
* Range Aggregation：数值和日期的范围分组，指定开始和结束，然后按段分组
* ....

综上所述，我们发现bucket aggregations 只负责对数据进行分组，并不进行计算，因此往往bucket中往往会嵌套另一种聚合：metrics aggregations即度量

> 度量（metrics） 相当于聚合的结果

分组完成以后，我们一般会对组中的数据进行聚合运算，例如求平均值、最大、最小、求和等，这些在ES中称为 `度量`

比较常用的一些度量聚合方式

* Avg Aggregation：求平均值
* Max Aggregation：求最大值
* Min Aggregation：求最小值
* Percentiles Aggregation：求百分比
* Stats Aggregation：同时返回avg、max、min、sum、count等
* Sum Aggregation：求和
* Top hits Aggregation：求前几
* Value Count Aggregation：求总数
* ...

为了测试聚合，我们先批量导入一些数据

**创建索引：**

```json
PUT /car 
{ 
  "mappings": { 
    "properties": { 
      "color": { 
        "type": "keyword" 
      },
      "make": { 
        "type": "keyword" 
      } 
    } 
  } 
}
```

**注意**：在ES中，需要进行聚合、排序、过滤的字段其处理方式比较特殊，因此不能被分词，必须使用keyword 或 数值类型 。这里我们将color和make这两个文字类型的字段设置为keyword类型，这个类型不会被分词，将来就可以参与聚合

导入数据，这里是采用批处理的API，直接复制到kibana运行即可：

`注意：在6版本或者之前 接口是/index/type/_bulk,在我当前版本并不是把类型名换成_doc而是直接取消，要保证奇数行是分隔符偶数行为数据行`

```json
POST /car/_bulk
{ "index": {}} 
{ "price" : 10000, "color" : "红", "make" : "本田", "sold" : "2020-10-28" } 
{ "index": {}} 
{ "price" : 20000, "color" : "红", "make" : "本田", "sold" : "2020-11-05" } 
{ "index": {}} 
{ "price" : 30000, "color" : "绿", "make" : "福特", "sold" : "2020-05-18" } 
{ "index": {}} 
{ "price" : 15000, "color" : "蓝", "make" : "丰田", "sold" : "2020-07-02" } 
{ "index": {}} 
{ "price" : 12000, "color" : "绿", "make" : "丰田", "sold" : "2020-08-19" } 
{ "index": {}} 
{ "price" : 20000, "color" : "红", "make" : "本田", "sold" : "2020-11-05" } 
{ "index": {}} 
{ "price" : 80000, "color" : "红", "make" : "宝马", "sold" : "2020-01-01" } 
{ "index": {}} 
{ "price" : 25000, "color" : "蓝", "make" : "福特", "sold" : "2020-02-12" }
```

![image-20220318094933134](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318094933134.png)



## 9.2 聚合为桶

首先，我们按照 汽车的颜色 color来 划分桶 ，按照颜色分桶，最好是使用TermAggregation类型，按照颜色的名称来分桶。

> 语法

```json
GET /car/_search
{
  "aggs": {
    "NAME": {
      "AGG_TYPE": {}
    }
  }
}
```

> 示例

![image-20220318095951520](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318095951520.png)

size： 查询条数，这里设置为0，因为我们不关心搜索到的数据，只关心聚合结果，提高效率

aggs：声明这是一个聚合查询，是aggregations的缩写

* popular_colors：指定的分组结果名称，自定义
* terms：聚合的类型，terms匹配
* field：划分桶的依赖字段

hits：查询结果为空，因为我们设置了size为0

aggregations：聚合的结果

popular_colors：我们定义的聚合名称

buckets：查找到的桶，每个不同的color字段值都会形成一个桶

* key：这个桶对应的color字段的值
* doc_count：这个桶中的文档数量

通过聚合的结果我们发现，目前红色的小车比较畅销



## 9.3 桶类度量

前面的例子告诉我们每个桶里面的文档数量，这很有用。 但通常，我们的应用需要提供更复杂的文档度量。 例如，每种颜色汽车的平均价格是多少？

因此，我们需要告诉Elasticsearch 使用哪个字段 ， 使用何种度量方式 进行运算，这些信息要嵌套在 桶内， 度量 的运算会基于 桶 内的文档进行

现在，我们为刚刚的聚合结果添加 求价格平均值的度量：

```json
GET /car/_search
{
  "size": 0, 
  "aggs": {
    "popular_colors": {
      "terms": {
        "field": "color"
      },
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

![image-20220318101122506](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318101122506.png)

**aggs：**我们在上一个aggs(popular_colors)中添加新的aggs。可见度量也是一个聚合

**avg_price：**聚合的名称

**avg：**度量的类型，这里是求平均值

**field：**度量运算的字段

可以看到每个桶中都有自己的 avg_price 字段，这是度量聚合的结果



# 十、Elasticsearch集群

## 10.1 单点的问题

单点的elasticsearch存在哪些可能出现的问题呢？

* 单台机器存储容量有限，无法实现高存储
* 单服务器容易出现单点故障，无法实现高可用
* 单服务的并发处理能力有限，无法实现高并发

所以，为了应对这些问题，我们需要对elasticsearch搭建集群



## 10.2 集群的结构

### 10.2.1 数据分片

首先，我们面临的第一个问题就是数据量太大，单点存储量有限的问题。

大家觉得应该如何解决？

没错，我们可以把数据拆分成多份，每一份存储到不同机器节点（node），从而实现减少每个节点数据量的目的。这就是数据的分布式存储，也叫做： 数据分片（Shard） 。 

![image-20220318102112241](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318102112241.png)

假如只有三个分片，可以将每一个分片放在不同的集群节点，以此实现存储

### 10.2.2 数据备份

数据分片解决了海量数据存储的问题，但是如果出现单点故障，那么分片数据就不再完整，这又该如何解决呢？

没错，就像大家为了备份手机数据，会额外存储一份到移动硬盘一样。我们可以给每个分片数据进行备份，存储到其它节点，防止数据丢失，这就是数据备份，也叫 数据副本（replica） 。

数据备份可以保证高可用，但是每个分片备份一份，所需要的节点数量就会翻一倍，成本实在是太高了！

为了在高可用和成本间寻求平衡，我们可以这样做：

* 首先对数据分片，存储到不同节点
* 然后对每个分片进行备份，放到对方节点，完成互相备份

这样可以大大减少所需要的服务节点数量，如图，我们以3分片，每个分片备份一份为例：

![image-20220318102533434](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318102533434.png)

三个分片0、1、2分别存在三个节点上，第一个节点存的分片0的备份在节点2也存一个。节点2存的分片1的备份在节点3存一份，节点3存的分片2的备份在节点1存一份。

在这个集群中，如果出现单节点故障，并不会导致数据缺失，所以保证了集群的高可用，同时也减少了节点中数据存储量。并且因为是多个节点存储数据，因此用户请求也会分发到不同服务器，并发能力也得到了一定的提升。

## 10.3 集群搭建

集群需要多台机器，我们这里用一台机器来模拟，因此我们需要在一台机器中部署多个elasticsearch节点，每个elasticsearch的端口都必须不一样。

一台机器进行模拟：将我们的ES的安装包复制三份，修改端口号，data和log存放位置的不同。

实际开发中：将每个ES节点放在不同的服务器上。

我们计划集群名称为：lagou-elastic，部署3个elasticsearch节点，分别是：

* **node-01**：http端口9201，TCP端口9301

* **node-02**：http端口9202，TCP端口9302

* **node-03**：http端口9203，TCP端口9303

http：表示使用http协议进行访问时使用端口，elasticsearch-head、kibana、postman请求端口号是9200。

tcp：集群间的各个节点进行通讯的端口，默认9300

**1）复制三个ES出来**

![image-20220318103632854](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318103632854.png)

**2）修改每个节点配置**

三个节点的配置文件几乎一致，除了：node.name、path.data、path.log、http.port、transport.port

```yaml
cluster.name: MY_SOME_ES
node.name: ES01
path.data: C:\Users\hao\MySpace\utils\Elasticsearch\elasticsearch-9201\datas\data
path.logs: C:\Users\hao\MySpace\utils\Elasticsearch\elasticsearch-9201\datas\log
network.host: 0.0.0.0
http.port: 9201
transport.port: 9301
discovery.seed_hosts: ["127.0.0.1:9301", "127.0.0.1:9302", "127.0.0.1:9303"]
cluster.initial_master_nodes: ["ES01","ES02","ES03"]
```

**3）启动集群**

把三个节点分别启动

使用head插件查看：

![image-20220318145225387](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318145225387.png)

## 10.4 测试集群中创建索引库

可以通过head插件界面创建，也可以用上面学习的命令。我们创建一个分片数为3副本为1的索引即可

![image-20220318145414296](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318145414296.png)

```json
PUT /hao
{ 
    "settings": { 
        "number_of_shards": 3, 
        "number_of_replicas": 1 
    } 
}
```

通过chrome浏览器的head查看，我们可以查看到分片的存储结构：

![image-20220318145929127](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318145929127.png)

可以看到，ES01保存了0分片以及1分片的副本，ES02保存了2分片以及0分片的副本，ES03保存了1分片以及2分片的副本

## 10.5 集群的工作原理

### 10.5.1 shad与replica机制

（1）一个index包含多个shard,也就是一个index存在多个服务器上

（2）每个shard都是一个最小工作单元，承载部分数据，比如有三台服务器,现在有三条数据,这三条数据在三台服务器上各方一条. 

（3）增减节点时，shard会自动在nodes中负载均衡

（4）primary shard（主分片）和replica shard（副本分片），每个document肯定只存在于某一个primary shard以及其对应的replica shard中，不可能存在于多个primary shard

（5）replica shard是primary shard的副本，负责容错，以及承担读请求负载

（6）primary shard的数量在创建索引的时候就固定了，replica shard的数量可以随时修改

（7）primary shard的默认数量是5，replica默认是1（每个主分片一个副本分片），默认有10个shard，5个primary shard，5个replica shard

（8）primary shard不能和自己的replica shard放在同一个节点上（否则节点宕机，primary shard和副本都丢失，起不到容错的作用），但是可以和其他primary shard的replica shard放在同一个节点上



### 10.5.2 集群写入数据

* 客户端选择一个node发送请求过去，这个node就是coordinating node (协调节点) 
* coordinating node，对document进行路由，将请求转发给对应的node。（根据一定的算法选择对应的节点进行存储）
* 实际上的node上的primary shard处理请求，将数据保存在本地，然后将数据同步到replica node
* coordinating node，如果发现primary node和所有的replica node都搞定之后，就会返回请求到客户端

**这个路由简单的说就是取模算法,比如说现在有3太服务器,这个时候传过来的id是5,那么5%3=2,就放在第2台服务器**



### 10.5.3 ES查询数据

查询有个算法叫倒排序:简单的说就是:通过分词把词语出现的id进行记录下来,再查询的时候先去查到哪些id包含这个数据,然后再根据id把数据查出来

**查询过程**

* 客户端发送一个请求给coordinate node
* 协调节点将搜索的请求转发给所有的shard对应的primary shard 或replica shard
* query phase（查询阶段）：每一个shard 将自己搜索的结果（其实也就是一些唯一标识），返回给协调节点，有协调节点进行数据的合并，排序，分页等操作，产出最后的结果
* fetch phase（获取阶段） ，接着由协调节点，根据唯一标识去各个节点进行拉取数据，最终返回给客户端



# 十一、JAVA客户端

十一章节下面所有测试的代码，已上传gihub地址如下：

https://github.com/Jasper-zh/Elasticsearch_Client_Test

## 11.1 客户端介绍

在elasticsearch官网中提供了各种语言的客户端：https://www.elastic.co/guide/en/elasticsearch/client/index.html

![image-20220318151422778](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318151422778.png)

进去选下其他版本，我是8.0.0

![image-20220318155836309](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318155836309.png)

现在的版本是已经不推荐使用 High Level REST Client，而支持 Java_API_Client。我这里就使用Java_API_Client的方式（High Level REST Client市面上也有很多教程了）

**Java Api Client文档**

https://www.elastic.co/guide/en/elasticsearch/client/java-api-client/7.17/index.html



## 11.2 创建Demo工程

### 11.2.1 初始化项目

创建一个空白maven工程，安装文档引入依赖

```xml
<dependencies>
    <dependency>
        <groupId>co.elastic.clients</groupId>
        <artifactId>elasticsearch-java</artifactId>
        <version>8.0.1</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.12.3</version>
    </dependency>
</dependencies>
```

### 11.2.2 获取API_Client

参照文档配置认证创建API_Client

```java
public static ElasticsearchClient getClient(){
    // 1.创建凭证
    CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
    credentialsProvider.setCredentials(AuthScope.ANY, new UsernamePasswordCredentials("elastic", "eA95WVi0HZarqp*LwoOa"));
    // 2.获取rest客户端，认证也在这部分解决
    RestClientBuilder builder = RestClient.builder(new HttpHost("127.0.0.1",9201))
        .setHttpClientConfigCallback(new RestClientBuilder.HttpClientConfigCallback() {
            @Override
            public HttpAsyncClientBuilder customizeHttpClient(
                HttpAsyncClientBuilder httpClientBuilder) {
                return httpClientBuilder
                    .setDefaultCredentialsProvider(credentialsProvider);
            }
        });
    RestClient restClient = builder.build();
    // 3.获取transport客户端
    ElasticsearchTransport transport = new RestClientTransport(restClient, new JacksonJsonpMapper());
    // 4.获取 api 客户端
    ElasticsearchClient client = new ElasticsearchClient(transport);
    return client;
}
```



## 11.3 索引操作

上面获取到ElasticsearchClient，就可以尝试去创建索引了`索引这个东西正常情况是直接在elasticsearch上创建定义好而不是在程序中，所以主要参考上面直接使用elasticsearch语法创建`

```java
client.indices().create(c -> c.index("user"));
```

`注意：这里由一个问题就就是ES一直默认是开启的ssl，在上面的学习使用当中一直都是使用https访问，在现在的这个客户端构成当中创建的RestClient是请求时是http因此最终发送请求会出现Connetion is closed 的异常.我这里将xpack.security.http.ssl:关了重新启动了ES`

**head插件查看**

![image-20220318182747404](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220318182747404.png)

成功创建索引，默认的不分片和1个副本。
那这是简单的创建索引，当然也可以去创建索引时配置field域与setting设置。就和数据库创建表同时配置字段和其他设置信息。虽然不会一般用程序创建但还是演示记录下。顺便记录下找接口的过程

**官方接口文档：https://artifacts.elastic.co/javadoc/co/elastic/clients/elasticsearch-java/8.1.0/index.html**

**源码地址：https://github.com/elastic/elasticsearch-java/**

通过`ElasticsearchClient`的Indices实际上是拿到了`ElasticsearchIndicesClient`

`ElasticsearchIndicesClient`的create方法是有重载两个，也就是传入`CreateIndexRequest`对象，创建这个对象需使用其构造器进行创建，第二个方法就是通过函数式接口那么我们只用写构造器构造链即可，它的方法里面会传入构造器执行我们的构造链得到`CreateIndexRequest`再调用之前的create方法。

`第二个方法就是为了让第一个方法可以写的更方便提供了函数式接口直接使用lambda表达式`

![image-20220322114337577](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220322114337577.png)

![carbon20220322](https://gitee.com/Jasper-zh/image_host/raw/master/carbon20220322.png)

也就是通过构造器CreateIndexRequestBuilder调用index(“user”)创建的CreateIndexRequest

看看构造器是怎么创建的CreateIndexRequest对象

![carbon20220322 (2)](https://gitee.com/Jasper-zh/image_host/raw/master/carbon20220322 (2).png)

就是一个再简化版也是最常用的一种构造器的写法，从这个构造器的代码中也能知道除了指定index名的属性之外还有各种各样的属性包括映射或者设置等等。

在创建时要配置映射，那么就通过mappings方法传入TypeMapping即可.

那么TypeMapping也是通过它的构造器进行构建且提供了函数式接口的方法

![image-20220322135234768](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220322135234768.png)

构建项中其中这三个是配置字段信息的。包含两个参数其中一个是字段名String类型另一个是这个字段的一些配置是Property类型。

Property它也是一个复杂对象，也提供了构造器。直接使用Property的构造器去创建，且也提供了函数式接口参数，就直接使用lambda去执行构造链即可。

如下示例：创建了一个名为product的索引且有一个“name”的映射字段，字段的属性设置了index(true）是否索引。

```java
client.indices().create(c -> c.index("product").mappings(t -> t.properties("name", p -> p.text(text -> text.index(true)))));
```

![carbon20220322 (3)](https://gitee.com/Jasper-zh/image_host/raw/master/carbon20220322 (3)-16479309196482.png)

这种形式用的少的话写起来会有点绕，实际上就是先索引库名以及映射，然后在映射里面配置typeMappings实际是包含各个property，对于property里面除了字段名称还有字段的配置对象，text设置完index还可以继续设置分词器analyzer.... 

除了添加还有删除以及索引其他的配置操作也都可以在官方Java文档中`ElasticsearchIndicesClient类`找到看就不一一演示了

![carbon20220323](https://gitee.com/Jasper-zh/image_host/raw/master/carbon20220323.png)

`基本上它提供的接口对象基本上都是完全应用了lambda以及构造器。因为之前写过一篇设计模式当中的构造器，因此代码看的还是比较舒适的，能够get到它的一个代码设计`

## 11.4 文档操作

根据上面的一些命名规则这里可以猜测一下它的文档操作可能是由一个叫做`ElasticsearchDocsClien`，去文档看一下

实际上不是的，就是ElasticsearchClient提供的操作

![image-20220323104831108](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323104831108.png)

它这上面有很多，我就把关于添加、删除、获取、更新以及批量列出来演示下。

![image-20220323104732853](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323104732853.png)



### 11.4.1 添加文档

先看下添加，使用index方法传入IndexRequest对象，同样使用构造器，且这个方法也提供函数式接口，写一个构造链即可。

那么就看下IndexRequest有哪些属性需要提供，看下构造器提供的属性方法。

![image-20220323105601912](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323105601912.png)

没有多少，项上面我们使用Kibana去添加文档就是使用POST请求然后接索引名然后`（_doc 类型没有了）`还有指定文档id，最后就请求体了也就是实体

对应到这里通过index(String)指定索引 id(String) 指定id。那么document(TDocument) 即作为请求头

```java
client.index(indexRequestBuild -> indexRequestBuild.index("product").id("001").document(new Product("小米12",4699)));
```

head插件查看，已经成功插入数据

![image-20220323114322292](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323114322292.png)



### 11.4.2 获取文档

然后看一下GET的，基本上也是差不多它的使用还是挺统一的。就是两个参数一个是GetRequest对象里面很多参数我们指定索引以及文档id即可，第二就是数据封装Java的class对象.

```java
GetResponse<Product> response = client.get(getRequestBuild -> getRequestBuild.index("product").id("001"),product.class);

System.out.println(response.source());
```

![image-20220323125605372](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323125605372.png)



### 11.4.3 修改文档

修改文档也是可以使用index方法，它即是添加也是更新，id存在则是更新，我们将刚刚的小米12更新下名称

```java
client.index(indexRequestBuild -> indexRequestBuild.index("product").id("001").document(new Product("小米全新版本 15",4699)));
```

成功完成更新

![image-20220323131514590](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323131514590.png)

**也提供了update方法专为修改操作**

![image-20220323132501270](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323132501270.png)

对于index()方法而言是它的更新是全量更新，对于update方法它提供了多种更新配置：普通的doc()是提供了部分更新也可以设置docAsUpsert等等。当文档id不存在会抛出异常，不过也可以使用upset()方法就可以更新或添加了。

**示例**

更新参数Product对象属性name为空 price为2999. 完成更新后name为原来值仅更新price

```java
client.update(updateRequestBuild -> updateRequestBuild.index("product").id("001").doc(new Product(2999)), Product.class);
```

![image-20220323133051988](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323133051988.png)

### 11.4.4 删除文档

写了上面几条之后删除操作都估计不用具体看文档即可猜出了

```java
// 构造器的参数名就不像上面写那么具体了直接用b表示
client.delete(b -> b.index("product").id("001"));
```



### 11.4.5 批量操作

![image-20220323134400765](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323134400765.png)

对于BulkRequest来说主要就是操作一个哪个索引index(String)，二是请求体哪些内容也就是operations

![image-20220323134513202](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323134513202.png)

那么operations里面就是各个BulkOperation，也就是批量里面每个实体的信息是在BulkOperation。那就看看它呗：

![image-20220323134856188](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323134856188.png)

BulkOperation内部的属性是包含各种增删改对象，而增删改对象里面就是实体。

接下来就试一试

我先准备3条数据，然后完成批量进行5个BulkOperation操作，分为3个添加2个删除1个修改

![image-20220323135742057](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323135742057.png)



```java
public static void bulkDoc() throws IOException {
    ArrayList<BulkOperation> list = new ArrayList<>();
    list.add(new BulkOperation.Builder().create(b -> b.id("004").document(new Product("冰箱", 2999))).build());
    list.add(new BulkOperation.Builder().create(b -> b.id("005").document(new Product("电视", 3299))).build());
    list.add(new BulkOperation.Builder().create(b -> b.id("006").document(new Product("投影仪", 3999))).build());
    list.add(new BulkOperation.Builder().delete(b -> b.id("001")).build());
    list.add(new BulkOperation.Builder().delete(b -> b.id("002")).build());
    boolean add = list.add(new BulkOperation.Builder().index(b -> b.id("003").document(new Product("佳能M6II", 6900))).build());
    // client执行bulk传入operations对象
    EsClientUtil.getClient().bulk(b -> b.index("product").operations(list));
    // 关闭client
    EsClientUtil.closeClient();
}
```

添加了004到006、删除了001和002、以及修改了003的name以及price

head插件查看：与预期一致

![image-20220323152411082](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323152411082.png)

## 11.5 查询数据

client的search方法用来进行查询

![image-20220323153310107](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323153310107.png)

主要是构建查询请求对象SearchRequest，看下它的一些构建参数

![image-20220323154434658](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323154434658.png)

### 11.5.1 查询所有match_all

对于我们最简单的查询：查询全部

```json
GET /goods/_search
{
  "query": {
    "match_all": {}
  }
}
```

需要指定的就是索引，以及查询实体里的query属性

对于这里来说构建SearchRequest对象，index(String)用来指定索引，query用来创建查询实体

我们可以看下query实体构建的属性，以下有非常多，其中就有matchAll

![image-20220323155238402](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323155238402.png)

它里面是MatchQuery这些也都可以再去看。对于查询全部来说它里面的MatchQuery就两个属性可以指定一个是查询名称queryName还有个是权重boost

```java
public static void queryAll() throws IOException {
    SearchResponse<Product> search = EsClientUtil.getClient().search(b -> b.index("product").query(
        q -> q.matchAll(m -> m.queryName("一个查询"))
    ), Product.class);
    for(Hit<Product> obj : search.hits().hits()){
        System.out.println(obj.source());
    }
}
```

![image-20220323161707522](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323161707522.png)

### 11.5.2 关键词匹配match

也是一样给search传入SearchRequest，只是这个对象的Query类型的属性值不一样

Query是具备两个属性一个是 _kind 一个是 _value，构造器的不同构建方法指定Quey查询类型（match、match_all、term等等），而参数对象指定这个查询下的多个配置所以对象包裹。

![image-20220323163458414](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323163458414.png)

```java
public static void queryMatch() throws IOException {
    SearchResponse<Product> search = EsClientUtil.getClient().search(b -> b.index("product").query(
        q -> q.match(m -> m.field("name").query("佳能"))
    ), Product.class);
    for(Hit<Product> obj : search.hits().hits()){
        System.out.println(obj.source());
    }
}
```

![image-20220323172149904](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220323172149904.png)

### 11.5.3 Term查询

同样也可以尝试使用Term查询不对查询值进行分词

```java
public static void queryTerm() throws IOException {
    SearchResponse<Product> search = EsClientUtil.getClient().search(b -> b.index("product").query(
        q -> q.term(m -> m.field("name").value("佳能"))
    ), Product.class);
    EsClientUtil.closeClient();
    for(Hit<Product> obj : search.hits().hits()){
        System.out.println(obj.source());
    }
}
```

很明显查不到结果，因为文档的词条没有叫佳能的，`佳能M6II` 默认分词这个两个字会划分成两个词条。

### 11.5.4 范围查询Range

也是一样找到query类，里面有range(xxx)方法进行配置range类型，传参是是一个对象包含比较符号以及值字段等属性内容

```java
public static void queryRange() throws IOException {
    SearchResponse<Product> search = EsClientUtil.getClient().search(b -> b.index("product").query(
        q -> q.range(m -> m.field("price").gte(JsonData.of(3000)))
    ), Product.class);
    EsClientUtil.closeClient();
    for(Hit<Product> obj : search.hits().hits()){
        System.out.println(obj.source());
    }
}
```

![image-20220324095645444](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324095645444.png)

### 11.5.5 _source过滤

一般查询就会返回命中文档的全部字段，如果我们只想要其中指定字段就可以使用_source过滤

在上面使用kibana请求是如下格式：

```json
GET /goods/_search
{
  "_source": ["name","price"], 
  "query": {
    "match_all": {}
  }
}
```

那么猜想这个客户端的设计，应该是给search方法传入的SearchRequest对象，是有多个字段属性。上面我们创建只指定了Query类型属性。

看文档：

![image-20220324100726526](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324100726526.png)

那么source的主要内容配置在SourceConfig，查看SourceConfig，最终是配置在SourceFilter

![image-20220324101022084](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324101022084.png)

查看SourceFilter，到这个地方我们就可以直接进行配置了也就是之前学的_source的excludes和includes

![image-20220324101144284](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324101144284.png)

**测试**

就给上面的范围查询，加个过滤只查出name字段

```java
public static void queryFilter() throws IOException {
    SearchResponse<Product> search = EsClientUtil.getClient().search(
        b -> b.index("product")
            .source(s -> s.filter(f -> f.includes("name")))
            .query(q -> q.range(m -> m.field("price").gte(JsonData.of(3000)))
            ),
        Product.class);
    EsClientUtil.closeClient();
    for(Hit<Product> obj : search.hits().hits()){
        System.out.println(obj.source());
    }
}
```

![image-20220324102119613](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324102119613.png)



## 11.6 排序

一样的看下它本来的一个结构

```json
GET /goods/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    { "price": { "order": "asc"} }
  ]
}
```

查询实体对象里面，除了query就是sort且sort是一个数组可以配置多个字段排序

看下文档searchRequest下，其中就是与sort() ，且传入的就是一个列表每个项是一个SortOptions对象。那么这个对象肯定应该包含字段名以及排序方式

![image-20220324103337597](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324103337597.png)

看下SortOptions的构造，可以知道它的结构完全和上面REST请求例子一样。还分一个（字段）类型的对象里面包含排序方式order

![image-20220324103617279](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324103617279.png)

看下面FieldSort的构造，里面就包含field和SortOrder，所以得再看下SortOrder

![image-20220324103919485](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324103919485.png)

那么SortOrder就是个枚举，包含降序与升序

![image-20220324104044119](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324104044119.png)

**测试**

价格从低到高

```java
public static void querySort() throws IOException {
    SearchResponse<Product> search = EsClientUtil.getClient().search(
        b -> b.index("product")
            .sort(s -> s.field(v -> v.field("price").order(SortOrder.Asc)))
            .query(q -> q.matchAll(v -> v.queryName(""))
            ),
        Product.class);
    EsClientUtil.closeClient();
    for(Hit<Product> obj : search.hits().hits()){
        System.out.println(obj.source());
    }
}
```

sort是可以传List的或者多个的，我这里只传了一个

![image-20220324104852922](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324104852922.png)

## 11.7 分页

老样子看下原来的请求结构，基本上客户端的对象层级结构也一样。

```json
GET /goods/_search
{
  "query": {
    "match_all": {}
  }, 
  "from": 0,
  "size": 2
}
```

这个看起来好像层级结构不多from和size直接对应了基本类型，看看Java客户端文档

果然在SearchRequest对象下这两个属性直接传整数型，而是向上面嵌套很多对象。

![image-20220324105403389](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324105403389.png)

**测试**

```java
public static void queryPage() throws IOException {
    SearchResponse<Product> search = EsClientUtil.getClient().search(
        b -> b.index("product")
            .from(0)
            .size(2)
            .query(q -> q.matchAll(v -> v.queryName(""))
            ),
        Product.class);
    EsClientUtil.closeClient();
    for(Hit<Product> obj : search.hits().hits()){
        System.out.println(obj.source());
    }
}
```

![image-20220324105804165](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324105804165.png)



# 十二、Spring Data Elasticsearch

上面通过了Java客户端的学习，从开始比较模糊到现在基本相对已经熟悉。但在真正使用还是需要进一步封装接下来可以开始学习Spring提供的elasticsearch组件：Spring Data Elasticsearch。

## 12.1 什么是SpringDataElasticsearch

Spring Data Elasticsearch（以后简称SDE）是Spring Data项目下的一个子模块。

Spring Data 的使命是给各种数据访问提供POJO为核心的轻松的数据交互，不管是关系型数据库（如MySQL），还是非关系数据库（如Redis），或者类似Elasticsearch这样的索引数据库。从而简化开发人员的代码，提高开发效率。

Spring Data Elasticsearch的页面：https://spring.io/projects/spring-data-elasticsearch

特征：

* 支持Spring的基于 @Configuration 的java配置方式，或者XML配置方式
* 提供了用于操作ES的便捷工具类 ElasticsearchTemplate 。包括实现文档到POJO之间的自动智能映射。
* 利用Spring的数据转换服务实现的功能丰富的对象映射
* 基于注解的元数据映射方式，而且可扩展以支持更多不同的数据格式，可以定义JavaBean：类名、属性
* 根据持久层接口自动生成对应实现方法，无需人工编写基本操作代码（类似mybatis，根据接口自动得到实现）。当然，也支持人工定制查询



![image-20220324150935841](https://gitee.com/Jasper-zh/image_host/raw/master/image-20220324150935841.png)

{% note info simple %}

目前由于SpringBoot的版本还不支持ES8的版本不支持新的客户端，因此这一部分暂时暂停

{% endnote %}
