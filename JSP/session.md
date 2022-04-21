## Session

웹 브라우저에 정보를 보관할 때 쿠키를 사용한다면, 세션은 웹 컨테이너에 정보를 보관할 때 사용한다. 세션은 오직 서버에만 생성된다. 웹 컨테이너는 기본적으로 한 웹 브라우저마다 한 세션을 생성한다. 같은 JSP 페이지라도 웹 브라우저에 따라 서로 다른 세션을 사용한다.

![s1](https://user-images.githubusercontent.com/103729286/164487544-aee3ade7-b692-4d9f-aa46-303bc8484696.png)





### 세션 생성하기

JSP에서 세션을 생성하려면 page directive의 session 속성을 “true”로 지정하면 된다.

`<%@ page session = “true” %>`

세션이 생성되면 session 기본 객체를 통해서 세션을 사용할 수 있다. 웹 브라우저가 처음 접속할 때 세션을 생성하면 이후로는 이미 생성된 세션을 사용한다.

### session 기본 객체

세션을 사용한다는 것은 session 기본 객체를 사용한다는 것을 말한다.

- getID() : 세션 id. 웹 서버는 세션 id를 웹 브라우저에게 전달하고, 웹 브라우저는 웹 서버에 연결할 때마다 매번 세션 id를 보내서 웹 서버가 어떤 세션을 사용할지 판단할 수 있게 함
- getCreationTime() : 세션 생성 시간.
- getLastAccessedTime() : 세션 종료 시간.
- 시간은 1970년 1월 1일 이후 흘러간 시간이며 단위는 1/1000초.

```
<%@ page contentType = "text/html; charset=utf-8" %>
<%@ page session = "true" %>
<%@ page import = "java.util.Date" %>
<%@ page import = "java.text.SimpleDateFormat" %>
<%
   Date time = new Date();
   SimpleDateFormat formatter =
     new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
%>
<html>
<head><title>세션정보</title></head>
<body>
세션ID: <%= session.getId() %> <br>
<%
   time.setTime(session.getCreationTime());
%>
세션생성시간: <%= formatter.format(time) %> <br>
<%
   time.setTime(session.getLastAccessedTime());
%>
최근접근시간: <%= formatter.format(time) %>

</body>
</html>

```

<img width="1193" alt="s2" src="https://user-images.githubusercontent.com/103729286/164487574-e0002c39-8e8f-4105-b5f7-15690ace3e5e.png">







한 번 생성된 세션은 지정한 유효 시간 동안 유지된다. request 기본 객체가 하나의 요청을 처리하는 데 사용되는 JSP 페이지 사이에서 공유된다면 session 기본 객체는 웹 브라우저의 여러 요청을 처리하는 JSP 페이지 사이에서 공유된다. 웹 브라우저와 일대일로 관련된 값을 저장할 때에는 쿠키 대신 세션을 사용할 수 있다.

`session.setAttribute("NAME", "세션에 정보 저장");`

`String name = (String)session.getAttribute(”NAME”);`


### Cookie vs Session

쿠키의 이름이나 데이터는 네트워크를 통해 전달되기 때문에 HTTP 프로토콜을 사용하는 경우 중간에 쿠키의 값을 읽어올 수 있다. 세션의 값은 오직 서버에만 저장되기 대문에 쿠키보다 보안에서 앞선다.

세션은 여러 서버에서 공유할 수 없다. 반면에 쿠키는 도메인을 이용해서 쿠키를 여러 도메인 주소에 공유할 수 있다.

### 세션 종료

세션을 종료하면 현재 사용 중인 session 기본 객체를 삭제하고 session 기본 객체에 저장했던 attributes 도 함께 삭제한다. `session.invalidate()` 메소드를 사용해서 세션을 종료한다.

### 세션 유효 시간

세션은 최근 접근 시간을 갖는다. JSP 페이지에서 session 속성값이 true라면 웹 브라우저가 JSP 페이지를 실행할 때마다 session 기본 객체에 접근하게 된다. `session.getLastAccessedTime()` 메소드로 최근에 session 기본 객체에 접근한 시간을 알 수 있다.

세션은 마지막 접근 이후 일정 시간 이내에 다시 세션에 접근하지 않는 경우 자동으로 세션을 종료하는 기능을 갖고 있다.

```
<?xml version="1.0" encoding="UTF-8"?>

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
             http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

   <session-config>
      <session-timeout>60</session-timeout>
   </session-config>
</web-app>
```

WEB-INF/web.xml 파일에서 <session-config> 태그를 사용하여 세션 유효 시간을 지정할 수 있다. 단위는 분이다.

또는 `session.setMaxInactiveInterval(60*60)` 메소드로 초 단위로 유효 시간을 설정할 수 있다.

유효 시간이 없는 상태에서 `session.invalidate()` 메소드가 실행하지 않으면 세션 객체가 계속 메모리에 남게 되어 메모리 부족 현상이 일어날 수 있기 때문에 세션 타임아웃 시간을 지정해줘야 한다.

   
### request.getSession()을 이용한 세션 생성

request 기본 객체의 getSession() 메소드를 통해 HttpSession을 생성할 수 있다. request.getSession()을 이용하면 page 디렉티브에서 session = “false”로 지정한다.

`request.getSession()`: session이 존재하면 해당 session을 리턴하고 존재하지 않으면 새롭게 session을 생성해서 리턴한다. session이 생성된 경우에만 session 객체를 구하고 싶으면 false를 인자로 전달한다.
   

### 세션을 사용한 로그인 상태 유지

1. 로그인에 성공하면 session 기본 객체의 특정 속성에 데이터를 기록한다.

   `session.setAttribute("MEMBERID", id);`

2. 이후로 session 기본 객체의 특정 속성이 존재하면 로그인한 것으로 간주한다.

    ```
    String memberId = (String)session.getAttribute("MEMBERID");
    boolean login = memberId == null ? false : true;
    ```

3. 로그아웃할 경우 session.invalidate() 메소드를 호출하여 세션을 종료한다.

   `session.invalidate();`


### 서블릿 컨텍스트와 세션

세션 id를 보관할 때 사용할 JSESSIONID 쿠키의 경로로 웹 어플리케이션의 컨텍스트 경로로 사용한다. 

만약 웹 어플리케이션의 컨텍스트 경로가 /apple 이면, 세션을 위한 JSESSIONID 쿠키의 경로도 /apple이 된다. 경로가 /apple인 쿠키는 URL이 /apple 경로로 시작하는 경우에만 전송되므로, /apple 웹 어플리케이션에서 생성한 JSESSIONID 쿠키는 /apple 웹 어플리케이션에서만 사용한다.

즉, 같은 브라우저라 하더라도 다른 어플리케이션에서 사용하는 session 기본 객체는 각각 다르다.
