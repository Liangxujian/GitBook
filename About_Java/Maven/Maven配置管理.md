#### Maven 配置管理

本篇笔记参考自博客：https://www.cnblogs.com/s1165482267/p/7928275.html

settings.xml 配置文件是用来重新指定本地仓库位置的，需注意的是此时配置的 Maven 的本地仓库是属于用户范围的。

##### 一、原始 settings.xml 文件

````xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

<!--
 | This is the configuration file for Maven. It can be specified at two levels:
 |
 |  1. User Level. This settings.xml file provides configuration for a single user,
 |                 and is normally provided in ${user.home}/.m2/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -s /path/to/user/settings.xml
 |
 |  2. Global Level. This settings.xml file provides configuration for all Maven
 |                 users on a machine (assuming they're all using the same Maven
 |                 installation). It's normally provided in
 |                 ${maven.conf}/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -gs /path/to/global/settings.xml
 |
 | The sections in this sample file are intended to give you a running start at
 | getting the most out of your Maven installation. Where appropriate, the default
 | values (values used when the setting is not specified) are provided.
 |
 |-->
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
  <localRepository>
    D:/apache-maven-3.5.2/repository
  </localRepository>

  <!-- interactiveMode
   | This will determine whether maven prompts you when it needs input. If set to false,
   | maven will use a sensible default value, perhaps based on some other setting, for
   | the parameter in question.
   |
   | Default: true
  <interactiveMode>true</interactiveMode>
  -->

  <!-- offline
   | Determines whether maven should attempt to connect to the network when executing a build.
   | This will have an effect on artifact downloads, artifact deployment, and others.
   |
   | Default: false
  <offline>false</offline>
  -->

  <!-- pluginGroups
   | This is a list of additional group identifiers that will be searched when resolving plugins by their prefix, i.e.
   | when invoking a command line like "mvn prefix:goal". Maven will automatically add the group identifiers
   | "org.apache.maven.plugins" and "org.codehaus.mojo" if these are not already contained in the list.
   |-->
  <pluginGroups>
    <!-- pluginGroup
     | Specifies a further group identifier to use for plugin lookup.
    <pluginGroup>com.your.plugins</pluginGroup>
    -->
  </pluginGroups>

  <!-- proxies
   | This is a list of proxies which can be used on this machine to connect to the network.
   | Unless otherwise specified (by system property or command-line switch), the first proxy
   | specification in this list marked as active will be used.
   |-->
  <proxies>
    <!-- proxy
     | Specification for one proxy, to be used in connecting to the network.
     |
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
    -->
  </proxies>

  <!-- servers
   | This is a list of authentication profiles, keyed by the server-id used within the system.
   | Authentication profiles can be used whenever maven must make a connection to a remote server.
   |-->
  <servers>
    <!-- server
     | Specifies the authentication information to use when connecting to a particular server, identified by
     | a unique name within the system (referred to by the 'id' attribute below).
     |
     | NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are
     |       used together.
     |
    <server>
      <id>deploymentRepo</id>
      <username>repouser</username>
      <password>repopwd</password>
    </server>
    -->

    <!-- Another sample, using keys to authenticate.
    <server>
      <id>siteServer</id>
      <privateKey>/path/to/private/key</privateKey>
      <passphrase>optional; leave empty if not used.</passphrase>
    </server>
    -->
  </servers>

  <!-- mirrors
   | This is a list of mirrors to be used in downloading artifacts from remote repositories.
   |
   | It works like this: a POM may declare a repository to use in resolving certain artifacts.
   | However, this repository may have problems with heavy traffic at times, so people have mirrored
   | it to several places.
   |
   | That repository definition will have a unique id, so we can create a mirror reference for that
   | repository, to be used as an alternate download site. The mirror site will be the preferred
   | server for that repository.
   |-->
  <mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>repositoryId</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
      <url>http://my.repository.com/repo/path</url>
    </mirror>
     -->
    <mirror>         
      <id>alimaven</id>
      <name>aliyun maven</name> 
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url> 
      <mirrorOf>central</mirrorOf> 
    </mirror> 

  </mirrors>

  <!-- profiles
   | This is a list of profiles which can be activated in a variety of ways, and which can modify
   | the build process. Profiles provided in the settings.xml are intended to provide local machine-
   | specific paths and repository locations which allow the build to work in the local environment.
   |
   | For example, if you have an integration testing plugin - like cactus - that needs to know where
   | your Tomcat instance is installed, you can provide a variable here such that the variable is
   | dereferenced during the build process to configure the cactus plugin.
   |
   | As noted above, profiles can be activated in a variety of ways. One way - the activeProfiles
   | section of this document (settings.xml) - will be discussed later. Another way essentially
   | relies on the detection of a system property, either matching a particular value for the property,
   | or merely testing its existence. Profiles can also be activated by JDK version prefix, where a
   | value of '1.4' might activate a profile when the build is executed on a JDK version of '1.4.2_07'.
   | Finally, the list of active profiles can be specified directly from the command line.
   |
   | NOTE: For profiles defined in the settings.xml, you are restricted to specifying only artifact
   |       repositories, plugin repositories, and free-form properties to be used as configuration
   |       variables for plugins in the POM.
   |
   |-->
  <profiles>
    <!-- profile
     | Specifies a set of introductions to the build process, to be activated using one or more of the
     | mechanisms described above. For inheritance purposes, and to activate profiles via <activatedProfiles/>
     | or the command line, profiles have to have an ID that is unique.
     |
     | An encouraged best practice for profile identification is to use a consistent naming convention
     | for profiles, such as 'env-dev', 'env-test', 'env-production', 'user-jdcasey', 'user-brett', etc.
     | This will make it more intuitive to understand what the set of introduced profiles is attempting
     | to accomplish, particularly when you only have a list of profile id's for debug.
     |
     | This profile example uses the JDK version to trigger activation, and provides a JDK-specific repo.
    <profile>
      <id>jdk-1.4</id>

      <activation>
        <jdk>1.4</jdk>
      </activation>

      <repositories>
        <repository>
          <id>jdk14</id>
          <name>Repository for JDK 1.4 builds</name>
          <url>http://www.myhost.com/maven/jdk14</url>
          <layout>default</layout>
          <snapshotPolicy>always</snapshotPolicy>
        </repository>
      </repositories>
    </profile>
    -->

    <!--
     | Here is another profile, activated by the system property 'target-env' with a value of 'dev',
     | which provides a specific path to the Tomcat instance. To use this, your plugin configuration
     | might hypothetically look like:
     |
     | ...
     | <plugin>
     |   <groupId>org.myco.myplugins</groupId>
     |   <artifactId>myplugin</artifactId>
     |
     |   <configuration>
     |     <tomcatLocation>${tomcatPath}</tomcatLocation>
     |   </configuration>
     | </plugin>
     | ...
     |
     | NOTE: If you just wanted to inject this configuration whenever someone set 'target-env' to
     |       anything, you could just leave off the <value/> inside the activation-property.
     |
    <profile>
      <id>env-dev</id>

      <activation>
        <property>
          <name>target-env</name>
          <value>dev</value>
        </property>
      </activation>

      <properties>
        <tomcatPath>/path/to/tomcat/instance</tomcatPath>
      </properties>
    </profile>
    -->
  </profiles>

  <!-- activeProfiles
   | List of profiles that are active for all builds.
   |
  <activeProfiles>
    <activeProfile>alwaysActiveProfile</activeProfile>
    <activeProfile>anotherAlwaysActiveProfile</activeProfile>
  </activeProfiles>
  -->
