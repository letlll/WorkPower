# 什么是Spring
Spring是一个开源框架，为简化企业级开发而生。它以IOC（控制反转）和AOP（面向切面）为思想内核，提供了控制层SpringMVC、数据层SpringData、服务层事务管理等众多技术，并可以整合众多第三方框架。

Spring将很多复杂的代码变得优雅简洁，有效的降低代码的耦合度，极大的方便项目的后期维护、升级和扩展。

Spring官网地址：https://spring.io/.\

Spring体系结构

Spring框架根据不同的功能被划分成了多个模块，这些模块可以满足一切企业级应用开发的需求，在开发过程中可以根据需求有选择性地使用所需要的模块。

Core Container：Spring核心模块，任何功能的使用都离不开该模块，是其他模块建立的基础。

Data Access/Integration：该模块提供了数据持久化的相应功能。

Web：该模块提供了web开发的相应功能。

AOP：提供了面向切面编程实现

Aspects：提供与AspectJ框架的集成，该框架是一个面向切面编程框架。

Instrumentation：提供了类工具的支持和类加载器的实现，可以在特定的应用服务器中使用。

Messaging：为Spring框架集成一些基础的报文传送应用

dTest：提供与测试框架的集成

IOC__控制反转思想
IOC(Inversion of Control) ：程序将创建对象的权利交给框架。

之前在开发过程中，对象实例的创建是由调用者管理的，代码如下

```java
public interface StudentDao {
  // 根据id查询学生
  Student findById(int id);
}


public class StudentDaoImpl implements StudentDao{
  @Override
  public Student findById(int id) {
    // 模拟从数据库查找出学生
    return new Student(1,"zbdx","山西");
   }
}


public class StudentService {
  public Student findStudentById(int id){
    // 此处就是调用者在创建对象
    StudentDao studentDao = new StudentDaoImpl();
    return studentDao.findById(1);
   }
}
```

这种写法有两个缺点：

浪费资源：StudentService调用方法时即会创建一个对象，如果不断调用方法则会创建大量StudentDao对象。

代码耦合度高：假设随着开发，我们创建了StudentDao另一个更加完善的实现类StudentDaoImpl2，如果在StudentService中想使用StudentDaoImpl2，则必须修改源码。

而IOC思想是将创建对象的权利交给框架，框架会帮助我们创建对象，分配对象的使用，控制权由程序代码转移到了框架中，控制权发生了反转，这就是Spring的IOC思想。而IOC思想可以完美的解决以上两个问题。

自定义对象容器

接下来我们通过一段代码模拟IOC思想。创建一个集合容器，先将对象创建出来放到容器中，需要使用对象时，只需要从容器中获取对象即可，而不需要重新创建，此时容器就是对象的管理者。

1.创建实体类

```java
public class Student {
  private int id;
  private String name;
  private String address;
    // 省略getter/setter/构造方法/tostring
}
2.创建Dao接口和实现类

public interface StudentDao {
  // 根据id查询学生
  Student findById(int id);
}

public class StudentDaoImpl implements StudentDao {
  @Override
  public Student findById(int id) {
    // 模拟从数据库查找出学生
    return new Student(1,"zbdx","山西");
   }
}

public class StudentDaoImpl2 implements StudentDao {
  @Override
  public Student findById(int id) {
    // 模拟从数据库查找出学生
    System.out.println("新方法！！！");
    return new Student(1,"zbdx","山西");
   }
}
```

3.创建配置文件bean.properties，该文件中定义管理的对象

```
studentDao=com.jjy.dao.impl.StudentDaoImpl
```

4.创建容器管理类，该类在类加载时读取配置文件，将配置文件中配置的对象全部创建并放入容器中。

```java
public class Container {
  static Map<String,Object> map = new HashMap<>();
  static {
    // 读取配置文件
    InputStream is = Container.class.getClassLoader().getResourceAsStream("bean.properties");
    Properties properties = new Properties();
    try {
      properties.load(is);
     } catch (IOException e) {
      throw new RuntimeException(e);
     }

    // 遍历配置文件的所有配置信息
    Enumeration<Object> keys = properties.keys();
    while (keys.hasMoreElements()){
      String key = keys.nextElement().toString();
      String value = properties.getProperty(key);
      try {
        // 创建对象，将对象放入集合
        Object o = Class.forName(value).newInstance();
        map.put(key,o);
       } catch (Exception e) {
        e.printStackTrace();
       }
     }

   }

  // 从容器中获取对象
  public static Object getBean(String key){
    return map.get(key);
   }
}


创建Dao对象的调用者StudentService

public class StudentService {
  public Student findStudentById(int id){
    StudentDao studentDao = (StudentDao) Container.getBean("studentDao");
    System.out.println(studentDao.hashCode());
    return studentDao.findById(id);
   }
}

测试StudentService

public class Test {
  public static void main(String[] args) {
    StudentService studentService = new StudentService();
    System.out.println(studentService.findStudentById(1));
    System.out.println(studentService.findStudentById(1));
   }
}
```

测试结果：

StudentService从容器中每次拿到的都是同一个StudentDao对象，节约了资源。
如果想使用StudentDaoImpl2对象，只需要修改bean.properties的内容为
studentDao=com.itbaizhan.dao.StudentDaoImpl2
即可，无需修改Java源码。
Spring实现IOC
接下来我们使用Spring实现IOC，Spring内部也有一个容器用来管理对象。

1.创建Maven工程，引入依赖

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.0.11</version>
  </dependency>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

2.创建POJO类、Dao类和接口

```java
public class Student {
  private int id;
  private String name;
  private String address;
    // 省略getter/setter/构造方法/tostring
}

public interface StudentDao {
  // 根据id查询学生
  Student findById(int id);
}

public class StudentDaoImpl implements StudentDao{
  @Override
  public Student findById(int id) {
    // 模拟从数据库查找出学生
    return new Student(1,"zbdx","山西");
   }
}
```

3.编写xml配置文件，配置文件中配置需要Spring帮我们创建的对象。

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="studentDao" class="com.jjy.dao.StudentDaoImpl"></bean>
  
</beans>

4.测试从Spring容器中获取对象。

```java
public class TestContainer {
  @Test
  public void t1(){
    // 创建Spring容器
    ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
    // 从容器获取对象
    StudentDao studentDao1 = (StudentDao) ac.getBean("studentDao");
    StudentDao studentDao2 = (StudentDao) ac.getBean("studentDao");
    System.out.println(studentDao1.hashCode());
    System.out.println(studentDao2.hashCode());
    System.out.println(studentDao1.findById(1));
   }
}
```

Spring容器类型

容器接口

BeanFactory：BeanFactory是Spring容器中的顶层接口，它可以对Bean对象进行管理。

ApplicationContext：ApplicationContext是BeanFactory的子接口。它除了继承 BeanFactory的所有功能外，还添加了对国际化、资源访问、事件传播等方面的良好支持。

ApplicationContext有以下三个常用实现类：

容器实现类

ClassPathXmlApplicationContext：该类可以从项目中读取配置文件

FileSystemXmlApplicationContext：该类从磁盘中读取配置文件

AnnotationConfigApplicationContext：使用该类不读取配置文件，而是会读取注解

@Test
```java
public void t2(){
  // 创建spring容器
  //     ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
  ApplicationContext ac = new FileSystemXmlApplicationContext("C:\\Users\\a\\IdeaProjects\\spring_demo\\src\\main\\resources\\bean.xml");
  
  // 从容器中获取对象
  StudentDao userDao = (StudentDao) ac.getBean("studentDao");
  System.out.println(userDao);
  System.out.println(userDao.findById(1));
}
```

对象的创建方式
Spring会帮助我们创建bean，那么它底层是调用什么方法进行创建的呢？

使用构造方法

Spring默认使用类的空参构造方法创建bean：

```java
// 假如类没有空参构造方法，将无法完成bean的创建
public class StudentDaoImpl implements StudentDao{
  public StudentDaoImpl(int a){}
  
  @Override
  public Student findById(int id) {
    // 模拟根据id查询学生
    return new Student(1,"zbdx","山西");
   }
}
```

使用工厂类的方法
Spring可以调用工厂类的方法创建bean：

1.创建工厂类，工厂类提供创建对象的方法：

