# 演示传统的Spring Web MVC项目

## Spring Web 注解驱动

- 与demo-spring-mvc-xml项目成对比项目
- 依然通过web.xml配置包扫描，Spring MVC容器，但是不同配置web层需要的Bean
- 所以这是个半xml半注解的方案，核心配置依然使用xml配置



### 开发版本依赖

- Spring Framework 3.1 +(要实现Web MVC注解驱动，Spring Framework至少是3.1或以上)

### 注解开发基本配置
- 注解配置：@Configuration (Spring 范式注解，模式注解)
- 组件激活： @EnableWebMvc(Spring 模块装配，替代配置文件)
- 自定义组件： WebMvcConfigurer(Spring Bean)

#### 注解配置：@Configuration (Spring 范式注解，模式注解)

```java
@Configuration
```
代表我是一个配置类，提供Bean


#### 组件激活： @EnableWebMvc(Spring 模块装配，替代配置文件)

- 启动模块注解，注入MVC模块需要的Bean，这是注解替代xml的启动注解，声明该注解，就会自动注入通用所需要的mvc配置bean
如RequestMappingHandlerMapping等

- @EnableWebMvc的本质是将DelegatingWebMvcConfiguration类注入到Spring容器中，所以需要看具体实现，则通过该类查看

- 所以@EnableWebMvc和@Configuration两个注解就可以代替web.xml配置的方式，实际初始化配置则是DelegatingWebMvcConfiguration类完成的


#### 自定义组件： WebMvcConfigurer(Spring Bean)

- 想要自定义组件的，我们可以通过实现WebMvcConfigurer接口去完成。
- 也许，你还会想到一个叫WebMvcConfigurerAdapter的抽象类，不过这个抽象类在Spring 5+ 之后就被淘汰了。因为之后的WebMvcConfigurer使用Java 8 语法实现，
接口可以有默认方法，然后再看看他们两者的代码，就会发现使用了java 1.8语法的WebMvcConfigurer和WebMvcConfigurerAdapter几乎一模一样，所以WebConfigurerAdapter就没有任何意义了。

```java
@Configuration
@EnableWebMvc
public class WebMvcConfig implements WebMvcConfigurer {


    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new HandlerInterceptor() {
            @Override
            public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
                System.out.println("Interceptor....");
                return true;
            }
        });

    }
}
```


### 如果不配置根Spring容器，那么SpringMVC容器则没有父容器

```xml
    <!-- 加载spring容器 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext-*.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
```

- 如果在web.xml文件中，没有配置这段代码，则不会初始化父容器

