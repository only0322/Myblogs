---
title: "SpringMVC详解"
date: 2022-01-18T16:05:34+08:00
draft: true
---

## 一、什么是MVC

MVC是指Model、View、Controller分离，但在Java中可能有另一层意思。

- 模型代表一个存取数据的对象或 JAVA POJO。它也可以带有逻辑，在数据变化时更新控制器。

- 视图代表模型包含的数据的可视化。

- 控制器作用于模型和视图上。它控制数据流向模型对象，并在数据变化时更新视图。它使视图与模型分离开。

## 二、使用

直接使用Spring MVC

```java
@Controller
public class UserController {
    @GetMapping("/register")
    public ModelAndView register() {
        ...
    }

    @PostMapping("/signin")
    public ModelAndView signin(@RequestParam("email") String email, @RequestParam("password") String password) {
        ...
    }
    ...
}
```


和普通Spring配置一样，我们编写正常的AppConfig后，只需加上@EnableWebMvc注解，就“激活”了Spring MVC：

```java
@Configuration
@ComponentScan
@EnableWebMvc // 启用Spring MVC
@EnableTransactionManagement
@PropertySource("classpath:/jdbc.properties")
public class AppConfig {
    ...
}
```

MVC已经用过了，不再赘述。