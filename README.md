# Tomcat基础-Tomcat源码部署

## 1.步骤

- 下载tomcat8源码

- 将tomcat8源码压缩包拷贝到itcast_project_tomcat工程文件夹下，并解压

- 进入到apache-tomcat-8.5.54-src目录中，创建文件夹名为home，并将conf和webapps文件夹移动到home文件夹中

  ![image-20200422210452156](https://raw.githubusercontent.com/Davis-Samuel/Tomcat_principle/master/20180518194018254.png)

- 然后在apache-tomcat-8.5.54-src目录下创建pom.xml文件，并引入依赖。

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   
      <modelVersion>4.0.0</modelVersion>
      <groupId>org.apache.tomcat</groupId>
      <artifactId>Tomcat8.5</artifactId>
      <name>Tomcat8.5</name>
      <version>8.5</version>
   
      <build>
          <finalName>Tomcat8.5</finalName>
          <sourceDirectory>java</sourceDirectory>
          <testSourceDirectory>test</testSourceDirectory>
          <resources>
              <resource>
                  <directory>java</directory>
              </resource>
          </resources>
          <testResources>
             <testResource>
                  <directory>test</directory>
             </testResource>
          </testResources>
          <plugins>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-compiler-plugin</artifactId>
                  <version>2.3</version>
                  <configuration>
                      <encoding>UTF-8</encoding>
                      <source>1.8</source>
                      <target>1.8</target>
                  </configuration>
              </plugin>
          </plugins>
      </build>
   
      <dependencies>
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.12</version>
              <scope>test</scope>
          </dependency>
          <dependency>
              <groupId>org.easymock</groupId>
              <artifactId>easymock</artifactId>
              <version>3.4</version>
          </dependency>
          <dependency>
              <groupId>ant</groupId>
              <artifactId>ant</artifactId>
              <version>1.7.0</version>
          </dependency>
          <dependency>
              <groupId>wsdl4j</groupId>
              <artifactId>wsdl4j</artifactId>
              <version>1.6.2</version>
          </dependency>
          <dependency>
              <groupId>javax.xml</groupId>
              <artifactId>jaxrpc</artifactId>
              <version>1.1</version>
          </dependency>
          <dependency>
              <groupId>org.eclipse.jdt.core.compiler</groupId>
              <artifactId>ecj</artifactId>
              <version>4.5.1</version>
          </dependency>
         
      </dependencies>
  </project>
  ```

- 然后在idea中，点击菜单栏Help->Find Action(Ctrl+Shift+A),输入Maven projects，点击添加apache-tomcat-8.5.54-src目录下的pom.xml文件。

- 看一下tomcat的启动原理，表示存在一个main方法。

  ```properties
  在:java/org/apache/catalina/startup/Bootstrap.java/main方法
  ```

- 配置启动类，步骤

  ![image-20200422212449988](https://raw.githubusercontent.com/Davis-Samuel/Tomcat_principle/master/image-20200422212449988.png)

  ![image-20200422212946566](https://raw.githubusercontent.com/Davis-Samuel/Tomcat_principle/master/image-20200422212946566.png)

  ```properties
  -Dcatalina.home=D:\MY\已安装\Java开发\Java.IDEA.Maven\Java.IDEA.Maven程序\Tomca核心原理_itcast_project_tomcat\apache-tomcat-8.5.54-src\home
  -Dcatalina.base=D:\MY\已安装\Java开发\Java.IDEA.Maven\Java.IDEA.Maven程序\Tomca核心原理_itcast_project_tomcat\apache-tomcat-8.5.54-src\home
  -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
  -Djava.util.logging.config.file=D:\MY\已安装\Java开发\Java.IDEA.Maven\Java.IDEA.Maven程序\Tomca核心原理_itcast_project_tomcat\apache-tomcat-8.5.54-src\home\conf\logging.properties
  ```

- 最后点击ok

- 开始启动tomcat：点击DeBug

- 如果出现test/util/TestCookieFilter.java中CookieFilter爆红出错，需要以下内容替换：

  ```java
  /*
   * Licensed to the Apache Software Foundation (ASF) under one or more
   * contributor license agreements.  See the NOTICE file distributed with
   * this work for additional information regarding copyright ownership.
   * The ASF licenses this file to You under the Apache License, Version 2.0
   * (the "License"); you may not use this file except in compliance with
   * the License.  You may obtain a copy of the License at
   *
   *     http://www.apache.org/licenses/LICENSE-2.0
   *
   * Unless required by applicable law or agreed to in writing, software
   * distributed under the License is distributed on an "AS IS" BASIS,
   * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   * See the License for the specific language governing permissions and
   * limitations under the License.
   */
  package util;
  
  import java.util.Locale;
  import java.util.StringTokenizer;
  
  /**
   * Processes a cookie header and attempts to obfuscate any cookie values that
   * represent session IDs from other web applications. Since session cookie names
   * are configurable, as are session ID lengths, this filter is not expected to
   * be 100% effective.
   *
   * It is required that the examples web application is removed in security
   * conscious environments as documented in the Security How-To. This filter is
   * intended to reduce the impact of failing to follow that advice. A failure by
   * this filter to obfuscate a session ID or similar value is not a security
   * vulnerability. In such instances the vulnerability is the failure to remove
   * the examples web application.
   */
  class CookieFilter {
  
      private static final String OBFUSCATED = "[obfuscated]";
  
      private CookieFilter() {
          // Hide default constructor
      }
  
      public static String filter(String cookieHeader, String sessionId) {
  
          StringBuilder sb = new StringBuilder(cookieHeader.length());
  
          // Cookie name value pairs are ';' separated.
          // Session IDs don't use ; in the value so don't worry about quoted
          // values that contain ;
          StringTokenizer st = new StringTokenizer(cookieHeader, ";");
  
          boolean first = true;
          while (st.hasMoreTokens()) {
              if (first) {
                  first = false;
              } else {
                  sb.append(';');
              }
              sb.append(filterNameValuePair(st.nextToken(), sessionId));
          }
  
  
          return sb.toString();
      }
  
      private static String filterNameValuePair(String input, String sessionId) {
          int i = input.indexOf('=');
          if (i == -1) {
              return input;
          }
          String name = input.substring(0, i);
          String value = input.substring(i + 1, input.length());
  
          return name + "=" + filter(name, value, sessionId);
      }
  
      public static String filter(String cookieName, String cookieValue, String sessionId) {
          if (cookieName.toLowerCase(Locale.ENGLISH).contains("jsessionid") &&
                  (sessionId == null || !cookieValue.contains(sessionId))) {
              cookieValue = OBFUSCATED;
          }
  
          return cookieValue;
      }
  }
  ```

- 运行项目，访问http://localhost:8080，得到结果：

  ![image-20200422214022633](https://raw.githubusercontent.com/Davis-Samuel/Tomcat_principle/master/image-20200422214022633.png)

- 原因是我们直接启动org.apache.catalina.startup.Bootstrap的时候没有加载org.apache.jasper.servlet.JasperInitializer，从而无法编译JSP。解决办法是在tomcat的源码org.apache.catalina.startup.ContextConfig中的configureStart函数中手动将JSP解析器初始化：双击shift键直接复制搜索即可：

  ```java
  protected synchronized void configureStart() {
          // Called from StandardContext.start()
  
          if (log.isDebugEnabled()) {
              log.debug(sm.getString("contextConfig.start"));
          }
          if (log.isDebugEnabled()) {
            log.debug(sm.getString("contextConfig.xmlSettings",
                      0.030.。。。。。context.getName(),
                      Boolean.valueOf(context.getXmlValidation()),
                      Boolean.valueOf(context.getXmlNamespaceAware())));
          }
  
          webConfig();
      
      
  //在 webConfig();加上一下一句话即可。
          context.addServletContainerInitializer(new JasperInitializer(), null);
  ```

- 运行结果

  ![tomcat源码运行](https://raw.githubusercontent.com/Davis-Samuel/Tomcat_principle/master/tomcat源码运行.png)

## 2.工具

- IntelliJ IDEA 2019.3.3
- tomcat8.5.54
- Edge

## 3.结果

- ![tomcat源码运行](https://raw.githubusercontent.com/Davis-Samuel/Tomcat_principle/master/tomcat源码运行.png)

## 4.理解

- ![20180518194018254](https://raw.githubusercontent.com/Davis-Samuel/Tomcat_principle/master/20180518194018254.png)

  在server.xml中，每创建一个web工程并将其运行，就会在host标签中创建一个context标签，path为servlet的地址。

- 1、用户点击网页内容，请求被发送到本机端口8080，被在那里监听的 HTTP/1.1， Connector获得。 2、Connector把该请求交给它所在的Service的Engine来处理，并等待Engine的回应。 3、Engine获得请求localhost/test/index.jsp，匹配所有的虚拟主机Host。 4、Engine匹配到名为localhost的Host（即使匹配不到也把请求交给该Host处理，因为该Host被定义为该Engine的默认主机），名为localhost的Host获得请求/test/index.jsp，匹配它所拥有的所有的Context。Host匹配到路径为/test的Context（如果匹配不到就把该请求交给路径名为“ ”的Context去处理）。 5、path=“/test”的Context获得请求/index.jsp，在它的mapping table中寻找出对应的Servlet。Context匹配到URL PATTERN为*.jsp的Servlet,对应于JspServlet类。 6、构造HttpServletRequest对象和HttpServletResponse对象，作为参数调用JspServlet的doGet（）或doPost（）.执行业务逻辑、数据存储等程序。 7、Context把执行完之后的HttpServletResponse对象返回给Host。 8、Host把HttpServletResponse对象返回给Engine。 9、Engine把HttpServletResponse对象返回Connector。 10、Connector把HttpServletResponse对象返回给客户Browser。

- Server组件

  <Server port="8005" shutdown="SHUTDOWN">

  它监听在8005端口以接收shutdown命令，使用 telnet 连接8005 端口可以直接执行 SHUTDOWN 命令来关闭 Tomcat。各Server的定义不能使用同一个端口，这意味着如果在同一个物理机上启动了多个Server实例，必须配置它们使用不同的端口。这个端口的定义用于为管理员提供一个关闭此实例的便捷途径，因此，管理员可以直接telnet至此端口使用SHUTDOWN命令关闭此实例。不过，基于安全角度的考虑，这通常不允许远程进行。

  Server的相关属性：

  className: 用于实现此Server容器的完全限定类的名称，默认为org.apache.catalina.core.StandardServer；

  port: 接收shutdown指令的端口，默认仅允许通过本机访问，默认为8005；

  shutdown：发往此Server用于实现关闭tomcat实例的命令字符串，默认为SHUTDOWN；

- Service组件

  Service主要用于关联一个引擎和与此引擎相关的连接器，每个连接器通过一个特定的端口和协议接收入站请求交将其转发至关联的引擎进行处理。困此，Service要包含一个引擎、一个或多个连接器。

  如上面示例中的定义：

  <Service name=”Catalina”>

  这定义了一个名为Catalina的Service，此名字也会在产生相关的日志信息时记录在日志文件当中。

  Service相关的属性：

  className： 用于实现service的类名，一般都是org.apache.catalina.core.StandardService。

  name：此服务的名称，默认为Catalina；

- Connector组件

  进入Tomcat的请求可以根据Tomcat的工作模式分为如下两类：

  Tomcat作为应用程序服务器：请求来自于前端的web服务器，这可能是Apache, [IIS](http://www.ttlsa.com/iis/), [Nginx](http://www.ttlsa.com/nginx/)等；

  Tomcat作为独立服务器：请求来自于web浏览器；

  Tomcat应该考虑工作情形并为相应情形下的请求分别定义好需要的连接器才能正确接收来自于客户端的请求。一个引擎可以有一个或多个连接器，以适应多种请求方式。

  定义连接器可以使用多种属性，有些属性也只适用于某特定的连接器类型。一般说来，常见于server.xml中的连接器类型通常有4种：

  1) HTTP连接器 2) SSL连接器 3) AJP 1.3连接器 4) proxy连接器

  如上面示例server.xml中定义的HTTP连接器：

  <Connector port=”8080″ protocol=”HTTP/1.1″
  maxThreads=”150″ connectionTimeout=”20000″
  redirectPort=”8443″/>

  定义连接器时可以配置的属性非常多，但通常定义HTTP连接器时必须定义的属性只有“port“，定义AJP连接器时必须定义的属性只有”protocol”，因为默认的协议为HTTP

- Engine组件

  Engine是Servlet处理器的一个实例，即servlet引擎，默认为定义在server.xml中的Catalina。Engine需要defaultHost属性来为其定义一个接收所有发往非明确定义虚拟主机的请求的host组件。如前面示例中定义的：

  <Engine name=”Catalina” defaultHost=”localhost”>

  常用的属性定义：

  defaultHost：Tomcat支持基于FQDN的虚拟主机，这些虚拟主机可以通过在Engine容器中定义多个不同的Host组件来实现；但如果此引擎的连接器收到一个发往非非明确定义虚拟主机的请求时则需要将此请求发往一个默认的虚拟主机进行处理，因此，在Engine中定义的多个虚拟主机的主机名称中至少要有一个跟defaultHost定义的主机名称同名；

  name：Engine组件的名称，用于日志和错误信息记录时区别不同的引擎；

  Engine容器中可以包含Realm、Host、Listener和Valve子容器。

- Host组件

  位于Engine容器中用于接收请求并进行相应处理的主机或虚拟主机，如前面示例中的定义：

  <Host name="localhost" appBase="webapps"
  unpackWARs="true" autoDeploy="true"
  xmlValidation="false" xmlNamespaceAware="false">
  </Host>

  虚拟主机定义示例：

  <Engine name=”Catalina” defaultHost=”localhost”>
  <Host name=”localhost” appBase=”webapps”>
  <Context path=”” docBase=”ROOT”/>
  <Context path=”/bbs” docBase=”/web/bss” 　#path路径是定义在defaultHost背后的
  reloadable=”true” crossContext=”true”/>
  </Host>

  <Host name=”mail.magedu.com” appBase=”/web/mail”>
  <Context path=”” docBase=”ROOT”/>
  </Host>
  </Engine>

  主机别名定义：

  如果一个主机有两个或两个以上的主机名，额外的名称均可以以别名的形式进行定义，如下：
  <Host name="www.ttlsa.com" appBase="webapps" unpackWARs="true">
  <Alias>feiyu.com</Alias>
  </Host>

- Context组件

  Context在某些意义上类似于apache中的路径别名，一个Context定义用于标识tomcat实例中的一个Web应用程序；如下面的定义：

  <!– Tomcat Root Context –>
  <Context path=”” docBase=”/web/webapps”/>

  <!– buzzin webapp –>
  <Context path="/bbs"
  docBase="/web/threads/bbs"
  reloadable="true">
  </Context>

  <!– chat server –>
  <Context path=”/chat” docBase=”/web/chat”/>

  <!– darian web –>
  <Context path=”/darian” docBase=”darian”/>

  在Tomcat6中，每一个context定义也可以使用一个单独的XML文件进行，其文件的目录为$CATALINA_HOME/conf//。可以用于Context中的XML元素有Loader，Manager，Realm，Resources和WatchedResource。
