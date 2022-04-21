# 웹 프로그래밍 기초 


## chp1 들어가며
Tomcat 설치

`sudo ln -s /usr/local/apache-tomcat-8.5.76 /Library/Tomcat`

`sudo chown -R <유저명> /Libraray/Tomcat`

`sudo chmod +x /Library/Tomcat/bin/*.sh`

Tomcat 실행

`sudo /Library/Tomcat/bin/startup.sh`

`sudo /Library/Tomcat/bin/shutdown.sh`

http://localhost:8080 에서 톰캣이 실행된 것을 확인할 수 있다. 


 
## chp2 웹 프로그래밍 기초

### URL 과 웹 페이지
```
https://www.11st.co.kr/html/category/1.html?xzone=ctgrl
```

https: 프로토콜

www.11st.co.kr: 서버 이름

/html/category/1.html: 경로 = 웹 페이지 상세 주소

?xzone=ctgrl : 쿼리 문자열


### 웹 브라우저와 웹 서버
웹 브라우저 → DNS : ip 주소 요청

DNS → 웹 브라우저 : ip 주소 응답

웹브라우저 → 웹 서버: url 요청

웹서버 → 웹브라우저: html 응답

http://localhost/ : 프로토콜이 http인 경우 기본 포트 80으로 연결된다.

톰캣 설치 폴더의 conf/server.xml 에서 기본 포트로 8080 설정하는 부분을 확인할 수 있다.

