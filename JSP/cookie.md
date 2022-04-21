## Cookie

쿠키는 웹 브라우저가 보관하는 데이터이며, 웹 서버와 웹 브라우저 양쪽에서 생성할 수 있다.

웹 서버는 쿠키를 이용해서 웹 브라우저에 정보를 전송한다. 웹 서버로부터 쿠키를 전달받은 웹 브라우저는 쿠키의 종류에 따라 메모리나 파일에 저장하고 이후 웹 서버에 요청을 보낼 때 쿠키를 함께 전송한다. → 상태 유지

JSP에서 생성하는 쿠키는 웹 서버에서 생성하는 쿠키이다.

### 쿠키의 구성

- 이름 : 하나의 웹 브라우저는 여러 개의 쿠키를 가질 수 있는데, 각 쿠키를 구분할 때 이름을 사용함
- 값 : 쿠키의 이름과 관련된 값
- 유효시간: 쿠키의 유지 기간, 별도의 유효시간이 없으면 웹 브라우저를 종료할 때 쿠기를 함께 삭제함
- 도메인 : 쿠키를 전송할 도메인
- 경로 : 쿠키를 전송할 요청 경로

### 쿠키 생성하기

JSP에서 Cookie 클래스를 통해 쿠키를 생성하고 response 기본 객체로 웹 브라우저에 쿠키 정보를 전송한다.

```
<%@ page contentType = "text/html; charset=utf-8" %>
<%
	Cookie cookie = new Cookie("name", "sd" );
	response.addCookie(cookie);
%>
<html>
<head><title>쿠키생성</title></head>
<body>

<%= cookie.getName() %> 쿠키의 값 = "<%= cookie.getValue() %>"

</body>
</html>
```

<img width="925" alt="c2" src="https://user-images.githubusercontent.com/103729286/164486656-1e7fb47f-f70e-422c-a287-b659f428c024.png">




### 쿠키 값 읽어오기

웹 브라우저가 요청 헤더에 쿠키를 저장해서 보내면 JSP는 request 객체를 통해서 쿠키 값을 읽어온다.

`Cookie[] cookies = request.getCookies();`

### 쿠키 값 변경, 삭제하기

쿠키 값을 변경하려면 같은 이름의 쿠키를 새로 생성해서 응답 데이터로 보내면 된다.

```
<%@ page contentType = "text/html; charset=utf-8" %>
<%@ page import = "java.net.URLEncoder" %>
<%
   Cookie[] cookies = request.getCookies();
   if (cookies != null && cookies.length > 0) { 
      for (int i = 0 ; i < cookies.length ; i++) {
         if (cookies[i].getName().equals("name")) { // 쿠키가 있는지 확인 
            Cookie cookie = new Cookie("name",
               URLEncoder.encode("JSP프로그래밍", "utf-8"));
            response.addCookie(cookie);
         }
      }
   }
%>
<html>
<head><title>값 변경</title></head>
<body>
name 쿠키의 값을 변경합니다.
</body>
</html>
```

쿠키 값을 삭제하려면 `cookie.setMaxAge(0);`으로 유효시간을 0으로 지정해준 후 응답 헤더에 추가한다.

### 쿠키의 도메인