</settings>
````

该settings.xml位于maven文件夹中的conf文件夹下

##### 二、配置修改

略过第一段注释（全是废话），看第二段注释

如第二段注释所说，配置有2处：用户配置和全局配置

用户配置路径为`${user.home}/.m2/settings.xml`，该配置需用户自行创建

全局配置路径为`${maven.conf}/settings.xml`，该配置为maven自带配置，即原文所在位置

当两者都存在时，Maven 会合并这2个配置；有相同配置参数时，有效值为用户配置中的参数

去除原有注释，为配置正文部分加上中文解释，如下

````xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
	<!-- 本地仓库路径，默认路径为${user.home}/.m2/repository -->
	<!-- 为了避免重装系统而被删除，可在maven文件夹下创建个repository文件夹，将该节点改为该repository文件夹的路径即可 -->
	<localRepository>${user.home}/.m2/repository</localRepository>

	<!-- 互动模式，默认值为true。该值使用默认值即可，无需更改 -->
	<interactiveMode>true</interactiveMode>

	<!-- 离线模式，默认值为false。当因为各种原因无法与远程仓库连接时，可将该值修改为true -->
	<offline>false</offline>

	<!-- 插件组，内部由n个pluginGroup节点组成，默认自带了org.apache.maven.plugins和org.codehaus.mojo。若有特殊的需求是可进行扩展:
	<pluginGroups>
		<pluginGroup>com.your.plugins</pluginGroup>
	</pluginGroups> -->
	<pluginGroups></pluginGroups>

	<!-- 该标签参考外部说明 -->
	<proxies></proxies>

	<!-- 该标签参考外部说明 -->
	<servers></servers>

	<!-- 该标签参考外部说明 -->
	<mirrors></mirrors>

	<!-- 该标签参考外部说明 -->
	<profiles></profiles>

	<!-- 该标签参考外部说明 -->
	<activeProfiles></activeProfiles>
