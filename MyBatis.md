# Mybatis

## <-!--sql-config核心配置--->

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```



<--! 映射前置--->

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper>

</mapper>
```

从 XML 中构建 SqlSessionFactory

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```



<--!properties配置-->

```xml
<-!> 需要在外部创建一个.properties</-!>
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>

<-!--> properties</-!-->

jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/newhelp
jdbc.username=root
jdbc.password=123

```

```xml
<-!->设置好的属性可以在整个配置文件中用来替换需要动态配置的属性值</-!->

<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, props);

// ... 或者 ...

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, props);
```

<--! 设置别名 可以快速调取 不用设置相对路径-->

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
<-!->可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean</-!->
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>


```

```java
//每一个在包 domain.blog 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名
@Alias("author")
public class Author {
    ...
}
```

Enviroments可以配置多个enviroment





映射无法找到 xml文件未出现在target目录

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>

        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.poroperties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```



log4j配置

```xml
#将等级为DEBUG的日志信息输出到console和file两个目的地
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.Target=System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=【%c】-%m%n

#文件输出的相关配置
log4j.appender.file=org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/kuang.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=【%p】[%d{yy-MM-dd}【%c】%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

```xml

<!--映射路径可以改变-->

<!--    <mappers>-->
<!--        <mapper class=""/>-->
<!--    </mappers>-->
<!--    <mappers>-->
<!--        <mapper url=""/>-->
<!--    </mappers>-->
```

核心文件属性配置顺序

```
The content of element type "configuration" must match "(properties?,settings?,typeAliases?,typeHandlers?,objectFactory?,objectWrapperFactory?,reflectorFactory?,plugins?,environments?,databaseIdProvider?,mappers?)".
```

## typeAliases

创建类名缩写

```xml
<!--   <typeAliases>-->
<!--       <typeAlias alias="AsyuDao" type ="com.qzjm.demo.dao.AstuDao" />-->
<!--   </typeAliases>-->
```

```xml
<!-- lombok依赖 -->

<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.20</version>
    <scope>provided</scope>
</dependency>
```

```xml
<!——MyBatis 会在幕后自动创建一个 ResultMap，再根据属性名来映射列到 JavaBean 的属性上。如果列名和属性名不能匹配上，可以在 SELECT 语句中设置列别名（这是一个基本的 SQL 特性）来完成匹配——>

<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>
```

```xml
<select id="selectUsers" resultMap="userResultMap">
  select user_id, user_name, hashed_password
  from some_table
  where id = #{id}
</select>

```



## 增删改查

```xml
    <select id="FindStudent" resultMap="studentTeacher">
select student.id as id,student.name as  name from class.student
    </select>

    <resultMap id="studentTeacher" type="student">
        <result property="id" column="id"/>
        <result property="name" column="name"/>
        <association property="teacher" column="tname" javaType="teacher" select="FindTeacher"/>
    </resultMap>
    <select id="FindTeacher" resultType="teacher">
select  teacher.name as tname from class.teacher where id = #{tid}
    </select>


    <insert id="insertStudent" >
        insert into class.student(name,tid)  values( #{name},#{tid} );
    </insert>


<update id="updateStudent">
    update class.student set name = #{name} where id = #{id}


</update>
    <delete id="deleteStudent">
delete from  class.student where student.id = #{id}

    </delete>
```



## 多对一和一对多查询方式   

一

property 为类名属性

column为sql语句中的属性名

```xml
x<select id="FindStudent" resultMap="teacherMap"   >
    select s.id,s.name,t.name  teacherName  from  class.student s,class.teacher t where s.tid = t.id
                                     and s.tid = #{tid}
</select>
<resultMap id="teacherMap" type="student" >
    <result property="id" column="id"/>
    <result property="name" column="name" />

    <association property="teacher" javaType="teacher">
        <result property="name" column="teacherName"/>
    </association>

    
</resultMap>
```

二

```xml
        <select id="getAllStudent" resultMap="studentTeacher">
            select * from student
        </select>
       <resultMap id="studentTeacher" type="Student">
            <result property="id" column="id" />
            <result property="name" column="name" />
            <association property="teacher" column="tid" select="getTeacher"/>
        </resultMap>
        <select id="getTeacher" resultType="Teacher">
            select * from teacher where id=#{tid}
        </select>
```





## 分页

```xml
<!-- 可以进行Map使用  -->
<select id="limitStudent" resultType="student" parameterType="map">
    select  * from student limit  #{sPage},#{ePage}

</select>
```

关键字limit   ，row bounds





## 动态sql语句



if

使用动态 SQL 最常见情景是根据条件包含 where 子句的一部分

```xml
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

choose、when、otherwise

有时候，我们不想使用所有的条件，而只是想从多个条件中选择一个使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>	
      AND featured = 1
    </otherwise>
  </choose>
</select>
```



trim、where、set

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```



foreach

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

## cache

一级缓存

- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。





二级缓存

默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。 要启用全局的二级缓存，只需要在你的 SQL 映射文件中添加一行：

```xml
1.<cache/>

2.自定义<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