![jsp1](https://user-images.githubusercontent.com/103729286/164481047-58897ffd-ac14-4738-9a16-695035116ffd.png)






### HTML과 HTTP
웹 서버는 URL에 해당하는 HTML 문서를 전송하고, 웹 브라우저는 HTML 문서로부터 화면을 생성하는 렌더링(rendering)을 한다.


HTTP 요청 규칙과 응답 규칙

- 요청 데이터: 요청 줄(GET /HTTP/1.1) , 헤더(서버가 응답을 생성하는데 참조할 수 있는 정보로, 브라우저의 종류, 언어), 바디(정보 전송 시 파일 등)

- 응답 데이터: 응답 줄(HTTP/1.1 200 OK), 헤더(응답에대한 정보, 어떤 데이터인지, 길이 등), 바디(웹 브라우저가 요청한 자원의 내용, HTML 문서나 파일 데이터 등)

웹 서버는 웹 브라우저가 요청한 경로를 분석한 뒤, 경로에 해당하는 파일을 읽어와 응답 데이터로 전송한다.


### 정적 자원과 동적 자원
URL에 해당하는 자원의 고정 유무


### 웹 프로그래밍과 JSP
웹 서버가 네트워크 처리, HTTP 헤더, 파일 입출력 처리를 하며, 웹 서버가 실행하는 프로그램을 작성하는 것이 웹 프로그래밍이다. 웹 서버의 종류에 따라 웹 프로그래밍 기술이 달라진다.

JSP = JavaServer Pages. 동적 페이지를 작성하는데 사용되는 자바의 표준 기술로서 HTML, XML, JSON, 바이너리 파일 등의 응답을 생성하는데 필요한 기능을 제공하고 있다.

JSP를 이용해서 만든 프로그램을 실행하려면 Tomcat과 같은 서버 프로그램이 필요하다. 단순 HTML이나 이미지를 제공하는 것과 달리 웹을 위한 연결, 프로그래밍 언어, 데이터베이스 연동과 같이 어플리케이션을 구현하는데 필요하는 기능을 제공하는데이를 WAS라고 부른다.

웹 브라우저 → (HTTP 요청) → WAS → JSP 실행


### JSP 실행하기
Tomcat 폴더 경로의 webapps 폴더에 jsp 파일을 작성한다.

![jsp2](https://user-images.githubusercontent.com/103729286/164481136-c78a5e49-455b-4c2b-9a19-d0b79afe31b2.png)



time.jsp 는 동적 자원에 해당된다. 웹 브라우저에 주소를 입력할 때마다 톰캣이 JSP를 매번 실행한다. 

![jsp3](https://user-images.githubusercontent.com/103729286/164481149-acbf3d73-c6fb-458e-a294-3eca831ed043.png)




## chp3 JSP로 시작하는 웹 프로그래밍

### JSP에서 HTML 문서 생성하는 기본 코드 구조
설정 부분
- `<%@ page contentType="text/html; charset=UTF-8" %>`
- JSP 페이지가 생성하는 문서의 타입, 사용할 커스텀 태그, 사용할 자바 클래스 지정 등

생성 부분


### JSP 구성 요소
- Directive
    - JSP 페이지에 대한 설정 정보 지정
    - JSP가 제공하는 디렉티브에는 page, taglib, include가 있다.
- Script ( Scriptlet, Expression, Declaration)
    - 문서의 내용을 동적으로 생성하기 위해 사용된다.
    - Expression : 값을 출력
    - Scriptlet: 자바 코드를 실행
    - Declaration: 자바 메서드를 생성
- Expression Language
- Implicit Object
    - request, session, response 기본 객체를 주로 사용한다.
- Static Data
- Action Tag
- Custom Tag and JSTL


### Page Directive
JSP 페이지가 어떤 문서를 생성하는지, 어떤 자바 클래스를 사용하는지, 세션에 참여하는지, 출력 버퍼의 존재 여부 등 JSP 페이지를 실행하는 데 필요한 정보를 입력할 수 있다.

- contentType : JSP가 생성할 문서의 MIME 타입 
  `<%@ page contentType="text/html; charset=UTF-8" %>`

- import : `<%@ page import="java.util.Date" %>`

- timeDirectiveWhitespaces :  줄바꿈 공백 문자 제거

  `<%@ page trimDirectiveWhitespaces="true" %>`

- pageEncoding : 파일이 BOM으로 시작하지 않을 경우 pageEncoding 속성을 검색하고 contentType 속성읜 charset의 값을 검색한다. pageEncoding = ‘utf-8’ (JSP 파일), charset=’euc-kr’ (응답 결과)로 다르게 설정할 수 있다.


### 스크립트 요소

- **Scriptlet**

  <% %>
  JSP에서 자바 코드를 실행할 때 사용하는 코드 블록

    ```
    <body>
    <%
       int sum = 0;
       for (int i = 1 ; i <= 10 ; i++) {
          sum = sum + i;
       }
    %>
    </body>
    ```

- **Expression**

  `1 부터 10까지의 합은 <%= sum %> 입니다.`

- **Declaration**: JSP의 Scriptlet, Expression에서 사용하는 method 작성 부분

    ```
    <%@ page contentType = "text/html; charset=utf-8" %>
    <%!
       public int multiply(int a , int b) {
          int c = a * b;
          return c;
       }
    %>
    <html>
    <head><title>선언부를 사용한 두 정수값의 곱</title></head>
    <body>
    
    10 * 25 = <%= multiply(10, 25) %>
    
    </body>
    </html>
    
    ```

    

### request 기본 객체
웹 브라우저에 웹 사이트의 주소를 입력하면, 웹 브러우저는 해당 웹 서버에 연결한 후 요청 정보를 전송하는데, 이 요청 정보를 제공하는 역할을 한다.

- 클라이언트 및 서버 정보
    - getRemoteAddr() : 웹 서버에 연결한 클라이언트의 IP 주소
    - getContentLength() : 클라이언트가 전송한 요청 정보의 길이
    - getServerPort() : 서버가 실행중인 포트 번호
    ```
    <%@ page contentType = "text/html; charset=utf-8" %>
    <html>
    <head><title>클라이언트 및 서버 정보</title></head>
    <body>
    
    클라이언트IP = <%= request.getRemoteAddr() %> <br>
    요청정보길이 = <%= request.getContentLength() %> <br>
    요청정보 인코딩 = <%= request.getCharacterEncoding() %> <br>
    요청정보 컨텐츠타입 = <%= request.getContentType() %> <br>
    요청정보 프로토콜 = <%= request.getProtocol() %> <br>
    요청정보 전송방식 = <%= request.getMethod() %> <br>
    요청 URI = <%= request.getRequestURI() %> <br>
    컨텍스트 경로 = <%= request.getContextPath() %> <br>
    서버이름 = <%= request.getServerName() %> <br>
    서버포트 = <%= request.getServerPort() %> <br>
    
    </body>
    </html>
    ```
    ![jsp4](https://user-images.githubusercontent.com/103729286/164481233-3f739fd7-5137-43bc-b0fe-38a659edccbd.png)

    



- 클라이언트가 전송한 요청 파라미터
    - HTML Form
    - request.getParameter(String name) : 이름이 name인 파라미터 값
    - request.getParameterNames(): 웹 브라우저가 전송한 파라미터의 이름 목록
    - GET, POST 방식 전송
        - RFC 2396 규약 : 공백 문자는 ‘+’, 알파벳과 숫자는 그대로, 특수문자들은 %HH 형태로 표시
        - GET은 HTTP URL에 파라미터가 함께 전송됨
        - POST는 HTTP 데이터 영역을 통해 파라미터가 전송됨
    - POST 방식으로 전송 시 인코딩 결정 규칙
        - 웹 브라우저에서 인코딩 → 서버에서 디코딩: 동일한 charset 사용해야 함
        - request.setCharacterEncoding() 메소드로 요청 파라미터의 값을 디코딩한다.
    - GET 방식으로 파라미터 전송 시 인코딩 결정 규칙
        - `<a>` 태그의 링크 태그에 쿼리 문자열 추가 - 웹 페이지 인코딩 사용
        - HTML Form method 속성값을 ‘GET’으로 지정해서 폼 전송 - 웹 페이지 인코딩 사용
        - 웹 브라우저 주소에 직접 쿼리 문자열을 포함하는 URL 입력 - 웹 브라우저마다 다름 ( Chorme: UTF-8, Internet Explorer: MS949)
        - WAS마다 GET 방식의 파라미터 값을 읽어올 때 사용하는 charset이 다름
    - Tomcat에서 GET 방식 파라미터 인코딩 처리
        - Tomcat 8.0 의 기본 charset = UTF-8
        - 별도로 지정하려면, Tomcat 설치 폴더의 server.xml 파일에서 <Connector> 의 useBodyEncodingForURI 속성을 true로 지정하면 GET 파라미터도 request.setCharacterEncoding() 메서드로 지정한 charset을 사용한다.

- 클라이언트가 전송한 요청 헤더( 웹 브라우저 종류, OS, 쿠키 정보)

    ```
    <%@ page contentType = "text/html; charset=utf-8" %>
    <%@ page import = "java.util.Enumeration" %>
    <html>
    <head><title>헤더 목록 출력</title></head>
    <body>
    <%
       Enumeration headerEnum = request.getHeaderNames();
       while(headerEnum.hasMoreElements()) {
          String headerName = (String)headerEnum.nextElement();
          String headerValue = request.getHeader(headerName);
    %>
    <%= headerName %> = <%= headerValue %> <br>
    <%
       }
    %>
    
    </body>
    </html>
    
    ```
    <img width="865" alt="jsp5" src="https://user-images.githubusercontent.com/103729286/164481286-50c21352-0f2f-41ae-acd5-29e8c2c0b396.png">

    

     


### response 기본 객체
웹 브라우저에 보내는 응답 정보를 담으며, 헤더 정보 입력, 리다이렉트 기능이 있다.

- 웹 브라우저에 헤더 정보 전송
    - addHeader(String name, String value), setDateHeader(String name, long date) 등
- 웹 브라우저 캐시 제어를 위한 응답 헤더 입력

  HTTP 1.1에서 지원하는 헤더

    - response.setHeader(”Cache-Control” , “no-cache”); 웹 브라우저가 응답 결과를 캐시하지 않음
    - response.addHeader(”Cache-Control” , “no-store”); 응답 결과가 캐시 저장소 자체에 보관되지 않음

  HTTP 1.0 에서 지원하는 헤더

    - response.setHeader(”Pragma”, “no-cache”); 웹 브라우저가 응답 결과를 캐시하지 않음
    - response.setDateHeader(”Expires”, 1L); 응답 결과의 만료일 지정
- 리다이렉트를 이용해서 페이지 이동
    - 웹 서버에서 웹 브라우저에게 어떤 페이지로 이동하라고 지정
    - response.setRedirect(String location);

    ```
    <%@ page pageEncoding="utf-8" %>
    <%@ page import = "java.net.URLEncoder" %>
    <%
       String value = "자바";
       String encodedValue = URLEncoder.encode(value, "utf-8");
       response.sendRedirect("/chap03/index.jsp?name=" + encodedValue);
    %>
    
    ```
     
    

### JSP 주석
<%— 주석입니다 —%> 
 



## chp4 필수 이해 요소

### JSP 처리 과정
![jsp6](https://user-images.githubusercontent.com/103729286/164481344-e3fe916f-46c5-4cec-881c-def790887e0f.png)


WAS에서 JSP 페이지를 요청할 때는 JSP를 직접 실행하는 것이 아닌, JSP를 자바 소스 코드로 변환한 뒤 컴파일해서 생성한 서블릿을 실행하는 것이다.

1.2 translation 단계

1.3 compile 단계

Tomcat은 work 폴더에 JSP를 변환한 자바 소스 코드와 서블릿 클래스를 생성한다.  JSP를 실행한다는 것은 JSP 페이지를 컴파일한 서블릿 클래스를 실행한다는 말이다.

![jsp7](https://user-images.githubusercontent.com/103729286/164481452-68f96a37-6b7f-4386-b972-481cfbf1054f.png)

 
 

### 출력 버퍼와 응답
JSP 페이지는 응답 결과를 출력 버퍼에 저장했다가 한 번에 웹 브라우저에 전송한다.
  
![jsp8](https://user-images.githubusercontent.com/103729286/164481459-ff4a3ff8-35e4-4731-b091-03ef7c895cfa.png)




`<%@ page buffer="1kb">`

page 디렉티브에서 kb 단위로 buffer 속성을 제공한다. buffer 속성을 지정하지 않으면 최소한 8KB 이상의 크기를 갖는 버퍼를 사용하도록 규정하고 있다.

`<%@ page buffer="none" %>`

- `<jsp:forward>` 기능 사용 불가
- 웹 브라우저로 바로 전송됨


4KB 이상의 데이터가 생성되는 jsp에서 `<%@ page buffer="1kb" autoFlush="false" %>` 지정할 경우

autoflash = “true” 버퍼가 다 차면 버퍼를 flush하고 계속해서 작업 진행

autoflash = “false” 버퍼가 다 차면 exception 발생시키고 작업 중단

![jsp9](https://user-images.githubusercontent.com/103729286/164481516-45600e9b-d8a5-4a8d-97f9-cfa6dc0989ba.png)


 


### 웹 어플리케이션 폴더 구성과 URL 매핑

웹 어플리케이션/WEB-INF : 웹 어플리케이션 설정 정보를 담고 있는 web.xml

웹 어플리케이션/WEB-INF/classes : 웹 어플리케이션에서 사용하는 클래스 파일

웹 어플리케이션/WEB-INF/lib : 웹 어플리케이션에서 사용하는 jar 파일

웹 어플리케이션의 폴더는 Tomcat의 webapps directory에 위치한 폴더들을 의미한다.
 
![jsp10](https://user-images.githubusercontent.com/103729286/164481565-0b0a028b-1d81-4225-8e0f-e6577839be99.png)


 

```
Tomcat/webapps/<컨텍스트 경로> → http://host:port/<컨텍스트 경로>

Tomcat/webapps/ROOT → http://host:port
```

URL의 첫 번째 경로와 일치하는 컨텍스트 경로를 가진 웹 어플리케이션이 존재하지 않으면 루트 웹 어플리케이션이 요청을 처리한다.

request 기본 객체는 컨텍스트 경로를 제공하는 메소드를 정의함 : request.getContextPath()

```
<%@ page contentType = "text/html; charset=utf-8" %>
<html>
<head><title>웹 어플리케이션 경로 구하기</title></head>
<body>

웹 어플리케이션 컨텍스트 경로: "<%= request.getContextPath() %>"

</body>
</html>

```
<img width="865" alt="jsp11" src="https://user-images.githubusercontent.com/103729286/164481612-7b503e50-16e0-4bfe-baee-14c1f2ba00e7.png">
<img width="865" alt="jsp12" src="https://user-images.githubusercontent.com/103729286/164481625-528ddb42-50a6-4bed-a988-cf4342071683.png">


 
 
### 웹 어플리케이션 배포

- FTP와 같은 파일 전송 기능을 이용해 파일을 직접 복사
- Tomcat에 war(Web Application Archive) 파일 배포
    - `jar cvf chap04.war *`
    - chap04.war 파일이 생성되면  실 서버의 Tomcat/webapps/ 경로에 복사하면 배포가 된다.