```java
public class StudentDaoFactory {
  public StudentDao getStudentDao(){
    return new StudentDaoImpl(1);
   }
}
```

2.在配置文件中配置创建bean的方式为工厂方式。

```html
<!--  id：工厂对象的id，class：工厂类  -->
<bean id="studentDaoFactory" class="com.jjy.dao.StudentDaoFactory"></bean>
<!--  id：bean对象的id，factory-bean：工厂对象的id，factory-method：工厂方法 -->
<bean id="studentDao" factory-bean="studentDaoFactory" factory-method="getStudentDao"></bean>
```
使用工厂类的静态方法
Spring可以调用工厂类的静态方法创建bean：

1.创建工厂类，工厂提供创建对象的静态方法。

```java
public class StudentDaoFactory2 {
  public static StudentDao getStudentDao2() {
    return new StudentDao  
1Impl();
   }
}
```

2.在配置文件中配置创建bean的方式为工厂静态方法。

```html
<!-- id:bean的id  class:工厂全类名 factory-method:工厂静态方法  -->
<bean id="studentDao" class="com.jjy.dao.StudentDaoFactory2" factory-method="getStudentDao2"></bean>
```

对象的创建策略
Spring通过配置<bean>中的scope属性设置对象的创建策略，共有五种创建策略：

singleton：单例，默认策略。整个项目只会创建一个对象，通过<bean>中的lazy-init属性可以设置单例对象的创建时机：
lazy-init=“false”(默认)：立即创建，在容器启动时会创建配置文件中的所有Bean对象。

lazy-init=“true”：延迟创建，第一次使用Bean对象时才会创建。

配置单例策略：

```html
<!-- <bean id="studentDao" class="com.itbaizhan.dao.StudentDaoImpl2" scope="singleton" lazy-init="true"></bean> -->
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl2" scope="singleton" lazy-init="false"></bean>
```

测试单例策略：

```html
// 为Bean对象的类添加构造方法
public StudentDaoImpl2(){
  System.out.println("创建StudentDao！！！");
}


@Test
public void t2(){
  // 创建Spring容器
  ApplicationContext ac = new ClassPathXmlApplicationContext("bean1.xml");
  // 从容器获取对象
  StudentDao studentDao1 = (StudentDao) ac.getBean("studentDao");
  StudentDao studentDao2 = (StudentDao) ac.getBean("studentDao");
  StudentDao studentDao3 = (StudentDao) ac.getBean("studentDao");
  System.out.println(studentDao1.hashCode());
  System.out.println(studentDao2.hashCode());
  System.out.println(studentDao3.hashCode());
}
```

prototype：多例，每次从容器中获取时都会创建对象。

```html
<!-- 配置多例策略 -->
<bean id="studentDao" class="com.itbaizhan.dao.StudentDaoImpl2" scope="prototype"></bean>
```

request：每次请求创建一个对象，只在web环境有效。

session：每次会话创建一个对象，只在web环境有效。

gloabal-session：一次集群环境的会话创建一个对象，只在web环境有效。

对象的创建策略不同，销毁时机也不同：

singleton：对象随着容器的销毁而销毁。
prototype：使用JAVA垃圾回收机制销毁对象。
request：当处理请求结束，bean实例将被销毁。
session：当HTTP Session最终被废弃的时候，bean也会被销毁掉。
gloabal-session：集群环境下的session销毁，bean实例也将被销毁。
对象的生命周期
Bean对象的生命周期包含创建——使用——销毁，Spring可以配置Bean对象在创建和销毁时自动执行的方法：

定义生命周期方法

```html
public class StudentDaoImpl2 implements StudentDao{
  // 创建时自动执行的方法
  public void init(){
    System.out.println("创建StudentDao！！！");
   }

  // 销毁时自动执行的方法
  public void destory(){
    System.out.println("销毁StudentDao！！！");
   }
}
```

配置生命周期方法

```html
<!-- init-method:创建对象时执行的方法  destroy-method:销毁对象时执行的方法  -->
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl2" scope="singleton"
   init-method="init" destroy-method="destory"></bean>
 
```

测试

```html
@Test
public void t3(){
  // 创建Spring容器
  ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("bean1.xml");

  // 销毁Spring容器，ClassPathXmlApplicationContext才有销毁容器的方法
  ac.close();
}
```

获取对象的方式
Spring有多种获取容器中对象的方式：

1.通过id/name获取
配置文件

```html
<bean name="studentDao" class="com.jjy.dao.StudentDaoImpl2"></bean>

<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl2"></bean>
```
获取对象

```java
StudentDao studentDao = (StudentDao) ac.getBean("studentDao");
```

通过类型获取

配置文件

```html
<bean name="studentDao" class="com.jjy.dao.StudentDaoImpl2"></bean>
```
获取对象

```html
StudentDao studentDao2 = ac.getBean(StudentDao.class);
```
可以看到使用类型获取不需要强转。

2.通过类型+id/name获取
虽然使用类型获取不需要强转，但如果在容器中有一个接口的多个实现类对象，则获取时会报错，此时需要使用类型+id/name获取

配置文件


```html
<bean name="studentDao" class="com.itbaizhan.dao.StudentDaoImpl2"></bean>
<bean name="studentDao1" class="com.itbaizhan.dao.StudentDaoImpl"></bean>
```

获取对象

```html
StudentDao studentDao2 = ac.getBean("studentDao",StudentDao.class);
```

DI_依赖注入
什么是依赖注入
依赖注入（Dependency Injection，简称DI），它是Spring控制反转思想的具体实现。

控制反转将对象的创建交给了Spring，但是对象中可能会依赖其他对象。比如service类中要有dao类的属性，我们称service依赖于dao。之前需要手动注入属性值，代码如下：

```java
public interface StudentDao {
  Student findById(int id);
}


public class StudentDaoImpl implements StudentDao{
  @Override
  public Student findById(int id) {
    // 模拟根据id查询学生
    return new Student(1,"jjy","北京");
   }
}


public class StudentService {
  // service依赖dao，手动注入属性值，即手动维护依赖关系
  private StudentDao studentDao = new StudentDaoImpl();


  public Student findStudentById(int id){
    return studentDao.findById(id);
   }
}
```

此时，当StudentService的想要使用StudentDao的另一个实现类如StudentDaoImpl2时，则需要修改Java源码，造成代码的可维护性降低。

而使用Spring框架后，Spring管理Service对象与Dao对象，此时它能够为Service对象注入依赖的Dao属性值。这就是Spring的依赖注入。简单来说，控制反转是创建对象，依赖注入是为对象的属性赋值。

Setter注入
在之前开发中，我们可以通过setter方法或构造方法设置对象属性值：
```
// setter方法设置属性
StudentService studentService = new StudentService();
StudentDao studentDao = new StudentDao();
studentService.setStudentDao(studentDao);


// 构造方法设置属性
StudentDao studentDao = new StudentDao();
StudentService studentService = new StudentService(studentDao);
```
Spring可以通过调用setter方法或构造方法给属性赋值

被注入类编写属性的setter方法
```java
public class StudentService {
  private StudentDao studentDao;


  public void setStudentDao(StudentDao studentDao) {
    this.studentDao = studentDao;
   }


  public Student findStudentById(int id){
    return studentDao.findById(id);
   }
}
```
配置文件中，给需要注入属性值的中设置
```html
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl"></bean>
<bean id="studentService" class="com.jjy.service.StudentService">
  <!-- 依赖注入 name:对象的属性名 ref：容器中对象的ID值-->
  <property name="studentDao" ref="studentDao"></property>
</bean>
```

测试是否注入成功
```
@Test
public void testDI1(){
  ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
  StudentService studentService = ac.getBean(StudentService.class);
  System.out.println(studentService.findStudentById(1));
}
```
构造方法注入
被注入类编写有参的构造方法
```
public class StudentService {
  private StudentDao studentDao;
  public StudentService(StudentDao studentDao) {
    this.studentDao = studentDao;
   }
}
```

