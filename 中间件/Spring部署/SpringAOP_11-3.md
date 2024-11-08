# AOP（面向切面编程）介绍

## 1. 什么是AOP

AOP的全称是**Aspect Oriented Programming**，即**面向切面编程**。它是一种实现功能统一维护的技术，旨在将业务逻辑的各个部分进行隔离，使开发人员在编写业务逻辑时可以专注于核心业务，从而提高开发效率。

### 作用

在不修改源代码的基础上，对已有方法进行增强。

### 实现原理

AOP的实现主要依赖于**动态代理技术**。

### 优势

- 减少重复代码
- 提高开发效率
- 维护方便

### 应用场景

- 事务处理
- 日志管理
- 权限控制
- 异常处理

## 2. AOP相关术语

为了更好地理解AOP，以下是一些相关术语的解释：

| 名称         | 说明                                                      |
|--------------|---------------------------------------------------------|
| Joinpoint    | 指能被拦截的点，在Spring中只有方法能被拦截。              |
| Pointcut     | 指要对哪些连接点进行拦截，即被增强的方法。                |
| Advice       | 指拦截后要执行的操作，即切点被拦截后执行的方法。           |
| Aspect       | 切点与通知的组合称为切面。                                |
| Target       | 被代理的对象。                                          |
| Proxy        | 代理对象。                                              |
| Weaving      | 生成代理对象的过程。                                    |

## 3. AOP入门

### AspectJ

**AspectJ**是一个基于Java语言的AOP框架，建议在Spring框架中使用AspectJ实现AOP。

### 示例：打印日志

以下是一个简单的AOP示例，实现在DAO层的每个方法结束后打印一条日志。

#### 引入依赖

```xml
<!-- spring -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>6.0.11</version>
</dependency>
<!-- AspectJ -->
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.8.7</version>
</dependency>
<!-- junit  -->
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
</dependency>
<!-- Spring整合测试模块 -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-test</artifactId>
  <version>6.0.11</version>
</dependency>
```

#### 编写连接点

```java
@Repository
public class UserDao {
  public void add() {
    System.out.println("用户新增");
  }
  
  public void delete() {
    System.out.println("用户删除");
  }
  
  public void update() {
    System.out.println("用户修改");
  }
}
```

#### 编写通知类

```java
public class MyAspectJAdvice {
  // 后置通知
  public void myAfterReturning() {
    System.out.println("打印日志...");
  }
}
```

#### 配置切面

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

  <context:component-scan base-package="com.jjy"></context:component-scan>

  <!-- 通知对象 -->
  <bean id="myAspectJAdvice" class="com.jjy.advice.MyAspectJAdvice"></bean>

  <!-- 配置AOP -->
  <aop:config>
    <!-- 配置切面 -->
    <aop:aspect ref="myAspectJAdvice">
      <!-- 配置切点 -->
      <aop:pointcut id="myPointcut" expression="execution(* com.jjy.dao.UserDao.*(..))"/>
      <!-- 配置通知 -->
      <aop:after-returning method="myAfterReturning" pointcut-ref="myPointcut"/>
    </aop:aspect>
  </aop:config>
</beans>
```

### 测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class UserDaoTest {
  @Autowired
  private UserDao userDao;

  @Test
  public void testAdd() {
    userDao.add();
  }

  @Test
  public void testDelete() {
    userDao.delete();
  }

  @Test
  public void testUpdate() {
    userDao.update();
  }
}
```

## 4. 通知类型

AOP常用的通知类型包括：

| 通知类型   | 描述                        |
|------------|-----------------------------|
| 前置通知   | 在方法执行前添加功能。      |
| 后置通知   | 在方法正常执行后添加功能。  |
| 异常通知   | 在方法抛出异常后添加功能。  |
| 最终通知   | 无论方法是否抛出异常，都会执行该通知。 |
| 环绕通知   | 在方法执行前后添加功能。    |

