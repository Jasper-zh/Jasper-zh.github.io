---
title: JDBC综合案例
date: 2021-04-04 09:51:59
tags: 总结文档
---

## 1. 商城案例表设计

通过对商城项目的部分表关系进行分析,完成数据库表的设计<!--more-->

### 1.1 表关系分析

![](https://gitee.com/Jasper-zh/image_host/raw/master/01.jpg) 

### 1.2 建库,建表

1) 创建名为 store的数据库, 对应商城项目

```mysql
create database db6 character set utf8;
```

2) 创建用户表

```mysql
CREATE TABLE user (
  uid varchar(32) PRIMARY KEY,	-- 用户ID
  username varchar(20) , -- 用户名
  password varchar(20) , -- 密码
  telephone varchar(20) , -- 电话
  birthday date , -- 生日
  sex varchar(10) -- 性别
);
```

```mysql
INSERT INTO USER VALUES 
('001','渣渣辉','123456','13511112222','2015-11-04','男'),
('002','药水哥','123456','13533334444','1990-02-01','男'),
('003','大明白','123456','13544445555','2015-11-03','男'),
('004','长海','123456','13566667777','2000-02-01','男'),
('005','乔杉','123456','13588889999','2000-02-01','男');
```

3) 创建订单表

```mysql
CREATE TABLE orders (
  oid varchar(32)   PRIMARY KEY, -- 订单id
  ordertime datetime ,	-- 下单时间 
  total double , -- 总金额
  name varchar(20), -- 收货人姓名
  telephone varchar(20) , -- 电话
  address varchar(30) , -- 地址
  state int(11) ,  -- 订单状态
  uid varchar(32), -- 外键字段 对应用户表id
  CONSTRAINT ofk_0001 FOREIGN KEY (uid) REFERENCES user (uid)
);
```

```mysql 
-- 插入一条订单数据
INSERT INTO orders 
VALUES('order001','2019-10-11',5500,'乔杉','15512342345','皇家洗浴',0,'001');
```

4) 创建商品分类表

```mysql
CREATE TABLE category (
  cid varchar(32) PRIMARY KEY,
  cname varchar(20)
); 
```

```mysql
INSERT INTO `category` VALUES ('1','手机数码'),('2','电脑办公'),('3','运动鞋服'),('4','图书音像');
```

5) 创建商品表

```mysql
CREATE TABLE product (
  pid varchar(32)  PRIMARY KEY,	-- 商品id
  pname varchar(50) , -- 商品名称 
  price double, -- 商品价格
  pdesc varchar(255), -- 商品描述
  pflag int(11) , -- 商品状态 1 上架 ,0 下架
  cid varchar(32) , -- 外键对应 分类表id
  KEY sfk_0001 (cid), 
  CONSTRAINT sfk_0001 FOREIGN KEY (cid) REFERENCES category (cid)
);
```

```mysql
INSERT INTO `product` VALUES 
('1','小米6',2200,'小米 移动联通电信4G手机 双卡双待',0,'1'),
('2','华为Mate9',2599,'华为 双卡双待 高清大屏',0,'1'),
('3','OPPO11',3000,'移动联通 双4G手机',0,'1'),
('4','华为荣耀',1499,'3GB内存标准版 黑色 移动4G手机',0,'1'),
('5','华硕台式电脑',5000,'爆款直降，满千减百',0,'2'),
('6','MacBook',6688,'128GB 闪存',0,'2'),
('7','ThinkPad',4199,'轻薄系列1)',0,'2'),
('8','联想小新',4499,'14英寸超薄笔记本电脑',0,'2'),
('9','李宁音速6',500,'实战篮球鞋',0,'3'),
('10','AJ11',3300,'乔丹实战系列',0,'3'),
('11','AJ1',5800,'精神小伙系列',0,'3');
```

5) 订单项表 (中间表)

