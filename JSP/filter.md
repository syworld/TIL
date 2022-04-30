# JSP Filter

## Filter

HTTP 요청과 응답을 변경할 수 있는 재사용 가능한 클래스 

필터는 객체의 형태로 존재하며 클라이언트에서 오는 요청 request와 최종 자원(JSP, servlet 등) 사이에 위치하여 클라이언트의 요청 정보를 알맞게 변경할 수 있다. 또한 최종 자원과 클라이언트로 가는 응답 response 사이에 위치하여 최종 자원의 요청 결과를 변경 또는 취소할 수 있다. 

![Untitled (5)](https://user-images.githubusercontent.com/103729286/166109943-84ee47be-a0e7-4a71-8fba-f90fb52fac38.png)




## 필터의 구현

- `javax.servlet.Filter` : 클라이언트와 최종 자원 사이에 위치하는 필터를 나타내는 객체가 구현해야 하는 인터페이스
- `javax.servlet.ServletRequestWrapper` : 필터가 요청을 변경한 결과를 저장하는 래퍼 클래스
- `javax.servlet.ServletResponseWrapper` : 필터가 응답을 변경하기 위해 사용하는 래퍼 클래스

필터 클래스를 컴파일하려면 서블릿 API가 포함된 jar 파일을 classpath에 추가해줘야 한다.

### 1) Filter 인터페이스

- `public void init(FilterConfig filterConfig) throws ServletException` :
    - 필터를 초기화할 때 호출된다.
- `public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws java.io.IOException, ServletException`
    - 필터 기능을 수행한다. chain을 이용해서 체인의 다음 필터로 처리를 전달할 수 있다.
    - 클라이언트가 요청한 자원이 필터를 거치는 경우 매 요청마다 doFilter() 가 호출된다.
- `public void destroy()`
    - 필터가 웹 컨테이너에서 삭제될 때 호출된다.

### 2) Filter 설정: web.xml

- `<filter>` : 웹 어플리케이션에서 사용할 필터 지정
    - `<init-param>` : 필터를 초기화할 때, init() 호출할 때 전달할 파라미터 설정
- `<filter-mapping>` : 특정 자원에 어떤 필터를 사용할지 지정
    - `<url-pattern>` 과  `<servlet-name>` 사용 가능
    - `<dispatcher>` : 필터 적용 시점 설정
        - `<dispatcher> REQUEST </dispatcher>`  : 클라이언트의 요청인 경우 필터를 적용
        - `<dispatcher> FORWARD </dispatcher>` : forward() 통해서 제어 흐름을 이동하는 경우 필터 적용
        - `<dispatcher> INCLUDE </dispatcher>`  : include() 통해서 포함되는 경우에 필터 적용
        - 웹 브라우저의 요청이 동시에 여러 개의 필터 매핑에 적용되는 경우 web.xml 에 표시한 순서대로 필터를 적용한다.

```
<filter>
    <filter-name>NullParameter</filter-name>
    <filter-class>filter.NullParameterFilter</filter-class>
    <init-param>
        <param-name>parameterNames</param-name>
        <param-value>id,name</param-value>
    </init-param>
</filter>

<filter-mapping>
		<filter-name>NullParameter</filter-name>
		<url-pattern>*.jsp</url-pattern>
</filter-mapping>
```

### 3) Filter 설정: @WebFilter 어노테이션

- `urlPatterns` : 필터를 적용할 URL 패턴 목록 지정
- `servletNames` : 필터를 적용할 서블릿 이름 목록 지정
- `filterName` : 필터 이름 지정
- `initParams` : 초기화 파라미터 목록 지정
- `dispatcherTypes` : 필터 적용 범위 지정. 기본값은 DispatchType.REQUEST

```
@WebFilter(
    filterName = "xsltFilter",
    urlPatterns = {"/xml/*"}
)
public class XSLTFilter implements Filter {

}
```

### 4) 요청 및 응답 래퍼 클래스

- `javax.servlet.ServletRequestWrapper`
- `javax.servlet.ServletResponseWrapper`

이 두 클래스는 각각 HttpServletRequest 인터페이스와 HttpServletResponse 인터페이스에 정의되어 있는 모든 메소드를 이미 구현해놓았다. 

## 필터의 응용

- 사용자 인증
- 캐싱 필터
- 자원 접근에 대한 로깅
- 응답 데이터 변환 (HTML 변환, 응답 헤더 변환, 데이터 암호화 등)
- 공통 기능 실행

Login Filter
```
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package filter;

import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

public class LoginCheckFilter implements Filter {
    public LoginCheckFilter() {
    }

    public void init(FilterConfig config) throws ServletException {
    }

    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest)request;
        HttpSession session = httpRequest.getSession(false);
        boolean login = false;
        if (session != null && session.getAttribute("MEMBER") != null) {
            login = true;
        }

        if (login) {
            chain.doFilter(request, response);
        } else {
            RequestDispatcher dispatcher = request.getRequestDispatcher("/loginForm.jsp");
            dispatcher.forward(request, response);
        }

    }

    public void destroy() {
    }
}

```