给需要注入属性值的中设置
```html
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl"></bean>

<bean id="studentService" class="com.jjy.service.StudentService">
  <!-- 依赖注入 -->
  <!-- name:对象的属性名  ref：配置文件中注入对象的id值 -->
  <constructor-arg name="studentDao" ref="studentDao"></constructor-arg>
</bean>
```
测试是否注入成功
```
@Test
public void t2(){
  ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
  StudentService studentService = (StudentService) ac.getBean("studentService");
  System.out.println(studentService.findStudentById(1));
}
```
自动注入
自动注入不需要在<bean>标签中添加其他标签注入属性值，而是自动从容器中找到相应的bean对象设置为属性值。

自动注入有两种配置方式：

全局配置：在<beans>中设置default-autowire属性可以定义所有bean对象的自动注入策略。
局部配置：在<bean>中设置autowire属性可以定义当前bean对象的自动注入策略。
autowire的取值如下：

no：不会进行自动注入。
default：全局配置default相当于no，局部配置default表示使用全局配置
byName：在Spring容器中查找id与属性名相同的bean，并进行注入。需要提供setter方法。
byType：在Spring容器中查找类型与属性类型相同的bean，并进行注入。需要提供setter方法。
constructor：在Spring容器中查找id与属性名相同的bean，并进行注入。需要提供构造方法。
测试自动注入：
1.为依赖的对象提供setter和构造方法：
```java
public class StudentService {
  // 依赖
  private StudentDao studentDao;


  // 构造方法
  public StudentService() {
   }
  
  public StudentService(StudentDao studentDao) {
    this.studentDao = studentDao;
   }


  // setter方法
  public void setStudentDao(StudentDao studentDao) {
    this.studentDao = studentDao;
   }


  // 调用依赖的方法
  public Student findStudentById(int id) {
    return studentDao.findById(id);
   }
}
```
配置自动注入：
```html
<!-- 根据beanId等于属性名自动注入 -->
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl"></bean>
<bean id="studentService" class="com.jjy.service.StudentService" autowire="byName"></bean>
<!-- 根据bean类型等于属性类型自动注入 -->
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl"></bean>
<bean id="studentService" class="com.jjy.service.StudentService" autowire="byType"></bean>
<!-- 利用构造方法自动注入 -->
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl"></bean>
<bean id="studentService" class="com.jjy.service.StudentService" autowire="constructor"></bean>
<!-- 配置全局自动注入 -->
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd"
    default-autowire="constructor"
```

注入bean类型、简单数据类型、List集合、Set集合、Map集合、Properties对象

DI支持注入bean类型、基本数据类型和字符串、List集合、Set集合、Map集合、Properties对象类型等，他们的写法如下：

1.准备注入属性的类
```java
public class StudentService2 {
  private StudentDao studentDao; // bean属性
  private String name; //字符串类型
  private int count; //基本数据类型
  private List<String> names; // 字符串类型List集合
  private List<Student> students1; // 对象类型List集合
  private Set<Student> students2; // 对象类型Set集合
  private Map<String,String> names2; // 字符串类型Map集合
  private Map<String,Student> students3; // 对象类型Map集合
  private Properties properties; //Properties类型
  
  // 省略getter/setter/toString
}
```
2.准备测试方法
```java
@Test
public void t2(){
  ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
  StudentService studentService = (StudentService) ac.getBean("studentService");
  System.out.println(studentService);
}
```
注入bean类型
写法一：
```java
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl"></bean>

<bean id="studentService2" class="com.jjy.service.StudentService2">
  <property name="studentDao" ref="studentDao"></property>
</bean>
```
写法二：
```
<bean id="studentDao" class="com.jjy.dao.StudentDaoImpl"></bean>

<bean id="studentService2" class="com.jjy.service.StudentService2">
  <property name="studentDao" >
    <ref bean="studentDao"></ref>
  </property>
</bean>
```
注入简单数据类型
简单数据类型包含基本数据类型和字符串，写法如下：
```
<bean id="studentService2" class="com.jjy.service.StudentService2">
  <!--  写法一 name：属性名  value：属性值-->
  <property name="name" value="jjy"></property>

  <!--  写法二 name：属性名  value：属性值-->
  <property name="count">
    <value>10</value>
  </property>
</bean>
```
注入List集合
```
<bean id="studentService" class="com.jjy.service.StudentService">
  <!-- 简单数据类型List集合 name：属性名 -->
  <property name="names">
    <list>
      <value>北京</value>
      <value>jjy</value>
    </list>
  </property>

  <!-- 对象类型List集合 name：属性名 -->
  <property name="students1">
    <list>
      <bean class="com.jjy.domain.Student">
        <property name="id" value="1"/>
        <property name="name" value="jjy"/>
        <property name="address" value="北京"/>
      </bean>
      <bean class="com.jjy.domain.Student">
        <property name="id" value="2"/>
        <property name="name" value="sxs"/>
        <property name="address" value="北京"/>
      </bean>
    </list>
  </property>
</bean>
```

注入Set集合
```
<bean id="studentService" class="com.jjy.service.StudentService">
  <!-- Set集合 -->
  <property name="students2">
    <set>
      <bean class="com.jjy.domain.Student">
        <property name="id" value="1"/>
        <property name="name" value="jjjy"/>
        <property name="address" value="北京"/>
      </bean>
      <bean class="com.jjy.domain.Student">
        <property name="id" value="2"/>
        <property name="name" value="jjy"/>
        <property name="address" value="北京"/>
      </bean>
    </set>
  </property>
</bean>
```
注入简单数据类型Map集合
```
<bean id="studentService" class="com.jjy.service.StudentService">
  <property name="names2">
    <map>
      <entry key="student1" value="bz"/>
      <entry key="student2" value="sxt"/>
    </map>
  </property>
</bean>
```
注入对象类型Map集合
```
<bean id="studentService" class="com.jjy.service.StudentService">
  <property name="students3">
    <map>
      <entry key="student1" value-ref="s1"/>
      <entry key="student2" value-ref="s2"/>
    </map>
  </property>
</bean>

<bean id="s1" class="com.jjy.domain.Student">
  <property name="id" value="1"/>
  <property name="name" value="jjy"/>
  <property name="address" value="北京"/>
</bean>
<bean id="s2" class="com.jjy.domain.Student">
  <property name="id" value="2"/>
  <property name="name" value="jj"/>
  <property name="address" value="北京"/>
</bean>
```

注入Properties对象
```
<bean id="studentService" class="com.jjy.service.StudentService">
  <property name="properties">
    <props>
      <prop key="配置1">值1</prop>
      <prop key="配置2">值2</prop>
    </props>
  </property>
</bean>
```
注解实现IOC
@Component
注解配置和xml配置对于Spring的IOC要实现的功能都是一样的，只是配置的形式不一样。
1.创建一个新的Spring项目。

2.编写pojo，dao，service类。

3.编写空的配置文件，Spring配置文件需要添加context约束，才能配置支持注解。
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/context
              http://www.springframework.org/schema/context/spring-context.xsd">
  


</beans>
```
4.使用@Component，将对象放入Spring容器管理。

@Component
作用：用于创建对象，放入Spring容器，相当于
位置：类上方

注意：

要在配置文件中配置扫描的包，扫描到该注解才能生效。
```
<context:component-scan base-package="com.jjy"></context:component-scan>
```
@Component注解配置bean的默认id是首字母小写的类名。也可以手动设置bean的id值。
```
// 此时bean的id为studentDaoImpl
@Component
public class StudentDaoImpl implements StudentDao{
  public Student findById(int id) {
    // 模拟根据id查询学生
    return new Student(1,"jjy","北京");
   }
}

// 此时bean的id为studentDao
@Component("studentDao")
public class StudentDaoImpl implements StudentDao{
  public Student findById(int id) {
    // 模拟根据id查询学生
    return new Student(1,"jjy","北京");
   }
}
```

@Repository、@Service、@Controller
作用：这三个注解和@Component的作用一样，使用它们是为了区分该类属于什么层。

位置：

@Repository用于Dao层
@Service用于Service层
@Controller用于Controller层
@Autowired
作用：从容器中查找符合属性类型的对象自动注入属性中。用于代替中的依赖注入配置。

位置：属性上方、setter方法上方、构造方法上方。

注意：

1.@Autowired写在属性上方进行依赖注入时，可以省略setter方法。
```
@Component
public class StudentService {
  @Autowired
  private StudentDao studentDao;
  public Student findStudentById(int id){
    return studentDao.findById(id);
   }
}

