---
layout: post
title:  "Spring Boot"
date:   2020-12-02 11:38:54 +0900
categories: spring
---

### 스프링부트 소개

제품 수준의 스프링 프레임워크 기반의 제품을 만들때 빠르게 만들수 있다.

스프링에서 가장 널리 쓰이는 설정을 기본적으로 설정한 것. (convention of configuration)
-> 원한다면 보다 더 쉽게 커스터마이징도 가능.

third-party 라이브러리에 대한 처리도 어느정도 제공 -> ex) 톰캣이 내부적으로 뜬다.

스프링 부트는 자바 8 이상부터 사용이 가능.

## 스프링부트 프로젝트 생성

```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>SpringBootLecture</artifactId>
    <version>1.0-SNAPSHOT</version>

<!--    스프링 부트 프레임워크가 부모 의존성 임을 명시-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>nexus-central</id>
            <name>Interpark Nexus Central Maven Repository</name>
            <url>http://nexus.interpark.com:8888/nexus/content/repositories/central/</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>

```

dependencies 항목들은 repositories 에서 의존성을 가져오고 plugin 들은 pluginRepositories에서 가져온다.



### Spring-boot maven 활용 직접 세팅하는 방법

pom.xml 설정

```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>SpringBootAutoConfigure</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>nexus-central</id>
            <name>Interpark Nexus Central Maven Repository</name>
            <url>http://nexus.interpark.com:8888/nexus/content/repositories/central/</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>nexus-central</id>
            <name>Interpark Nexus Central Maven Repository</name>
            <url>http://nexus.interpark.com:8888/nexus/content/repositories/central/</url>
        </pluginRepository>
    </pluginRepositories>
</project>

```

2. 원하는 패키지 생성하고 거기에 Application.java 파일 생성


Application.java

```java

package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

```

### 스프링 부트 프로젝트 구조.

```
    src
        main
            java
            resource
        test
            java
            resource
```

main application의 추천하고 있는 위치는 사용하고 있는 최상위 default package 에 넣는걸 추천.
-> @ComponentScan 이 붙어있기 때문에 그렇다. 이 파일이 들어있는 패키지 기준으로 컴포넌트를 스캔하기 때문.

하나 주의해야할 점은. 이 메인 어플리케이션이 하위에 있는 자바 파일들만 빈등록이 되는것. 그 외 패키지에 있는것은 등록이 안됩니다.!!!

@SpringBootApplication 이 붙은걸 메인 어플리케이션이라고 부름.


### 스프링 부트 의존성 원리

pom.xml 안에 <parent> 태그에 우리는 아래처럼 'spring-boot-starter-parent' 을 받고있다. 여기 안에 들어가 보면 또 부모가 있는걸 볼수 있는데 그 녀석 이름이 'spring-boot-dependencies' 이다. 여기에 또 들어가보면 기본적으로 Spring 을 사용하는데 필요한 의존성들을 모두 주입해둔것을 볼 수 있다. dependencyManagement 라는 태그로 되어있음.

```xml

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

```

이렇게 의존성을 스프링 부트 내부적으로 관리해주고 우리는 spring-boot-starter-web 이런식으로 한두개만 의존성 관리할게 될때의 장점은. 버전을 올리거나 내려야 할 상황이 올때
모든 라이브러리들이 호환이 잘되는지 않되는지를 명확히 알기 어렵기도 하기 때문에 이런 버전 관리가 어렵다. 그래서 이렇게 관리해주고 있는 이러한 스프링 부트만을 의존성을 받게 되면.
이미 관리되어진 의존성만을 가져오는 거기때문에 버전 문제에 대한것을 쉽게 처리할 수 있다.

<parent/> 태그를 사용하지 않고 의존성을 관리하는 방법도 있다.

<parent/> 가 만약 다른 걸로 고정이되어서 수정할 수 없느경우 <dependencyManagement/> 이 태그를 사용해서 보다 더 커스터마이징된 의존성을 넣을 수 있다. 여기에 spring-boot-dependencies를 넣으면 됨.

단점 -> parent 쪽에서 들어오는거가 dependency 만 들어오는게 아니고 다른 속성들 (인코딩, 자바 버전, 플러그인 설정, yml 파일 사용) 도 설정을 해주는 거기 때문에 이런것들을 모두 관리해야 한다.
parent에서 의존성 말고 관리해주느 것들을 <parent/> 이 태그로 못가져 오기 때문에. 이 부분들을 모두 나의 프로젝트에서 관리해주어야 한다.


