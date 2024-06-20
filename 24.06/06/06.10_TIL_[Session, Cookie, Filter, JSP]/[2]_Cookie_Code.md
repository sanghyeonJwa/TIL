# 2024.06.10 - TIL
<br>

# 1. Cookie 예제 코드
- 쿠키 관련 코드 실습해보자!
<br>

## 1-1. Cookie 생성

### 1-1-1. index.jsp
- 성과 이름을 form 형태로 전달하기 
<br>

```html
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP - Hello World</title>
</head>

<body>
    <h1>Cookie handling</h1>

    <form action="cookie" method="post">
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
</body>
</html>
```
<br>

### 1-1-2. CookieHandlerServlet
- jsp에 있는 html 페이지에서 입력한 성과 이름을 쿠키에 저장해보기
- 그 이후 Redirect를 통해 쿠키에 잘 저장되어 있는지 확인해보자.
<br>

```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;

@WebServlet("/cookie")
public class CookieHandlerServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String firstName = request.getParameter("firstName");
        String lastName = request.getParameter("lastName");

        System.out.println("firstName = " + firstName);
        System.out.println("lastName = " + lastName);

//        response.sendRedirect("redirect");
        
        /*
        쿠키를 사용하는 방법은 간단하다.
        1. 쿠키를 생성한다.
        2. 생성한 쿠키의 만료시간을 설정한다.
        3. 응답헤더에 쿠키를 담는다.
        4. 응답한다.
        
        하지만 쿠키는 일부 제약사항이 있다.
        쿠키의 이름은 ascii문자만을 사용해야 하며 한번 설정한 쿠키의 이름은 변경할 수 없다.
        또한 쿠키의 이름에는 공백문자와 일부 특수문자([ ] ( ) = , \ ? * @ : ;)를 사용할 수 없다.
         */

        // 쿠키 생성
        Cookie firstNameCookie = new Cookie("firstName", firstName);
        Cookie lastNameCookie = new Cookie("lastName", lastName);

        // 쿠키 만료 시간 설정
        firstNameCookie.setMaxAge(60 * 60 * 24);
        lastNameCookie.setMaxAge(60 * 60 * 24);

        // 쿠키 추가
        response.addCookie(firstNameCookie);
        response.addCookie(lastNameCookie);

        // redirect로 다른 servlet 이동
        response.sendRedirect("redirect");
    }
}
```
<br>

### 1-1-3. RedirectServlet

```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/redirect")
public class RedirectServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String firstName = request.getParameter("firstName");
        String lastName = request.getParameter("lastName");

        System.out.println("firstName = " + firstName);
        System.out.println("lastName = " + lastName);

        /*
        쿠키를 사용하는 방법
        1. request에서 쿠키 목록을 배열 형태로 꺼내온다.
        2. 쿠키의 getName와 getValue를 이용해서 쿠키에 담긴 값을 사용한다.
         */

        // 1. 배열 형태로 쿠키 가져오기
        Cookie[] cookies = request.getCookies();

        // 쿠키 값 출력해보기
        for (Cookie cookie : cookies) {
            System.out.println("[cookies] = " + cookie.getName() + ", " + cookie.getValue());

            if("firstName".equals(cookie.getName())) {
                firstName = cookie.getValue();
            } else if("lastName".equals(cookie.getName())) {
                lastName = cookie.getValue();
            }
        }

        // html로 출력
        StringBuilder responseText = new StringBuilder();
        responseText.append("<!doctype html>\n")
                .append("<html>\n")
                .append("<head>\n")
                .append("</head>\n")
                .append("<body>\n")
                .append("<h3>your first name is ")
                .append(firstName)
                .append(" and last name is ")
                .append(lastName)
                .append("</h3>")
                .append("</body>\n")
                .append("</html>\n");

        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.print(responseText.toString());

        out.flush();
        out.close();
    }
}
```

