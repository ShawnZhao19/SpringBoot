[TOC]

# springboot自动配置原理再理解

![](F:\千锋自主学习\SpringBoot笔记及代码\springboot自动配置原理再理解\img\HttpEncodingAutoConfiguration.jpg)

![](F:\千锋自主学习\SpringBoot笔记及代码\springboot自动配置原理再理解\img\Conditional扩展注解.jpg)

```yml
# k = v
# 对空格的要求 十分的高
# 普通的 key-value
# 注入到我们的配置类当中

# springboot的多环境配置： 可以选择激活哪一个配置文件
#spring:
  #profiles:
    #active:

# 配置项目的访问路径
#server:
  #servlet:
    #context-path: /****

# 配置文件到底能写什么   spring.factories;

# 在我们这配置文件中n能配置的东西， 都存有一个固定的规律：
# xxxAutoConfiguration：默认值  xxxProperties 和 配置文件绑定，我们就可以使用自定义的配置

# 开启springboot的调试类
#debug: true
```

```text
这就是自动装配的原理!
精髓:
1).SpringBoot启动会加载大量的自动配置类
2)．我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中;
3)、我们再来看这个自动配置类中到底配置了哪些组件;(只要我们要用的组件存在在其中，我们就不需要再手动配置了)
4)、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可;
	xxxxAutoConfigurartion:自动配置类;给容器中添加组件
	xxxxProperties:封装配置文件中相关属性;
```

