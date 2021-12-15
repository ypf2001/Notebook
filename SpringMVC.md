```xml

```

ssm整合

dao

```xml
<?xml version="1.0" encoding="UTF8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--开启注解-->
    <context:component-scan base-package="com.qzjm"/>
    <!--    关联数据库文件-->
    <context:property-placeholder location="database.properties"/>
    <!--配置session工厂-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 配置连接池属性 -->
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <!-- c3p0连接池的私有属性 -->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!-- 关闭连接后不自动commit -->
        <property name="autoCommitOnClose" value="false"/>
        <!-- 获取连接超时时间 -->
        <property name="checkoutTimeout" value="10000"/>
        <!-- 当获取连接失败重试次数 -->
        <property name="acquireRetryAttempts" value="2"/>
    </bean>


    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:/mybatis-config.xml"/>
    </bean>
    <!--扫描dao接口-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 注入sqlSessionFactory -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!-- 给出需要扫描Dao接口包 -->
        <property name="basePackage" value="com.qzjm.dao"/>
    </bean>
    <!--配置事务管理器-->
<!--    <bean id="TransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">-->
<!--        <property name="dataSource">-->
<!--            <ref bean="dataSource"/>-->
<!--        </property>-->
<!--    </bean>-->
    <!--配置事务通知-->
    <!-- 4.配置扫描Dao接口包，动态实现Dao接口注入到spring容器中 -->
    <!--解释 ：https://www.cnblogs.com/jpfss/p/7799806.html-->

<!--    <tx:advice id="txAdvice" transaction-manager="TransactionManager">-->
<!--        <tx:attributes>-->
<!--            &lt;!&ndash;select方法&ndash;&gt;-->
<!--            <tx:method name="find" read-only="true"/>-->
<!--            <tx:method name="*" isolation="DEFAULT"/>-->
<!--        </tx:attributes>-->
<!--    </tx:advice>-->
    <!--配置AOP相关增强-->



</beans>
```

service

```xml
<?xml version="1.0" encoding="UTF8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 扫描service相关的bean -->
    <context:component-scan base-package="com.qzjm.service"/>

    <!--BookServiceImpl注入到IOC容器中-->
    <bean id="bookServiceImpl" class="com.qzjm.service.bookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>


    <!-- 配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>

    </bean>

</beans>
```

mvc

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">
     <!--扫描Controller相关注解-->
    <context:component-scan base-package="com.xiaohe">
        <!--表示只扫描Controller注解-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    <!--配置视图解析器-->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--配置前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"></property>
        <!--配置后缀-->
        <property name="suffix" value=".jsp"></property>
    </bean>
    <!--设置静态代码不过滤-->
    <mvc:default-servlet-handler/>
    <!--开启Spring注解支持-->
    <mvc:annotation-driven />
</beans>x
```