### 스프링 부트 의존성 관리 응용

mvnrepository -> maven 의존성의 허브사이트.

1. 의존성을 추가하는 방법

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
```

modelMapper -> domain을 가지고 flatDesign을 만들어준다

```xml
        <dependency>
            <groupId>org.modelmapper</groupId>
            <artifactId>modelmapper</artifactId>
            <version>2.3.0</version>
        </dependency>
```

version은 항상 명시해줘야한다. Spring Boot가 지원해주는 애들은 version이 자동 관리가 되긴 함 이거는 intellij 소스창 옆에 보면 동그랗게 뜸


```xml
    <properties>
        <spring.version>5.0.6.RELEASE</spring.version>
    </properties>
```

스프링 버전을 바꾸고싶다면 properties 태그에 위와같이 추가한다. 이는 Spring-boot parent 에 dependencies parent 에 가보면 동일한 형태로 정의가 되고 있음을 볼수 있다.
자바 버전을 바꾸고싶다면 properties 태그에 똑같이 아래와 같이 수정하면된다.

```xml
    <properties>
        <java.version>1.8</java.version>
    </properties>
```

### 자동 설정 이해 (EnableAutoConfiguration)

@SpringBootApplication 는 중요한 3개의 어노테이션을 합친것

@SpringBootConfiguration
@ComponentScan
@EnableAutoConfiguration


스프링 부트는 2단계로 의존성을 가져온다?
ComponentScan + EnableAutoConfiguration

@EnableAutoConfiguration 이 없어도 어플리케이션을 실행할 수는 있다. 그러나 이것은 웹서버는 없다. 웹어플리케이션에 대한 초기 자동 설정을 버리고 그냥 스프링 프레임워크만 띄웠다는 의미인듯.

```java

import org.springframework.boot.SpringApplication;
import org.springframework.boot.WebApplicationType;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan
public class Application {
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(Application.class);
        application.setWebApplicationType(WebApplicationType.NONE);
        application.run(args);
    }
}

```

@ComponentScan 으로 빈을 먼저 등록한 다음에
@EnableAutoConfiguration 이걸로 추가적인 빈 등록을 한다 2단계로 나누어진다.

@ComponentScan -> 스프링의 기본적인 내용
    @Component, @Configuration @Repository, @Service @Controller @RestController 등등의 어노테이션이 되어있는 인스턴스를 리플렉션으로 생성해서 스프링 컨테이너에 빈으로서
    넣어두는거 같음. 기본적인 내용.
    자기 어노테이션 기준으로 하위 패키지에 저 어노테이션들이 박혀있는 클래스들을 인스턴스화 후 빈 등록.

@EnableAutoConfiguration -> Spring meta 파일을 보고 가져온다.
org.springframework.boot:spring-boot-autoconfigure 프로젝트 안에
META-INF 폴더가 있고 이안에 spring.factories 라는 파일이 있다.
거기 안에 org.springframework.boot.autoconfigure.EnableAutoConfiguration=\ 라는 경로로 키가 설정 되어있오 그밑으로 Configuration value 값들이 들어있는데 이걸 다 가져오는거 같아.
여하튼 이 파일을 보고 여기에 설정되어있는 값들을 모두 빈등록.

WebMvcAutoConfiguration 이 클래스도 EnableAutoConfiguration 으로 빈 등록되는 객체인데
그 안에보면 @Conditional.... 조건에 따라서 등록되고 안되는것도 볼수있다.

모두 빈 등록을 하지만 조건에 따라 안되는것도 있음을 참고해야함.


### 자동 설정 만들기 1부: Starter와 AutoConfigure

Xxx-Spring-Boot-Autoconfigure => 자동설정 등을 설정할때 이런식으로 이름을 만들고 프로젝트를 만든다.
Xxx-Spring-Boot-Starter => 필요한 의존성등을 정의할때 보통 이런식으로 이름을 만든다.
그냥 하나로 하고싶을 때에는 Starter 이름을 사용하자.

```java

/* Holoman.java */

package me.whiteship;

public class Holoman {
    String name;
    int howLong;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getHowLong() {
        return howLong;
    }

    public void setHowLong(int howLong) {
        this.howLong = howLong;
    }

    @Override
    public String toString() {
        return "Holoman{" +
                "name='" + name + '\'' +
                ", howLong=" + howLong +
                '}';
    }
}

```

```java

