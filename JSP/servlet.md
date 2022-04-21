# Servlet



### 서블릿 기초

JSP 표준이 나오기 전에 만들어진 표준으로 자바로 웹 어플리케이션을 개발할 수 있도록 하기 위해 만들어졌다. 서블릿을 이용하면 자바 클래스를 이용해서 웹 어플리케이션을 개발한다. 


< 서블릿 개발 과정 >
1. 서블릿 규약에 따라 자바 코드를 작성
2. 자바 코드를 컴파일하여 클래스 파일 생성
3. 클래스 파일을 /WEB-INF/classes 폴더에 위치
4. web.xml 파일에 서블릿 클래스를 설정
5. 톰캣 등의 컨테이너 실행
6. 웹 브라우저 실행




### 서블릿 구현

- HttpServlet 클래스 상속받아야 서블릿으로 동작한다.
- HttpServlet 클래스 상속받고 HTTP method에 따라 메소드 재정의해서 구현한다.
- HttpServletRequest, HttpServletResponse 는 JSP의 request 기본객체와 response 기본 객체에 해당된다.
- 재정의한 메소드는 request를 이용해 웹 브라우저의 요청 정보를 읽어오거나 response를 이용해서 응답을 전송할 수 있다.

```
package example;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Date;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class NowServlet extends HttpServlet {

   @Overridea
   protected void doGet(HttpServletRequest request,
   HttpServletResponse response) throws ServletException, IOException {
      response.setContentType("text/html; charset=utf-8");

      PrintWriter out = response.getWriter();
      out.println("<html>");
      out.println("<head><title>현재시간</title></head>");
      out.println("<body>");
      out.println("현재 시간은");
      out.println(new Date());
      out.println("입니다.");
      out.println("</body></html>");
   }

}
```

직접 자바 코드를 작성하고 컴파일하려면 서블릿 API를 classpath에 추가해야한다. 서블릿 관련 jar 파일은 `톰캣/lib/servlet-api.jar` 파일에 포함되어 있고 이 파일을 classpath에 추가하고 컴파일해야한다. 



### **web.xml로 매핑하기** 
(`/WEB-INF/web.xml`)

- 서블릿으로 사용할 클래스
- 서블릿과 URL 매핑

```
<servlet>
   <servlet-name>now</servlet-name>
   <servlet-class>example.NowServlet</servlet-class>
</servlet>

<servlet-mapping>
   <servlet-name>now</servlet-name>
   <url-pattern>/now</url-pattern>
</servlet-mapping>
```

<img width="1091" alt="servlet1" src="https://user-images.githubusercontent.com/103729286/164487915-8dab8fa8-a2f4-46e0-adcf-a039e2687fdc.png">





### 어노테이션으로 매핑하기

서블릿 3.0 부터 `@WebServlet` 어노테이션을 사용하면 web.xml 파일에 따로 등록하지 않아도 서블릿으로 등록된다. 

```html
@Webservlet(urlPatterns={”/now”, "/now2"})
public class NowServlet extends HttpServlet{...}
```

서블릿이 처리해야 할 URL 패턴이 변경될 때마다 자바 소스 코드의 urlPatterns 속성값을 변경하고 다시 컴파일해야한다. 

@WebServlet 어노테이션이 적용된 서블릿을 web.xml 파일에 등록하면? 

→ @WebServlet 어노테이션이 적용된 클래스의 객체가 생성되고, web.xml 파일에서 지정한 클래스의 객체 또한 생성된다. 서로 다른 두 개의 객체가 생성되고 각각 지정한 URL 패턴에 매핑된다. 



### HTTP 구현 메소드

GET 방식: `doGet(HttpRequest request, HttpServletResponse response)`

POST 방식: `doPost(HttpRequest request, HttpServletResponse response)` 


 
 
### 서블릿 로딩

서블릿 컨테이너는 처음 서블릿을 실행할 때 서블릿 객체를 생성한다. 이후 요청이 오면 이전에 생성한 서블릿 객체를 그대로 사용한다. 

![servlet2](https://user-images.githubusercontent.com/103729286/164487960-43d37326-912d-4d5b-a910-69406875fc18.png)




서블릿 로딩: 웹 컨테이너가 서블릿 객체를 생성하고 ServletConfig 파라미터를 갖는 init() 메소드를 호출한다. `init(ServletConfig)`메소드는 파라미터가 없는`init()` 메소드를 호출하므로 초기화가 필요한 서블릿은 `init()` 메소드를 재정의하면 된다. 

초기화 작업은 시간이 오래 걸리므로 웹 컨테이너를 처음 구동하는 시점에 초기화를 진행하는 것이 좋다. 

: `<load-on-startup>1</load-on-startup>` 태그의 값은 로딩 순서를 의미하고 오름차순으로 서블릿을 로딩한다. 



### 초기화 파라미터

- <param-name> : 초기화 파라미터 이름
- <param-value> : 초기화 파라미터 값

```
<servlet>
		<servlet-name>DBCPInit2</servlet-name>
		<servlet-class>jdbc.DBCPInit2</servlet-class>
		<init-param>
			<param-name>jdbcdriver</param-name>
			<param-value>com.mysql.jdbc.Driver</param-value>
		</init-param>
		<init-param>
			<param-name>jdbcUrl</param-name>
			<param-value>
	            jdbc:mysql://localhost:3306/chap14?characterEncoding=utf8
	        </param-value>
		</init-param>
		<init-param>
			<param-name>dbUser</param-name>
			<param-value>james</param-value>
		</init-param>
		<init-param>
			<param-name>dbPass</param-name>
			<param-value>root1234</param-value>
		</init-param>
		<init-param>
			<param-name>poolName</param-name>
			<param-value>chap14</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
</servlet>
```

서블릿 클래스에서 초기화 파라미터에 접근

```
String driverClass = getInitParameter("jdbcdriver");
String jdbcUrl = getInitParameter("jdbcUrl");
String username = getInitParameter("dbUser");
String pw = getInitParameter("dbPass");
String poolName = getInitParameter("poolName");
```

`@WebServlet` 어노테이션으로 매핑한 경우 `initParams` 속성의 값으로 `@WebInitParam` 어노테이션 목록을 전달하면 된다.
