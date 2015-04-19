Summary
===============

* I. Spring Boot文档
    * [1. 关于本文档](I. Spring Boot Documentation/1. About the documentation.md)
    * [2. 获取帮助](I. Spring Boot Documentation/2. Getting help.md)
    * [3. 第一步](I. Spring Boot Documentation/3. First steps.md)
    * [4. 使用Spring Boot](I. Spring Boot Documentation/4. Working with Spring Boot.md)
    * [5. 了解Spring Boot特性](I. Spring Boot Documentation/5. Learning about Spring Boot features.md)
    * [6. 迁移到生存环境](I. Spring Boot Documentation/6. Moving to production.md)
    * [7. 高级主题](I. Spring Boot Documentation/7. Advanced topics.md)

* II. 开始
    * [8. Spring Boot介绍](II. Getting started/8. Introducing Spring Boot.md)
    * [9. 系统要求](II. Getting started/9. System Requirements.md)
      * [9.1. Servlet容器](II. Getting started/9.1. Servlet containers.md)
    * [10. Spring Boot安装](II. Getting started/10. Installing Spring Boot.md)
      * [10.1. 为Java开发者准备的安装指南](II. Getting started/10.1. Installation instructions for the Java developer.md)
         * [10.1.1. Maven安装](II. Getting started/10.1.1. Maven installation.md)
         * [10.1.2. Gradle安装](II. Getting started/10.1.2. Gradle installation.md)
      * [10.2. Spring Boot CLI安装](II. Getting started/10.2. Installing the Spring Boot CLI.md)
         * [10.2.1. 手动安装](II. Getting started/10.2.1. Manual installation.md)
         * [10.2.2. 使用GVM安装](II. Getting started/10.2.2. Installation with GVM.md)
         * [10.2.3. 使用OSX Homebrew进行安装](II. Getting started/10.2.3. OSX Homebrew installation.md)
         * [10.2.4. 使用MacPorts进行安装](II. Getting started/10.2.4. MacPorts installation.md)
         * [10.2.5. 命令行实现](II. Getting started/10.2.5. Command-line completion.md)
         * [10.2.6. Spring CLI示例快速入门](II. Getting started/10.2.6. Quick start Spring CLI example.md)
      * [10.3. 从Spring Boot早期版本升级](II. Getting started/10.3. Upgrading from an earlier version of Spring Boot.md)
   * [11. 开发你的第一个Spring Boot应用](II. Getting started/11. Developing your first Spring Boot application.md)
      * [11.1. 创建POM](II. Getting started/11.1. Creating the POM.md)
      * [11.2. 添加classpath依赖](II. Getting started/11.2. Adding classpath dependencies.md)
      * [11.3. 编写代码](II. Getting started/11.3. Writing the code.md)
         * [11.3.1. @RestController和@RequestMapping注解](II. Getting started/11.3.1. The @RestController and @RequestMapping annotations.md)
         * [11.3.2. @EnableAutoConfiguration注解](II. Getting started/11.3.2. The @EnableAutoConfiguration annotation.md)
         * [11.3.3. main方法](II. Getting started/11.3.3. The “main” method.md)
      * [11.4. 运行示例](II. Getting started/11.4. Running the example.md)
      * [11.5. 创建一个可执行jar](II. Getting started/11.5. Creating an executable jar.md)
   * [12. 接下来阅读什么](II. Getting started/12. What to read next.md)
    
