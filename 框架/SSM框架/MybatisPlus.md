## 简介

- MybatisPlus是Mybatis的增强版，为简化操作、提高效率而生

## 简单入门

### 简化增删改查操作

- 建user表

  ```sql
  DROP TABLE IF EXISTS user;
  
  CREATE TABLE user
  (
  	id BIGINT(20) NOT NULL COMMENT '主键ID',
  	name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
  	age INT(11) NULL DEFAULT NULL COMMENT '年龄',
  	email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
  	PRIMARY KEY (id)
  );
  DELETE FROM user;
  
  INSERT INTO user (id, name, age, email) VALUES
  (1, 'Jone', 18, 'test1@baomidou.com'),
  (2, 'Jack', 20, 'test2@baomidou.com'),
  (3, 'Tom', 28, 'test3@baomidou.com'),
  (4, 'Sandy', 21, 'test4@baomidou.com'),
  (5, 'Billie', 24, 'test5@baomidou.com');
  ```

- 创建springboot工程

  - application.yml

    ```yaml
    # DataSource Config
    spring:
      datasource:
        driver-class-name: com.mysql.jdbc.Driver
        url: jdbc:mysql://xxxx:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=GMT%2B8
        username: root
        password: root
    ```

  - 导入的依赖

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.1</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

  - POJO

    ```java
    @Data
    public class User {
        private Long id;
        private String name;
        private Integer age;
        private String email;
    }
    ```

  - dao.UserMapper接口，继承BaseMapper<User>。不用再写UserMapper.xml

    ```java
    public interface UserMapper extends BaseMapper<User> {
    }
    ```

  - 启动类上加注解

    ```java
    @MapperScan("com.ly.dao")
    ```

  - 测试类

    ```java
    @RunWith(SpringRunner.class)
    @SpringBootTest
    class MybatisPlusStudyApplicationTests {
        @Autowired
        private UserMapper userMapper;
    
        @Test
        public void test(){
            List<User> users = userMapper.selectList(null);
            users.forEach(System.out::println);
        }
    }
    ```

    