@Test
public void t2(){
  ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
  StudentService studentService = (StudentService) ac.getBean("studentService");
  System.out.println(studentService.findStudentById(1));
}
```
2.容器中没有对应类型的对象会报错
```
// 如果StudentDaoImpl没有放到容器中会报错
//@Component("studentDao")
public class StudentDaoImpl implements StudentDao{
  public Student findById(int id) {
    // 模拟根据id查询学生
    return new Student(1,"jjy","北京");
   }
}
```
3.容器中有多个对象匹配类型时，会找beanId=属性名的对象，找不到会报错。
```
// 如果容器中都多个同类型对象，会根据id值等于属性名找对象
@Component("studentDao")
public class StudentDaoImpl implements StudentDao{
  public Student findById(int id) {
    // 模拟根据id查询学生
    return new Student(1,"jjy","北京");
   }
}

@Component
public class StudentDaoImpl2 implements StudentDao{
  public Student findById(int id) {
    // 模拟根据id查询学生
    return new Student(1,"jjy","北京");
   }
}
```

@Qualifier
作用：在按照类型注入对象的基础上，再按照bean的id注入。

位置：属性上方

注意：@Qualifier必须和@Autowired一起使用。
```
@Component
public class StudentService {
  @Autowired
  @Qualifier("studentDaoImpl2")
  private StudentDao studentDao;
  public Student findStudentById(int id){
    return studentDao.findById(id);
   }
}
```

@Value
作用：注入简单数据类型的属性值。

位置：属性上方

用法：

直接设置固定的属性值
```
@Service
public class StudentService {
  @Value("1")
  private int count;

  @Value("hello")
  private String str;
}
```
获取配置文件中的属性值：

1.编写配置文件db.properties
```
jdbc.username=root
jdbc.password=123456
```

2.spring核心配置文件扫描配置文件
```
<context:property-placeholder location="db.properties"></context:property-placeholder>
```
3.注入配置文件中的属性值
```
@Value("${jdbc.username}")
private String username;

@Value("${jdbc.password}")
private String password;
```

@Scope
作用：指定bean的创建策略
位置：类上方
取值：singleton prototype request session globalsession
```
@Service
@Scope("singleton")

public class StudentService {}
```
@Configuration
此时基于注解的IOC配置已经完成，但是我们依然离不开Spring的xml配置文件。接下来我们脱离bean.xml，使用纯注解实现IOC。

在真实开发中，我们一般还是会保留xml配置文件，很多情况下使用配置文件更加方便。

纯注解实现IOC需要一个Java类代替xml文件。这个Java类上方需要添加@Configuration，表示该类是一个配置类，作用是代替配置文
件。
```
@Configuration

public class SpringConfig {   
}
```
@ComponentScan
作用：指定spring在初始化容器时扫描的包。
位置：配置类上方
```
@Configuration
@ComponentScan("com.jjy")

public class SpringConfig {
}
```
@PropertySource
作用：代替配置文件中的 <context:property-placeholder> 扫描配置文件
位置：配置类上方
注意：配置文件位置前要加关键字 classpath
```
@Configuration
@PropertySource("classpath:db.properties")

public class JdbcConfig {
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;
}
```

@Bean
作用：将方法的返回值对象放入Spring容器中。

位置：配置类的方法上方。

属性：name：给bean对象设置id。
```
@Configuration
@ComponentScan("com.jjy")
@PropertySource("classpath:db.properties")
public class SpringConfig {
  @Bean
  public StudentService getStudentService(){
    return new StudentService();
   }
}
```
如果想将第三方类的对象放入容器，可以使用@Bean

注意：
```
@Bean修饰的方法如果有参数，Spring会根据参数类型从容器中查找可用对象。
如果容器中有多个该类型的对象，可以在参数前加@Qualifier指定对象的id。
@Configuration
@ComponentScan("com.jjy")
@PropertySource("classpath:db.properties")
public class SpringConfig {
  @Bean
  public StudentService getStudentService(@Qualifier("studentDaoImpl") StudentDao studentDao){
    System.out.println(studentDao);
    return new StudentService();
   }
}
```
@Import
作用：如果配置过多，会有多个配置类，该注解可以为主配置类导入其他配置类

位置：主配置类上方
```
// Service配置类
@Configuration
public class ServiceConfig {
  @Bean
  public StudentService getStudentService(@Qualifier("studentDaoImpl2") StudentDao studentDao){
    System.out.println(studentDao);
    return new StudentService();
   }
}


// 主配置类
@Configuration
@ComponentScan("com.jjy")
@Import(JdbcConfig.class)
public class SpringConfig {


}
```

Spring整合MyBatis
创建项目
我们知道使用MyBatis时需要写大量创建SqlSessionFactoryBuilder、SqlSessionFactory、SqlSession等对象的代码，而Spring的作用是帮助我们创建和管理对象，所以我们可以使用Spring整合MyBatis，简化MyBatis开发。

创建maven项目，引入依赖。
```
<dependencies>
  <!-- mybatis -->
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.13</version>
  </dependency>
  <!-- mysql驱动 -->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.26</version>
  </dependency>
  <!-- druid -->
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.16</version>
  </dependency>
  <!-- spring -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.0.11</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>6.0.11</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>6.0.11</version>
  </dependency>
  <!-- junit -->
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
  </dependency>
  <!-- MyBatis与Spring的整合包，该包可以让Spring创建MyBatis的对象 -->
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>3.0.2</version>
  </dependency>
</dependencies>
```

编写配置文件
1.编写数据库配置文件db.properties
```
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql:///student
jdbc.username=root
jdbc.password=root
```
2.创建MyBatis配置文件SqlMapConfig.xml，数据源、扫描接口都交由Spring管理，不需要在MyBatis配置文件中设置。
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
</configuration>
```
3.创建Spring配置文件applicationContext.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
     http://www.springframework.org/schema/beans/spring-beans.xsd
     http://www.springframework.org/schema/context
     http://www.springframework.org/schema/context/spring-context.xsd">
  <!-- 包扫描 -->
  <context:component-scan base-package="com.jjy"></context:component-scan>


  <!-- 读取配置文件 -->
  <context:property-placeholder location="classpath:db.properties"></context:property-placeholder>
  <!-- 创建druid数据源对象 -->
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driverClassName}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
  </bean>


  <!-- Spring创建封装过的SqlSessionFactory -->
  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"></property>
  </bean>


  <!-- Spring创建封装过的SqlSession -->
  <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"/>
  </bean>
</beans>
```

编写Java代码
准备数据库
```
CREATE DATABASE `student`;
USE `student`;
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
 `id` int(11) NOT NULL AUTO_INCREMENT,
 `name` varchar(255) DEFAULT NULL,
 `sex` varchar(10) DEFAULT NULL,
 `address` varchar(255) DEFAULT NULL,
 PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

insert into `student`(`id`,`name`,`sex`,`address`) values (1,'中南大学','男','北京'),(2,'中北大学','女','北京');
```

准备实体类
```
public class Student {
  private int id;
  private String name;
  private String sex;
  private String address;
  
  // 省略构造方法/getter/setter/tostring
}
```
编写持久层接口
```
@Repository
public interface StudentDao {
  // 查询所有学生
  @Select("select * from student")
  List<Student> findAll();
}
```

编写service类
```
@Service
public class StudentService {
  // SqlSession对象
  @Autowired
  private SqlSessionTemplate sqlSession;

  // 使用SqlSession获取代理对象
  public List<Student> findAllStudent(){
    StudentDao studentDao = sqlSession.getMapper(StudentDao.class);
    return studentDao.findAll();
   }
}
```

自动创建代理对象
Spring提供了MapperScannerConfigurer对象，该对象可以自动扫描包创建代理对象，并将代理对象放入容器中，此时不需要使用SqlSession手动创建代理对象。

创建MapperScannerConfigurer对象
```
<!-- 该对象可以自动扫描持久层接口，并为接口创建代理对象 -->
<bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
  <!-- 配置扫描的接口包 -->
  <property name="basePackage" value="com.jjy.dao"></property>
</bean>
```
Service类直接使用代理对象即可
```
@Service
public class StudentService {
  // 直接注入代理对象
  @Autowired
  private StudentDao studentDao;
 
