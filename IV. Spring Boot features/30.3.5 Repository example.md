### 30.3.5 仓库示例
```java
package com.example.myapp.domain;

import org.springframework.data.domain.*;
import org.springframework.data.repository.*;

public interface CityRepository extends GraphRepository<City> {

    Page<City> findAll(Pageable pageable);

    City findByNameAndCountry(String name, String country);

}
```
**注** 想详细了解Spring Data Neo4j，包括它丰富的对象映射技术，可查看它的[参考文档](http://projects.spring.io/spring-data-neo4j/)。
