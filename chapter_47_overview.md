# **Chapter 46. Build Init Plugin**

第46章 构建初始化插件

The Build Init plugin is currently incubating. Please be aware that the DSL and other configuration may change in later Gradle versions.

构建初始化插件正在开发中，在以后的gradle版本中可能会发生DSL或者其他配置的变化。

The Gradle Build Init plugin can be used to bootstrap the process of creating a new Gradle build. It supports creating brand new projects of different types as well as converting existing builds (e.g. An Apache Maven build) to be Gradle builds.

gradle构建初始化插件可以在引导一个新的gradle中间时使用，它支持在不同类型得项目中创建标记以更好的转化为gradle的构建任务，例如：Apache Maven build

Gradle plugins typically need to be applied to a project before they can be used (see Section 21.3, “Applying plugins”). The Build Init plugin is an automatically applied plugin, which means you do not need to apply it explicitly. To use the plugin, simply execute the task named init where you would like to create the Gradle build. There is no need to create a “stub” build.gradle file in order to apply the plugin.

Gradle插件需要在使用前在配置文件里声明，查看21.3“Applying plugins”，而构建初始化插件是呗自动应用的插件，那意味着你不需要在配置文件里去配置它，使用这个初始化插件其实就是在你创建Gradle build的时候就开始了，不需要创建一个“桩”build.gradle文件去应用该插件。

It also leverages the wrapper task from the Wrapper plugin (see Chapter 48, Wrapper Plugin), which means that the Gradle Wrapper will also be installed into the project.

在wrapper类型任务里也是一样，你只需要保证Gradle Wrapper插件配置到项目里了就好了。请查看48，Wrapper Plugin。

## **47.1. Tasks**

47.1 任务

The plugin adds the following tasks to the project:

插件添加如下的任务到项目里：

Table 47.1. Build Init plugin - tasks

|Task name|	Depends on|	Type|	Description|
|--
|init	|wrapper	|InitBuild|	Generates a Gradle project.|
|wrapper	|-	|Wrapper|	Generates Gradle wrapper files.|

## **47.2. What to set up**

47.2 需要设置什么

The init supports different build setup types. The type is specified by supplying a --type argument value. For example, to create a Java library project simply execute: gradle init --type java-library.

初始化支持不同得构建设置类型。类型可以使用参数来进行传递，例如：创建一个java library项目，你可以执行：gradle init --type java-library

If a --type parameter is not supplied, Gradle will attempt to infer the type from the environment. For example, it will infer a type value of “pom” if it finds a pom.xml to convert to a Gradle build.

如果--type参数不支持，那么gradle可以内引用环境变量。例如：我想要内引用一个pom类型得值，gradle将会寻找pom.xml
完成一个gradle构建

If the type could not be inferred, the type “basic” will be used.

如果类型没有因引用，那么basic类型会被采用

All build setup types include the setup of the Gradle Wrapper.

支持所有的构建类型包括Wrapper类型项目

## **47.3. Build init types**

47.3 构建初始化类型

As this plugin is currently incubating, only a few build init types are currently supported. More types will be added in future Gradle releases.

因为插件正在开发中，所以仅仅只有一少部分的初始化类型支持，更多得类型可以在未来的Gradle release版本中会加入。

### **47.3.1. “pom” (Maven conversion)**

47.3.1 “pom”（Maven标记）

The “pom” type can be used to convert an Apache Maven build to a Gradle build. This works by converting the POM to one or more Gradle files. It is only able to be used if there is a valid “pom.xml” file in the directory that the init task is invoked in. This type will be automatically inferred if such a file exists.

pom类型就可以将Apache Maven项目转化为gradle构建. 插件将把pom文件转化为一到多个gradle配置文件。这个插件仅仅在pom文件有效的情况下使用，如果这个文件存在，那么类型将会自动内引用。

The Maven conversion implementation was inspired by the maven2gradle tool that was originally developed by Gradle community members.

插件是使用maven2gradle工具实现的，工具是由gradle community 小组成员开发的。

The conversion process has the following features:

转化的过程有如下的特征：

Uses effective POM and effective settings (support for POM inheritance, dependency management, properties)

使用有效的POM文件以及有效的设置

Supports both single module and multimodule projects

支持单模块项目以及多样化模块项目

Supports custom module names (that differ from directory names)

支持自定义模块名

Generates general metadata - id, description and version

产生大致数据-例如id，描述，和版本

Applies maven, java and war plugins (as needed)

应用maven，java以及war插件

Supports packaging war projects as jars if needed

支持打包war项目到jar包

Generates dependencies (both external and inter-module)

生成依赖，包含外置以及内置依赖

Generates download repositories (inc. local Maven repository)

生成下载资源库，例如本地Maven仓库

Adjusts Java compiler settings

仅支持java编译设置

Supports packaging of sources and tests

支持打包原文件以及测试

Supports TestNG runner

支持TestNG runner

Generates global exclusions from Maven enforcer plugin settings

依靠maven实施插件设置生成全局扩展

### **47.3.2. “java-library”**

The “java-library” build init type is not inferable. It must be explicitly specified.

“java-library”类型初始化当前不支持推断使用，只能明确得声明为这个类型才能使用，这个和刚才的pom类型是有区别的，pom类型可以不声明type

It has the following features:

它有以下的特征：

Uses the “java” plugin

需要使用java插件

Uses the “jcenter” dependency repository

需要使用jcenter依赖资源库

Uses JUnit for testing

需要使用Junit测试

Has directories in the conventional locations for source code

拥有本地源码转化目录

Contains a sample class and unit test, if there are no existing source or test files

包含简单的类以及单元测试，如果项目不存在源码和测试文件的话


### **47.3.3. “scala-library”**

The “scala-library” build init type is not inferable. It must be explicitly specified.

scala-library也是需要明确指定type类型得

It has the following features:

它有以下特征：

Uses the “scala” plugin

使用scala插件

Uses the “jcenter” dependency repository

使用jcenter依赖仓库

Uses Scala 2.10

使用Scala2.10版本

Uses ScalaTest for testing

使用ScalaTest测试

Has directories in the conventional locations for source code

拥有本地源码转化目录

Contains a sample scala class and an associated ScalaTest test suite, if there are no existing source or test files

包含简单的scala类以及ScalaTest测试套件，如果项目不存在源码和测试文件的话

### **47.3.4. “groovy-library”**

The “groovy-library” build init type is not inferable. It must be explicitly specified.

groovy-library需要明确指定构建type

It has the following features:

拥有如下特征：

Uses the “groovy” plugin

使用groovy插件

Uses the “jcenter” dependency repository

使用jcenter仓库

Uses Groovy 2.x

使用groovy 2.x版本

Uses Spock testing framework for testing

使用Spock测试框架来测试

Has directories in the conventional locations for source code

拥有源码转化目录

Contains a sample Groovy class and an associated Spock specification, if there are no existing source or test files

包含简单的Groovy类以及associated Spock specification，如果项目不存在源码和测试文件的话

### **47.3.5. “basic”**

47.3.5  基本类型

The “basic” build init type is useful for creating a fresh new Gradle project. It creates a sample build.gradle file, with comments and links to help get started.

基本类型在创建一个新的gradle项目的时候非常有用，它可以创建一个简单的gradle构建文件，拥有备注以及连接帮助你开始使用。

This type is used when no type was explicitly specified, and no type could be inferred.

这种类型不需要你配置，也不需要你明确指定。