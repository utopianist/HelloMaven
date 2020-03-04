

A ==*Project Object Model*== or *POM* is the fundamental unit of work in Maven. 

## [ **Namespace related attributes in XML and XML Schema (XSD)**](https://stackoverflow.com/questions/34202967/xmlns-xmlnsxsi-xsischemalocation-and-targetnamespace/34204799)

- [**`xmlns`**](http://www.w3.org/TR/xml-names/#ns-decl) is part of the [W3C Namespaces in XML Recommendation](http://www.w3.org/TR/xml-names/):

  > The prefix **xmlns** is used only to declare namespace bindings and is by definition bound to the namespace name http://www.w3.org/2000/xmlns/.

  **In your example**, it declares that `http://maven.apache.org/POM/4.0.0` is the default namespace for the elements in your Maven project.

- [**`xmlns:xsi`**](http://www.w3.org/TR/xmlschema-1/#Instance_Document_Constructions) declares a standard namespace prefix (`xsi`) for a core namespace used in XSD: `http://www.w3.org/2001/XMLSchema-instance`

  > *XML Schema: Structures* also defines several attributes for direct use in any XML documents. These attributes are in a different namespace, which has the namespace name http://www.w3.org/2001/XMLSchema-instance. For brevity, the text and examples in this specification use the prefix xsi: to stand for this latter namespace; in practice, any prefix can be used.

  **In your example**, it declares the conventional binding of the `xsi` namespace prefix to `http://www.w3.org/2001/XMLSchema-instance`, which properly sets up the use of the following attributes:

  - [**`xsi:type`**](http://www.w3.org/TR/xmlschema-1/#xsi_type) allows an XML instance to associate element type information directly rather than through an XSD. See [*How to restrict the value of an XML element using xsi:type in XSD?*](https://stackoverflow.com/questions/33808790/how-to-restrict-the-value-of-an-xml-element-using-xsitype-in-xsd)

    **In your example**, `xsi:type` is not used; included here for completeness regarding `xsi`.

  - [**`xsi:nil`**](http://www.w3.org/TR/xmlschema-1/#xsi_nil) allows an empty element to be considered to be valid when the XSD might not otherwise have allowed it.

    **In your example**, `xsi:nil` is not used; included here for completeness regarding `xsi`.

  - [**`xsi:schemaLocation`** and **`xsi:noNamespaceSchemaLocation`**](http://www.w3.org/TR/xmlschema-1/#schema-loc) provide hints to the XML processor as to how to associate an XSD with an XML document. Use `xsi:schemaLocation` when there is a namespace; use `xsi:noNamespaceSchemaLocation` when there is no namespace.

    **In your example**, there is a namespace, so you properly use `xsi:schemaLocation`, whose values are space-separated **pairs** of ***namespace\*** and ***XSD-location-URI\***. Your example uses the namespace, `http://maven.apache.org/POM/4.0.0`, and namespaces are lexical naming constructs that ***[need not be retrivable](https://stackoverflow.com/a/27614076/290085)\***. Your example also uses the XSD-location-URI, `http://maven.apache.org/xsd/maven-4.0.0.xsd`, which is retrivable as it should be.

    If your example did not use a namespace, you would use `xsi:noNamespaceSchemaLocation`, whose value is a single ***XSD-location-URI\*** that hints to the location of the intended XSD and which should be retrievable.

- [**`targetNamespace`**](https://www.w3.org/TR/2001/REC-xmlschema-1-20010502/#a-target_namespace) is an attribute on the `xs:schema` root element of an XSD which specifies the namespace of the root element of the XML document instances the XSD is intended to govern. It must match the default or explicit namespace of those XML documents' root elements.

## [What does “xmlns” in XML mean?](https://stackoverflow.com/questions/1181888/what-does-xmlns-in-xml-mean)

It means [XML namespace](http://en.wikipedia.org/wiki/XML_namespace).

Basically, every element (or attribute) in [XML](http://en.wikipedia.org/wiki/XML) belongs to a namespace, a way of "qualifying" the name of the element.

Imagine you and I both invent our own XML. You invent XML to describe people, I invent mine to describe cities. Both of us include an element called `name`. Yours refers to the person’s name, and mine to the city name—OK, it’s a little bit contrived.

```xml
<person>
    <name>Rob</name>
    <age>37</age>
    <homecity>
        <name>London</name>
        <lat>123.000</lat>
        <long>0.00</long>
    </homecity>
</person>
```

If our two XMLs were combined into a single document, how would we tell the two names apart? As you can see above, there are two `name` elements, but they both have different meanings.

The answer is that you and I would both assign a namespace to our XML, which we would make unique:

```xml
<personxml:person xmlns:personxml="http://www.your.example.com/xml/person"
                  xmlns:cityxml="http://www.my.example.com/xml/cities">
    <personxml:name>Rob</personxml:name>
    <personxml:age>37</personxml:age>
    <cityxml:homecity>
        <cityxml:name>London</cityxml:name>
        <cityxml:lat>123.000</cityxml:lat>
        <cityxml:long>0.00</cityxml:long>
    </cityxml:homecity>
</personxml:person>
```

Now we’ve fully qualified our XML, there is no ambiguity as to what each `name` element means. All of the tags that start with `personxml:` are tags belonging to your XML, all the ones that start with `cityxml:` are mine.

There are a few points to note:

- If you exclude any namespace declarations, things are considered to be in the default namespace.

- If you declare a namespace without the identifier, that is, `xmlns="http://somenamespace"`, rather than `xmlns:rob="somenamespace"`, it specifies the default namespace for the document.

- The actual namespace itself, often a [IRI](http://en.wikipedia.org/wiki/Internationalized_Resource_Identifier), is of no real consequence. It should be unique, so people tend to choose a IRI/URI that they own, but it has no greater meaning than that. Sometimes people will place the schema (definition) for the XML at the specified IRI, but that is a convention of some people only.

- The prefix is of no consequence either. The only thing that matters is what namespace the prefix is defined as. Several tags beginning with different prefixes, all of which map to the same namespace are considered to be the same.

  For instance, if the prefixes `personxml` and `mycityxml` both mapped to the same namespace (as in the snippet below), then it wouldn't matter if you prefixed a given element with `personxml` or `mycityxml`, they'd both be treated as the same thing by an XML parser. The point is that an XML parser doesn't care what you've chosen as the prefix, only the namespace it maps too. The prefix is just an *indirection* pointing to the namespace.

  ```xml
  <personxml:person 
       xmlns:personxml="http://example.com/same/url"
       xmlns:mycityxml="http://example.com/same/url" />
  ```

- Attributes can be qualified but are generally not. They also do *not* inherit their namespace from the element they are on, as opposed to elements (see below).

Also, element namespaces are inherited from the parent element. In other words I could equally have written the above XML as

```xml
<person xmlns="http://www.your.example.com/xml/person">
    <name>Rob</name>
    <age>37</age>
    <homecity xmlns="http://www.my.example.com/xml/cities">
        <name>London</name>
        <lat>123.000</lat>
        <long>0.00</long>
    </homecity>
</person>
```



## 配置IDEA 和 Maven

一定要用自己的 Maven ，不要用系统自带的 Maven。

==不同版本的 IDEA 和 Maven 根本不能一起搭配工作。== 比如，

```
IntelliJ IDEA 2019.1.3 (Ultimate Edition)
Build #IU-191.7479.19, built on May 28, 2019
Licensed to 超级 程序员
You have a perpetual fallback license for this version
Subscription is active until January 4, 2021
JRE: 1.8.0_202-release-1483-b58 x86_64
JVM: OpenJDK 64-Bit Server VM by JetBrains s.r.o
macOS 10.13.6
```

该版 IDEA 和 Maven 3.6.3 在一起根本用不了，配置完毕后，项目完全不会自动加载。

建议用 Maven 3.3.9。

### 配置 Maven

解压好的 Maven bin包就放在 `/Library` 下如，`/Library/apache-maven-3.3.9` 。

在该文件夹下自建一个`repo` 文件夹用作本地仓库。

修改 `conf`  目录下的 `settings.xml` :

```
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
 |                 ${maven.home}/conf/settings.xml.
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
  <localRepository>/Library/apache-maven-3.3.9/repo</localRepository>
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
  <mirror>
    <id>uk</id>  
    <mirrorOf>central</mirrorOf>  
    <name>Human Readable Name for this Mirror.</name>  
    <url>http://uk.maven.org/maven2/</url>
  </mirror>
  <mirror>
    <id>CN</id>
    <name>OSChina Central</name>
    <url>http://maven.oschina.net/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
  </mirror>

  <mirror>
    <id>nexus</id>
    <name>internal nexus repository</name>
    <!-- <url>http://192.168.1.100:8081/nexus/content/groups/public/</url>-->
    <url>http://repo.maven.apache.org/maven2</url>
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

```

这里主要改了两个地方，本地仓库目录 `<localRepository>/Library/apache-maven-3.3.9/repo</localRepository>`和 镜像源。

Mac 下修改 `~/.bahs_profile` ，

```
##Maven
export M2_HOME=/Library/apache-maven-3.3.9
export PATH=$PATH:$M2_HOME/bin
```

完了之后  `source ~/.bahs_profile`

检查 Maven 是否 安装成功 `mvn -v`

### 配置 IDEA

偏好设置里更改 Maven 配置，以下三个选项都要改成自己的 Maven 仓库配置。

![image-20200304165413732](https://cdn.jsdelivr.net/gh/utopianist/PicGO@master/uPic/image-20200304165413732.png)



## tomcat

通过配置 tomcat 运行 web 应用

![image-20200304171644307](https://cdn.jsdelivr.net/gh/utopianist/PicGO@master/uPic/image-20200304171644307.png)

### Exploded deployments vs WAR packages

![image-20200304171727431](https://cdn.jsdelivr.net/gh/utopianist/PicGO@master/uPic/image-20200304171727431.png)

There are two ways to deploy Java web applications.

The first way is to deploy a WAR file. A WAR file is just a ZIP archive with a directory structure that is recognised by Java application servers like Tomcat. WAR files are convenient because they are a single package that is easy to copy, and the contents of the WAR file are compressed making it quite a compact package.

The second way is to deploy all the individual files that make up a web application. This is called an exploded deployment, or an ==exploded WAR==. This kind of deployment can be very useful during development, as files like HTML pages and CSS files can be edited while the application is deployed and reloaded on the fly.

### The `webapps` Directory

The `webapps` directory is ==where deployed applications reside in Tomcat.==

The `webapps` directory is the default deployment location, but this can be configured with the `appBase` attribute on the `Host` element.

If Tomcat is set to autodeploy applications ==(and it is set to do this by default)== then ==any WAR file or exploded deployment copied into the `webapps` folder will be deployed automatically while Tomcat is running.==

The autodeployment of applications can be disabled by setting the `autoDeploy` attribute on the `` element to `false`. In this case, applications will de deployed on startup.

In turn, the deployment of applications on startup can be disabled by setting the `deployOnStartup` attribute on the `` element to `false`.

If both `autoDeploy` and `deployOnStartup` are false, you can deploy applications by manually adding a `` element inside the `` element in the `conf/server.xml` file. See the section "Defining the context in the `server.xml` file" for an example.

- https://octopus.com/blog/defining-tomcat-context-paths

