## Spring Boot

**官网 [Spring Boot](https://spring.io/projects/spring-boot)     [start.spring.io](https://start.spring.io/)**

**1. Hello Spring Boot**

~~~kotlin
@RestController
class HelloController {
    @GetMapping("/")
    fun hello(): String {
        return "hello spring boot"
    }
}
~~~

**2. lombok**

**application.properties rename to application.yml**

~~~xml
//add dependency in pom.xml    
<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
   <optional>true</optional>
</dependency>
~~~

~~~json
//add custom key value in application.yml
user:
  username: wanghao
  password: 123456
~~~

~~~kotlin
//add custom Config.kt
@Data
@Component
@ConfigurationProperties(prefix = "user")
class Config(var username: String = "", var password: String = "")
~~~

~~~java
//test and print it
@Autowired
private Config config;
@Test
void testUser() {
    System.out.println(config.getUsername());
    System.out.println(config.getPassword());
}
~~~