  // 直接使用代理对象
  public void addStudent(Student student){
    studentDao.add(student);
   }
}
```

测试
```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:applicationContext.xml")
public class StudentServiceTest {
  @Autowired
  private StudentService studentService;
  
  @Test
  public void testAdd(){
    Student student = new Student(0, "jjy", "男", "上海");
    studentService.addStudent(student);
   }
}
```
AOP
什么是AOP
AOP的全称是Aspect Oriented Programming，即面向切面编程。是实现功能统一维护的一种技术，它将业务逻辑的各个部分进行隔离，使开发人员在编写业务逻辑时可以专心于核心业务，从而提高了开发效率。

作用：在不修改源码的基础上，对已有方法进行增强。
实现原理：动态代理技术。
优势：减少重复代码、提高开发效率、维护方便
应用场景：事务处理、日志管理、权限控制、异常处理等方面。
AOP相关术语

为了更好地理解AOP，就需要对AOP的相关术语有一些了解

名称	说明
Joinpoint（连接点）	指能被拦截到的点，在Spring中只有方法能被拦截。
Pointcut（切点）	指要对哪些连接点进行拦截，即被增强的方法。
Advice（通知）	指拦截后要做的事情，即切点被拦截后执行的方法。
Aspect（切面）	切点+通知称为切面
Target（目标）	被代理的对象
Proxy（代理）	代理对象
Weaving（织入）	生成代理对象的过程
AOP入门
AspectJ是一个基于Java语言的AOP框架，在Spring框架中建议使用AspectJ实现AOP。

接下来我们写一个AOP入门案例：dao层的每个方法结束后都可以打印一条日志：
引入依赖
```
<!-- spring -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>6.0.11<version>
</dependency>
<!-- AspectJ -->
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.8.7</version>
</dependency>
<!--  junit  -->
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
</dependency>
<!--Spring整合测试模块 -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-test</artifactId>
  <version>6.0.11</version>
</dependency>
```

编写连接点

@Repository
```
public class UserDao {
  public void add(){
    System.out.println("用户新增");
   }
  public void delete(){
    System.out.println("用户删除");
   }
  public void update(){
    System.out.println("用户修改");
   }
}
```

编写通知类
```
public class MyAspectJAdvice {
  // 后置通知
  public void myAfterReturning() {
    System.out.println("打印日志...");
   }
}
```

配置切面
```
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
  <bean id="myAspectJAdvice" class="com.jjy.advice.MyAspectAdvice"></bean>

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

测试

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class UserDaoTest {
  @Autowired
  private UserDao userDao;

  @Test
  public void testAdd(){
    userDao.add();
   }

  @Test
  public void testDelete(){
    userDao.delete();
   }

  @Test
  public void testUpdate(){
    userDao.update();
   }
}
```

通知类型
AOP有以下几种常用的通知类型：

通知类型	描述
前置通知	在方法执行前添加功能
后置通知	在方法正常执行后添加功能
异常通知	在方法抛出异常后添加功能
最终通知	无论方法是否抛出异常，都会执行该通知
环绕通知	在方法执行前后添加功能
编写通知方法

```html
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
gt

配置切面
```
<!-- 配置AOP -->
<aop:config>
  <!-- 配置切面 -->
  <aop:aspect ref="myAspectJAdvice">
    <!-- 配置切点 -->
    <aop:pointcut id="myPointcut" expression="execution(* com.jjy.dao.UserDao.*(..))"/>
    <!-- 前置通知 -->
    <aop:before method="myBefore" pointcut-ref="myPointcut"></aop:before>
    <!-- 后置通知 -->
    <aop:after-returning method="myAfterReturning" pointcut-ref="myPointcut"/>
    <!-- 异常通知 -->
    <aop:after-throwing method="myAfterThrowing" pointcut-ref="myPointcut" throwing="ex"/>
    <!-- 最终通知 -->
    <aop:after method="myAfter" pointcut-ref="myPointcut"></aop:after>
    <!-- 环绕通知 -->
    <aop:around method="myAround" pointcut-ref="myPointcut"></aop:around>
  </aop:aspect>
</aop:config>
```
切点表达式
AspectJ需要使用切点表达式配置切点位置，写法如下：

标准写法：方法的访问修饰符 方法返回值 包名.类名.方法名(参数列表)

访问修饰符可以省略。

返回值使用*代表任意类型。

包名使用表示任意包，多级包结构要写多个，使用*…表示任意包结构

类名和方法名都可以用*实现通配。

参数列表
基本数据类型直接写类型
引用类型写包名.类名
*表示匹配一个任意类型参数
…表示匹配任意类型任意个数的参数

全通配：* ….*(…)

多切面配置

我们可以为切点配置多个通知，形成多切面，比如希望dao层的每个方法结束后都可以打印日志并发送邮件：

编写发送邮件的通知：
```
public class MyAspectJAdvice2 {
  // 后置通知
  public void myAfterReturning(JoinPoint joinPoint) {
    System.out.println("发送邮件");
   }
}
```
配置切面：
```
<!-- 通知对象 -->
<bean id="myAspectJAdvice" class="com.jjy.advice.MyAspectAdvice"></bean>
<bean id="myAspectJAdvice2" class="com.jjy.advice.MyAspectAdvice2"></bean>

<!-- 配置AOP -->
<aop:config>
  <!-- 配置切面 -->
  <aop:aspect ref="myAspectJAdvice">
    <!-- 配置切点 -->
    <aop:pointcut id="myPointcut" expression="execution(* *..*.*(..))"/>
    <!-- 后置通知 -->
    <aop:after-returning method="myAfterReturning" pointcut-ref="myPointcut"/>
  </aop:aspect>
  
  <aop:aspect ref="myAspectJAdvice2">
    <aop:after-returning method="myAfterReturning" pointcut-ref="myPointcut"/>
  </aop:aspect>
</aop:config>
```

注解配置AOP
Spring可以使用注解代替配置文件配置切面：
在xml中开启AOP注解支持
``
<!-- 开启注解配置Aop -->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

在通知类上方加入注解@Aspect

在通知方法上方加入注解@Before/@AfterReturning/@AfterThrowing/@After/@Around
```
@Aspect
@Component
public class MyAspectAdvice {
  // 后置通知
  @AfterReturning("execution(* com.jjy.dao.UserDao.*(..))")
  public void myAfterReturning(JoinPoint joinPoint) {
    System.out.println("切点方法名：" + joinPoint.getSignature().getName());
    System.out.println("目标对象：" + joinPoint.getTarget());
    System.out.println("打印日志" + joinPoint.getSignature().getName() + "方法被执行了！");
   }


  // 前置通知
  @Before("execution(* com.jjy.dao.UserDao.*(..))")
  public void myBefore() {
    System.out.println("前置通知...");
   }


  // 异常通知
  @AfterThrowing(value = "execution(* com.jjy.dao.UserDao.*(..))",throwing = "ex")
  public void myAfterThrowing(Exception ex) {
    System.out.println("异常通知...");
    System.err.println(ex.getMessage());
   }


  // 最终通知
  @After("execution(* com.jjy.dao.UserDao.*(..))")
  public void myAfter() {
    System.out.println("最终通知");
   }


  // 环绕通知
  @Around("execution(* com.jjy.dao.UserDao.*(..))")
  public Object myAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
    System.out.println("环绕前");
    Object obj = proceedingJoinPoint.proceed(); // 执行方法
    System.out.println("环绕后");
    return obj;
   }
}
```

测试：
```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext2.xml")
public class UserDaoTest2 {
  @Autowired
  private UserDao userDao;

  @Test
  public void testAdd(){
    userDao.add(1);
   }
}
```
如何为一个类下的所有方法统一配置切点：

在通知类中添加方法配置切点
```
@Pointcut("execution(* com.jjy.dao.UserDao.*(..))")
public void pointCut(){}
1
2
在通知方法上使用定义好的切点

@Before("pointCut()")
public void myBefore(JoinPoint joinPoint) {
  System.out.println("前置通知...");
}

@AfterReturning("pointCut()")
public void myAfterReturning(JoinPoint joinPoint) {
  System.out.println("后置通知...");
}
```
配置类如何代替xml中AOP注解支持？

