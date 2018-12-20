mybatis框架  

简单介绍：Mybatis是一个开源的数据持久层框架，它内部封装了通过jdbc访问数据库的操作，执行普通的sql查询，存储过程和高级映射，几乎消除了所有的jdbc代码和参数配置及结果集的检索。

接下来说说Mybatis的优缺点：
优点：与JDBC相比，减少了50%以上的代码量，最简单的持久化框架，小巧简单易学，SQL代码从程序中彻底分离，可重用。提供了XML标签，支持编写动态SQL，提供映射标签，支持对象与数据库的ORM字段映射。   
缺点：SQL语句编写量大，对于开发人员有一定的要求，数据库移植性差。  
那么什么是ORM呢？   
ORM（Object RelationMapping）即对象关系映射，是一种数据持久化技术，它在对象模型和关系型数据库之间建立对应关系。    

```
接下来来介绍一下Mybatis编译的依赖包
  asm-3.3.1.jar：操作Java字节码的类库  
  cglib-2.2.2.jar：用来动态集成Java类或实现接口
  commons-logging-1.1.1.jar：用于通用日志处理
  javassist-3.17.1-GA。jar：分析、编辑和创建java字节码的类库、
  log4j-1.2.17.jar：日志系统
  slf4j-api-1.7.5.jar：日志系统的封装，对外提供统一的API接口
  slf4j-log4j12-1.7.5.jar：slf4j对log4j的相应驱动，完成slf4j绑定log4j
```
Mybatis的核心配置文件configuration.xml 
```
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <!-- 引入database.properties -->
        <properties resource="database.properties"/>
        <!-- 配置mybatis的log实现LOG4J -->
        <settings>
            <setting name="logImpl" value="LOG4J"/>
        </settings>
        <!-- 配置mybatis多套运行环境 -->
        <environments default="development">
            <environment id="development">
                <!-- 配置事务管理，采用JDBC的事务管理 -->
                <transactionManager type="JDBC"></transactionManager>
                <!-- POOLED:mybatis自带的数据源，JNDI：基于Tomcat的数据源 -->
                <dataSource type="POOLED">
                    <property name="driver" value="${driver}"/>
                    <property name="url" value="${url}"/>
                    <property name="username" value="${username}"/>
                    <property name="password" value="${password}"/>
                </dataSource>
            </environment>
        </environments>
        <!-- 将mapper文件加入到配置文件中 -->
        <mappers>
            <mapper resource="POJO/UserMapper.xml"/>
        </mappers>
    </configuration>
```

```
mybatis-config.xml文件的几个常用元素的作用如下：

configuration：配置文件的根元素节点
properties：通过resource属性从外部指定properties属性文件（database.properties）,该属性文件描述数据库连接的相关配置（数据库驱动、连接数据库的url、数据库用户名、数据库密码），位置也是在/resource目录下。
settings：设置Mybatis运行中的一些行为，比如此处设置Mybatis的log日志实现为LOG4J，即使用log4j实现日志功能。
environments：配置Mybatis的多套运行环境，将SQL映射到多个不同的数据库上，该元素节点下可以配置多个environment子元素节点，但是必须指定其中一个默认运行环境（通过default指定）。
environment：配置Mybatis的一套运行环境，需指定ID、事务管理器、数据源配置等相关信息。
mappers：作用是告诉Mybatis去哪里找到SQL映射文件（该文件内容是开发者定义的映射SQL语句），整个项目可以有1个或者多个SQL映射文件
mapper：mappers的子元素节点，具体指定SQL映射文件的路径，其中resource属性的值表述了SQL映射文件的路径（类资源路径）
在这里我稍微提一下注意事项：mybatis-config.xml文件的元素节点是有一定的顺序，节点位置若不按照顺序排位的话，那么XML文件会报错，至于元素的排列顺序是：
1.properties  
2.settings  
3.typeAliases  
4.typeHandlers   
5.objectFactory  
6.objectWrapperFactory  
7.plugins  
8.environments?   
9.databaseIdProvider  
10.mappers
```

有了Mybatis的核心配置文件后，接下来就要准备持久化类和SQL映射文件
那么什么是持久化类呢？持久化类是指其实例状态需要被Mybatis持久化到数据库中的类，在应用的设计中，持久化类通常对应业务中的业务实体，Mybatis一般采用POJO编程模型来实现持久化类，与POJO类配合完成持久化工作是Mybatis最常见的工作模式
既然都讲到这了，我就再讲讲什么是POJO吧！
POJO（Plain Ordinary Java Object）：从字面上来说就是普通的Java对象，POJO类可以简单地理解为符合JavaBean规范的实体类，它不需要继承和实现任何特殊的Java基类或者接口，JavaBean对象的状态保存咋属性中，访问属性必须通过对应的getter和setter方法

SQL映射文件
```
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="POJO.UserMapper">
        <!-- 查询用户列表记录数 -->
        <select id="count" resultType="int">
            select count(1) as count from smbms_user
        </select>
    </mapper>
```

关于SQL映射文件的几个顶级元素配置：
顶级元素配置  
mapper：映射文件的根元素节点，只有一个属性namespace（命名空间）作用如下:用于区分不同的mapper，全局唯一绑定DAO接口，即面向接口编程。当namespace绑定某一接口之后，可以不用写该接口的实现类， MyBatis会通过接口的完全限定名查找到对应的mapper配置来执行SQL语句，因此namespace的命名必须要跟接口同名.     
cache：配置给定命名空间的缓存    
cache-ref：从其他命名空间引用缓存配置    
resultMap：用来描述数据库结果集和对象的对应关系  
sql：可以重用的SQL块，也可以被其他语句引用  
insert：映射插入语句  
update：映射更新语句  
delete：映射删除语句  
select：映射查询语句  


测试类
```
SqlSession sqlSession=null;
List<User> userList=new ArrayList<User>(); try{
    sqlSession=MyBatisUtil.createSqlSession();
    User user=new User();
    user.setUserName("赵");
    user.setUserRole(3);
    userList=sqlSession.getMapper(UserMapper.class).getUserListByUser(user);
}catch (Exception ex){
    ex.printStackTrace();
}finally {
    MyBatisUtil.closeSqlSession(sqlSession);
}
for (User user:
        userList) {
    System.out.println(user.getUserName()+"\t"+user.getUserRole())
```

