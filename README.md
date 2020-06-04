# 一、源码搭建说明
## 依赖环境
- apache-tomcat-8.5.55
- jdk 1.8.171
- maven
- idea

##源码搭建步骤

步骤一：官网下载tomcat源代码（https://tomcat.apache.org/download-80.cgi），解压缩得到目录。

![image-20200604141100527](C:\Users\tracy.chen\AppData\Roaming\Typora\typora-user-images\image-20200604141100527.png)

步骤二：IDEA新建Maven项目apache-tomcat-8.5.55-src，基于JDK 1.8.171，将解压后的tomcat源码拷贝到该项目下。

步骤三：pom.xml添加maven依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.apache.tomcat</groupId>
    <artifactId>apache-tomcat-8.5.55-src</artifactId>
    <name>Tomcat8.5.55</name>
    <version>8.5.55</version>

    <build>
        <!-- 指定源代码目录 -->
        <finalName>Tomcat8.5.55</finalName>
        <sourceDirectory>java</sourceDirectory>
        <resources>
            <resource>
                <directory>java</directory>
            </resource>
        </resources>

        <plugins>
            <!-- 引入编译插件，指定编译级别和编码 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                    <source>11</source>
                    <target>11</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <!-- Tomcat是java开发的，封装了很多功能，他需要依赖一些基础jar包 -->
    <dependencies>
        <!-- 远程过程调用工具包 -->
        <dependency>
            <groupId>javax.xml</groupId>
            <artifactId>jaxrpc</artifactId>
            <version>1.1</version>
        </dependency>
        <!-- soap协议处理工具包 -->
        <dependency>
            <groupId>javax.xml.soap</groupId>
            <artifactId>javax.xml.soap-api</artifactId>
            <version>1.4.0</version>
        </dependency>
        <!-- 解析webservice的wsdl文件工具 -->
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.2</version>
        </dependency>
        <!-- Eclipse java编译器 -->
        <dependency>
            <groupId>org.eclipse.jdt.core.compiler</groupId>
            <artifactId>ecj</artifactId>
            <version>4.5.1</version>
        </dependency>
        <!-- ant管理工具 -->
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.0</version>
        </dependency>
        <!-- easymock辅助单元测试 -->
        <dependency>
            <groupId>org.easymock</groupId>
            <artifactId>easymock</artifactId>
            <version>3.4</version>
        </dependency>
    </dependencies>
</project>
```

步骤四：在项目中创建source文件夹，将conf、webapps目录移动到改文件夹中。

步骤五：给tomcat的源码程序启动类Bootstrap配置VM参数，因为tomcat源码运行也需要加载配置文件等。

```
-Dcatalina.home=D:\work\workspace_new\github\apache-tomcat-8.5.55-src\apache-tomcat-8.5.55-src\source
-Dcatalina.base=D:\work\workspace_new\github\apache-tomcat-8.5.55-src\apache-tomcat-8.5.55-src\source
-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
-Djava.util.logging.config.file=D:\work\workspace_new\github\apache-tomcat-8.5.55-src\apache-tomcat-8.5.55-src\source\conf\logging.properties
```

步骤六：运行Bootstrap的main函数，此时就启动了tomcat。

步骤七：访问本地8080端口，发现报错。原因是tomcat中jsp的引擎Jasper并没有初始化，从而无法处理jsp。

![image-20200604212329029](C:\Users\tracy.chen\AppData\Roaming\Typora\typora-user-images\image-20200604212329029.png)

步骤八：org.apache.catalina.startup.ContextConfig#configureStart中增加如下代码，加载Jasper。

![image-20200604212510363](C:\Users\tracy.chen\AppData\Roaming\Typora\typora-user-images\image-20200604212510363.png)

步骤九：重新访问。

![image-20200604212540374](C:\Users\tracy.chen\AppData\Roaming\Typora\typora-user-images\image-20200604212540374.png)