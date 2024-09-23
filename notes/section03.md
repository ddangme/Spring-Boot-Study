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
## 톰캣 설정
## 서블릿 컨테이너 초기화
## 스프링 컨테이너 등록
## 스프링 MVC 서블릿 컨테이너 초기화 지원
## 정리