在配置类上方添加@EnableAspectJAutoProxy即可
```
@Configuration
@ComponentScan("com.jjy")
@EnableAspectJAutoProxy
public class SpringConfig {

}
```
原生Spring实现AOP
除了AspectJ，Spring本身也是可以实现AOP的。

Spring原生方式实现AOP时，只支持四种通知类型：

通知类型	实现接口
前置通知	MethodBeforeAdvice
后置通知	AfterReturningAdvice
异常通知	ThrowsAdvice
环绕通知	MethodInterceptor
引入依赖
```
<!-- AOP -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aop</artifactId>
  <version>6.0.11</version>
</dependency>
```
编写通知类
```
// Spring原生Aop通知类
public class SpringAopAdvice implements MethodBeforeAdvice, AfterReturningAdvice, ThrowsAdvice, MethodInterceptor {
  /**
   * 前置通知
   * @param method 目标方法
   * @param args 目标方法的参数列表
   * @param target 目标对象
   * @throws Throwable
   */
  @Override
  public void before(Method method, Object[] args, Object target) throws Throwable {
    System.out.println("前置通知");
   }

  /**
   * 后置通知
   * @param returnValue 目标方法的返回值
   * @param method 目标方法
   * @param args 目标方法的参数列表
   * @param target 目标对象
   * @throws Throwable
   */
  @Override
  public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
    System.out.println("后置通知");
   }

  /**
   * 环绕通知
   * @param invocation 目标方法
   * @return
   * @throws Throwable
   */
  @Override
  public Object invoke(MethodInvocation invocation) throws Throwable {
    System.out.println("环绕前");
    Object proceed = invocation.proceed();
    System.out.println("环绕后");
    return proceed;
   }

  /**
   * 异常通知
   * @param ex 异常对象
   */
  public void afterThrowing(Exception ex){
    System.out.println("发生异常了！");
   }
}

```

编写配置类
```
<!-- 包扫描 -->
<context:component-scan base-package="com.jjy"></context:component-scan>

<!-- 通知对象 -->
<bean id="springAopAdvice" class="com.jjy.advice.SpringAopAdvice"></bean>

<!-- 配置代理对象 -->
<bean id="userDaoProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
  <!-- 配置目标对象 -->
  <property name="target" ref="userDao"></property>
  <!-- 配置通知 -->
  <property name="interceptorNames">
    <list>
      <value>springAopAdvice</value>
    </list>
  </property>
  <!-- 代理对象的生成方式 true:使用CGLib false：使用原生JDK生成 -->
  <property name="proxyTargetClass" value="true"></property>
</bean>

```
编写测试类
```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext3.xml")
public class UserDaoTest4 {
  @Autowired
  @Qualifier("userDaoProxy")
  private UserDao userDao;

  @Test
  public void testAdd(){
    userDao.add(1);
   }
}
```
SchemaBased实现AOP
SchemaBased(基础模式)配置方式是指使用Spring原生方式定义通知，而使用AspectJ框架配置切面。

编写通知类
```
public class SpringAop implements MethodBeforeAdvice, AfterReturningAdvice, ThrowsAdvice, MethodInterceptor {
  /**
   * 前置通知
   * @param method 目标方法
   * @param args 目标方法的参数列表
   * @param target 目标对象
   * @throws Throwable
   */
  @Override
  public void before(Method method, Object[] args, Object target) throws Throwable {
    System.out.println("前置通知");
   }

  /**
   * 后置通知
   * @param returnValue 目标方法的返回值
   * @param method 目标方法
   * @param args 目标方法的参数列表
   * @param target 目标对象
   * @throws Throwable
   */
  @Override
  public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
    System.out.println("后置通知");
   }

  /**
   * 环绕通知
   * @param invocation 目标方法
   * @return
   * @throws Throwable
   */
  @Override
  public Object invoke(MethodInvocation invocation) throws Throwable {
    System.out.println("环绕前");
    Object proceed = invocation.proceed();
    System.out.println("环绕后");
    return proceed;
   }

  /**
   * 异常通知
   * @param ex 异常对象
   */
  public void afterThrowing(Exception ex){
    System.out.println("发生异常了！");
   }
}

```
配置切面
```
<!-- 通知对象 -->
<bean id="springAop2" class="com.jjy.aop.SpringAop2"/>

<!-- 配置切面 -->
<aop:config>
  <!-- 配置切点-->
  <aop:pointcut id="myPointcut" expression="execution(* com.jjy.dao.UserDao.*(..))"/>
  <!-- 配置切面：advice-ref：通知对象 pointcut-ref：切点 -->
  <aop:advisor advice-ref="springAop2" pointcut-ref="myPointcut"/>
</aop:config>
```
测试
```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext4.xml")
public class UserDaoTest5 {
  @Autowired
  private UserDao userDao;

  @Test
  public void testAdd(){
    userDao.add(1);
   }
}
```
Spring事务
事务：不可分割的原子操作。即一系列的操作要么同时成功，要么同时失败。

开发过程中，事务管理一般在service层，service层中可能会操作多次数据库，这些操作是不可分割的。否则当程序报错时，可能会造成数据异常。

如：张三给李四转账时，需要两次操作数据库：张三存款减少、李四存款增加。如果这两次数据库操作间出现异常，则会造成数据错误。

准备数据库
```
CREATE DATABASE `spring` ;
USE `spring`;
DROP TABLE IF EXISTS `account`;

CREATE TABLE `account` (
 `id` int(11) NOT NULL AUTO_INCREMENT,
 `username` varchar(255) DEFAULT NULL,
 `balance` double DEFAULT NULL,
 PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

insert into `account`(`id`,`username`,`balance`) values (1,'张三',1000),(2,'李四',1000);
```
创建maven项目，引入依赖
```
<dependencies>
  <!--  mybatis  -->
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.13</version>
  </dependency>
  <!--  mysql驱动包  -->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.26</version>
  </dependency>
  <!--  druid连接池  -->
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.16</version>
  </dependency>
  <!-- spring -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.0.11</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>6.0.11</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>6.0.11</version>
  </dependency>
  <!-- MyBatis与Spring的整合包，该包可以让Spring创建MyBatis的对象 -->
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>3.0.2</version>
  </dependency>

  <!-- junit -->
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
  </dependency>
  <!-- spring整合测试模块 -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>6.0.11</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

创建配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
     http://www.springframework.org/schema/beans/spring-beans.xsd
     http://www.springframework.org/schema/context
     http://www.springframework.org/schema/context/spring-context.xsd">
  <!-- 包扫描 -->
  <context:component-scan base-package="com.jjy"></context:component-scan>
  <!-- 创建druid数据源对象 -->
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql:///spring"></property>
    <property name="username" value="root"></property>
    <property name="password" value="root"></property>
  </bean>

  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"></property>
  </bean>

  <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.jjy.dao"></property>
  </bean>
</beans>
```

编写Java代码
```
// 账户
public class Account {
  private int id; // 账号
  private String username; // 用户名
  private double balance; // 余额
  
  // 省略getter/setter/tostring/构造方法
}

@Repository
public interface AccountDao {
  // 根据id查找用户
  @Select("select * from account where id = #{id}")
  Account findById(int id);

  // 修改用户
  @Update("update account set balance = #{balance} where id = #{id}")
  void update(Account account);
}

@Service
public class AccountService {
  @Autowired
  private AccountDao accountDao;

  /**
   * 转账
   * @param id1 转出人id
   * @param id2 转入人id
   * @param price 金额
   */
  public void transfer(int id1, int id2, double price) {
    // 转出人减少余额
    Account account1 = accountDao.findById(id1);
    account1.setBalance(account1.getBalance()-price);
    accountDao.update(account1);

    int i = 1/0; // 模拟程序出错

    // 转入人增加余额
    Account account2 = accountDao.findById(id2);
    account2.setBalance(account2.getBalance()+price);
    accountDao.update(account2);
   }
}
```

测试转账
```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:applicationContext.xml")
public class AccountServiceTest {
  @Autowired
  private AccountService accountService;

  @Test
  public void testTransfer(){
    accountService.transfer(1,2,500);
   }
}
```
此时没有事务管理，会造成张三的余额减少，而李四的余额并没有增加。所以事务处理位于业务层，即一个service方法是不能分割的。