/* HolimanConfiguration.java */
package me.whiteship;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class HolomanConfiguration {

    @Bean
    public Holoman holoman() {
        Holoman holoman = new Holoman();
        holoman.setHowLong(5);
        holoman.setName("Keesun");
        return holoman;
    }
}

```

src/main/resource/META-INF에 spring.factories 파일 생성

이 파일은 스프링부트가 아니고 스프링에서 원래 제공해주는 형식의 파일이다 여기서 라이프사이클도 조절이 가능하다.

```factories

org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  me.whiteship.HolomanConfiguration

```

그 다음에 다른 프로젝트에서 이 프로젝트를 사용할 수 있도록 mvn install 해서 빌드해서 jar 파일로 만들고 이걸 로컬 메이븐 저장소에다가 등록 다른 lib에 등록해준다.

사용하려고하는 프로젝트를 열고 의존성 추가 의존성 groupId 와 artifactId, version 등은 Holoman 프로젝트 pom.xml 에서 확인할 수 있다.

```xml

    <dependencies>
        <dependency>
            <groupId>me.whiteship</groupId>
            <artifactId>kidong-spring-boot-starter</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
    
```

ApplicationRunner 는 스프링 프레임워크가 시작되었을 때 자동으로 실행되는 빈을 만들고 싶을때 사용하면 된다. 인터페이스 객체임으로 implements 받아서 사용가능

```java

package com.example;

import me.whiteship.Holoman;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

@Component
public class HolomanRunner implements ApplicationRunner {

    /* 이 프로젝트에서는 어디에서도 Holoman 객체를 빈으로 등록하지 않았다 */
    @Autowired
    Holoman holoman;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(holoman);
    }
}

```

이 방법의 문제는 내가 직업 위처럼 자동설정한 빈을 아래처럼 직접 다시 생성하면 생성한 빈이 무시가 된다. 

```java

package com.example;

import me.whiteship.Holoman;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.WebApplicationType;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(Application.class);
        application.setWebApplicationType(WebApplicationType.NONE);
        application.run(args);
    }

    @Bean
    public Holoman holoman() {
        Holoman holoman = new Holoman();
        holoman.setName("whiteship");
        holoman.setHowLong(60);
        return holoman;
    }
}

```

왜냐하면 빈 등록을 하는 단계가 2개라고 이전에 언급했는데
@ComponentScan 방법이 먼저이고 @EnableAutoConfiguration 인데 직접 생성한 빈 설정은 @ComponentScan 방법에서 만들어졌다가 2단계에서 묻히는것.
@EnableAutoConfiguration 요거에 특정 케이스에는 빈 등록을 안하게끔 하는 설정들이 있엇다 그걸 활용하면 이 문제를 개선 가능하다.
내 설정보다 자동설정이 더 우선된다.

최근 버전으로 살펴보니 아예 오류가 떨어진다.

```
The bean 'holoman', defined in class path resource [me/whiteship/HolomanConfiguration.class], could not be registered. A bean with that name has already been defined in com.example.Application and overriding is disabled.
```


### 자동 설정 만들기 2부: @ConfigurationProperties

@ConditionalOnMissingBean -> 이 타입의 빈이 없을 때만 자동설정 해라. 위에서 본것처럼 자동설정으로 빈을 등록하고, 또 실제 프로젝트에서 빈을 등록하게 되면 오류가 반환된다.
 실제 프로젝트에서 빈을 등록하고 싶다면 이 어노테이션을 붙여서 실제 프로젝트에서 빈 등록을 안할 때에만 자동 설정이 되도록 할 수 있다.

 스프링 부트가 여러가지 설정들을 커스터마이징 할수 있도록 하는 기능들 중 하나이다.

 위에서 한 방법은 빈 자체를 새롭게 등록했다. 그런데 만약 빈의 사이즈가 커서 빈 자체를 새롭게 등록하는 것이 아니고 빈의 일부 데이터만 변경하고 싶을때에는 Properties를 활용해서 값을 넘겨준다. 빈 자체는 자동설정에서 만들고 빈을 만들 때 필요한 Properties 값을 메인 프로젝트에서 가져온다.

ConfigurationProperties 어노테이션을 사용하기 위해서는 의존성을 추가해줘야 한다.

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

아래는 Properties 구조에 맞는 POJO 객체이다

```java

 import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties("holoman")
public class HolomanProperties {
    private String name;

    private int howLong;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getHowLong() {
        return howLong;
    }