</settings>
````

###### proxies 标签说明

proxies 标签用于代理设置，内部由 n 个 proxy 节点组成。部分公司出于安全考虑会需要员工通过代理才可上网，这时，就需配置该节点的参数了。

````xml
<proxies>
	<proxy>
        <!-- 唯一标识符 -->
		<id>optional</id>
        <!-- 是否被激活使用，默认为true。当有多个proxy节点存在时，用于选择使用哪个代理，但同一时间仅有一个代理会被激活 -->
		<active>true</active>
        <!-- 协议类型 -->
		<protocol>http</protocol>
        <!-- 用户名，没有可不填 -->
		<username>proxyuser</username>
        <!-- 密码，没有可不填 -->
		<password>proxypass</password>
        <!-- IP地址 -->
		<host>proxy.host.net</host>
		<!-- 端口号 -->
		<port>80</port>
		<!-- 不使用代理的地址 -->
		<nonProxyHosts>local.net|some.host.com</nonProxyHosts>
	</proxy>
</proxies>
````

###### servers 标签说明

服务器，内部由 n 个 server 节点组成。有2种配置方式：一种是通过 id 匹配，username 和 password 进行认证登录；另一种是通过 id 匹配指向一个 privateKey（私钥）和一个 passphrase。

````xml
<servers>
	<server>
		<id>deploymentRepo</id>
		<username>repouser</username>
		<password>repopwd</password>
	</server>
	<server>
		<id>siteServer</id>
		<privateKey>/path/to/private/key</privateKey>
		<passphrase>optional; leave empty if not used</passphrase>
	</server>
</servers>
````

###### mirrors 标签说明

````xml
<mirrors>
    <mirror>
		<id>mirrorId</id>
		<mirrorOf>repositoryId</mirrorOf>
		<name>Human Readable Name for this Mirror.</name>
		<url>http://my.repository.com/repo/path</url>
    </mirror>
</mirrors>
````

镜像仓库，内部由 n 个 mirror 节点组成。用来代替中央仓库（Maven 默认的远程仓库），用于提升传输速度。因此此处可以添加一个镜像，以提升我们下载的速度。

````xml
<mirrors>
    <mirror>         
		<id>alimaven</id>
		<name>aliyun maven</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <!-- 如果仓库的 id 设置成"central"，则该配置会覆盖 Maven 默认的中央仓库配置 -->
		<mirrorOf>central</mirrorOf> 
    </mirror> 
</mirrors>
````

###### profiles 标签说明

用于覆盖在一个 POM 或者 profiles.xml 文件中的任何相同 id 的 profiles 节点。

````xml
<profiles>
	<profile>
		<id>jdk-1.4</id>
		<activation>
			<jdk>1.4</jdk>
		</activation>
		<repositories>
			<repository>
				<id>jdk14</id>
				<name>Repository for JDK 1.4 builds</name>
				<url>http://www.myhost.com/maven/jdk14</url>
				<layout>default</layout>
				<snapshotPolicy>always</snapshotPolicy>
			</repository>
		</repositories>
	</profile>
	<profile>
		<id>env-dev</id>
		<activation>
			<property>
				<name>target-env</name>
				<value>dev</value>
			</property>
		</activation>
		<properties>
			<tomcatPath>/path/to/tomcat/instance</tomcatPath>
		</properties>
    </profile>
</profiles>
````

###### activeProfiles 标签说明

activeProfile 值为 profile 中所定义的 id，在这里定义的 activeProfile 总是被激活。

````xml
<activeProfiles>
	<activeProfile>alwaysActiveProfile</activeProfile>
	<activeProfile>anotherAlwaysActiveProfile</activeProfile>
</activeProfiles>
````

##### 三、总结

一般情况而言，settings.xml 无需配置太多的东西，作为优化，配置 localRepository 和 mirrors 即可

````xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

	<localRepository>E:/MavenResponse</localRepository>

	<pluginGroups></pluginGroups>

	<proxies></proxies>

	<servers></servers>

	<mirrors>
		<mirror>         
			<id>alimaven</id>
			<name>aliyun maven</name> 
			<url>http://maven.aliyun.com/nexus/content/groups/public/</url> 
			<mirrorOf>central</mirrorOf> 
		</mirror> 
	</mirrors>

	<profiles></profiles>
</settings>
````

