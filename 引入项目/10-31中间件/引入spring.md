J2EE第三次作业: 对Spring中的AOP编程进行仿真实现

作业报告：Spring AOP 编程仿真实现

一、作业目的

对Spring AOP编程部分内容的仿真实现。

二要求编程作业过程要叙述清晰，附代码和结果运行图。

三、 作业环境

操作系统：Mac OS 开发工具：Xcode、Homebrew、Maven 编程语言：Java Spring框架版本：最新稳定版编辑器：vs code

四、 作业 SpringAOPExample 项目文件结构

SpringAOPExample/

├── pom.xml

├── src/

│   └── main/

│       └── java/

│           └── com/

│               └── example/

│                   ├── Config.java

│                   ├── Main.java

│                   ├── Service.java

│                   ├── Student.java

│                   └── Teacher.java

└── target/

五、作业项目文件代码

pom.xml文件

<project xmlns="http://maven.apache.org/POM/4.0.0"           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>

    <artifactId>spring-aop-demo</artifactId>

    <version>1.0-SNAPSHOT</version>

    <properties>

        <maven.compiler.source>1.8</maven.compiler.source>

        <maven.compiler.target>1.8</maven.compiler.target>

    </properties>

    <dependencies>

        <dependency>

            <groupId>org.springframework</groupId>

            <artifactId>spring-context</artifactId>

            <version>5.3.15</version>

        </dependency>

        <dependency>

            <groupId>org.springframework</groupId>

            <artifactId>spring-aop</artifactId>

            <version>5.3.15</version>

        </dependency>

        <dependency>

            <groupId>org.aspectj</groupId>

            <artifactId>aspectjweaver</artifactId>

            <version>1.9.7</version>

        </dependency>

    </dependencies>

    <build>

        <plugins>

            <plugin>

                <groupId>org.codehaus.mojo</groupId>

                <artifactId>exec-maven-plugin</artifactId>

                <version>3.0.0</version>

                <configuration>

                    <mainClass>com.example.Main</mainClass>

                </configuration>

            </plugin>

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

Config.java文件

package com.example;

import org.springframework.context.annotation.Bean; import org.springframework.context.annotation.ComponentScan; import org.springframework.context.annotation.Configuration; import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration

@EnableAspectJAutoProxy

@ComponentScan("com.example") public class Config {

    @Bean

    public Teacher teacher() {         return new Teacher();

    }

    @Bean

    public Student student() {         return new Student();

    }

}

Main.java文件

package com.example;

import org.springframework.context.ApplicationContext; import org.springframework.context.annotation.AnnotationConfigApplicationCon text;

public class Main {     public static void main(String[] args) {

        ApplicationContext context = new

AnnotationConfigApplicationContext(Config.class);

        Service teacher = context.getBean(Service.class);         teacher.serve();

    }

}

Service.java文件

package com.example;

public interface Service {     void serve();

}

Student.java文件 package com.example;

import org.aspectj.lang.annotation.AfterReturning; import org.aspectj.lang.annotation.Before; import org.aspectj.lang.annotation.Aspect; import org.springframework.stereotype.Component;

@Aspect

@Component

public class Student {

    @Before("execution(* com.example.Teacher.serve(..))")     public void takeSeats() {

        System.out.println("Students are taking their seats...");

    }

    @Before("execution(* com.example.Teacher.serve(..))")     public void silencePhones() {

        System.out.println("Students are silencing their phones...");

    }

    @AfterReturning("execution(* com.example.Teacher.serve(..))")     public void askQuestions() {

        System.out.println("Class is over, any questions?");

    }

}

Teacher.java文件 package com.example;

import org.springframework.stereotype.Component;

@Component

public class Teacher implements Service {     private String course = "English";     @Override

    public void serve() {

        System.out.println("Giving the " + course + " lecture.");

    }

}

六、作业步骤

1.  安装 Xcode Command Line Tools 终端指令：

xcode-select --install

作用：安装 Xcode 的命令行工具，以便使用基本的开发工具进行编译和构建。



2.  安装 Homebrew 终端指令：

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Home brew/install/HEAD/install.sh)" 作用：Homebrew 是 macOS 的包管理器，简化软件的安装和管理。


3.  配置 Homebrew 环境变量终端指令：

echo >> /Users/macbook/.zprofile

作用：在 .zprofile 文件中添加一个空行，确保后续的环境变量设置清晰可读。



4.  将 Homebrew 的环境变量添加到 .zprofile 终端指令：

echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/macboo

k/.zprofile

作用：将 Homebrew 的环境变量添加到 .zprofile 文件，以便系统能够识别 brew 命令。



5.  立即生效 Homebrew 环境变量终端指令：

eval "$(/opt/homebrew/bin/brew shellenv)" 作用：使前面的环境变量配置立即生效。



6.  验证 Homebrew 是否安装成功

终端指令：

brew --version 作用：确认 Homebrew 是否成功安装及其版本。


7.  更新 Homebrew 和包列表终端指令：

brew update 作用：更新 Homebrew 本身以及已安装软件包的列表。



8.  验证 Maven 是否安装成功

终端指令：

mvn -v 作用：检查 Maven 是否成功安装及其版本。


9.  执行 Maven 项目中的主类

终端指令：

mvn exec:java -Dexec.mainClass="com.example.Main" 说明：运行 Maven 项目中的主类，输出程序运行结果。


10. 进入 SpringAOPExample 目录终端指令：

cd ~/Java/SpringAOPExample 作用：切换到项目的工作目录 SpringAOPExample。



11.  清理并编译 Maven 项目终端指令：

mvn clean compile 作用：清理项目并编译，确保项目的构建是最新的。



12.  安装并构建 Maven 项目终端指令：

mvn clean install 作用：清理项目并重新构建，确保项目的构建是最新的。



13.  执行 Maven 项目中的主类

终端指令：

mvn exec:java -Dexec.mainClass="com.example.Main" 作用：再次运行 Maven 项目中的主类，确保项目的功能正常。



四、作业结果

在成功执行以上指令后，Spring AOP应用程序正常运行，控制台输出如下：



可以看到：

Students are silencing their phones...

Students are taking their seats...

Giving the English lecture.

Class is over, any questions? 出现了，即作业成功。

注释1: target文件目录是在运行Maven时系统加上去的，可以删掉，为了提高运行速度可以不删。注释2:要进入SpringAOPExample目录才运行Maven。

注释3:windows上不需要Homebrew，但是可以考虑安装Chocolatey来管理软件包。