    public void setHowLong(int howLong) {
        this.howLong = howLong;
    }
}

```

@EnableConfigurationProperties 어노테이션을 적용해야 동작한다. 하나 더 달라진 점은 Bean 등록시 holoman 함수에서 파라미터를 받고 있는데 저 값을 메인 프로젝트의 spring.properties 에서 가져올 것이다.

```java

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableConfigurationProperties(HolomanProperties.class)
public class HolomanConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public Holoman holoman(HolomanProperties properties) {
        Holoman holoman = new Holoman();
        holoman.setHowLong(properties.getHowLong());
        holoman.setName(properties.getName());
        return holoman;
    }
}

```

메인 프로젝트의 application.properties 에 아래처럼 설정해준다.
```
holoman.name = keesun
holoman.how-long = 6
```

### 내장 웹서버 이해

스프링 부트는 서버가 아니다. 스프링 프레임워크를 쉽게 사용할 수 있도록 하는 툴이지 특히나 웹서버가 아니다. 스프링부트가 제공하는 자동설정을 활용하지 않고 내장 톰캣을 사용하는 방법

```java

public class Application {
    public static void main(String[] args) throws LifecycleException {
        Tomcat tomcat = new Tomcat();
        tomcat.setPort(8080);

        Context context = tomcat.addContext("/", "/");

        HttpServlet servlet = new HttpServlet() {
            @Override
            protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
                PrintWriter writer = resp.getWriter();
                writer.println("<html><head><title>");
                writer.println("Hey, Tomcat");
                writer.println("</title></head>");
                writer.println("<body><h1>Hello Tomcat</h1></body");
                writer.println("</html");
            }
        };

        String servletName = "helloServlet";
        tomcat.addServlet("/", servletName, servlet);
        context.addServletMappingDecoded("/hello", servletName);

        tomcat.getConnector();
        tomcat.start();
    }
}

```

톰캣을 먼저 띄우고, 서블릿을 만들고 이 서블릿을 톰캣에 할당하고 /hello 경로로 들어올 때 이 서블릿으로 들어가도록 매핑

스프링 부트의 자동설정을 활용해 위와 같은 톰캣 설정을 구현한다.

웹서버의 경우 spring-boot-autoconfigure 프로젝트에 ServletWebServerfactoryAutoConfiguration 객체를 참조해 자동설정이 구현된다 내부를 보면 톰캣, 제티, 네티, 언더토우 등등 존재한다. 여기서 Import 하는 부분에 EmbeddedTomcat을 들어가보면 TomcatServletWebServerFactory 이객체가 있는데 여기서 위 예시처럼 톰캣을 구현하고 있다.
TomcatServletWebServerFactoryCustomizer 여기세어 커스터마이징도 가능하다.

DispatcherServletAutoConfiguration -> 
Spring에서는 DispatcherServlet이 서블릿 개념으로 돈다. 이 녀석은 스프링에서 HttpServlet을 상속받아 만든 놈 스프링 MVC의 핵심 클래스. 여기에서 자동설정을 한다.

Servlet 컨테이너들은 바뀔수 있지만 Servlet은 변하지 않는다. 다시 말하자면 톰캣 위에 띄울수 있고, 언더토우 위에 띄울 수 있고 이런말.

### 내장 서블릿 컨테이너 응용 1부 : 컨테이너와 서버 포트

Spring boot의 기본 설정은 tomcat. 자동설정에서 그렇게 만듬.

다른 서블린 컨테이너로 변경하기 위해서는

1. spring-boot-starter-web 여기 의존성에서 기본적으로 tomcat을 가져옴 이걸 빼야함.

```xml

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

2. 다른 서블릿 컨테이너의 의존성을 추가

```xml

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jetty</artifactId>
        </dependency>
```

application.properies 여기에 아래 코드를 넣으면 서버가 웹서버로 뜨지 않는다.

spring.main.web-application-type=none

서버포트 변경하려면 application.properties 여기에 아래 코드 입력
server.port=7070

아래 방법으로 포트 번호를 알수 있다. 웹서블릿이 생성될때 아래 리스너가 실행되고 여기서 값을 얻어온다.
```java

@Component
public class PortListener implements ApplicationListener<ServletWebServerInitializedEvent> {
    @Override
    public void onApplicationEvent(ServletWebServerInitializedEvent servletWebServerInitializedEvent) {
        ServletWebServerApplicationContext applicationContext = servletWebServerInitializedEvent.getApplicationContext();
        System.out.println(applicationContext.getWebServer().getPort());
    }
}

```

