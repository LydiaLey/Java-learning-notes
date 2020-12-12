## spring源码解读

### Ioc容器

- 前置知识

  - **容器中的Bean都是通过反射创建的，默认都是单例的**。

    ![image-20201211121104006](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201211121104006.png)

    

    - 可以通过对象.getClass()获取Class对象
    - 需要获取构造器来创建对象。

  - **可以通过xml配置文件指定**Bean的定义信息
    - ![image-20201211121351984](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201211121351984.png)

- Bean的实例化与初始化<init>

  ![image-20201211222549597](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201211222549597.png)

- BeanFactoryPostProcessor 完成对BeanFactory的功能扩展

  ![image-20201212204409170](spring源码解读.assets/image-20201212204409170.png)