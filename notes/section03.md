# SECTION 03 - 웹 서버와 서블릿 컨테이너
🌱 [웹 서버와 스프링 부트 소개](#웹-서버와-스프링-부트-소개)  
🌱 [톰캣 설치](#톰캣-설치)  
🌱 [프로젝트 설정](#프로젝트-설정)  
🌱 [WAR 빌드와 배포](#war-빌드와-배포)  
🌱 [톰캣 설정](#톰캣-설정)  
🌱 [서블릿 컨테이너 초기화](#서블릿-컨테이너-초기화)  
🌱 [스프링 컨테이너 등록](#스프링-컨테이너-등록)  
🌱 [스프링 MVC 서블릿 컨테이너 초기화 지원](#스프링-mvc-서블릿-컨테이너-초기화-지원)  
🌱 [정리](#정리)  

## 웹 서버와 스프링 부트 소개
### 외장 서버 vs 내장 서버
![외장 서버 vs 내장 서버.png](imgs/%EC%99%B8%EC%9E%A5%20%EC%84%9C%EB%B2%84%20vs%20%EB%82%B4%EC%9E%A5%20%EC%84%9C%EB%B2%84.png)  
#### 전통적인 방식
과거에 자바로 웹 애플리케이션을 개발할 때는 먼저 서버에 톰캣같은 WAS(웹 애플리케이션 서버)를 설치했다.
그리고 WAS에서 동작하도록 서블릿 스펙에 맞추어 코드를 작성하고 WAR 형식으로 빌드해서 WAR 파일을 만들었다.
이렇게 만들어진 WAR 파일을 WAS에 전달해서 배포하는 방식으로 전체 개발 주기가 동작했다.

이런 방식은 WAS 기반 위에서 개발하고 실행해야 한다. IDE 같은 개발 환경에서도 WAS와 연동해서 실행되도록
복잡한 추가 설정이 필요하다.

#### 최근 방식
최근에는 스프링 부트가 내장 톰캣을 포함하고 있다. 애플리케이션 코드 안에 톰캣 같은 WAS가 라이브러리로 내장되어 있다는 뜻이다.
개발자는 코드를 작성하고 JAR로 빌드한 다음에 해당 JAR를 원하는 위치에서 실행하기만 하면 WAS도 함께 실행된다.
쉽게 이야기 해서 개발자는 `main()` 메서드만 실행하면 되고, WAS 설치나 IDE 같은 개발 환경에서 WAS와 연동하는 복잡한 일은
수행하지 않아도 된다.

## 톰캣 설치
> 🚨 자바 버전 주의  
> 스프링 3.0 이상은 자바 17이 최소 요구 버전이다.

## 프로젝트 설정
1. [기본 프로젝트](https://github.com/ddangme/Spring-Boot-Study/commit/8702ad8adbba6442b89e9af0c64aa9700e075920)를 다운로드 한다.
2. IDE에서 `build.gradle`을 선택한다.
3. [build.gradle](../server/build.gradle)을 확인한다.
   - `id 'war'`: 톰캣 같은 웹 애플리케이션 서버(WAS) 위에서 동작하는 WAR 파일을 만들어주는 플러그인이다.
   - `jakarta.servlet-api`: 서블릿을 사용할 때 필요한 라이브러리이다.
4. HTML 등록
`/src/main/webapp` 에서 [index.html](../server/src/main/webapp/index.html) 파일을 만든 뒤 코드를 작성한다.
    ```html
    <html>
    <body>index html</body>
    </html>
    ```
5. 서블릿 등록
   - 전체 설정이 잘 동작하는지 확인하기 위한 간단한 서블릿
   - 웹 서버를 통해 이 서블릿이 실행되어야 한다.
   - [TestServlet.java](../server/src/main/java/hello/servlet/TestServlet.java) 파일을 만든 뒤 코드를 작성한다.
      ```java
      package hello.servlet;
      
      import jakarta.servlet.annotation.WebServlet;
      import jakarta.servlet.http.HttpServlet;
      import jakarta.servlet.http.HttpServletRequest;
      import jakarta.servlet.http.HttpServletResponse;
      import java.io.IOException;
      /**
       * http://localhost:8080/test
       */
      @WebServlet(urlPatterns = "/test")
      public class TestServlet extends HttpServlet {
          @Override
          protected void service(HttpServletRequest req, HttpServletResponse resp)
                  throws IOException {
              System.out.println("TestServlet.service");
              resp.getWriter().println("test");
          }
      }
      ```
   - `/test`로 요청이 오면 이 서블릿이 실행된다.
   - `TestServlet.service`를 로그에 출력한다.
   - `test`를 응답한다. 웹 브라우저로 요청하면 이 서블릿이 실행되고 화면에 `test`가 출력되어야 한다.
   - 이 서블릿을 실행하려면 톰캣과 같은 웹 애플리케이션 서버(WAS)에 이 코드를 배포해야 한다.

## WAR 빌드와 배포
### 프로젝트 빌드
- 프로젝트 폴더로 이동
- 프로젝트 빌드
  - `./gradlew build`
- WAR 파일 생성 확인
  - `build/libs/server-0.0.1-SNAPSHOP.war`

> 🍀참고  
> `build.gradle`에 보면 `war` 플러그인이 사용된 것을 확인할 수 있다. 이 플러그인이 `war` 파일을 만들어준다.
> ```java
> plugins {
>     id 'java'
>     id 'war'
> }
> ```

### WAR 압축 풀기
- 빌드한 war 파일의 압축을 풀어서 내용물을 확인해보자.
- `build/libs` 폴더로 이동하자.
- 다음 명령어를 사용해서 압축을 풀자.

#### WAR 압축 풀기 결과
- `WEB-INF`, `classes`, `lib` 같은 특별한 폴더들이 보인다.
- ![WAR 압축 풀기 결과.png](imgs/WAR%20%EC%95%95%EC%B6%95%20%ED%92%80%EA%B8%B0%20%EA%B2%B0%EA%B3%BC.png)

### JAR 소개
자바는 여러 클래스와 리소스를 묶어서 `JAR(Java Archive)`라고 하는 압축 파일로 만들 수 있다.
이 파일은 JVM 위에서 직접 실행되거나 다른 곳에서 사용하는 라이브러리로 제공된다.
직접 실행하는 경우 `main()` 메서드가 필요하고, `MANIFEST.MF` 파일에 실행할 메인 메서드가 있는 클래스를 지정해두어야 한다.

실행 예) `java -jar abc.jar`   
jar는 쉽게 이야기해서 클래스와 관련 리소스를 압축한 단순한 파일이다. 필요한 경우 이 파일을 직접 실행할 수도 있고, 다른 곳에서 라이브러리로 사용할 수도 있다.

### WAR 소개
WAR(Web Application Archive)라는 이름에서 알 수 있듯 WAR 파일은 웹 애플리케이션 서버(WAS)에 배포할 때 사용하는 파일이다.
JAR 파일이 JVM 위에서 실행된다면, WAR는 웹 애플리케이션 서버 위에서 실행된다.  
웹 애플리케이션 서버 위에서 실행되고, HTML 같은 정적 리소스와 클래스 파일을 모두 함께 포함하기 때문에 JAR와 비교해서 구조가 더 복잡하다.

아래의 구조를 지켜야한다.
- `WEB-INF`: 자바 클래스와 라이브러리, 설정 정보를 담아두는 곳으로, 이 외의 영역은 HTML, CSS 같은 정적 리소스가 사용되는 영역이다.
  - `classes`: 실행 클래스 모음
  - `lib`: 라이브러리 모음
  - `web.xml`: 웹 서버 배치 설정 파일(생략 가능)
- `index.html`: 정적 리소스

### WAR 배포
이렇게 생성된 WAR 파일을 톰캣 서버에 실제 배포해보자.
1. 톰캣 서버를 종료한다. 
   - `./shutdown.sh`
2. `톰캣 폴더/webapps` 하위를 모두 삭제한다.
3. 빌드된 `server-0.0.1-SNAPSHOT.war`를 복사한다.
4. 톰캣폴더/webapps` 하위에 붙여넣는다. 
   - `톰캣폴더/webapps/server-0.0.1-SNAPSHOT.war`
5. 이름을 변경한다.
   - `톰캣폴더/webapps/ROOT.war` (ROOT 대문자 잘 지키기)
6. 톰캣 서버를 실행한다.
   - `./startup.sh` 

### 실행 결과 확인
- http://localhost:8080/index.html
- http://localhost:8080/test

실행해보면 `index.html` 정적 파일과 `/test`로 만들어둔 `TestServlet` 모두 잘 동작하는 것을 확인할 수 있다.

> 🍀 참고  
> 진행이 잘 되지 않으면 `톰캣폴더/logs/catalina.out` 로그를 확인하자.

실제 서버에서는 이렇게 사용하면 되지만, 개발 단계에서는 `war` 파일을 만들고, 이것을 서버에 복사해서
배포하는 과정이 번잡하다.  
IntelliJ와 같은 IDE는 이 부분을 편리하게 자동화해준다.

## 톰캣 설정
## 서블릿 컨테이너 초기화
## 스프링 컨테이너 등록
## 스프링 MVC 서블릿 컨테이너 초기화 지원
## 정리