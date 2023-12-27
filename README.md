# mwav-luca

Luca 프로젝트 리포지토리 입니다.

## Requirements

어플리케이션 구성 스펙은 아래와 같습니다.
- [JDK 17](https://jdk.java.net/java-se-ri/17)
- [Springboot 3.1.*](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot)
- [Gradle](https://gradle.org/releases/)
- [Tomcat](https://tomcat.apache.org/download-10.cgi)

## Installation

1. git clone
2. 사용하는 IDE(VS Code, IntelliJ, Eclipse)에 JDK 17 설정
3. Gradle Reload 실행

## Running the application locally

Spring boot 어플리케이션을 로컬에서 구동하는 법은 여러가지 입니다.<br/>
일반적으로는 **main class(src/main/java/com/mwav/luca/LucaApplication.java)**을 실행시켜서 구동합니다.<br/>
사용하는 IDE에 따라서 어플리케이션을 실행시켜주는 단축키 혹은 UI 버튼이 있으니 참고하시길 바랍니다.

## Deploy

현재 CI/CD 프로세스는 외장 tomcat에 Jenkins를 연동하여 war를 배포하는 것으로 예정되어 있습니다.<br>
Spring boot는 기본적으로 내장 컨테이너를 포함한 executable jar를 통해 배포하도록 되어있기에,<br> 배포 시 내장 컨테이너를 제외한 war를 생성하려면 몇 가지 추가적인 설정이 필요합니다.<br>

1. build.gradle 설정
    1) war plugin 추가<br>
    gradle task에 bootWar, war task가 추가됩니다.<br>
    gradle plugin에 대한 자세한 내용은 공식 문서를 참조하세요.

    2) tomcat dependency 추가</br>
    어플리케이션의 내장 서블릿 컨테이너가 외부 서블릿컨테이너의 동작에 영향을 주지 않도록 scope를 providedRuntime으로 지정합니다.

    3) bootWar, war task 수정
    bootWar: 실행 가능한 war 파일 생성. 내장 서블릿 컨테이너 포함하여 생성
    war: 외장 서블릿 컨테이너에 배포하기 위한 war 파일 생성

    ```gradle
    plugins {
        id 'war'
    }

    dependencies {
        providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
    }

    bootWar.enabled = false
    war.enabled = true
    ```

2. main class에 SpringBootServletInitializer 추가
```java
@SpringBootApplication
public class LucaApplication extends SpringBootServletInitializer {

    public static void main(String[] args) {
        SpringApplication.run(LucaApplication.class, args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(LucaApplication.class);
    }

}
```

3. gradle war task 실행
```
CLI 기반 실행
.\gradlew war

GUI 기반 실행
gradle Tasks - build - war
```

출처: https://gangda.tistory.com/14