* III. 使用Spring Boot
   * [13. 构建系统](III. Using Spring Boot/13. Build systems.md)
      * [13.1. Maven](III. Using Spring Boot/13.1. Maven.md)
         * [13.1.1. 继承starter parent](III. Using Spring Boot/13.1.1. Inheriting the starter parent.md)
         * [13.1.2. 使用没有父POM的Spring Boot](III. Using Spring Boot/13.1.2. Using Spring Boot without the parent POM.md)
         * [13.1.3. 改变Java版本](III. Using Spring Boot/13.1.3. Changing the Java version.md)
         * [13.1.4. 使用Spring Boot Maven插件](III. Using Spring Boot/13.1.4. Using the Spring Boot Maven plugin.md)
      * [13.2. Gradle](III. Using Spring Boot/13.2. Gradle.md)
      * [13.3. Ant](III. Using Spring Boot/13.3. Ant.md)
      * [13.4. Starter POMs](III. Using Spring Boot/13.4. Starter POMs.md)
   * [14. 组织你的代码](III. Using Spring Boot/14. Structuring your code.md)
      * [14.1. 使用"default"包](III. Using Spring Boot/14.1. Using the “default” package.md)
      * [14.2. 定位main应用类](III. Using Spring Boot/14.2. Locating the main application class.md)
   * [15. 配置类](III. Using Spring Boot/15. Configuration classes.md)
      * [15.1. 导入其他配置类](III. Using Spring Boot/15.1. Importing additional configuration classes.md)
      * [15.2. 导入XML配置](III. Using Spring Boot/15.2. Importing XML configuration.md)
   * [16. 自动配置](III. Using Spring Boot/16. Auto-configuration.md)
      * [16.1. 逐步替换自动配置](III. Using Spring Boot/16.1. Gradually replacing auto-configuration.md)
      * [16.2. 禁用特定的自动配置](III. Using Spring Boot/16.2. Disabling specific auto-configuration.md)
   * [17. Spring Beans和依赖注入](III. Using Spring Boot/17. Spring Beans and dependency injection.md)
   * [18. 使用@SpringBootApplication注解](III. Using Spring Boot/18. Using the @SpringBootApplication annotation.md)
   * [19. 运行应用程序](III. Using Spring Boot/19. Running your application.md)
      * [19.1. 从IDE中运行](III. Using Spring Boot/19.1. Running from an IDE.md)
      * [19.2. 作为一个打包后的应用运行](III. Using Spring Boot/19.2. Running as a packaged application.md)
      * [19.3. 使用Maven插件运行](III. Using Spring Boot/19.3. Using the Maven plugin.md)
      * [19.4. 使用Gradle插件运行](III. Using Spring Boot/19.4. Using the Gradle plugin.md)
      * [19.5. 热交换](III. Using Spring Boot/19.5. Hot swapping.md)
   * [20. 打包用于生产的应用程序](III. Using Spring Boot/20. Packaging your application for production.md)
   * [21. 接下来阅读什么](III. Using Spring Boot/21. What to read next.md)

* IV. Spring Boot特性
   * [22. SpringApplication](IV. Spring Boot features/22. SpringApplication.md)
      * [22.1. 自定义Banner](IV. Spring Boot features/22.1. Customizing the Banner.md)
      * [22.2. 自定义SpringApplication](IV. Spring Boot features/22.2. Customizing SpringApplication.md)
      * [22.3. 流畅的构建API](IV. Spring Boot features/22.3. Fluent builder API.md)
      * [22.4. Application事件和监听器](IV. Spring Boot features/22.4. Application events and listeners.md)
      * [22.5. Web环境](IV. Spring Boot features/22.5. Web environment.md)
      * [22.6. 命令行启动器](IV. Spring Boot features/22.6. Using the CommandLineRunner.md)
      * [22.7. Application退出](IV. Spring Boot features/22.7. Application exit.md)
   * [23.外化配置](IV. Spring Boot features/23. Externalized Configuration.md)
      * [23.1. 配置随机值](IV. Spring Boot features/23.1. Configuring random values.md)
      * [23.2. 访问命令行属性](IV. Spring Boot features/23.2. Accessing command line properties.md)
      * [23.3. Application属性文件](IV. Spring Boot features/23.3. Application property files.md)
      * [23.4. 特定的Profile属性](IV. Spring Boot features/23.4. Profile-specific properties.md)
      * [23.5. 属性占位符](IV. Spring Boot features/23.5. Placeholders in properties.md)
      * [23.6. 使用YAML代替Properties](IV. Spring Boot features/23.6. Using YAML instead of Properties.md)
         * [23.6.1. 加载YAML](IV. Spring Boot features/23.6.1. Loading YAML.md)
         * [23.6.2. 在Spring环境中使用YAML暴露属性](IV. Spring Boot features/23.6.2. Exposing YAML as properties in the Spring Environment.md)
         * [23.6.3. Multi-profile YAML文档](IV. Spring Boot features/23.6.3. Multi-profile YAML documents.md)
         * [23.6.4. YAML缺点](IV. Spring Boot features/23.6.4. YAML shortcomings.md)
      * [23.7. 类型安全的配置属性](IV. Spring Boot features/23.7. Typesafe Configuration Properties.md)
         * [23.7.1. 第三方配置](IV. Spring Boot features/23.7.1. Third-party configuration.md)
         * [23.7.2. 松散的绑定（Relaxed binding）](IV. Spring Boot features/23.7.2. Relaxed binding.md)
         * [23.7.3. @ConfigurationProperties校验](IV. Spring Boot features/23.7.3. @ConfigurationProperties Validation.md)
   * [24. Profiles](IV. Spring Boot features/24. Profiles.md)
      * [24.1. 添加激活的配置(profiles)](IV. Spring Boot features/24.1. Adding active profiles.md)
      * [24.2.以编程方式设置profiles](IV. Spring Boot features/24.2. Programmatically setting profiles.md)
      * [24.3. Profile特定配置文件](IV. Spring Boot features/24.3. Profile specific configuration files.md)