Spring事务管理方案
在service层手动添加事务可以解决该问题：
```
@Autowired
private SqlSessionTemplate sqlSession;


publicbvoid transfer(int id1, int id2, double price) {
  try{
    // account1修改余额
    Account account1 = accountDao.findById(id1);
    account1.setBalance(account1.getBalance()-price);
    accountDao.update(account1);


    int i = 1/0; // 模拟转账出错


    // account2修改余额
    Account account2 = accountDao.findById(id2);
    account2.setBalance(account2.getBalance()+price);
    accountDao.update(account2); 
    sqlSession.commit();
   }catch(Exception ex){
    sqlSession.rollback();
   }
}

```
但在Spring管理下不允许手动提交和回滚事务。此时我们需要使用Spring的事务管理方案，在Spring框架中提供了两种事务管理方案：

编程式事务：通过编写代码实现事务管理。
声明式事务：基于AOP技术实现事务管理。
在Spring框架中，编程式事务管理很少使用，我们对声明式事务管理进行详细学习。
Spring的声明式事务管理在底层采用了AOP技术，其最大的优点在于无需通过编程的方式管理事务，只需要在配置文件中进行相关的规则声明，就可以将事务规则应用到业务逻辑中。

使用AOP技术为service方法添加如下通知：


Spring事务管理器
Spring依赖事务管理器进行事务管理，事务管理器即一个通知类，我们为该通知类设置切点为service层方法即可完成事务自动管理。由于不同技术操作数据库，进行事务操作的方法不同。如：JDBC提交事务是connection.commit()，MyBatis提交事务是sqlSession.commit()，所以Spring提供了多个事务管理器。

事务管理器名称	作用
org.springframework.jdbc.datasource.DataSourceTransactionManager	针对JDBC技术提供的事务管理器。适用于JDBC和MyBatis。
org.springframework.orm.hibernate3.HibernateTransactionManager	针对于Hibernate框架提供的事务管理器。适用于Hibernate框架。
org.springframework.orm.jpa.JpaTransactionManager	针对于JPA技术提供的事务管理器。适用于JPA技术。
org.springframework.transaction.jta.JtaTransactionManager	跨越了多个事务管理源。适用在两个或者是多个不同的数据源中实现事务控制。
我们使用MyBatis操作数据库，接下来使用DataSourceTransactionManager进行事务管理。

引入依赖
```
<!-- 事务管理 -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-tx</artifactId>
  <version>6.0.11</version>
</dependency>
<!-- AspectJ -->
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.8.7</version>
</dependency>
```
在配置文件中引入约束

xmlns:aop="http://www.springframework.org/schema/aop"
xmlns:tx="http://www.springframework.org/schema/tx"

http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop.xsd
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd

进行事务配置
```
<!-- 事务管理器  -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <property name="dataSource" ref="dataSource"></property>
</bean>

<!-- 进行事务相关配置 -->
<tx:advice id = "txAdvice">
  <tx:attributes>
    <tx:method name="*"/>
  </tx:attributes>
</tx:advice>

<!-- 配置切面 -->
<aop:config>
  <!-- 配置切点 -->
  <aop:pointcut id="pointcut" expression="execution(* com.jjy.service.*.*(..))"/>
  <!-- 配置通知 -->
  <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut"></aop:advisor>
</aop:config>
```
事务控制的API
Spring进行事务控制的功能是由三个接口提供的，这三个接口是Spring实现的，在开发中我们很少使用到，只需要了解他们的作用即可：

PlatformTransactionManager接口

PlatformTransactionManager是Spring提供的事务管理器接口，所有事务管理器都实现了该接口。该接口中提供了三个事务操作方法：

TransactionStatus getTransaction（TransactionDefinition definition）：获取事务状态信息。
void commit（TransactionStatus status）：事务提交
void rollback（TransactionStatus status）：事务回滚
TransactionDefinition接口

TransactionDefinition是事务的定义信息对象，它有如下方法：

String getName()：获取事务对象名称。
int getIsolationLevel()：获取事务的隔离级别。
int getPropagationBehavior()：获取事务的传播行为。
int getTimeout()：获取事务的超时时间。
boolean isReadOnly()：获取事务是否只读。
TransactionStatus接口

TransactionStatus是事务的状态接口，它描述了某一时间点上事务的状态信息。它有如下方法：

void flush() 刷新事务
boolean hasSavepoint() 获取是否存在保存点
boolean isCompleted() 获取事务是否完成
boolean isNewTransaction() 获取是否是新事务
boolean isRollbackOnly() 获取是否回滚
void setRollbackOnly() 设置事务回滚
事务的相关配置
在<tx:advice>中可以进行事务的相关配置：
```
<tx:advice id="txAdvice">
  <tx:attributes>
    <tx:method name="*"/>
  </tx:attributes>
</tx:advice>
```
<tx:method>中的属性：

name：指定配置的方法。表示所有方法，find表示所有以find开头的方法。
read-only：是否是只读事务，只读事务不存在数据的修改，数据库将会为只读事务提供一些优化手段，会对性能有一定提升，建议在查询中开启只读事务。
timeout：指定超时时间，在限定的时间内不能完成所有操作就会抛异常。默认永不超时
rollback-for：指定某个异常事务回滚，其他异常不回滚。默认所有异常回滚。
no-rollback-for：指定某个异常不回滚，其他异常回滚。默认所有异常回滚。 propagation：事务的传播行为
isolation：事务的隔离级别
传播行为
事务传播行为是指多个含有事务的方法相互调用时，事务如何在这些方法间传播。

如果在service层的方法中调用了其他的service方法，假设每次执行service方法都要开启事务，此时就无法保证外层方法和内层方法处于同一个事务当中。
```
// method1的所有方法在同一个事务中
public void method1(){
  // 此时会开启一个新事务，这就无法保证method1()中所有的代码是在同一个事务中
  method2();
  System.out.println("method1");
}


public void method2(){
  System.out.println("method2");
}
```
事务的传播特性就是解决这个问题的，Spring帮助我们将外层方法和内层方法放入同一事务中。

传播行为	介绍
REQUIRED	默认。支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择。
SUPPORTS	支持当前事务，如果当前没有事务，就以非事务方式执行。
MANDATORY	支持当前事务，如果当前没有事务，就抛出异常。
REQUIRES_NEW	新建事务，如果当前存在事务，把当前事务挂起。
NOT_SUPPORTED	以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
NEVER	以非事务方式执行，如果当前存在事务，则抛出异常。
NESTED	必须在事务状态下执行，如果没有事务则新建事务，如果当前有事务则创建一个嵌套事务
注解配置事务
Spring支持使用注解配置声明式事务。用法如下：

注册事务注解驱动
```
<!-- 注册事务注解驱动 -->
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```
在需要事务支持的方法或类上加@Transactional
```
@Service
// 作用于类上时，该类的所有public方法将都具有该类型的事务属性
@Transactional(propagation = Propagation.REQUIRED,isolation = Isolation.DEFAULT)
public class AccountService {
  @Autowired
  private AccountDao accountDao;

  /**
   * 转账
   * @param id1 转出人id
   * @param id2 转入人id
   * @param price 金额
   */
  // 作用于方法上时，该方法将都具有该类型的事务属性
  @Transactional(propagation = Propagation.REQUIRED,isolation = Isolation.DEFAULT)
  public void transfer(int id1, int id2, double price) {
    // account1修改余额
    Account account1 = accountDao.findById(id1);
    account1.setBalance(account1.getBalance()-price);
    accountDao.update(account1);

    int i = 1/0; // 模拟转账出错

    // account2修改余额
    Account account2 = accountDao.findById(id2);
    account2.setBalance(account2.getBalance()+price);
    accountDao.update(account2);
   }
}
```
配置类代替xml中的注解事务支持：在配置类上方写@EnableTranscationManagement
```
@Configuration
@ComponentScan("com.jjy")
@EnableTransactionManagement
public class SpringConfig {

  @Bean
  public DataSource getDataSource(){
    DruidDataSource druidDataSource = new DruidDataSource();
    druidDataSource.setDriverClassName("com.mysql.jdbc.Driver");
    druidDataSource.setUrl("jdbc:mysql:///spring");
    druidDataSource.setUsername("root");
    druidDataSource.setPassword("root");
    return druidDataSource;
   }

  @Bean
  public SqlSessionFactoryBean getSqlSession(DataSource dataSource){
    SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
    sqlSessionFactoryBean.setDataSource(dataSource);
    return sqlSessionFactoryBean;
   }

  @Bean
  public MapperScannerConfigurer getMapperScanner(){
    MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
    mapperScannerConfigurer.setBasePackage("com.itbaizhan.dao");
    return mapperScannerConfigurer;
   }

  @Bean
  public DataSourceTransactionManager getTransactionManager(DataSource dataSource){
    DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
    dataSourceTransactionManager.setDataSource(dataSource);
    return dataSourceTransactionManager;
   }
}
```
SpringTask_定时任务
定时任务即系统在特定时间执行一段代码，它的场景应用非常广泛：

