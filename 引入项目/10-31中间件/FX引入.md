# Spring AOP 编程仿真实现报告

## 一、作业目的

本次作业旨在通过仿真实现Spring框架中的面向切面编程（AOP），加深对AOP概念及其在Spring中的应用的理解。通过编写示例项目，掌握如何使用Spring AOP进行方法拦截、增强以及切面的定义与配置。

## 二、作业要求

- 清晰叙述编程作业过程。
- 附上完整代码及运行结果截图。

## 三、作业环境

- **操作系统**：Mac OS
- **开发工具**：Xcode、Homebrew、Maven
- **编程语言**：Java
- **Spring框架版本**：5.3.15
- **编辑器**：Visual Studio Code

## 四、项目结构

项目名称：**SpringAOPExample**

```
SpringAOPExample/
├── pom.xml
├── src/
│   └── main/
│       └── java/
│           └── com/
│               └── example/
│                   ├── Config.java
│                   ├── Main.java
│                   ├── Service.java
│                   ├── Student.java
│                   └── Teacher.java
└── target/
```

## 五、项目代码

### 1. pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>spring-aop-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
    <dependencies>
        <!-- Spring Context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.15</version>
        </dependency>
        <!-- Spring AOP -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>5.3.15</version>
        </dependency>
        <!-- AspectJ Weaver -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.7</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <!-- Exec Maven Plugin -->
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>3.0.0</version>
                <configuration>
                    <mainClass>com.example.Main</mainClass>
                </configuration>
            </plugin>
            <!-- Maven Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### 2. Config.java

```java
package com.example;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@EnableAspectJAutoProxy
@ComponentScan("com.example")
public class Config {

    @Bean
    public Teacher teacher() {
        return new Teacher();
    }

    @Bean
    public Student student() {
        return new Student();
    }
}
```

### 3. Service.java

```java
package com.example;

public interface Service {
    void serve();
}
```

### 4. Teacher.java

```java
package com.example;

import org.springframework.stereotype.Component;

@Component
public class Teacher implements Service {
    private String course = "English";

    @Override
    public void serve() {
        System.out.println("Giving the " + course + " lecture.");
    }
}
```

### 5. Student.java

```java
package com.example;

import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class Student {

    @Before("execution(* com.example.Teacher.serve(..))")
    public void takeSeats() {
        System.out.println("Students are taking their seats...");
    }

    @Before("execution(* com.example.Teacher.serve(..))")
    public void silencePhones() {
        System.out.println("Students are silencing their phones...");
    }

    @AfterReturning("execution(* com.example.Teacher.serve(..))")
    public void askQuestions() {
        System.out.println("Class is over, any questions?");
    }
}
```

### 6. Main.java

```java
package com.example;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
        Service teacher = context.getBean(Service.class);
        teacher.serve();
    }
}
```

## 六、作业步骤

### 1. 安装 Xcode Command Line Tools

**终端指令：**

```bash
xcode-select --install
```

**作用**：安装Xcode的命令行工具，以便使用基本的开发工具进行编译和构建。

### 2. 安装 Homebrew

**终端指令：**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**作用**：Homebrew是macOS的包管理器，简化软件的安装和管理。

### 3. 配置 Homebrew 环境变量

**终端指令：**

```bash
echo >> ~/.zprofile
```

**作用**：在`.zprofile`文件中添加一个空行，确保后续的环境变量设置清晰可读。

### 4. 添加 Homebrew 环境变量到 `.zprofile`

**终端指令：**

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

**作用**：将Homebrew的环境变量添加到`.zprofile`文件，以便系统能够识别`brew`命令。

### 5. 使 Homebrew 环境变量立即生效

**终端指令：**

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

**作用**：使前面的环境变量配置立即生效。

### 6. 验证 Homebrew 是否安装成功

**终端指令：**

```bash
brew --version
```

**作用**：确认Homebrew是否成功安装及其版本。

### 7. 更新 Homebrew 和包列表

**终端指令：**

```bash
brew update
```

**作用**：更新Homebrew本身以及已安装软件包的列表。

### 8. 验证 Maven 是否安装成功

**终端指令：**

```bash
mvn -v
```

**作用**：检查Maven是否成功安装及其版本。

### 9. 进入项目目录

**终端指令：**

```bash
cd ~/Java/SpringAOPExample
```

**作用**：切换到项目的工作目录`SpringAOPExample`。

### 10. 清理并编译 Maven 项目

**终端指令：**

```bash
mvn clean compile
```

**作用**：清理项目并编译，确保项目的构建是最新的。

### 11. 安装并构建 Maven 项目

**终端指令：**

```bash
mvn clean install
```

**作用**：清理项目并重新构建，确保项目的构建是最新的。

### 12. 执行 Maven 项目中的主类

**终端指令：**

```bash
mvn exec:java -Dexec.mainClass="com.example.Main"
```

**作用**：运行Maven项目中的主类，输出程序运行结果。

## 七、作业结果

在成功执行上述指令后，控制台输出如下：

```
Students are silencing their phones...
Students are taking their seats...
Giving the English lecture.
Class is over, any questions?
```

**说明**：

- `Students are silencing their phones...` 和 `Students are taking their seats...` 是在`Teacher.serve()`方法执行前由`@Before`切面方法拦截并执行的增强逻辑。
- `Giving the English lecture.` 是`Teacher`类中的`serve()`方法的实际业务逻辑输出。
- `Class is over, any questions?` 是在`Teacher.serve()`方法执行后由`@AfterReturning`切面方法拦截并执行的增强逻辑。

**截图**：

（请在报告中插入上述控制台输出的截图以证明作业的成功执行。）

## 八、注意事项

1. **target目录**：`target`文件夹是在运行Maven构建时自动生成的，可以根据需要保留或删除。为了提高构建速度，建议定期清理。

2. **项目路径**：确保在执行Maven命令前已切换到项目的根目录`SpringAOPExample`。

3. **Windows用户**：Windows系统上不需要安装Homebrew，但可以使用Chocolatey作为包管理器来管理软件包。

4. **Java版本**：本项目使用Java 1.8，请确保本地环境中已安装对应版本的JDK。

5. **依赖管理**：`pom.xml`中声明了必要的Spring和AspectJ依赖，确保网络通畅以便Maven能成功下载依赖包。

## 九、总结

通过本次作业，成功仿真实现了Spring AOP的基本功能，包括定义切面、配置AOP代理、拦截目标方法以及添加前置和后置增强逻辑。该过程不仅加深了对AOP概念的理解，还提升了使用Spring框架进行实际项目开发的能力。