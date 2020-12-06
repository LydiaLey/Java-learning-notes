## 前言

- Mybatis是一个持久层框架
  - 持久层就是做**持久化**工作的代码块
    - 持久化简而言之就是**让数据在内存与磁盘之间流通**，负责**数据的读取与存储**
- 不用Mybatis也是可以实现持久化的，**技术本没有高低贵贱之分**，但是Mybatis用的人多，也确实有很多优点
- Mybatis的==查询==，简单来说，做的就是==执行sql语句，然后做结果集映射==，也就是==将数据库查询出来的结果封装成POJO==
  - 可见数据库理论是相当重要的，==高效的sql语句很重要==
  - MySQL引擎、InnoDB底层原理、索引与优化
- 资料获取
  - maven
  - 官方文档
  - GitHub

## 使用

- 简单入门

  - 建立一个数据库，测试用

    ```sql
    CREATE DATABASE `mybatis`;
    USE `mybatis`;
    
    CREATE TABLE `user`(
    	`id` INT(20) NOT NULL PRIMARY KEY,
    	`name` VARCHAR(30) DEFAULT NULL,
    	`pwd` VARCHAR(30) DEFAULT NULL
    )ENGINE=INNODB DEFAULT CHARSET=utf8;
    
    INSERT INTO `user`(`id`,`name`,`pwd`) VALUES
    (1,'张三','11111'),
    (2,'李四','22222'),
    (3,'王五','33333');
    ```

  - 建立一个普通maven工程，**删掉src目录，变成父工程**，**它的子项目（模块）不需要重复导包**

  - 父工程导入依赖

    ```xml
         <!-- 导入依赖 -->
        <dependencies>
            <!--mysql驱动-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.47</version>
            </dependency>
            <!-- mybatis -->
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.5.2</version>
            </dependency>
            <!-- junit -->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
            </dependency>
        </dependencies>
    ```

  - 子工程pom文件中添加以下配置，保证能正常读取java包下的配置文件

    ``` xml
    	<build>
            <resources>
                <resource>
                    <directory>
                        src/main/resources
                    </directory>
                    <includes>
                        <include>
                            **/*.properties
                        </include>
                        <include>
                            **/*.xml
                        </include>
                    </includes>
                </resource>
                <resource>
                    <directory>
                        src/main/java
                    </directory>
                    <includes>
                        <include>
                            **/*.properties
                        </include>
                        <include>
                            **/*.xml
                        </include>
                    </includes>
                </resource>
            </resources>
    	</build>
    ```

    

  - 将Mybatis核心配置写在mybatis-config.xml ，放在resource文件夹下

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <environments default="development">
            <environment id="development">
                <transactionManager type="JDBC"/>
                <dataSource type="POOLED">
                    <property name="driver" value="com.mysql.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://xx.xx.xx.xx:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT%2B8"/>
                    <property name="username" value="root"/>
                    <property name="password" value="root"/>
                </dataSource>
            </environment>
        </environments>
        <mappers>
            <mapper resource="com/ly/dao/UserLYMapper.xml"/>
        </mappers>
    </configuration>
    ```

  - 看官方文档，得知创建SqlSessionFactory和SqlSession的代码是固定的。我们可以写一个工具类，避免重复代码

    ```java
    public class MybatisUtils {
    
        private static SqlSessionFactory sqlSessionFactory;
    
        static {
            String resource = "mybatis-config.xml";
            InputStream inputStream = null;
            try {
                inputStream = Resources.getResourceAsStream(resource);
                SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    
        public static SqlSession getSqlSession(){
            return sqlSessionFactory.openSession();
        }
    }
    ```

  - 要实现查询数据库，我们需要一个实体类、Mapper接口、Mapper.xml配置文件

    - 实体类

    - Mapper接口，相当于DAO接口

      ```java
      public interface UserMapper {
          List<User> getUserList();
      }
      ```

    - Mapper.xml配置文件，Mybatis自动生成实现类

      ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="com.ly.dao.UserMapper">
          <select id="getUserList" resultType="com.ly.POJO.User">
              select * from mybatis.user
          </select>
      </mapper>
      ```

  - 编写测试类，进行测试

    - test包的结构尽量与main包一致

    - UserMapperTest

      ```java
      public class UserMapperTest {
          @Test
          public void test(){
              SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              List<User> userList = mapper.getUserList();
              System.out.println(userList);
          }
      }
      ```

  - 完整版增删改查代码

    - UserMapper接口

      ```java
      public interface UserMapper {
          //查询所有
          List<User> getUserList();
      
          //根据id查询
          User selectById(int id);
      
          //增加用户
          int insertUser(User user);
      
          //修改用户
          int updateUser(User user);
      
          //删除用户
          int deleteUser(int id);
      
      }
      ```

    - UserMapper.xml

      ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="com.ly.dao.UserMapper">
      
          <delete id="deleteUser" parameterType="int">
              delete from mybatis.user where id = #{id}
          </delete>
      
          <select id="getUserList" resultType="com.ly.POJO.User">
              select * from mybatis.user
          </select>
      
          <select id="selectById" parameterType="int" resultType="com.ly.POJO.User">
              select * from mybatis.user where id = #{id}
          </select>
      
          <insert id="insertUser" parameterType="com.ly.POJO.User">
              insert into mybatis.user(id, name, pwd) values (#{id},#{name},#{pwd})
          </insert>
      
          <update id="updateUser" parameterType="com.ly.POJO.User">
              update mybatis.user set name = #{name}, pwd = #{pwd}  where id = #{id}
          </update>
      </mapper>
      ```

    - 测试代码

      ```java
      public class UserMapperTest {
          @Test
          public void test(){
              SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              List<User> userList = mapper.getUserList();
              //System.out.println(userList);
              for (User user : userList) {
                  System.out.println(user);
              }
              sqlSession.close();
          }
      
          @Test
          public void selectById(){
              SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              User user = mapper.selectById(1);
              System.out.println(user);
              sqlSession.close();
          }
      
          @Test
          public void insertUser(){
              SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              mapper.insertUser(new User(4,"王五","555555"));
              //记得提交事务
              sqlSession.commit();
              sqlSession.close();
          }
      
          @Test
          public void updateUser(){
              SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              mapper.updateUser(new User(4,"芜湖","233333"));
              //记得提交事务
              sqlSession.commit();
              sqlSession.close();
          }
      
          @Test
          public void deleteUser(){
              SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              mapper.deleteUser(3);
              //记得提交事务
              sqlSession.commit();
              sqlSession.close();
          }
      }
      ```

  ## 日志

  - 默认只能用标准日志工厂实现

    ```xml
    <!-- mybatis-config.xml -->
    <settings>
            <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    ```

  - 使用Log4j日志框架

    - 在导入Log4j依赖

      ```xml
      <!-- pom.xml -->
          <dependencies>
              <!-- https://mvnrepository.com/artifact/log4j/log4j -->
              <dependency>
                  <groupId>log4j</groupId>
                  <artifactId>log4j</artifactId>
                  <version>1.2.17</version>
              </dependency>
          </dependencies>
      
      ```

    - log4j.properties

      ```properties
      #将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
      log4j.rootLogger=DEBUG,console,file
      
      #控制台输出的相关设置
      log4j.appender.console = org.apache.log4j.ConsoleAppender
      log4j.appender.console.Target = System.out
      log4j.appender.console.Threshold=DEBUG
      log4j.appender.console.layout = org.apache.log4j.PatternLayout
      log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
      
      #文件输出的相关设置
      log4j.appender.file = org.apache.log4j.RollingFileAppender
      log4j.appender.file.File=./log/ly.log
      log4j.appender.file.MaxFileSize=10mb
      log4j.appender.file.Threshold=DEBUG
      log4j.appender.file.layout=org.apache.log4j.PatternLayout
      log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
      
      #日志输出级别
      log4j.logger.org.mybatis=DEBUG
      log4j.logger.java.sql=DEBUG
      log4j.logger.java.sql.Statement=DEBUG
      log4j.logger.java.sql.ResultSet=DEBUG
      log4j.logger.java.sql.PreparedStatement=DEBUG
      ```

  - 手动使用log4j

    - 先导包、写好配置文件

    - 创建logger、使用logger打印日志

      ```java
      public class UserMapperTest {
      
          static Logger logger = Logger.getLogger(UserMapperTest.class);
          
          @Test
          public void testLog4j(){
              logger.info("进入test方法");
          }
      }
      ```

  ## 分页查询

  - 作用是减少查询量

    ```java
        //分页查询
        List<User> selectByLimit(Map map);
    ```

    ```xml
        <select id="selectByLimit" parameterType="map" resultType="User">
            select * from mybatis.user limit #{startIndex},#{pageSize}
        </select>
    ```

    ```java
        @Test
        public void selectByLimit(){
            SqlSession sqlSession = MybatisUtils.getSqlSession();
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            HashMap<String, Object> map = new HashMap<String, Object>();
            map.put("startIndex",0);
            map.put("pageSize",2);
            List<User> users = mapper.selectByLimit(map);
            for (User user : users) {
                logger.info(user);
            }
            sqlSession.close();
        }
    ```

  ## 注解开发

  - 面向接口编程

    - 解耦，接口与实现分离。通过接口即可理解系统的整体逻辑。

    - Mapper.xml -->  注解

      ```java
      public interface UserMapper {
          @Select("SELECT * FROM user WHERE id = #{id}")
          User selectUser(int id);
      }
      ```

  - 注解的工作原理

  ## 多表查询

  ### 一对多（对于POJO来说，多个Teacher对应一个Teacher）

  - 每张表对应一个POJO，每张表之间有联系，那么相应的POJO也存在组合关系。关键是**如何将数据库查询的结果封装到POJO里**。

  - POJO

    ```java
    @Data
    public class Student {
        private int id;
        private String name;
        private Teacher teacher;
    }
    
    
    @Data
    public class Teacher {
        private int id;
        private String name;
    }
    ```

  - 按照查询嵌套处理，相当于子查询。先查到外键，根据外键再做查询，最后将结果封装

    ```xml
    <resultMap id="StudentTeacher" type="Student">
            <result property="id" column="id"></result>
            <result property="name" column="name"></result>
            <association property="teacher" column="tid" javaType="Teacher" select="selectTeacher"></association>
    </resultMap>
    
    <select id="selectAll" resultMap="StudentTeacher">
        select * from student
    </select>
    
    <select id="selectTeacher" resultType="Teacher">
        select * from teacher where id = #{id}
    </select>
    ```

  - 按照结果嵌套处理，相当于联表查询。直接从多张表中查询出想要的结果，再将结果封装。

    ```xml
    <select id="selectAll2" resultMap="StudentTeacher2">
        select s.id sid,s.name sname,t.name tname
        from student s,teacher t
        where s.tid = t.id
    </select>
    <resultMap id="StudentTeacher2" type="Student">
        <result property="id" column="sid"></result>
        <result property="name" column="sname"></result>
        <association property="teacher" javaType="Teacher">
            <result property="name" column="tname"></result>
        </association>
    </resultMap>
    ```

  ### 多对一（对于POJO来说，一个Teacher对应多个Student）

  - POJO

    ```java
    @Data
    public class Student {
        private int id;
        private String name;
        private int tid;
    }
    
    @Data
    public class Teacher {
        private int id;
        private String name;
        private List<Student> students;
    }
    ```

  - 联表查询

    ```xml
    <select id="selectTeacherById" resultMap="TeacherStudent">
        select s.id sid,s.name sname,t.id tid,t.name tname
        from student s,teacher t
        where s.tid = t.id and t.id = #{tid}
    </select>
    <resultMap id="TeacherStudent" type="Teacher">
        <result property="id" column="tid"></result>
        <result property="name" column="tname"></result>
        <collection property="students" ofType="Student">
            <result property="name" column="sname"></result>
            <result property="tid" column="tid"></result>
            <result property="id" column="sid"></result>
        </collection>
    </resultMap>
    ```

  - 子查询

    ```xml
    <select id="selectTeacherById2" resultMap="TeacherStudent2">
        select * from teacher where id = #{tid}
    </select>
    <select id="selectStudentByTid" resultType="Student">
        select * from student where tid = #{tid}
    </select>
    <resultMap id="TeacherStudent2" type="Teacher">
        <collection property="students" javaType="ArrayList" ofType="Student" select="selectStudentByTid" column="id">
            <result property="id" column="id"></result>
            <result property="name" column="name"></result>
            <result property="tid" column="tid"></result>
        </collection>
    </resultMap>
    ```

  ## 动态SQL

  - 环境搭建

    - 生成UUID

      ```java
      public class IDUtils {
          public static String getID(){
              return UUID.randomUUID().toString().replaceAll("-","");
          }
      }
      ```

      