购买游戏的月卡会员后，系统每天给会员发放游戏资源。

管理系统定时生成报表。

定时清理系统垃圾。

定时任务的实现主要有以下几种方式：

Java自带的java.util.Timer类，这个类允许调度一个java.util.TimerTask任务。使用这种方式可以让程序按照某一个频度执行，但不能在指定时间运行。一般用的较少。
Quartz。这是一个功能比较强大的的调度器，可以让程序在指定时间执行，也可以按照某一个频度执行，配置起来稍显复杂。
Spring3.0以后自带Spring Task，可以将它看成一个轻量级的Quartz，使用起来比Quartz简单许多，在课程中我们使用Spring Task实现定时任务
案例
创建Maven项目，引入Spring依赖
```
<dependencies>
  <!-- Spring -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.0.11</version>
  </dependency>
</dependencies> 
```
编写定时要执行的方法
```
@Component
public class MySpringTask {
  // 打印时间
  public void printTime(){
    SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
    String now = sdf.format(new Date());
    System.out.println(now);
   }
}
```
创建Spring配置文件applicationContext.xml，注册定时任务方法。
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:task="http://www.springframework.org/schema/task"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.2.xsd
    http://www.springframework.org/schema/task
    http://www.springframework.org/schema/task/spring-task-3.2.xsd">

  <context:component-scan base-package="com.jjy"></context:component-scan>

  <!-- 配置执行定时任务的对象、方法、时间 -->
  <task:scheduled-tasks>
    <task:scheduled ref="mySpringTask" method="printTime" cron="* * * * * *" />
  </task:scheduled-tasks>

</beans>
```
编写测试类，读取Spring配置文件，开启定时任务
```
public class Test {
  public static void main(String[] args) {
    new ClassPathXmlApplicationContext("applicationContext.xml");
   }
}
```
Spring Task_Cron表达式
Spring Task依靠Cron表达式配置定时规则。Cron表达式是一个字符串，分为6或7个域，每一个域代表一个含义，以空格隔开。有如下两种语法格式：

Seconds Minutes Hours DayofMonth Month DayofWeek Year
Seconds Minutes Hours DayofMonth Month DayofWeek
Seconds（秒）：域中可出现, - * /四个字符，以及0-59的整数

*：表示匹配该域的任意值，在Seconds域使用，表示每秒钟都会触发
,：表示列出枚举值。在Seconds域使用5,20，表示在5秒和20秒各触发一次。
-：表示范围。在Seconds域使用5-20，表示从5秒到20秒每秒触发一次
/：表示起始时间开始触发，然后每隔固定时间触发一次。在Seconds域使用5/20, 表示5秒触发一次，25秒，45秒分别触发一次。
Minutes（分）：域中可出现, - * /四个字符，以及0-59的整数

Hours（时）：域中可出现, - * /四个字符，以及0-23的整数

DayofMonth（日期）：域中可出现, - * / ? L W C八个字符，以及1-31的整数

C：表示和当前日期相关联。在DayofMonth域使用5C，表示在5日后的那一天触发，且每月的那天都会触发。比如当前是10号，那么每月15号都会触发。

L：表示最后，在DayofMonth域使用L，表示每个月的最后一天触发。

W：表示工作日，在DayofMonth域用15W，表示最接近这个月第15天的工作日触发，如果15号是周六，则在14号即周五触发；如果15号是周日，则在16号即周一触发；如果15号是周二则在当天触发。

注：

1.该用法只会在当前月计算，不会到下月触发。比如在DayofMonth域用31W，31号是周日，那么会在29号触发而不是下月1号。
2.在DayofMonth域用LW，表示这个月的最后一个工作日触发。
Month（月份）:域中可出现, - * /四个字符，以及1-12的整数或JAN-DEC的单词缩写

DayofWeek（星期）:可出现, - * / ? L # C八个字符，以及1-7的整数或SUN-SAT
单词缩写，1代表星期天，7代表星期六

C：在DayofWeek域使用2C，表示在2天后的那一天触发，且每周的那天都会触发。比如当前是周一，那么每周三都会触发。
L ：在DayofWeek域使用L，表示在一周的最后一天即星期六触发。在DayofWeek域使用5L，表示在一个月的最后一个星期四触发。
#：用来指定具体的周数，#前面代表星期几，#后面代表一个月的第几周，比如5#3表示一个月第三周的星期四。
?：在无法确定是具体哪一天时使用，用于DayofMonth和DayofWeek域。一般定义了其中一个域，另一个域是不确定的，比如每月20日触发，无法确定20日是星期几，写法如下：0 0 0 20 * ?；或者在每周一触发，此时无法确定该日期是几号，写法如下：0 0 0 ? * 2
Year（年份）：域中可出现, - * /四个字符，以及1970~2099的整数。该域可以省略，表示每年都触发。

含义	表达式
每隔5分钟触发一次	0 0/5 * * * *
每小时触发一次	0 0 * * * *
每天的7点30分触发	0 30 7 * * *
周一到周五的早上6点30分触发	0 30 7 ? * 2-6
每月最后一天早上10点触发	0 0 10 L * ?
每月最后一个工作日的18点30分触发	0 30 18 LW * ?
2030年8月每个星期六和星期日早上10点触发	0 0 10 ? 8 1,7 2030
每天10点、12点、14点触发	0 0 10,12,14 * * *
朝九晚五工作时间内每半小时触发一次	0 0 0/30 9-17 ? * 2-6
每周三中午12点触发一次	0 0 12 ? * 4
每天12点触发一次	0 0 12 * * *
每天14点到14:59每分钟触发一次	0 * 14 * * *
每天14点到14:59每5分钟触发一次	0 0/5 14 * * *
每天14点到14:05每分钟触发一次	0 0-5 14 * * *
每月15日上午10:15触发	0 15 10 15 * ?
每月最后一天的上午10:15触发	0 15 10 L * ?
每月的第三个星期五上午10:15触发	0 15 10 ? * 6#3
注解配置定时任务
我们也可以使用注解的方式配置定时任务：

在Spring配置文件中开启注解任务，使注解生效：
```
<!-- 开启注解任务 -->
<task:annotation-driven />
```
在方法上方添加@Scheduled，指定该方法定时执行
```
// 定时任务类
@Component
public class MySpringTask2 {
  // 打印时间
  @Scheduled(cron = "* * * * * *")
  public void printTime(){
    SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
    String now = sdf.format(new Date());
    System.out.println(now+"定时任务2");
   }
}
```
运行测试类

在配置配置文件开启注解任务：
在配置类上添加@EnableScheduling注解

多线程任务
Spring Task定时器默认是单线程的，如果项目中使用多个定时器，使用一个线程会造成效率低下。代码如下：
```
// 定时任务类
@Component
public class MySpringTask3 {
  // 5秒才可以执行完任务1
  @Scheduled(cron = "* * * * * *")
  public void task1() throws InterruptedException {
    System.out.println(Thread.currentThread().getId()+"线程执行任务1");
    Thread.sleep(5000);
   }


  @Scheduled(cron = "* * * * * *")
  public void task2() {
    System.out.println(Thread.currentThread().getId()+"线程执行任务2");
   }
}
```
任务1较浪费时间，会阻塞任务2的运行。此时我们可以给Spring Task配置线程池。
```
@Configuration
@ComponentScan("com.jjy")
@EnableScheduling
public class SpringConfig implements SchedulingConfigurer {
  @Override
  public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
    // 创建线程池
    taskRegistrar.setScheduler(Executors.newScheduledThreadPool(5));
   }
}
```