### 내장 서블릿 컨테이너에 HTTPS, HTTP2 적용하기

HTTPS 를 사용하려면 먼저 인증서 작업을 해야함 키를 만들어야함 keytool 명령어를 활용해 인증서를 만든다. jdk/bin 폴더에가면 keytool.exe 프로그램이 있다. 이를 실행

```
keytool -genkey -alias spring -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore keystore.p12 -validity 4000
```

생성된 키를 프로젝트의 루트에 두는 경우

```
server.ssl.key-store= keystore.p12
```

이렇게 application.properties 에 설정해주시고, resources 폴더에 넣고싶다면 넣고

```
server.ssl.key-store= classpath:keystore.p12
```

해주면 된다.

HTTPS 를 적용하면 HTTP로 받을 수없다 커넥터가 한개고 이거를 HTTPS가 덮어씌웠기 때문. 아래처럼 커넥터를 새로 만들어서 적용하면 두 프로토콜을 모두 쓸 수 있다.


```java

@SpringBootApplication
@RestController
public class Springbootdemo4Application {

    @GetMapping("/hello")
    public String hello() {
        return "Hello Spring";
    }

    public static void main(String[] args) {
        SpringApplication.run(Springbootdemo4Application.class, args);
    }

    @Bean
    public ServletWebServerFactory serverFactory() {
        TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
        tomcat.addAdditionalTomcatConnectors(createStandardConnector());
        return tomcat;
    }

    private Connector createStandardConnector() {
        Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
        connector.setPort(8080);
        return connector;
    }
}

```

```
server.ssl.key-store= keystore.p12
server.ssl.key-store-type= PKCS12
server.ssl.key-store-password=123456
server.ssl.key-alias=spring
server.port=8443
```

HTTP2 쓰기

 
### 독립적으로 실행 가능한 JAR (Spring-boot-maven-plugin)

mvn clean package를 실행 하면 모든 의존성을 가지고 있는 실행 가능한 JAR 파일 하나가 생성됨.
이안에 의존성 jar 파일들이 모두 들어있음.

스프링 부트는 내장 JAR로 해서 JAR안에 JAR로 묶어둔다.

target.app.BOOT-INF.lib 여기에 의존성들이 들어간다.

JarLauncher 이 파일이 SpringBoot 어플리케이션을 기본적으로 실행한다.


### SpringApplication 객체의 기능

1. Edit configuration 에서 jvm option에 -Ddebug 옵션을 주거나 program argument 에 --debug 옵션을 주면 어플리케이션을 디버그 모드로 실행할 수 있다.

2. 이 객체에서 banner를 조작가능.

3. ApplicationEvent.
    스프링 생명주기 같은거 같다.

    이벤트 처리를 할때 중요한점은 어플리케이션 컨텍스트가 발생하기 이전에 발생하는 이벤트들은 빈이 없기때문에 빈주입하더라도 리스너 동작하지 않는다.
    빈이 없기 때문이다.

```java

@Component
public class SampleListener implements ApplicationListener<ApplicationStartingEvent> {
    @Override
    public void onApplicationEvent(ApplicationStartingEvent applicationStartingEvent) {
        System.out.println("Application is starting");
    }
}

```

그래서 위와같이 applicationContext가 생성되지 않는 라이프 사이클에 이벤트 처리를 하고 싶으면 직접 객체 생성을 해줘야 한다.

```java

    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(Springbootdemo4Application.class);
        app.addListeners(new SampleListener());
        app.run(args);
    }
```

아래 케이스는 applicationContext가 생성되고 난 뒤의 이벤트이기 때문에 정상 실행이 되야 한다.

```java

@Component
public class SampleListener implements ApplicationListener<ApplicationStartedEvent> {
    @Override
    public void onApplicationEvent(ApplicationStartedEvent applicationStartedEvent) {
        System.out.println("Started");
    }
}

```

### WebApplicationType

Spring MVC, SPRING WebFlux 둘을 구분하기 위함 둘 다 구현되어 있다면 기본적으로 MVC를 선택한다. 만약 이 상황에서 WebFlux 를 적용하고 싶다면

```java

   public static void main(String[] args) {
        SpringApplication app = new SpringApplication(Springbootdemo4Application.class);
        app.setWebApplicationType(WebApplicationType.REACTIVE);
        app.run(args);
    }

```

