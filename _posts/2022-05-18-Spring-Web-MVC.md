---
categories : [Spring, MVC]
tags : [spring, springmvc]
---

1. __Spring Web MVC__



    1.1 __DispatchServlet__

    DispatcherServlet은 다른 Servlet처럼 Java config 혹은 web.xml을 통해 선언되고 mapping 되어야 한다.
    차례로 DispatcherServlet은 요청 매핑, 뷰 결정, 예외 처리 등등을 위해 필요한 위임 요소들을 찾기 위해 스프링 설정을 이용한다.

    <br>__DispatcherServlet의 등록 및 초기화를 위한 Java config 예제__

    ```java
    public class MyWebApplicationInitializer implements WebApplicationInitializer{
        @Override
        public void onStartup(ServletContext servletContext){
        // Load Spring web application configuration
            AnnotationConfigWebApplicationContext context 
                        = new AnnotationConfigWebApplicationContext();
            context.register(AppConfig.class);
            
            // Create and register the DispatcherServlet
            DispatcherServlet servlet = new DispatcherServlet(context);
            ServletRegistration.Dynamic registration 
                        = servletContext.addServlet("app", servlet);
            registration.setLoadOnStartup(1);
            registration.addMapping("/app/*");
            }
        }
    ```


    > 게다가 ServletContext API를 사용함으로써, `AbstractAnnotationConfigDispatcherServletInitializer`를 상속받고 특정 메서드들을 오버라이드 할 수 있다.

    > 프로그래밍적인 사용 사례의 경우에는(?), `GenericWebApplicationContext`이 `AbstractAnnotationConfigDispatcherServletInitializer`의 대안이 될 수 있다.


    <br>__DispatcherServlet의 등록 및 초기화를 위한 web.xml 설정 예제__

    ```xml
    <web-app>
        <listener>
            <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>
        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/app-context.xml</param-value>
        </context-param>    
        <servlet>
            <servlet-name>app</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value></param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <servlet-name>app</servlet-name>
            <url-pattern>/app/*</url-pattern>
        </servlet-mapping>
    </web-app>
    ```


    > 스프링부트는 다른 초기화 과정을 따르는데, 
    서블릿 컨테이너의 라이프사이클보다는 스프링 configuration을 사용하여 자기자신과 내장 서블릿 컨테이너를 등록한다. 
   
   <br>
    1.1.1  __Context Hierarchy__
    <br>DispatcherServlet은 WebApplicationContext를 자신의 설정으로 간주한다. 
    하나의 WebApplicationContext를 사용할 수도 있고, root WebApplicationContext를 공유하고 각각 자신의 WebApplicationContext 설정을 가진 DispatcherServlet들에게 공유시킬 수도 있다.
    root WebApplicationContext는 주로 data repository, business service와 같이 여러 servlet 인스턴스들에게 공유될 기초적(기반적) bean들을 포함하고 있다.

    ![mvc-context-hierarchy](../../assets/img/mvc-context-hierarchy.png)

    __WebApplicationContext 계층구조 설정 예제__

    __Java방식__
    ```java
    public class MyWebAppInitializer 
            extends AbstractAnnotationConfigDispatcherServletInitializer {

        @Override
        protected Class<?>[] getRootConfigClasses() {
            return new Class<?>[] { RootConfig.class };
        }

        @Override
        protected Class<?>[] getServletConfigClasses() {
            return new Class<?>[] { App1Config.class };
        }

        @Override
        protected String[] getServletMappings() {
            return new String[] { "/app1/*" };
        }
    }
    ```

    __xml방식__
    ```xml
    <web-app>

        <listener>
            <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>

        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/root-context.xml</param-value>
        </context-param>

        <servlet>
            <servlet-name>app1</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>/WEB-INF/app1-context.xml</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>

        <servlet-mapping>
            <servlet-name>app1</servlet-name>
            <url-pattern>/app1/*</url-pattern>
        </servlet-mapping>

    </web-app>
    ```

    계층구조가 필요 없다면
    java설정의 경우에는 getRootConfigClasses()를 통해 모든 설정을 받고 getServletConfigClasses()는 null을 리턴하도록 할 수 있으며, 
    xml 설정의 경우에는 root context만 사용하고 contextConfigLocation을 비워두도록 한다.

    <br>
    __//TODO..정리필요__
    <br>
    1.1.2 Special Bean Types
    <br>[link](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/web.html#mvc-servlet-special-bean-types) 
    

* * *
# Reference

<https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/web.html#mvc>