기본적으로 쿠키는 그 쿠키를 생성한 서버에만 전송된다. 같은 도메인을 사용하는 모든 서버에 쿠키를 보내야 할 때는 setDomain() 메소드를 사용한다. ( [naver.com](http://naver.com) 과 blog.naver.com)

setDomain()의 값으로 현재 서버의 도메인 및 상위 도메인만 전달할 수 있다. blog.naver.com에서 JSP 페이지가 실행된다면 .naver.com은 가능하지만 www.naver.com은 불가능하다.

웹 브라우저가 타 도메인으로 지정한 쿠키를 받지 않는데 이유는 보안 문제 때문이다. 웹 브라우저는 현재 서버의 도메인과 다른 도메인에 대한 쿠키 생성은 허용하지 않는다.

### 쿠키의 경로

도메인이 쿠키를 공유할 도메인 범위를 지정한다면, 경로는 쿠키를 공유할 기준 경로를 지정한다. Cookie 클래스의 setPath() 메소드를 사용한다.

```
<%@ page contentType = "text/html; charset=utf-8" %>
<%@ page import = "java.net.URLEncoder" %>
<%
   Cookie cookie1 = new Cookie("path1",
         URLEncoder.encode("경로:/chap09/path1", "utf-8"));
   cookie1.setPath("/chap09/path1");
   response.addCookie(cookie1);

   Cookie cookie2 = new Cookie("path2",
         URLEncoder.encode("경로:", "utf-8"));
   response.addCookie(cookie2);

   Cookie cookie3 = new Cookie("path3",
         URLEncoder.encode("경로:/", "utf-8"));
   cookie3.setPath("/");
   response.addCookie(cookie3);

   Cookie cookie4 = new Cookie("path4",
         URLEncoder.encode("경로:/chap09/path2", "utf-8"));
   cookie4.setPath("/chap09/path2");
   response.addCookie(cookie4);
%>

<html>
<head><title>쿠키 경로 지정</title></head>
<body>

다음과 같이 쿠키를 생성했습니다.<br>
<%= cookie1.getName() %>=<%= cookie1.getValue() %>
[<%= cookie1.getPath() %>]
<br>
<%= cookie2.getName() %>=<%= cookie2.getValue() %>
[<%= cookie2.getPath() %>]
<br>
<%= cookie3.getName() %>=<%= cookie3.getValue() %>
[<%= cookie3.getPath() %>]
<br>
<%= cookie4.getName() %>=<%= cookie4.getValue() %>
[<%= cookie4.getPath() %>]

</body>
</html>

```

http://localhost:8080/chap09/path1 : path1, path2, path3 쿠키가 전송됨( chap09/path1 경로에 JSP 작성)

http://localhost:8080/ : path3 쿠키가 전송됨

http://localhost:8080/chap09/path2 : path3, path4 쿠키가 전송됨


<img width="999" alt="c3" src="https://user-images.githubusercontent.com/103729286/164486744-8b81ac11-1c24-43c8-a1cd-25b58888b4af.png">





### 쿠키의 유효시간

쿠키의 유효시간을 정해 놓으면 웹 브라우저가 종료되어도 유효시간이 지나지 않았으면 쿠키를 삭제하지 않는다.

유효 시간을 지정하려면 setMaxAge() 메소드를 사용하여 초단위로 유효 시간을 지정한다.

아이디 기억하기 및 자동 로그인 기능을 구현할 때 쿠키를 사용한다.

### 쿠키와 헤더

response.addCookie()로 쿠키를 추가하면 실제로 Set-Cookie 헤더를 통해 전달된다. 쿠키는 응답 헤더를 사용해서 웹 브라우저에 전달하기 때문에 쿠키 역시 출력 버퍼가 flush 된 이후에는 새롭게 추가 할 수 없다.

`Set-Cookie: 쿠키이름=쿠키값; Domain=도메인값; Path=경로값; Expires=GMT 형식의 만료일시`



### 쿠키 처리를 위한 유틸리티 클래스

```java
package util;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.Cookie;
import java.util.Map;
import java.net.URLEncoder;
import java.net.URLDecoder;
import java.io.IOException;

public class Cookies {

   private Map<String, Cookie> cookieMap =
         new java.util.HashMap<String, Cookie>();

   public Cookies(HttpServletRequest request) {
      Cookie[] cookies = request.getCookies();
      if (cookies != null) {
         for (int i = 0 ; i < cookies.length ; i++) {
            cookieMap.put(cookies[i].getName(), cookies[i]);
         }
      }
   }

   public Cookie getCookie(String name) {
      return cookieMap.get(name);
   }

   public String getValue(String name) throws IOException {
      Cookie cookie = cookieMap.get(name);
      if (cookie == null) {
         return null;
      }
      return URLDecoder.decode(cookie.getValue(), "utf-8");
   }

   public boolean exists(String name) {
      return cookieMap.get(name) != null;
   }

   public static Cookie createCookie(String name, String value)
   throws IOException {
      return new Cookie(name, URLEncoder.encode(value, "utf-8"));
   }

   public static Cookie createCookie(String name, String value, String path,
      int maxAge) throws IOException {
      Cookie cookie = new Cookie(name, URLEncoder.encode(value, "utf-8"));
      cookie.setPath(path);
      cookie.setMaxAge(maxAge);
      return cookie;
   }

   public static Cookie createCookie(String name, String value, String domain,
         String path, int maxAge) throws IOException {
      Cookie cookie = new Cookie(name, URLEncoder.encode(value, "utf-8"));
      cookie.setDomain(domain);
      cookie.setPath(path);
      cookie.setMaxAge(maxAge);
      return cookie;
   }

}
```

Tomcat 폴더의 webapps/chap09/WEB-INF/src/util/Cookies.java

Tomcat 폴더의 webapps/chap09/WEB-INF/classes/util/Cookies.class

Cookies 클래스를 사용함으로써 쿠키를 사용하는 코드가 간결해지고 가독성이 향상된다.



### 쿠키를 사용한 로그인 상태 유지

1. 로그인에 성공하면 특정 이름을 갖는 쿠키를 생성한다.

```
response.addCookie(
   Cookies.createCookie("AUTH", id, "/", -1)
);
```

2. 해당 쿠키가 존재하면 로그인한 상태라고 판단한다.

```
<%@ page contentType = "text/html; charset=utf-8" %>
<%@ page import = "util.Cookies" %>
<%
   Cookies cookies = new Cookies(request);
%>
<html>
<head><title>로그인여부 검사</title></head>
<body>

<%
   if (cookies.exists("AUTH")) {
%>
아이디 "<%= cookies.getValue("AUTH") %>"로 로그인 한 상태
<%
   } else {
%>
로그인하지 않은 상태
<%
   }
%>
</body>
</html>
```

3. 로그아웃하면 해당 쿠키를 삭제한다.

```
<%@ page contentType = "text/html; charset=utf-8" %>
<%@ page import = "util.Cookies" %>
<%
   response.addCookie(
      Cookies.createCookie("AUTH", "", "/", 0)
   );
%>
<html>
<head><title>로그아웃</title></head>
<body>

로그아웃하였습니다.

</body>
</html>

```

로그인에 성공했음을 나타내는 쿠키를 생성한 이후, 웹 브라우저는 요청을 보낼 때마다 쿠키를 전송한다. 따라서 로그인에 성공할 때 생성되는 쿠키가 존재하는지 확인하면 현재 로그인했는지 여부를 판단할 수 있다.

아이디를 평문 형태로 쿠키 값으로 사용하면 보안에 큰 문제가 생긴다. 웹 브라우저는 자체적으로 개발도구를 제공하고 있는데 이 개발 도구를 사용하면 쿠키 값을 쉽게 변경할 수 있기 때문에, 쿠키 값을 변경해서 서버에 전송할 수 있다. 쿠키에 아이디를 저장할 때는 암호화해서 저장해야 한다.