이렇게 적용하면 된다. WebApplicationType.SERVLET 가 MVC 를 실행하는 설정.

-D 로들어오는 것은 jvm option, -- 로 들어오는것은 application arguments.

```java

public Contructor(ApplicationArguments arguments) {
    System.out.println("foo: " + arguments.containsOption("foo"));
    System.out.println("bar: " + arguments.containsOption("bar"));
}

```

ApplicationArguments 객체로 Program Arguments를 받을 수 있다 (-- 옵션으로 들어오는 값들)

애플리케이션 아규먼트 사용하기
    - ApplicationArguments를 빈으로 등록해 주니까 가져다 쓰면 된다.

애플리케이션 실행한 뒤 뭔가 실행하고 싶을 때
    - ApplicationRunner (추천) 또는 CommandLineRunner
    - 순서 지정 가능 @Order

ApplicationRunner, CommandRunner 둘다 jvm options 은 못받음, program arguments 만 받음


# 외부설정

application.properties 이런 파일들을 말함. 이런데서 설정한 값들을 외부 설정값이라고 생각하면된다 스프링부트가 이런 값들을 참고한다.

application.properties
```
keesun.name = keesun
```

```java

@Component
public class SampleRunner implements ApplicationRunner {

    @Value("${keesun.name}")
    private String name;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("=========================");
        System.out.println(name);
        System.out.println("=========================");
    }
}

```

name 필드에 application.properties 안에 있는 값을 주입한다.

우선순위

커맨드라인 argument로 주는게 application.properties 보다 높다. 이방법으로 주면 덮어씌워진다. 테스트해보려면 mvn clean package 해서 jar로 패키징하고 
해당 jar 파일을 java -jar ... 로 실행할때 --keesun.name=whiteship 이런식으로 준다.

@SpringBootTest 어노테이션에 properties 속성으로 외부설정 값을 주눈게 커맨드라인보다 더 높다.

이런 외부값들을 가져오는 다른방법 Environment 객체 활용

```java
@Component
public class SampleRunner implements ApplicationRunner {

    @Value("${keesun.name}")
    private String name;

    @Autowired
    Environment environment;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("=========================");
        System.out.println(environment.getProperty("keesun.name"));
        System.out.println("=========================");
    }
}

```

테스트용 외부 설정이 따로 필요할때는 application.properties 이파일을 테스트쪽 같은 패키지에 동일하게 생성한다.
그러면 테스트 외부설정의 값을 사용한다. 

서버포트에 랜덤값을 주고싶을때에는 application.properties 에서 ${random.int} 이걸 사용하면 안되고 단순히 0 값을 줘야한다.
0은 가용한 범위내애서 포트번호를 주지만 ${random.int} 이 값은 무작위로 값을 주기때문. -가 나올수도 있고, 루트권한이 필요한 포트일수도 있고..

위 방법의 단점은 테스트용 application.properties 값들의 존재여부가, 리얼 application.properties 와다르다면, 주입시 실패할 수도 있다는점 참고해야한다.

이 문제를 해결하기 위해서는 src 쪽의 application.properties 와는 다른 이름을 가진 properties 파일을 테스트 패키지 쪽에 넣고, 이를 테스트 클래스에서 추가해주면 된다.

외부설정 값들을 하나의 빈으로도 받을 수 있다.

```
keesun.name = keesun
keesun.age = ${random.int(100)}
keesun.fullnName = ${keesun.name} Baik
```

```java

@Component
@ConfigurationProperties("keesun")
public class KeesunProperties {
    private String name;
    private int age;
    private String fullName;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getFullName() {
        return fullName;
    }

    public void setFullName(String fullName) {
        this.fullName = fullName;
    }
}

```

```java

@Component
public class SampleRunner implements ApplicationRunner {

    @Autowired
    KeesunProperties keesunProperties;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("=========================");
        System.out.println(keesunProperties.getName());
        System.out.println(keesunProperties.getAge());
        System.out.println("=========================");
    }
}


```

### 프로파일

특정한 프로파일에서만 특정한 빈을 등록하고 싶다. 이런 케이스에서 사용함 빈생성을 특정 상황(스테이징인지, 로컬인지, 운영인지) 에 맞게 할수 있음

```java
@Profile("prod")
@Configuration
public class BaseConfiguration {

    @Bean
    public String hello() {
        return "hello";
    }
}
```

```java
@Profile("test")
@Configuration
public class TestConfiguration {
    @Bean
    public String hello() {
        return "test";
    }
}
```

