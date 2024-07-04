# 2024.06.10 - TIL
<br>

# 1. Session 실습 코드

Session 실습하기.
<br><br>


## 1-1. Session 생성
- 성과 이름을 받아서 Session에 넣어서 다른 Servlet에서 출력해보자!

### 1-1-1. index.jsp
<br>

```html
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP - Hello World</title>
</head>

<body>
    <h1>Session Object Handling</h1>
    <form action="session" method="post">
        <table>
            <tr>
                <td>firstName : </td>
                <td><input type="text" name="firstName"></td>
            </tr>
            <tr>
                <td>lastName : </td>
                <td><input type="text" name="lastName"></td>
            </tr>
            <tr>
                <td colspan="2">
                    <button type="submit">전송</button>
                </td>
            </tr>
        </table>
    </form>
    
    <a href="delete">세션 데이터 지우기</a>
</body>
</html>
```
<br>

### 1-1-2. SessionHandlerServlet
<br>

```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;

@WebServlet("/session")
public class SessionHandlerServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String firstName = request.getParameter("firstName");
        String lastName = request.getParameter("lastName");

        System.out.println("firstName = " + firstName);
        System.out.println("lastName = " + lastName);

//        response.sendRedirect("redirect");

        // Session은 request쪽에 있다,
        HttpSession session = request.getSession();
        System.out.println("session default 유지 시간 : " + session.getMaxInactiveInterval());

        session.setMaxInactiveInterval(1800 * 2);

        System.out.println("변경 후 session default 유지 시간 : " + session.getMaxInactiveInterval());

        System.out.println("session id = " + session.getId());

        session.setAttribute("firstName", firstName);
        session.setAttribute("lastName", lastName);

        response.sendRedirect("redirect");
    }
}
```

### 1-1-3. RedirectServlet
<br>

```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

@WebServlet("/redirect")
public class RedirectServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 여기는 왜 doGet을 사용하는가..
        /*
        주소 요청을 하기 때문에 get을 사용해야 한다.
         */

        // 현재 이 코드의 request는 redirect 메소드로 넘어온 새로운 request이기 때문에 값을 불러오지 못한다.
        // 그러므로 값을 넘기기 위해 세션을 사용한다.
        String firstName = request.getParameter("firstName");
        String lastName = request.getParameter("lastName");

        System.out.println("firstName = " + firstName);
        System.out.println("lastName = " + lastName);

        HttpSession session = request.getSession();
        System.out.println("session.getID() = " + session.getId());

        // 세션에 담긴 모든 Attribute 키 목록 조회
        Enumeration<String> attributeNames = request.getAttributeNames();

        while(attributeNames.hasMoreElements()){
            System.out.println(attributeNames.nextElement());
        }

        // 세션에서 값을 가져오기
        String sessionFirstName = session.getAttribute("firstName").toString();
        String sessionLastName = session.getAttribute("lastName").toString();

        System.out.println("sessionFirstName = " + sessionFirstName);
        System.out.println("sessionLastName = " + sessionLastName);

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        
        // HTML에 값 담아서 출력하기
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Servlet RedirectServlet</title>");
        out.println("</head>");
        out.println("<body>");
        out.println("<h3>Your first Name is </h3>");
        out.println(sessionFirstName);
        out.println("<h3>Your last Name is </h3>");
        out.println(sessionLastName);
        out.println("</body>");
        out.println("</html>");

        out.flush();
        out.close();
    }
}
```

## 1-2. Session Data 삭제
- Session에 저장된 데이터를 삭제해보자.
- 위의 jsp 파일과 Session 생성한 Servlet 재사용.
<br>

### 1-2-1. DeleteSessionDataServlet
<br>

```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;
import java.util.Enumeration;

@WebServlet("/delete")
public class DeleteSessionDataServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        HttpSession session = request.getSession(); // 세션의 값을 처리하기 위해서 세션 값을 가져온다.

        /*
        세션의 데이터를 지우는 방법은 여러가지가 있다.
        1. 설정한 만료시간이 지낙면 세션이 만료된다.
        2. removeAttribute()로 Session의 Attribute를 지운다.
        3. invalidate()를 호출하면 세션의 모든 데이터가 제거된다.
         */

        // 수정, 생성 = setAttribute()
        System.out.println("=========================================");
        session.removeAttribute("firstName");


        // 세션에 담긴 모든 Attribute 키 목록 조회
        Enumeration<String> attributeNames = session.getAttributeNames();

        // 위에서 session에 "firstName"을 삭제했기 때문에 "lastName" 밖에 안나오게 된다.
        while(attributeNames.hasMoreElements()){
            System.out.println(attributeNames.nextElement());
        }

        // invalidate는 모든 Session 데이터를 제거하기 때문에 Attribute가 아무것도 안나오게 된다.
        // 로그아웃을 구현할 때 많이 사용한다. (세션을 비우고 로그인 페이지 혹은 메인 페이지로 넘겨주면 된다.)
        session.invalidate();
        System.out.println("=========================================");
        attributeNames = session.getAttributeNames();
        while(attributeNames.hasMoreElements()){
            System.out.println(attributeNames.nextElement());
        }
    }
}
```