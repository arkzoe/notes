- 坐标
	- jar唯一标识，通过该坐标唯一定位资源位置
	- groupId：Maven项目隶属组织名称
	- artifactId：项目名称
	- version
		- SNAPSHOT：快照版
		- RELEASE：发行版
	- pom.xml写入dependencies
	- dependency引入坐标
	- 定义
- exclusions排除依赖，刷新
# maven生命周期
- clean：清理
	- clean：移除上一次构建生成的代码
- default：核心，编译、测试、打包、安装、部署等
	- compile：编译项目源码
	- test：使用合适单元测试框架进行测试
	- package：将编译后的文件打包
	- install：安装项目到本地仓库
- site：生成报告、发布站点
# 测试
- 单元测试、集成测试、系统测试、验收测试
- 白盒测试
	- 验证代码、逻辑正确性
- 黑盒测试
	- 验证软件功能、兼容性
- 灰盒测试
	- 即关注内部结构又考虑外部表现
- [[Java高级技术#单元测试]]
# 依赖范围
- scope设置范围
- 主程序main
- 测试test
- 打包package

| 值           | 主程序 | 测试  | 打包  |
| ----------- | --- | --- | --- |
| compile(默认) | Y   | Y   | Y   |
| test        | -   | Y   | -   |
| provided    | Y   | Y   | -   |
| runtime     | -   | Y   | Y   |
# 常见问题
- 依赖爆红--重下
# 高级
## 分模块
1. 按功能模块拆分
2. 按层拆分
3. 功能模块+层
- 引入其他模块
- 引入前需打包安装
## 依赖传递
- 依赖：当前项目运行需要的jar
- 格式dependencies.../dependencies
- 传递性
	- 直接依赖：在当前项目中通过依赖配置建立的依赖关系
	- 间接依赖：被资源的资源如果依赖其他资源，当前项目间接依赖其他资源
- 传递冲突
	- 路径优先：当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高
	- 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
	- 特殊优先：当同级配置了相同资源的不同版本，后配置的覆盖先配置的
- 可选依赖
	- 坐标中加`optional`true，隐藏当前工程所依赖的资源，隐藏以后不具有依赖传递性
- 排除依赖
	- 坐标中加`exclusions`，隐藏当前资源对应的依赖关系，指定GA
## 聚合
- 将多个模块组织成一个整体，同时进行项目的构建
 - 聚合工程
	- 一个不具有业务功能的“空”工程(有且仅有一个pom文件)
- 作用
	- 快速构建项目(无需根据依赖关系手动构建，直接在聚合工程上构建即可)
- 实现
	- maven中可以通过 < modules >设置当前聚合工程所包含的子模块名称
## 继承
- 概念：继承描述的是两个工程间的关系，与java中的继承相似，子工程可以继承父工程中的配置信息，常见于依赖关系的继承
- 作用：简化依赖配置、统一管理依赖
- 实现 ：< parent> ...< /parent>
1. 创建maven模块parent，为父工程，设置==打包方式pom==（默认jar）
	- jar：普通模块打包，springboot项目基本都是jar包(内嵌tomcat运行)
	- war：普通web程序打包，需要部署在外部的tomcat服务器中运行
	- pom：父工程或聚合工程，该模块不写代码，仅进行依赖管理
2. ==子工程==的pom.xml，配置继承关系
3. ==父工程==中配置各个工程共有的依赖--dependencies直接引入依赖
## 属性
- properties中存变量名

| 分类        | 引用格式            |
| --------- | --------------- |
| 自定义属性     | ${自定义属性名}       |
| 内置属性      | ${内置属性名}        |
| Setting属性 | ${setting.属性名}  |
| Java系统属性  | ${系统属性分类.系统属性名} |
| 环境变量属性    | ${env.环境变量属性名}  |
```xml
<properties>
	<lombok.version>1.18.30</lombok.version>
	<jjwt.version>0.9.1</jjwt.version>
</properties>

<dependencies>
	<dependency>
		<groupId>org.projectlombok</groupId>
		<artifactId>lombok</artifactId>
		<version>${lombok.version}</version>
	</dependency>
</dependencies>
```
### 配置文件加载属性
1. 定义属性
```xml
<properties>
	<spring.version>5.2.10.RELEASE</spring.version>
	<junit.version>4.12</junit.version>
	<jdbc.ur1>jdbc:mysq1://127.0.0.1:3306/ssm_db</jdbc.url>
</properties>
```
2. 配置文件中引用
```xml
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=${jdbc.url}
jdbc.username=root
jdbc.password=root
```
3. 开启资源文件目录加载属性的过滤器
```xml
<build>
	<resources>
		<resource>
			<directory>${project.basedir}/src/main/resources</directory>
			<filtering>true</filtering>
		</resource>
	</resources>
</build>
```
4. maven打包war包，忽略web.xml检查
```xml
<plugin>
	<groupId>org.apache.maven.plugins</ groupId>
	<artifactId>maven-war-plugin</artifactId>
	<version>3.2.3</version>
	<configuration>
		<fail0nMissingWebXml>false</fail0nMissingWebXml>
	</configuration>
</plugin>
```
### 版本管理
- 工程版本:
	- SNAPSHOT（快照版本）
		- 项目开发过程中临时输出的版本，称为快照版本
		- 快照版本会随着开发的进展不断更新
	- RELEASE（发布版本）
		- 项目开发到进入阶段里程碑后，向团队外部发布较为稳定的版本，这种版本所对应的构件文件是稳定的，即便进行功能的后续开发，也不会改变当前发布版本内容，这种版本称为发布版本
- 发布版本
	- alpha版
	- beta版
	- 纯数字版
## 多环境配置与应用
### 多环境开发
1. 定义多环境
```xml
<！--定义多环境-->
<profiles>
	<!--定义具体的环境：生产环境-->
	<profile>
		<!--定义环境对应的唯一名称-->
		<id>env_dep</id>
		<!--定义环境中专用的属性值-->
		<properties>
			<jdbc.ur1>jdbc:mysql: //127.0.0.1:3306/ssm_db</jdbc.url>
		</properties>
		<！--设置默认启动-->
		<activation>
			<activeByDefault>true</activeByDefault>
		</activation>
	</profile>
		<！--定义具体的环境：开发环境--><profile>
		<id>env_pro</id>
	</profile>
</profiles>
```
2. 使用多环境
```bash
mvn 指令 -P 环境定义id
# 例如
mvn intall -P pro_env
```
### 跳过测试
- 场景
	- 功能更新中没开发完
	- 快速打包
```bash
mvn 指令 -D skipTests
```
- 细粒度控制跳过
```xml
<plugin>
	<artifactId>maven-surefire-plugin</artifactId>
	<version>2.22.1</version>
	<configuration>
	<skipTests>true</skipTests><!--设置跳过测试-->
	<includes><!--包含指定的测试用例-->
		<include>**/User*Test.java</include>
	</includes>
	<excludes><!--排除指定的测试用例-->
		<exclude>**/User*TestCase.java</exclude>
	</excludes>
	</configuration>
</plugin>
```
## 私服
1. 设置私服访问用户名/密码（settings.xml中的servsers配置）
2. idea的maven工程pom文件中配置上传地址
3. 设置私服依赖下载的仓库组地址（settings.xml中的mirrors、profiles配置）