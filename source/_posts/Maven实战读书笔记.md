---
title: Maven实战读书笔记
date: 2016-10-19 09:44:10
tags: 读书笔记
---

### 坐标和依赖
#### 依赖的范围
* compile 编译依赖范围。默认范围，对于编译、测试、运行三种classpath都有效
* test 测试依赖范围。只对测试classpath有效，如：JUnit
* provided 已提供依赖范围。对于编译和测试classpath有效，如：servlet-api
* runtime 运行时依赖范围。对于测试和运行classpath有效。如：JDBC驱动
* system 系统依赖范围
* import 导入依赖范围
 <!--more-->
### 生命周期
> Maven的生命周期包含了项目的清理、初始化、编译、测试、打包、集成测试、验证、部署和站点生成。Maven的生命周期是抽象的，实际的任务都交由插件完成。

#### 三套生命周期
##### clean生命周期
目的是清理项目
* pre-clean 执行一些清理前需要完成的工作
* clean 清理上一次构建生成的文件
* post-clean 执行一些清理后需要完成的工作

##### default生命周期
定义了真正构建时所需要执行的所有步骤，是所有生命周期中最核心的部分
* valicate
* initialize
* generate-sources
* process-sources 处理项目主资源文件，一般来说是对src/main/resources目录的内容进行变量替换等工作后，复制到项目输出的主classpath目录中
* generate-resources
* process-resources
* compile 编译项目的主源码，一般来说是src/main/java目录下的java文件到项目输出的主classpath目录中
* process-classes
* generate-test-sources
* process-test-sources 处理项目测试资源文件，一般来说是对src/test/resources目录的内容进行变量替换等工作后，复制到项目输出的测试classpath目录中
* generate-test-resources
* process-test-resources
* test-compile 编译项目的测试代码，一般来说是src/test/java目录下的java文件到项目输出的测试classpath目录中
* process-test-classes
* test 使用单元测试框架运行测试，测试代码不会被打包或部署
* prepare-package
* package 接受编译好的代码，打包成可发布的格式，如jar
* pre-integration-test
* interation-test
* post-integration-test
* verify
* install 将包安装到Maven本地仓库，供本地其他maven项目使用
* deploy 将最终的包复制到远程仓库，供其他开发人员个maven使用

##### site生命周期
目的是建立和发布项目站点，Maven能够基于POM所包含的信息，自己生成一个友好的站点，方便团队交流和发布项目信息
* pre-site 执行一些站点生成前所需完成的工作
* site 生成项目站点文档
* post-site 执行一些站点生成后需要完成的工作
* site-deploy 将生成的项目站点发布到服务器上

#### 命令行与生命周期
* mvn clean：该命令调用clean生命周期的clean阶段。实际执行的阶段为clean生命周期的pre-clean和clean阶段
* mvn test：调用default生命周期的test阶段。实际执行的阶段为default生周期的validate、initialize等，直到test的所有阶段。
* mvn install：调用clean生命周期的clean阶段和default生命周期的install阶段。实际执行的阶段为clean生命周期的pre-clean和clean阶段，以及default生周期的从validate到install的所有阶段。
* mvn clean deploy site-deploy：调用了clean生命周期的clean阶段、default生命周期的deploy阶段，以及site生命周期的site-deploy阶段。

#### 插件目标
一个插件可以实现多个功能，为了复用，将每个功能作为一个插件目标

#### 插件绑定
Maven的生命周期的阶段与插件的目标相互绑定，用以完成实际的构建任务。

如项目编译这一任务，它对应了default生命周期的compile这一阶段，而maven-compiler-plugin这一插件的compile目标能够完成该任务，因此，将他们绑定就能实现项目编码的目的。

##### 内置绑定
为了让用户几乎不用任何配置就能构建Maven项目，Maven在核心为一些主要的生命周期阶段绑定了很多插件的目标，当用户通过命令行调用生命周期阶段的时候，对应的插件目标就会执行相应的任务。

###### clean生命周期

生命周期阶段 | 插件目标
---|---
pre-clean | 
clean | maven-clean-plugin:clean
post-clean | 

###### site生命周期

生命周期阶段 | 插件目标
---|---
pre-site | 
site | maven-site-plugin : site
post-site | 
site-deploy | maven-site-plugin:deploy

###### default生命周期

生命周期阶段 | 插件目标 | 执行任务
---|---|---
process-resources | maven-resources-plugin:resources | 复制主资源文件至主输出目录
compile | maven-compiler-plugin:compile | 编译主代码至主输出目录
process-test-resources | maven-resources-plugin:testRecources | 复制测试资源文件至测试输出目录
test-compile | maven-compiler-plugin:testCompile | 编译测试代码至测试输出目录
test | maven-surefire-plugin:test | 执行测试用例
package | maven-jar-plugin:jar | 创建项目jar包
install | maven-install-plugin | 将项目输出构建安装到本地仓库
deploy | maven-deploy-plugin | 将项目输出构建安装到远程仓库

##### 自定义绑定
除了内置绑定外，用户还能自己选择将某个插件目标绑定到生命周期的某个阶段上。

如：创建项目的源码jar包

maven-source-plugin可以完成该任务，它的jar-no-fork目标能够将项目的主代码打包成jar文件，可以将其绑定到default生命周期的verify阶段上，在执行完集成测试后和安装构建之前创建源码jar包。

### 聚合与继承
#### 聚合
如果想要一次构建两个项目，而不是到两个模块的目录下分别执行mvn命令。Maven聚合（或者成为多模块）就是为该需求服务的。

需要额外再创建一个模块，通过其构建整个项目的所有模块。

POM
```
<groupId>...</groupId>
<artifactId>...</artifactId>
<version>...</version>
<packaging>POM</packaging>
<name>...</name>
<modules>
    <module>子模块1</module>
    <module>子模块2</module>
</modules>
```

#### 继承
如果几个模块拥有很多相同的配置，就可以通过POM的继承的机制抽取出重复的配置。

为了不给子模块引入多余的依赖，可以使用dependencyManagement元素。

dependencyManagement元素可以约束dependencies下的依赖的使用。

pluginManagement元素可以约束dependencies下的依赖的使用。

#### 反应堆
即构建各个模块的顺序，会根据依赖关系来构建。

##### 裁剪反应堆
-am  同时构建所列模块的依赖模块
-adm  同时构建依赖于所列模块的模块
-pl  构建指定的模块，模块间用逗号分隔
-rf  从指定的模块回复反应堆

### 使用Maven进行测试
#### maven-surfire-plugin
会自动执行测试源码路径下的所有符合一组命名模式的测试类。这组模式为：
* Test*.java 任意子目录下所有命名以Test开头的Java类。
* *Test.java 任意子目录下所有命名以Test结尾的Java类。
* *TestCase.java 任意子目录下所有命名以TestCase结尾的Java类。

#### 跳过测试
* mvn package-DskipTests
* 也可通过设置插件配置跳过

#### 测试报告
maven-surefire-plugin等插件可以为用户以文件的形式生成丰富的测试报告。


### 标签
```
<dependencies>
    <dependency>
        <exclusions>
            <exclusion>
            <!--排除依赖-->
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```



