### B.1.3. 可重复的元数据节点

在同一个元数据文件中出现多次相同名称的"property"和"group"对象是可以接受的。例如，Spring Boot将`spring.datasource`属性绑定到Hikari，Tomcat和DBCP类，并且每个都潜在的提供了重复的属性名。这些元数据的消费者需要确保他们支持这样的场景。
