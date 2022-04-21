# JDBC

### JSP에서 JDBC 프로그래밍하기

자바에서 데이터베이스 프로그래밍을 할 때 JDBC API를 사용한다. DMBS 종류에 상관없이 하나의 JDBC API를 사용해서 데이터베이스 작업을 처리할 수 있다. 각각의 DBMS는 자신에게 알맞는 JDBC 드라이버를 제공하고 잇다. JDBC 드라이버는 클래스 형태로 존재하며 일반적으로 jar 파일로 제공된다.

![db3](https://user-images.githubusercontent.com/103729286/164483564-5b66493a-2904-431b-b527-aa8db134de67.png)




### JDBC 프로그래밍의 코딩 스타일

1. **JDBC 드라이버 로딩**

   JDBC 드라이버에 해당하는 클래스들은 Class.forName() 메소드를 통해서 로딩될 때 자동으로 JDBC 드라이버로 등록한다.

   `jdbc:mysql://localhost:3306/chap14?useUnicode=true&characterEncoding=utf8`

   MySQL JDBC 드라이버가 MySQL 서버와 데이터를 주고받을 때 사용하는 캐릭터 셋을 올바르게 지정하지 않으면 잘못된 값으로 데이터베이스에 저장될 수 있다.
   매번 JDBC를 로딩하도록 구현하지 않으며, 웹 어플리케이션을 구동할 때 한번만 로딩하도록 구현하면 된다.

2. **데이터베이스 커넥션**

   `conn = DriverManager.getConnection(jdbcDriver, dbUser, dbPass);`

   Connection을 구하지 못하면 SQLException이 발생한다.

3. **쿼리 실행을 위한 Statement 객체 생성**

   `stmt = conn.createStatement();`

   Connection 객체로부터 Statement 생성하고 쿼리를 실행할 수 있다.

4. **쿼리 실행**

   `ResultSet executeQuery(String query)` : SELECT 쿼리 실행

   `int executeUpdate(String query)` : INSERT, UPDATE, DELETE 쿼리 실행

   `rs = stmt.executeQuery(``"select * from MEMBER order by MEMBERID");`

5. **쿼리 실행 결과 사용**

   `while(rs.next())`

   ResultSet은 SELECT 쿼리 결과를 저장하며 커서를 통해 각 행의 데이터에 접근한다.
   
   
   ![db4](https://user-images.githubusercontent.com/103729286/164483622-65d05397-6757-4d67-8a00-66cd3772783f.png)

   
   
6. **Statement 종료**

    `if (rs != null) try { rs.close(); } catch(SQLException ex) {}`
    
    `if (stmt != null) try { stmt.close(); } catch(SQLException ex) {}`

7. **데이터베이스 커넥션 종료**

   `if (conn != null) try { conn.close(); } catch(SQLException ex) {}`

   finally 블록에서 Connection 자원을 반납한다. `DriverManager.getConnection()` 메소드가 exception 발생시킬 경우 커넥션 객체가 할당되지 않으므로 체크한다.

	```
	<%@ page contentType = "text/html; charset=utf-8" %>
	<%@ page import = "java.sql.DriverManager" %>
	<%@ page import = "java.sql.Connection" %>
	<%@ page import = "java.sql.Statement" %>
	<%@ page import = "java.sql.ResultSet" %>
	<%@ page import = "java.sql.SQLException" %>

	<html>
	<head><title>회원 목록</title></head>
	<body>

	MEMBER 테이블의 내용
	<table width="100%" border="1">
	<tr>
	   <td>이름</td><td>아이디</td><td>이메일</td>
	</tr>
	<%
	   // 1. JDBC 드라이버 로딩
	   Class.forName("com.mysql.jdbc.Driver");

	   Connection conn = null;
	   Statement stmt = null;
	   ResultSet rs = null;

	   try {
	      String jdbcDriver = "jdbc:mysql://localhost:3306/chap14?" +
			     "useUnicode=true&characterEncoding=utf8";
	      String dbUser = "james";
	      String dbPass = "root1234";

	      String query = "select * from MEMBER order by MEMBERID";

	      // 2. 데이터베이스 커넥션 생성
	      conn = DriverManager.getConnection(jdbcDriver, dbUser, dbPass);

	      // 3. Statement 생성
	      stmt = conn.createStatement();

	      // 4. 쿼리 실행
	      rs = stmt.executeQuery(query);

	      // 5. 쿼리 실행 결과 출력
	      while(rs.next()) {
	%>
	<tr>
	   <td><%= rs.getString("NAME") %></td>
	   <td><%= rs.getString("MEMBERID") %></td>
	   <td><%= rs.getString("EMAIL") %></td>
	</tr>
	<%
	      }
	   } catch(SQLException ex) {
	      out.println(ex.getMessage());
	      ex.printStackTrace();
	   } finally {
	      // 6. 사용한 Statement 종료
	      if (rs != null) try { rs.close(); } catch(SQLException ex) {}
	      if (stmt != null) try { stmt.close(); } catch(SQLException ex) {}

	      // 7. 커넥션 종료
	      if (conn != null) try { conn.close(); } catch(SQLException ex) {}
	   }
	%>
	</table>

	</body>
	</html>

	```


### ResultSet에서 LONG VARCHAR 타입 읽어오기

ResultSet에서 LONG VARCHAR 타입의 데이터를 읽어로려면 `getCharacterStream()` 메소드를 사용해야 한다.

MySQL에서는 LONG VARCHAR를 MEDIUMTEXT로 표시하고 있으며 LONGTEXT, MEDIUMTEXT, TINYTEXT, TEXT 4가지의 텍스트 타입을 지원한다. MySQL를 포함한 다수의 JDBC 드라이버 getString() 메소드를 사용해서  LONG VARCHAR 타입을 읽어올 수 있다.


```
<%@ page contentType = "text/html; charset=utf-8" %>

<%@ page import = "java.sql.DriverManager" %>
<%@ page import = "java.sql.Connection" %>
<%@ page import = "java.sql.Statement" %>
<%@ page import = "java.sql.ResultSet" %>
<%@ page import = "java.sql.SQLException" %>
<%@ page import = "java.io.Reader" %>
<%@ page import = "java.io.IOException" %>

<%
   String memberID = request.getParameter("memberID");
%>
<html>
<head><title>회원 정보</title></head>
<body>

<%
   Class.forName("com.mysql.jdbc.Driver");

   Connection conn = null;
   Statement stmt = null;
   ResultSet rs = null;

   try {
      String jdbcDriver = "jdbc:mysql://localhost:3306/chap14?" +
                     "useUnicode=true&characterEncoding=utf8";
      String dbUser = "james";
      String dbPass = "root1234";
      String query =  "select * from MEMBER_HISTORY "+
                  "where MEMBERID = '"+memberID+"'";

      conn = DriverManager.getConnection(jdbcDriver, dbUser, dbPass);
      stmt = conn.createStatement();

      rs = stmt.executeQuery(query);
      if(rs.next()) {
%>
<table border="1">
<tr>
   <td>아이디</td><td><%= memberID %></td>
</tr>
<tr>
   <td>히스토리</td>
   <td>
<%
         String history = null; // 스트림으로 읽어온 데이터를 저장
         Reader reader = null; // LONG VARCHAR 데이터 읽어올 스트림 
         try {
            reader = rs.getCharacterStream("HISTORY"); // 스트림 읽어옴 

            if (reader != null) {
		// 스트림에서 읽어온 데이터를 저장할 버퍼를 생성
               StringBuffer buff = new StringBuffer();
               char[] ch = new char[512];
               int len = -1;

               while( (len = reader.read(ch)) != -1) {
                  buff.append(ch, 0, len);
               }
		// 버퍼에 저장한 내용을 String으로 변환 
               history = buff.toString();
            }
         } catch(IOException ex) {
            out.println("익셉션 발생:"+ex.getMessage());
         } finally {
	    // Reader 종료 
            if (reader != null) try { reader.close(); } catch(IOException ex) {}
         }
%>
   <%= history %>
   </td>
</tr>
</table>
<%
      } else {
%>
<%= memberID %> 회원의 히스토리가 없습니다.
<%
      }
   } catch(SQLException ex) {
%>
에러 발생: <%= ex.getMessage() %>
<%
   } finally {
      if (rs != null) try { rs.close(); } catch(SQLException ex) {}
      if (stmt != null) try { stmt.close(); } catch(SQLException ex) {}
      if (conn != null) try { conn.close(); } catch(SQLException ex) {}
   }
%>

</body>
</html>

```


### PreparedStatement를 사용한 쿼리 처리

`java.sql.PreparedStatement` 는 `java.sql.Statement`와 동일한 기능을 제공한다.

1. Connection.prepareStatement() 메서드를 사용하여 PreparedStatement 생성
2. PreparedStatement의 set 메서드를 사용하여 필요한 값 지정
3. PreparedStatement의 executeQuery() 또는 executeUpdate() 메소드를 사용하여 쿼리를 실행
4. finally 블록에서 사용한 PreparedStatement를 닫음 - close() 메소드 실행

**PreparedStatement에서 LONG VARCHAR 타입 값 지정**

`setCharacterStream(int index, Reader reader, int length)` 메소드는 Reader로부터 length 글자 수만큼 데이터를 읽어와 저장한다.

**PreparedStatement 쿼리를 사용하는 이유**

- 값 변환 자동

  쿼리 실행시 작은따옴표가 포함된 것을 처리해준다.

- 간결한 코드

  TIMESTAMP, DATE, TIME 타입의 경우는 DBMS마다 날짜와 시간을 표현하는 방식이 다르기 때문에 Statement를 이용해서 직접 쿼리에 값을 지정하면 DBMS마다 코드가 달라진다. 반면에 PreparedStatement 는 동일한 코드를 사용한다.

  UPDATE 쿼리 또한 간결하다.

- SQL Injection 대응

  PreparedStatement에서 바인딩 변수를 사용할 때 쿼리의 문법 처리과정이 미리 선 수행되기 때문에 바인딩 데이터는 SQL문법적인 의미를 가질 수 없다.


	```
	conn = DriverManager.getConnection(jdbcDriver, dbUser, dbPass);
	conn.setAutoCommit(false);

	pstmtItem = conn.prepareStatement("insert into ITEM values (?, ?)");
	pstmtItem.setInt(1, id);
	pstmtItem.setString(2, "상품 이름 " + id);
	pstmtItem.executeUpdate();
	```

### 웹 어플리케이션 구동 시 JDBC 드라이버 로딩하기

톰캣과 같은 웹 컨테이너가 시작될 때 자동으로 JDBC 드라이버를 로딩하도록 지정하면 JSP 페이지에서 매번 JDBC 드라이버를 로딩할 필요가 없다.

```
package jdbc;

import javax.servlet.http.HttpServlet;
import javax.servlet.ServletConfig;
import javax.servlet.ServletException;

public class MySQLDriverLoader extends HttpServlet {
		// 서블릿 초기화할 때 최초에 한 번 실행되는 init() 
    public void init(ServletConfig config) throws ServletException {
        try {
            Class.forName("com.mysql.jdbc.Driver"); //JDBC 드라이버 로딩 
        } catch(Exception ex) {
            throw new ServletException(ex);
        }
    }
}
```



클래스 파일 생성

`set CLASSPATH=/apache-tomcat-8.5.76/lib/servelet-api.jar`

`cd /apache-tomcat-8.5.76/webapps/chap14/WEB-INF/`

`mkdir classes`

`javac -d classes src/jdbc/MySQLDriverLoader.java`


`/apache-tomcat-8.5.76/webapps/chap14/web.xml`에 <servlet> 태그를 추가하여 웹 어플리케이션이 시작될 때 자동으로 MySQLDriverLoader 서블릿 클래스를 실행하도록 설정한다.

→ MySQLDriverLoader 클래스의 init() 메소드 실행
	

```
<servlet>
   <servlet-name>mysqlDriverLoader</servlet-name>
   <servlet-class>jdbc.MySQLDriverLoader</servlet-class>
   <load-on-startup>1</load-on-startup>
</servlet>
```



### Transaction

DBMS는 transaction을 이용해서 두 개 이상의 쿼리를 마치 한 개의 쿼리처럼 처리할 수 있도록 한다.

트랜잭션이 시작되면 이후로 실행되는 쿼리 결과는 DBMS에 곧바로 반영되지 않고 임시로 보관된다. 이후 트랜잭션 commit 하면 임시 보관된 모든 쿼리 결과를 실제 데이터에 반영한다. 트랜잭션 rollback하면 모든 쿼리 결과가 DB에 반영되지 않는다.

트랜잭션 구현 방법

- JDBC auto commit 모드를 false - 단일 데이터베이스

    ```
    // 트랜잭션 시작
    conn.setAutoCommit(false);
    // 쿼리 실행
    pstmtItem = conn.prepareStatement("insert into ITEM values (?, ?)");
    pstmtItem.setInt(1, id);
    pstmtItem.setString(2, "상품 이름 " + id);
    pstmtItem.executeUpdate();
    
    if (request.getParameter("error") != null) {
       throw new Exception("의도적 익셉션 발생");
    }
    
    pstmtDetail = conn.prepareStatement(
       "insert into ITEM_DETAIL values (?, ?)");
    pstmtDetail.setInt(1, id);
    pstmtDetail.setString(2, "상세 설명 " + id);
    pstmtDetail.executeUpdate();
    
    // 트랜잭션 커밋 
    conn.commit();
    ```

- Java Transaction API - 두 개 이상의 데이터베이스

	
	
	
### Connection Pool

데이터베이스와 연결된 커넥션을 미리 만들어서 pool 속에 저장해 두고 있다가 필요할 때 커넥션을 풀에서 가져다 쓰고 다시 풀에 반환하는 기법이다.

- pool에 미리 커넥션이 생성되어 있기 때문에 커넥션을 생성하는 데 드는 연결 시간을 줄일 수 있다.
- 커넥션을 계속해서 재사용하기 때문에 생성되는 커넥션 수가 일정하게 유지된다.

→ 어플리케이션 실행 속도가 빨라지고, 한 번에 생성될 수 있는 커넥션 수를 제어하기 때문에 동시 접속자 수가 몰려도 웹 어플리케이션이 쉽게 다운되지 않는다.

	
	
	
### DBCP API 이용해서 Connection Pool 사용하기

1. DBCP 관련 jar 파일과 JDBC 드라이버 jar 파일 설치하기 (WEB-INF/lib/ 경로에 복사)
2. 커넥션 풀 초기화
    1. 유효 커넥션 검사 주기
    2. 풀에 보관 중인 커넥션이 유효한지 검사할지 여부
    3. 커넥션 최소 개수
    4. 커넥션 최대 개수
3. 커넥션 풀에서 커넥션 사용
    `jdbc:apache:commons:dbcp:풀 이름` : (아래 코드에선 풀이름을 chap14로 등록함)


	```
	package jdbc;

	import java.sql.DriverManager;
	import java.util.Properties;

	import javax.servlet.ServletException;
	import javax.servlet.http.HttpServlet;

	import org.apache.commons.dbcp2.BasicDataSource;
	import org.apache.commons.dbcp2.BasicDataSourceFactory;
	import org.apache.commons.dbcp2.ConnectionFactory;
	import org.apache.commons.dbcp2.DriverManagerConnectionFactory;
	import org.apache.commons.dbcp2.PoolableConnection;
	import org.apache.commons.dbcp2.PoolableConnectionFactory;
	import org.apache.commons.dbcp2.PoolingDriver;
	import org.apache.commons.pool2.ObjectPool;
	import org.apache.commons.pool2.impl.GenericObjectPool;
	import org.apache.commons.pool2.impl.GenericObjectPoolConfig;

	public class DBCPInit extends HttpServlet {

	   @Override
	   public void init() throws ServletException {
	      loadJDBCDriver();
	      initConnectionPool();
	   }

	   private void loadJDBCDriver() {
	      try {
		 Class.forName("com.mysql.jdbc.Driver"); // 커넥션 풀이 내부에서 사용할 JDBC 드라이버를 로딩 
	      } catch (ClassNotFoundException ex) {
		 throw new RuntimeException("fail to load JDBC Driver", ex);
	      }
	   }

	   private void initConnectionPool() {
	      try {
		 String jdbcUrl =
		       "jdbc:mysql://localhost:3306/chap14?" +
		       "useUnicode=true&characterEncoding=utf8";
		 String username = "james";
		 String pw = "root1234";

		// 커넥션 풀이 새로운 커넥션을 생성할 때 사용할 커넥션 팩토리 생성. MySQL에 연결할 때 사용할 JDBC URL, DB 계정, 암호를 생성자로 지정  
		 ConnectionFactory connFactory =
		       new DriverManagerConnectionFactory(jdbcUrl, username, pw);

		// PoolableConnectionFactory는 내부적으로 실제 커넥션을 담고 있으며 커넥션 풀을 관리하는 데 필요한 기능을 제공함 
		 PoolableConnectionFactory poolableConnFactory =
		       new PoolableConnectionFactory(connFactory, null);

		// 커넥션이 유효한지 여부 검사 
		 poolableConnFactory.setValidationQuery("select 1");

		// 커넥션 풀 설정 정보 생성 
		 GenericObjectPoolConfig poolConfig = new GenericObjectPoolConfig();
		 poolConfig.setTimeBetweenEvictionRunsMillis(1000L * 60L * 5L);
		 poolConfig.setTestWhileIdle(true);
		 poolConfig.setMinIdle(4);
		 poolConfig.setMaxTotal(50);

		// 커넥션 풀 생성
		 GenericObjectPool<PoolableConnection> connectionPool =
		       new GenericObjectPool<>(poolableConnFactory, poolConfig);
		 poolableConnFactory.setPool(connectionPool);
		// 커넥션 풀을 제공하는 JDBC 드라이버 등록 
		 Class.forName("org.apache.commons.dbcp2.PoolingDriver");
		 PoolingDriver driver =
		       (PoolingDriver) DriverManager.getDriver("jdbc:apache:commons:dbcp:");
		 driver.registerPool("chap14", connectionPool);
	      } catch (Exception e) {
		 throw new RuntimeException(e);
	      }
	   }
	}

	```
	
	

**web.xml에 서블릿을 자동으로 실행하도록 설정**

```
<servlet>
   <servlet-name>DBCPInit</servlet-name>
   <servlet-class>jdbc.DBCPInit</servlet-class>
   <load-on-startup>1</load-on-startup>
</servlet>
```

	
	
**커넥션 풀로부터 커넥션 사용**

```
<%@ page contentType = "text/html; charset=utf-8" %>
<%@ page import = "java.sql.DriverManager" %>
<%@ page import = "java.sql.Connection" %>
<%@ page import = "java.sql.Statement" %>
<%@ page import = "java.sql.ResultSet" %>
<%@ page import = "java.sql.SQLException" %>
<html>
<head><title>회원 목록</title></head>
<body>

MEMBER 테이블의 내용
<table width="100%" border="1">
<tr>
   <td>이름</td><td>아이디</td><td>이메일</td>
</tr>
<%

   Connection conn = null;
   Statement stmt = null;
   ResultSet rs = null;

   try {
      String jdbcDriver = "jdbc:apache:commons:dbcp:chap14";
      String query = "select * from MEMBER order by MEMBERID";
      conn = DriverManager.getConnection(jdbcDriver);
      stmt = conn.createStatement();
      rs = stmt.executeQuery(query);
      while(rs.next()) {
%>
<tr>
   <td><%= rs.getString("NAME") %></td>
   <td><%= rs.getString("MEMBERID") %></td>
   <td><%= rs.getString("EMAIL") %></td>
</tr>
<%
      }
   } finally {
      if (rs != null) try { rs.close(); } catch(SQLException ex) {}
      if (stmt != null) try { stmt.close(); } catch(SQLException ex) {}

		// 커넥션 풀에서 구한 Connection의 close 메소드 호출하면 DB 연결을 끊지 않고 해당 커넥션을 커넥션 풀에 반환함
      if (conn != null) try { conn.close(); } catch(SQLException ex) {} 

   }
%>
</table>

</body>
</html>

```
	
	

**커넥션 풀 속성**

커넥션 풀에 있는 커넥션 중 오랜 시간 동안 사용되지 않는 커넥션은 DBMS와 연결이 끊길 가능성이 높다. 연결이 끊긴 커넥션을 사용하면 프로그램 실행 도중 오류가 발생하기 때문에 주기적으로 커넥션 풀에 있는 커넥션을 검사해서 사전에 제거해줘야 한다.

- 커넥션이 최소 유휴 시간보다 오래 풀에 잇는 경우 : `minEvictableIdleTimeMills()`
- 커넥션이 유효한지 여부 확인: `testWhileIdle()`
