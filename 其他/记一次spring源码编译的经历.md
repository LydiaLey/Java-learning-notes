## 记一次spring源码编译的经历

- 首先是从GitHub上导入项目。这里我先将GitHub上的项目导入Gitee，再从Gitee上clone，这样速度能快点。然后import到IDEA。

  - 项目Gitee地址

    ```
    https://gitee.com/LydiaLei/spring-framework.git
    ```

- 然后经过漫长的gradle构建，最终是报了几个警告，没太在意。

- 编译spring-oxm

  ```
  ./gradlew :spring-oxm:compileTestJava
  ```

  报错

  ```
  '.' 不是内部或外部命令，也不是可运行的程序
  ```

  想着应该是没有配置环境变量，

