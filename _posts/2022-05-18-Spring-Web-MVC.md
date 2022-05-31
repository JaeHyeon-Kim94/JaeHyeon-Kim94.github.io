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
    //TODO..정리필요
    <br>
    1.1.2 Special Bean Types
    <br>[link](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/web.html#mvc-servlet-special-bean-types) 
    
    1.1.3 Web MVC Config
    <br>애플리케이션은 상기한 Special Bean Types에 있는, 요청을 처리하는데 있어서 필요한 구조적 Bean들을 선언할 수 있다. `DispatcherServlet`은 `WebApplicationContext`에서 각각의 special bean을 확인한다. 만약 일치하는 bean type이 없다면, `DispatcherServlet.properties`에 있는 default types로 돌아온다.

    대부분의 경우, MVC Config는 최고의 시작점이다. Java 혹은 XML에 필요한 bean들을 선언하고, 상위 수준의 설정 callback API을 커스터마이즈 할 수 있게 한다.

    > 스프링부트에서는 Java bean 설정을 사용하며 추가적인 여러 편의성 옵션들을 제공한다.

    1.1.4 Servlet Config
    Servlet3.0 이상의 환경에서, 서블릿 컨테이너를 web.xml 대신 프로그래밍적으로(자바 빈을 말하는듯) 구성할지, web.xml과 같이 할지를 선택할 수 있음. 다음은 DispatcherServlet을 등록하는 예제임.

    ```java
    import org.springframework.web.WebApplicationInitializer;

    public class MyWebApplicationInitializer implements WebApplicationInitializer{
        @Override
        public void onStartup(ServletContext container){
            XmlWebApplicationContext context = new XmlWebApplicationContext();
            context.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");

            ServletRegistration.Dynamic registration 
                    = container.addServlet("dispatcher", new registration.setLoadOnStartup(1));
            registration.addMapping("/");
        }
    }
    ```
    `WebApplicationInitializer`는 Spring MVC에서 지원하는 인터페이스로서, 당신의 구현체들이 탐지되고 서블릿 컨테이너의 초기화에 자동적으로 사용된다는 것을 보증합니다.
    `WebApplicationInitializer`의 디폴트 구현체인 `AbstractDispatcherServletInitializer`는 서블릿 매핑과 디스패쳐 서블릿의 위치를 특정하는 메서드를 오버라이딩 함으로써 디스패쳐 서블릿을 좀 더 손쉽게 등록할 수 있도록 해줍니다.

    다음은 추천하는 자바 빈 기반의 설정 방식의 예 입니다.
    ```java
    public class MyWebInitializer extends AbstractAnnotationConfigDispatcherServletInitializer{
            @Override
        protected Class<?>[] getRootConfigClasses() {
            return null;
        }

        @Override
        protected Class<?>[] getServletConfigClasses() {
            return new Class<?>[] { MyWebConfig.class };
        }

        @Override
        protected String[] getServletMappings() {
            return new String[] { "/" };
        }
    }
    ```
    만약 당신이 XML 기반의 스프링 설정을 사용하고 있다면, 다음처럼 `AbstractDispatcherServletInitializer`를 직접적으로 상속받으세요.

    ```java
    public class MyWebAppInitializer extends AbstractDispatcherServletInitializer {

        @Override
        protected WebApplicationContext createRootApplicationContext() {
            return null;
        }

        @Override
        protected WebApplicationContext createServletApplicationContext() {
            XmlWebApplicationContext cxt = new XmlWebApplicationContext();
            cxt.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");
            return cxt;
        }

        @Override
        protected String[] getServletMappings() {
            return new String[] { "/" };
        }
    }
    ```

    또한 `AbstractDispatcherServletInitializer`는 `Filter` 인스턴스들을 DispatcherServlet에 자동적으로 매핑될 수 있도록 편리한 방법을 제공하는데, 다음이 그 예시입니다.
    ```java
    public class MyWebAppInitializer extends AbstractDispatcherServletInitializer {

        // ...

        @Override
        protected Filter[] getServletFilters() {
            return new Filter[] {
                new HiddenHttpMethodFilter(), new CharacterEncodingFilter() };
        }
    }
    ```
    각각의 필터는 그것의 구체적인 타입을 기반으로 한 이름으로 등록되고, 자동적으로 `DispatcherServlet`에 매핑됩니다.

    `AbstractDispaterServletInitializer`의 protected method `isAsyncSupported`는 비동기적인 지원을 가능하게 하는 단일 공간(?)을 `DispatcherServlet`에게 제공하며, 모든 필터들이 매핑됩니다. zero config는 true.

    마지막으로, `DispatcherServlet`을 커스터마이즈 할 필요가 있다면 createDispatcherServlet 메서드를 오버라이드 하세요.

* * *
# Reference

<https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/web.html#mvc>