```mysql
-- 订单项表
CREATE TABLE orderitem (
  itemid VARCHAR(32) PRIMARY KEY, -- 订单项ID
  pid VARCHAR(32),  -- 外键 对应商品表 id
  oid VARCHAR(32), -- 外键 对应订单表 id
  KEY fk_0001 (pid),
  KEY fk_0002 (oid),
  CONSTRAINT fk_0001 FOREIGN KEY (pid) REFERENCES product (pid),
  CONSTRAINT fk_0002 FOREIGN KEY (oid) REFERENCES orders (oid)
);
```

```mysql
-- 向中间表中插入两条数据
INSERT INTO orderitem VALUES('item001','1','order001');
INSERT INTO orderitem VALUES('item002','11','order001');
```



## 2.环境搭建

### 2.1 项目结构

```
com.lagou.app 测试包 用于对DAO代码进行测试
com.lagou.dao dao包  数据访问层,包含所有对数据库的相关操作的类
com.lagou.entity 实体包 保存根据数据库表 对应创建的JavaBean类
com.lagou.utils 工具包
```

![](https://gitee.com/Jasper-zh/image_host/raw/master/03.jpg) 

### 2.2 导入所需Jar包

```
我们只需要导入myjar仓库到项目中就可以了
```

![](https://gitee.com/Jasper-zh/image_host/raw/master/04.jpg) 

### 2.3 导入配置文件及工具类

![](https://gitee.com/Jasper-zh/image_host/raw/master/05.jpg)

 

## 3. JavaBean类创建

### 3.1 设计用户与订单

#### 3.1.1 一对多关系分析

- 在Java一对多的数据关系中，需要遵循以下设计原则：

- 1. Java类的名称 = 实体表的名称
  2. Java类的属性 = 实体表的字段
  3. Java类的一个对象 = 表的一行记录
  4. 外键关系 = 引用配置

- 一个用户拥有多个订单,所以 用户是一的一方, 订单是多的一方

  ![](https://gitee.com/Jasper-zh/image_host/raw/master/06.jpg) 

#### 3.1.2 User类

```java
/**
 *    用户表 对应 User类
 *   `uid` VARCHAR(32) NOT NULL,
 *   `username` VARCHAR(20) DEFAULT NULL,
 *   `password` VARCHAR(20) DEFAULT NULL,
 *   `telephone` VARCHAR(20) DEFAULT NULL,
 *   `birthday` DATE DEFAULT NULL,
 *   `sex` VARCHAR(10) DEFAULT NULL,
 * */
public class User {

    private String uid;

    private String username;

    private String password;

    private String telephone;

    private String birthday;

    private String sex;

  	//提供 get set toString方法
}
```

#### 3.1.3 Orders类

```java
/**
 *   订单表
 *   `oid` VARCHAR(32) NOT NULL,
 *   `ordertime` DATETIME DEFAULT NULL,
 *   `total` DOUBLE DEFAULT NULL,
 *   `name` VARCHAR(20) DEFAULT NULL,
 *   `telephone` VARCHAR(20) DEFAULT NULL,
 *   `address` VARCHAR(30) DEFAULT NULL,
 *   `state` INT(11) DEFAULT NULL,
 *   `uid` VARCHAR(32) DEFAULT NULL,
 *
 * */
public class Orders {

    private String oid; //订单号
    private String ordertime; //下单时间
    private double total; //订单的总金额
    private String name; //收货人姓名
    private String telephone; //收货人电话
    private String address;  //收货人地址
    private int state; //订单状态 1 代表已支付 , 0 代表未支付

    //订单属于哪个用户呢 ?
    
    //提供 get set toString方法
}
```

####  3.1.4 Orders类设计分析

- 第一种方式

  - 根据两张表关系的描述  我们可以在 订单类中 添加一个uid 成员变量,表示订单属于哪个用户

    ```java
    private String uid;
    ```

  - 但是这样设计会存在一些问题,比如 我要查询的是订单是属于哪个用户的用户名 ? 但是我们只有一个uid

- 第二种方式

  - Java类表示一对多关系,可以在多的一方添加一个成员变量,这个成员变量的类型 就是一的一方的类型.

  - 再在订单表中 添加一个 User对象,User对象中 ,保存该订单关联的用户的所有信息

    ```java
    private String uid;
    private User user;
    ```

#### 3.1.4 修改Orders类

```java
public class Orders {

    private String oid; //订单号
    private String ordertime; //下单时间
    private double total; //订单的总金额
    private String name; //收货人姓名
    private String telephone; //收货人电话
    private String address;  //收货人地址
    private int state; //订单状态 1 代表已支付 , 0 代表未支付

    //订单属于哪个用户呢 ?
    private String uid; //表示外键
    private User user; //用来保存订单对应的详细的用户信息
    
    //提供 get set toString方法
}
```

### 3.2 设计商品与分类

分类与商品 同样是一对多关系,  我们可以在多的一方进行操作 添加一个成员变量 类型是一的一方的类型

![](https://gitee.com/Jasper-zh/image_host/raw/master/07.jpg) 

#### 3.2.1 Category类

```java
public class Category {

    private String cid;
    private String cname;
    
    //提供 get set toString方法
}
```

#### 3.2.2 Product类

```java
public class Product {

    private String pid;

    private String pname;

    private double price;

    private String pdesc;

    private int pflag; //是否上架 1 上架 ,0 下架

    private String cid; //外键 对应分类表主键

    private Category category; //用于保存Category的详细数据
    
     //提供 get set toString方法
}
```

### 3.3 设计订单项

#### 3.3.1 多对多关系分析

商品与订单是多对多关系, 一个订单上可以有多个商品, 一个商品可以出现在多个订单中.

多对多建表原则 需要一张中间表,中间表中至少有两个字段,作为中间表的外键分别指向另外两张表的主键

![](https://gitee.com/Jasper-zh/image_host/raw/master/11.jpg)  

#### 3.3.2 创建OrderItem

```java
/**
 *   订单项表(中间表)
 *   `itemid` VARCHAR(32) NOT NULL,
 *   `pid` VARCHAR(32) DEFAULT NULL,
 *   `oid` VARCHAR(32) DEFAULT NULL,
 *
 * */
public class OrderItem {

    //订单项 指的是中间表中的一条数据
    private String itemid; //订单项的id

    private String pid; //外键 指向商品表主键

    private String oid; //外键 指向订单表的主键
    
    private Product product;//订单项内部的商品详细信息

    private Orders orders;//订单项属于哪个订单
    
}
```



## 4.编写DAO类

### 4.1 UserDao

- 需求一: 编写一个注册用户的方法,接收的参数是一个User对象
- 需求二: 编写一个 用户登录的方法,接收的参数是 用户名 和密码, 返回值是User对象

#### 4.1.1 编写UserDao

```java
public class UserDao {

    /**
     * 注册用户
     * */
    public int register(User user) throws SQLException {

        //1.获取QueryRunner
        QueryRunner qr = new QueryRunner(DruidUtils.getDataSource());

        //2.编写SQL
        String sql = "insert into user values(?,?,?,?,?,?)";

        Object[] param = {user.getUid(), user.getUsername(), user.getPassword(),
                user.getTelephone(), user.getBirthday(), user.getSex()};

        //3.执行插入操作
        int update = qr.update(sql,param);

        //4.返回受影响的行数
        return update;
    }

    /**
     * 用户注册
     * */
    public User login(String username , String password) throws SQLException {

        QueryRunner qr = new QueryRunner(DruidUtils.getDataSource());

        String sql = "select * from user where username = ? and password = ?";

        //返回的是一个User对象 使用BeanHandler将结果集的第一条和数据封装到一个Javabean中
        User user = qr.query(sql, new BeanHandler<User>(User.class), username, password);

        return user;
    }

}
```

#### 4.1.2 测试注册与登录功能

```java
public class TestUserDao {

    //创建UserDao
    UserDao userDao = new UserDao();

    //测试注册功能
    @Test
    public void testRegister() throws SQLException {

        //1. 创建User对象
        User user = new User();

        //2. 对User对象进行赋值
        user.setUid(UUIDUtils.getUUID());
        user.setUsername("大郎");
        user.setPassword("654321");
        user.setTelephone("15052005200");
        user.setSex("男");
        user.setBirthday(DateUtils.getDateFormart());

        //3.执行注册
        int register = userDao.register(user);

        //4.判断注册是否成功
        if(register > 0){
            System.out.println("注册成功,欢迎您: " + user.getUsername());
        }else{
            System.out.println("注册失败! !");
        }

    }

    //测试登录功能
    @Test
    public void testLogin() throws SQLException {

        //调用UserDao的 login方法,传入用户名密码
        User user = userDao.login("大郎", "654321");

        //判断user不为空 登录成功
        if(user != null){
            System.out.println(user.getUsername() +" 欢迎您!");
        }else{
            System.out.println("用户名或者密码错误! !");
        }

    }
}
```

### 4.2 ProductDao

- 需求1: 根据商品ID 获取商品名称 ,商品价格 以及商品所属分类的名称
  - 参数 pid, 返回值 product对象

- 需求2: 根据分类ID 获取商品分类信息
  - 参数 cid , 返回值 category对象

- 需求3: 查询指定分类ID 下的商品个数
  - 参数 cid , 返回值 int类型 商品个数

- 需求4: 查询指定分类ID 下的所有商品信息
  - 参数分类ID ,返回值 List集合 集合中保存商品对象

#### 4.2.1 编写 ProductDao

```java
public class ProductDao {

    //1.根据商品ID 获取商品名称 ,商品价格 以及商品所属分类的名称
    public Product findProductById(String pid) throws SQLException {

        QueryRunner qr = new QueryRunner(DruidUtils.getDataSource());

        String sql = "select * from product where pid = ?";

        Product product = qr.query(sql, new BeanHandler<Product>(Product.class), pid);

        //调用 findCategoryById()方法, 传递外键cid 获取商品对应 的分类信息
        Category category = findCategoryById(product.getCid());

        //将category保存到商品对象中
        product.setCategory(category);

        return product;
    }

    //2.根据分类ID 获取商品分类信息
    public Category findCategoryById(String cid) throws SQLException {

        QueryRunner qr = new QueryRunner(DruidUtils.getDataSource());

        String sql = "select * from category where cid = ?";

        Category category = qr.query(sql, new BeanHandler<Category>(Category.class),cid);

        return category;
    }

    //3.查询指定分类ID 下的商品个数
    public int getCount(String cid) throws SQLException {
        QueryRunner qr = new QueryRunner(DruidUtils.getDataSource());

        String sql = "select count(*) from product where cid = ?";

        //获取的单列数据 ,使用ScalarHandler 封装
        Long count = (Long)qr.query(sql,new ScalarHandler<>(),cid);

        //将Lang类型转换为 int 类型,并返回
        return count.intValue();
    }


    //4.查询指定分类下的所有商品信息
    public List<Product> findProductByCid(String cid) throws SQLException {
        
        QueryRunner qr = new QueryRunner(DruidUtils.getDataSource());
        
        String sql = "select * from product where cid = ?";

        //查询结果是一个List集合, 使用BeanListHandler 封装结果集
        List<Product> list = qr.query(sql, new BeanListHandler<Product>(Product.class), cid);
        
        return list;
    }
}

```

#### 4.2.2 测试 ProductDao

```java
public class TestProductDao {

    ProductDao productDao = new ProductDao();

    //1.测试 根据商品ID 获取商品名称 ,商品价格 以及商品所属分类的名称
    @Test
    public void testFindProductById() throws SQLException {

        Product product = productDao.findProductById("1");

        System.out.println("商品名称: "+product.getPname()+ ", 商品价格: " + product.getPrice() +
                ", 商品所属分类: "+ product.getCategory().getCname());
    }


    //2.测试 查询指定分类ID下的商品数
    @Test
    public void testGetCount() throws SQLException {
        //查询 cid为3的分类下有多少个商品
        int count = productDao.getCount("3");
        System.out.println("分类ID为3的分类下商品个数: " + count);
    }

    //3.测试 查询指定分类下的所有商品信息
    @Test
    public void testFindProductByCid() throws SQLException {

        //查询cid为 2的分类下 所有的商品信息
        List<Product> list = productDao.findProductByCid("2");

        for (Product product : list) {
            System.out.println(product);
        }

    }
}
```

### 4.3 OrdersDao

#### 4.3.1 多对一分析

OrderItem表与Orders表的关系是 多对一

![](https://gitee.com/Jasper-zh/image_host/raw/master/09.jpg) 

之前我们一直是在描述一对多,那么我们再反向描述一下 多对一

方式是在Orders中应该有一个 集合用来保存订单中的订单项信息

![](https://gitee.com/Jasper-zh/image_host/raw/master/10.jpg) 

在Orders类中添加 订单项的集合

```java
 	//该订单中有多少订单项
    List<OrderItem> orderItems = new ArrayList<OrderItem>();

    public List<OrderItem> getOrderItems() {
        return orderItems;
    }

    public void setOrderItems(List<OrderItem> orderItems) {
        this.orderItems = orderItems;
    }
```



#### 4.3.2 创建OrdersDao

- 需求1: 获取 uid为 001 的用户的所有订单信息

- 参数 uid, 返回值 LIst<Orders> 订单集合 

- 需求2: 获取订单编号为 order001的订单中的所有商品信息

  - 参数 oid, 返回值List<Product> 商品集合

  ```sql
  -- 获取订单编号为: order001的订单中的所有商品信息
  
  -- 1.查询订单项表中 oid是order001的 所有商品信息
  SELECT 
  	oi.pid
  FROM orderitem oi WHERE oid = 'order001';
  
  -- 2.将上面的查询语句作为in函数的条件, 查询product表
  SELECT * FROM product WHERE pid IN
  (SELECT 
  	oi.pid
  FROM orderitem oi WHERE oid = 'order001');
  ```

```java
public class OrdersDao {

    //需求1: 获取 uid为 001 的用户的所有订单信息
    public List<Orders>  findAllOrders(String uid) throws SQLException {

        QueryRunner qr = new QueryRunner(DruidUtils.getDataSource());

        String sql = "select * from orders where uid = ?";

        //一个用户所有的订单信息
        List<Orders> ordersList = qr.query(sql, new BeanListHandler<Orders>(Orders.class), uid);

        return ordersList;
    }

    //需求2: 获取订单编号为 order001的订单中的所有商品信息
    public List<Product> findOrderById(String oid) throws SQLException {

        QueryRunner qr = new QueryRunner(DruidUtils.getDataSource());

        //1.查询订单项表 获取订单项表中 订单ID为order001的数据
        String sql = "SELECT pid FROM orderitem WHERE oid = ? ";

        //2.查询的结果是 多条订单项数据
        List<OrderItem> list = qr.query(sql, new BeanListHandler<OrderItem>(OrderItem.class), oid);

        //3.创建集合保存商品信息
        List<Product> productList = new ArrayList<>();

        ProductDao productDao = new ProductDao();

        //4.遍历订单项集合 获取Pid
        for (OrderItem orderItem : list) {

            //4.1从orderitem中获取 pid
            String pid = orderItem.getPid();

            //4.2 调用productDao
            Product product = productDao.findProductById(pid);

            //4.3 保存到集合
            productList.add(product);
        }

        //返回 订单中对应的商品信息
        return productList;
    }


}
```

#### 4.3.3 测试OrdersDao

```java
public class TestOrderDao {

    OrdersDao ordersDao = new OrdersDao();

    //1.获取 uid为 001 的用户的所有订单信息
    @Test
    public void testFindAllOrders() throws SQLException {

        List<Orders> allOrders = ordersDao.findAllOrders("001");

        //遍历打印订单信息
        for (Orders order : allOrders) {
            System.out.println(order);
        }
    }

    //测试 获取订单编号为: order001的订单中的所有商品信息
    @Test
    public void testFindOrderById() throws SQLException {

        List<Product> list = ordersDao.findOrderById("order001");
        System.out.println("订单编号为order001中的商品有: ");
        for (Product product : list) {
            System.out.println(product);
        }
    }
}
```