```
// application.properties
spring.profiles.active=test
```

혹은 커맨드라인 아규먼트로 위 값을 제공해도 된다. 이방법이 실제적으로 더 많이 쓰일듯. 왜냐하면 어느 환경에서 프로젝트가 실행하는지는 외부에서 설정값을 가져와야 하기 떄문에.

### 스프링부트 로깅

로깅 퍼사드 vs 로깅

로거 API 들을 추상화 해놓은 인터페이스들 
주로 프레임워크들은 로깅 퍼사드들을 활용해서 개발.
로깅퍼사드를 안써도되는데 장점은 로거들을 바꿔낄수 있다.
로깅퍼사드 -> 그냥 로거들의 추상화된 객체인가보네. 구현체를 바꿔낄수 있게 하려고.

프레임워크에서는 로깅퍼사드를 쓰는 이유는 어플리케이션 개발자가 쓰는 로거가 여러개 이기 때문에 이를 모두 수용하려고 추상체를 사용. 인터페이스의 사용 개념과 동일.

Commons Logging -> 스프링에서 기본적으로 제공하지만 지금은 안쓰는 추세, 뭔가 불안정했던게 많아다고함.

최종적으로 Logback을 쓰는거임. 이게 구현체인가보다. 로거 Slf4j, commons logging 이것들은 로깅 퍼사드.

로깅한 내용을 파일로 떨구고싶으면 application.properties 여기에 logging.path=logs 이런식으로 디렉토리, 파일을 설정해주면 된다.

```
logging.path=logs
```

로깅 레벨도 패키지마다 설정가능.

```
logging.level.[패키지경로]=DEBUG
```

```
logging.file.path=logs
logging.level.com.example.springdemo=DEBUG
```

```java

@Component
public class SampleRunner implements ApplicationRunner {

    private Logger logger = LoggerFactory.getLogger(SampleRunner.class);

    @Autowired
    private String hello;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        logger.info("==============================");
        logger.info(hello);
        logger.info("==============================");
    }
}

```

### Spring boot Http Message Converter

-> Http 요청 전문을 객체로 변경하거나, 객체를 Http 응답 전문으로 변경할때 사용되는 객체.
요 객체를 호출하는 어노테이션이 @ResponseBody, @RequestBody 이다. 이건 익숙하지.

우리가 객체를 반환하게끔 코드를 짰지만 이거를 Http 전문으로 변경해주는 것이 기본적으로 jackson을 사용. String, int 같은 원시타입도 메세지 컨버터가 사용된다.
@Controller 어노테이션을 쓰고 @ResponseBody 어노테이션을 안넣으면 REST 형태가 아니기때문에 return 값에 해당하는 view resolver 를 찾게된다.


```java

@RestController
public class UserController {

    @GetMapping("/hello")
    public String hello() {
        return "hello";
    }

    @PostMapping("/users/create")
    public User create(@RequestBody User user) {
        return user;
    }
}

```

```java

@RunWith(SpringRunner.class)
@WebMvcTest(UserController.class)
public class UserControllerTest {
    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        mockMvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string("hello"));
    }

    @Test
    public void createUser_JSON() throws Exception {
        String userJson = "{\"username\":\"keesun\", \"password\":\"123\"}";

        mockMvc.perform(post("/users/create")
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .accept(MediaType.APPLICATION_JSON_UTF8)
                .content(userJson))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.username", is(equalTo("keesun"))))
                .andExpect(jsonPath("$.password", is(equalTo("123"))));
    }
}

```

```java

public class User {
    private Long id;
    private String username;
    private String password;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}

```

### Spring boot ViewResolver

ContentNegotiationViewResolver - 들어오는 요청의 accept Header 에 따라서 응답을 다르게 해주는 resolver

```java

    @PostMapping("/users/create")
    public User create(@RequestBody User user) {
        return user;
    }
```

메인에 해당하는 위 소스를 수정하지 않아도 accept 에따라서 xml 객체가 내려갈수도 json 객체가 내려갈수도 있다. 위에 있는 contentNegotialtionViewResolver 이녀석 덕분에.

HttpMediaTypeNotAcceptableException 406 에러가 뜬다면 객체를 accept에 원하는 전문의 형태로 변환해줄 객체가 없다는 의미.

spring boot 자동설정에 messageConvert 쪽을 보면 Xml을 매핑해주는 객체가 기본적으로 없다. 그래서 아래 의존성을 추가해야한다.

