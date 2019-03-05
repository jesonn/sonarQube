# sonarQube
## 能解决什么问题：
SonarQube是一个开源的代码质量分析平台，便于管理代码的质量，可检查出项目代码的漏洞和潜在的逻辑问题，代码质量的评分，健康状况等。而且提供了丰富的插件，支持多种语言的检测， 如 Java、Javascript、Python、C#、等编程语言的检测。

## 配置安装流程
使用sonarQube需要jdk支持，还需要数据库来存放检测代码的数据报告，检测的时候还需要使用sonar-runner来使用。

以下需要用到的软件：
- [JDK (JAVA的运行环境)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- [SonarQube](https://www.sonarqube.org/downloads/)
- [sonar-runner（项目检测使用）](https://docs.sonarqube.org/display/sonarqube45/installing+and+configuring+sonarqube+runner)
- [MySQL ( 创建Sonar数据库 )](http://link.zhihu.com/?target=https%3A//dev.mysql.com/downloads/)


## (1) 安装JDK
JDK不详细写了，以下是百度的教程，怎么去安装和配置环境变量：
https://jingyan.baidu.com/article/6dad5075d1dc40a123e36ea3.html

## (2) 安装sonarQube
- 下载完直接解压SonarQube就好，进入sonar/bin目录下，进入对应的系统目录，启动sonar，再用浏览器打开，输入URL：localhost:9000，如果可以看到后台界面说明安装成功。


![](https://user-gold-cdn.xitu.io/2019/2/25/169249b6255cde9d?w=1920&h=604&f=png&s=431212)

## (3) 配置数据库和修改Sonar配置
(1) 安装完sonar还需要创建sonar的用户和数据库，使用MySQL Command Line Client输入以下命令创建（或者使用Navicat Premium客户端来建立一个Sonar数据库）

打开数据库命令行客户端，输入命令：
```
　CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci; // 创建sonar数据库
　CREATE USER 'sonar' IDENTIFIED BY 'sonar'; // 创建sonar用户
　GRANT ALL ON sonar.* TO 'sonar'@'%' IDENTIFIED BY 'sonar'; // 给sonar用户分配可对所有数据库的所有表进行所有操作的权限，并设定口令为sonar
　GRANT ALL ON sonar.* TO 'sonar'@'localhost' IDENTIFIED BY 'sonar'; // 给本机用户sonar分配可对所有数据库的所有表进行所有操作的权限，并设定口令为sonar
　FLUSH PRIVILEGES; // 刷新权限
```

查看是否成功创建了一个名为sonar的数据库：
```
show databases;
```

![](https://user-gold-cdn.xitu.io/2019/2/25/169249bce8b653c0?w=699&h=462&f=png&s=54189)

(2) 修改Sonar配置，找到安装包下conf/sonar.properties文件，去掉sonar.jdbc.username、sonar.jdbc.password、sonar.jdbc.url前面的注释符号，再填写具体的username、password和url：
```
sonar.jdbc.username=root
sonar.jdbc.password=123456
sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false
```

![](https://user-gold-cdn.xitu.io/2019/2/25/169249c2996fea82?w=1896&h=879&f=png&s=155213)
## 在开发项目中使用：
Sonar的命令行分析端软件有两种分别是Sonar-Runner和Sonar-Scanner，这里我们用Sonar-Runner来验证。

- (1) [安装Sonar-Runner](https://link.jianshu.com/?t=http://repo1.maven.org/maven2/org/codehaus/sonar/runner/sonar-runner-dist/2.4/sonar-runner-dist-2.4.zip)
- (2) 配置Sonar-Runner，找到安装包下conf/sonar-runner.properties文件，配置如下：
```
sonar.jdbc.username=root 
sonar.jdbc.password=123456 
sonar.jdbc.url=jdbc:postgresql: 
```
- (3) 在检测项目根目录新建sonar-project.properties，配置如下：
```
sonar.sources=.
sonar.sourceEncoding=UTF-8
sonar.projectVersion=1.0
sonar.projectName=test // 填写自己的项目名称
sonar.projectKey=test // 填写自己的项目的唯一标识
```

 在项目根目录中执行命令：
```
sonar-runner
```

检测完成，再次打开localhost:9000，就可以看到项目的检测报告。


![](https://user-gold-cdn.xitu.io/2019/2/25/169249d06d4c7920?w=1911&h=896&f=png&s=161370)
## 在jenkins构建中使用：
我们的项目测试都需要经过jenkins构建发布，在jenkins构建这里加上SonarQube来监控代码是比较合理的。
- (1) 安装jenkins插件SonarQube Scanner For Jenkins


![](https://user-gold-cdn.xitu.io/2019/2/25/169249daf8eaca85?w=1912&h=831&f=png&s=133666)
- (2) 配置sonar服务，Manage Jenkins > Configure System > SonarQube servers，并添加sonar token


![](https://user-gold-cdn.xitu.io/2019/2/25/169249e0f685fb59?w=1919&h=748&f=png&s=91949)
- (3) 设置sonar安装路径，Manage Jenkins > Global Tool Configuration


![](https://user-gold-cdn.xitu.io/2019/2/25/169249e834dc515e?w=1912&h=895&f=png&s=91685)
- (4) 任务设置，动态化添加sonar-project.properties参数


![](https://user-gold-cdn.xitu.io/2019/2/25/169249ebeb969cb6?w=1915&h=850&f=png&s=83926)
- (5) 构建任务完成后即可打开SonarQube后台地址查看分析报告。


![](https://user-gold-cdn.xitu.io/2019/2/25/169249f0c6e1ca95?w=1916&h=887&f=png&s=140875)