### 编写通知方法

```java
// 通知类
public class MyAspectAdvice {
  // 后置通知
  public void myAfterReturning(JoinPoint joinPoint) {
    System.out.println("切点方法名：" + joinPoint.getSignature().getName());
    System.out.println("目标对象：" + joinPoint.getTarget());
    System.out.println("打印日志" + joinPoint.getSignature().getName() + "方法被执行了！");
  }

  // 前置通知
  public void myBefore() {
    System.out.println("前置通知...");
  }

  // 异常通知
  public void myAfterThrowing(Exception ex) {
    System.out.println("异常通知...");
    System.err.println(ex.getMessage());
  }

  // 最终通知
  public void myAfter() {
    System.out.println("最终通知");
  }

  // 环绕通知
  public Object myAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
    System.out.println("环绕前");
    Object obj = proceedingJoinPoint.proceed(); // 执行方法
    System.out.println("环绕后");
    return obj;
  }
}
```

### 配置切面

```xml
<!-- 配置AOP -->
<aop:config>
  <!-- 配置切面 -->
  <aop:aspect ref="myAspectJAdvice">
    <!-- 配置切点 -->
    <aop:pointcut id="myPointcut" expression="execution(* com.jjy.dao.UserDao.*(..))"/>
    <!-- 前置通知 -->
    <aop:before method="myBefore" pointcut-ref="myPointcut"/>
    <!-- 后置通知 -->
    <aop:after-returning method="myAfterReturning" pointcut-ref="myPointcut"/>
    <!-- 异常通知 -->
    <aop:after-throwing method="myAfterThrowing" pointcut-ref="myPointcut" throwing="ex"/>
    <!-- 最终通知 -->
    <aop:after method="myAfter" pointcut-ref="myPointcut"/>
    <!-- 环绕通知 -->
    <aop:around method="myAround" pointcut-ref="myPointcut"/>
  </aop:aspect>
</aop:config>
```

## 5. 切点表达式

AspectJ使用切点表达式来配置切点位置，写法如下：

- **标准写法**：`方法的访问修饰符 方法返回值 包名.类名.方法名(参数列表)`
- 访问修饰符可以省略。
- 返回值使用`*`代表任意类型。
- 包名使用`*`表示任意包，多级包结构要写多个，使用`*..*`表示任意包结构。
- 类名和方法名都可以用`*`实现通配。
- 参数列表：
  - 基本数据类型直接写类型。
  - 引用类型写`包名.类名`。
  - `*`表示匹配一个任意类型参数。
  - `...`表示匹配任意类型任意个数的参数。

**全通配**：`*..*(...)`

## 6. 多切面配置

我们可以为切点配置多个通知，形成多切面，例如希望在DAO层的每个方法结束后打印日志并发送邮件。

### 编写发送邮件的通知：

```java
public class MyAspectJAdvice2 {
  // 后置通知
  public void myAfterReturning(JoinPoint joinPoint) {
    System.out.println("发送邮件");
  }
}
```

### 配置切面：

```xml


<bean id="myAspectJAdvice2" class="com.jjy.advice.MyAspectJAdvice2"></bean>
<aop:aspect ref="myAspectJAdvice2">
  <aop:pointcut id="myPointcut" expression="execution(* com.jjy.dao.UserDao.*(..))"/>
  <aop:after-returning method="myAfterReturning" pointcut-ref="myPointcut"/>
</aop:aspect>
```

## 7. 总结

AOP为开发人员提供了一种强大的方式，以便在业务逻辑与横切关注点之间建立明确的界限，使得代码更为整洁，易于维护。AOP通过其连接点、切点、通知等概念，帮助开发者有效地管理应用程序中的横切关注点。

---

这样处理后，内容结构更加清晰，逻辑更流畅，代码示例也添加了必要的注释，方便阅读和理解。如果还有其他具体需求，请随时告诉我！