```xml

        <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-xml</artifactId>
            <version>2.9.6</version>
        </dependency>

```

### Spring boot 정적리소스 지원

이미 만들어져있는 리소스를 정적리소스. 이거를 바로 반환할수 있도록 지원함

### Spring boot 웹JAR

클라이언트에서 사용하는 자바스크립트 라이브러리 react, vue, bootstrap, jquery 이것들을 jar 파일로 dependency를 추가할 수 있다.

jquery를 추가해보자.

```xml
        <dependency>
            <groupId>org.webjars.bower</groupId>
            <artifactId>jquery</artifactId>
            <version>3.3.1</version>
        </dependency>
```

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
Hello Static Resource AHHHA

<script src="/webjars/jquery/3.3.1/dist/jquery.min.js"></script>
<script>
    $(function() {
        alert("ready");
    })
</script>
</body>
</html>

```

webjars-locator-core 의존성을 추가하면 버저닝도 생략가능

```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>webjars-locator-core</artifactId>
    <version>0.35</version>
</dependency>
```

### favicon, index 페이지

static 디렉토리에 favicon.ico 파일을 넣는다. 그리고 빌드하면 끝.

안바뀌면 파비콘을 직접 호출한 후에, 브라우저 새로고침하면 됨. 브라우저 캐시때문에 발생하는 문제.

### Thymeleaf

템플릿엔진이라는 것은 뷰를 만드는데 주로쓰고, 코드 제너레이션, 이메일 템플릿 이런거 사용

jsp를 권장하지 않는이유.
-> jar 패키징 할때는 동작하지 않고 war 패키징을 해야함.
-> 독립적으로 임베디드 톰캣으로 구현하기가 맞지 않음.
-> undertow는 jsp를 지원하지 않음.
-> 의존성 문제도 꽤 있다고 함.

Thymeleaf
-> 비교적 최근에 만들어진 템플릿 엔진
-> 웹서버를 띄우지않고도 프론트쪽 작업이 가능하다. 데이터를 임시로 placeholder 처럼 처리가 가능.

view쪽 테스트코드 작성하기

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1 th:text="${name}">Name</h1>
</body>
</html>
```

```java
@Controller
public class SampleController {

    @GetMapping("/hello")
    public String hello(Model model) {
        model.addAttribute("name", "keesun");
        return "hello";
    }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest(SampleController.class)
public class SampleControllerTest {
    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        mockMvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andDo(print())
                .andExpect(view().name("hello"))
                .andExpect(model().attribute("name", is("keesun")))
                .andExpect(content().string(containsString("keesun")));
    }
}
```

### HtmlUnit

html을 단위테스트하기 위한 툴

form의 submit 행위를 모킹도 가능
특정 브라우저인척 할수도 있다.
dom tree element 탐색 가능

### Spring HATEOAS

REST API 를 만들 때 연관된 링크정보들을 클라이언트에게 함께 제공

```java

@RestController
public class SampleController {

    @GetMapping("/hello")
    public Resource<Hello> hello() {
        Hello hello = new Hello();
        hello.setPrefix("Hey,");
        hello.setName("Keesun");

        Resource<Hello> helloResource = new Resource<>(hello);
        helloResource.add(linkTo(methodOn(SampleController.class).hello()).withSelfRel());

        return helloResource;
    }
}

```

```java

@RunWith(SpringRunner.class)
@WebMvcTest(SampleController.class)
public class SampleControllerTest {
    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        mockMvc.perform(get("/hello"))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(jsonPath("$._links.self").exists());

    }
}
```

### 스프링 데이터 7부 : 데이터베이스 초기화

ddl이 실행될때 hibernate가 자동실행하는 것 말고 우리가 sql 을 스크립트로 작성할 수 있다.

application.properties
```
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
# spring.jpa.hibernate.ddl-auto=validate
# spring.jpa.generate-ddl=false
```
이렇게 해두고 schema.sql 파일을 만들어서 여기에 스크립트를 넣는다.

resource/schema.sql

그다음에 data.sql 이걸 정의에서 여기에 초기 데이터를 넣을 수 있다.


### 스프링 데이터 8부 : 데이터베이스 마이그레이션

Flyway, Liquibase 이 두가지가 대표적인 데이터베이스 마이그레이션 툴

### 스프링 데이터 9부 : 레디스

도커로 레디스 실행

```
docker run -p 6379:6379 --name redis_boot -d redis
docker exec -i -t redis_boot redis-cli
```

