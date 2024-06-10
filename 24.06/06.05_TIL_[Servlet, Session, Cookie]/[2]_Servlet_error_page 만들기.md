# 2024.06.05 - TIL
<br>

# 1. Servlet으로 에러 페이지 띄우기

Servlet Container
1. ExceptionHandlerServlet
2. Show404ErrorServlet
3. Show500ErrorServlet

<br>

[ 웹페이지를 사용하다가 404에러가 발생하면 사용자에게 보여줄 에러 페이지 만들어보기 ]<br>


---

### 1-1. index.jsp


사용자가 링크를 클릭하는 부분. 클릭하면 해당 Servlet을 호출하게 된다.<br>
- 해당 코드
```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>Exception Handler</title>
</head>

<body>
    <h1>Exception Handler</h1>
    <ul>
        <li><a href="show404error">404에러에요</a></li>
        <li><a href="show500error">500에러에요</a></li>
    </ul>
</body>
</html>
```
<br>

### 1-2. Show404ErrorServlet
위 jsp에서 `404에러에요` 링크를 클릭하게 되면 `Show404ErrorServlet`을 호출하게 된다.<br>
`response.sendError(404, "페이지를 찾을 수 없습니다.");` 통해서 404 에러를 발생시키게 된다.


```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;

@WebServlet("/show404error")
public class Show404ErrorServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.sendError(404, "페이지를 찾을 수 없습니다.");
    }
}
```
<br>

### 1-3. web.xml
404에러가 발생하게 되면 `web.xml` 파일에 정의된 에러 페이지 Mapping에 따라서 `/showErrorPage`(Servlet)로 요청이 포워딩이 된다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
    <error-page>
        <error-code>404</error-code>
        <location>/showErrorPage</location>
    </error-page>
    <error-page>
        <error-code>500</error-code>
        <location>/showErrorPage</location>
    </error-page>
</web-app>
```
<br>

### 1-4. ExceptionHandlerServlet
Annotation이 `/showErrorPage`인 ExceptionHandelrServlet을 호출하게 된다.<br>
현재 이부분에서 에러페이지(HTML)을 생성해서 response를 보내준다.

```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

@WebServlet("/showErrorPage")
public class ExceptionHandlerServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Enumeration<String> attributeNames = request.getAttributeNames();
        while (attributeNames.hasMoreElements()) {
            System.out.println(attributeNames.nextElement());
        }

        // 다운캐스팅 했음.

        // jakarta.servlet.forward.request_uri: 포워딩된 요청의 URI
        String forwardRequestURI = (String) request.getAttribute("jakarta.servlet.forward.request_uri");
        // jakarta.servlet.forward.context_path: 컨텍스트 경로
        String contextPath = (String) request.getAttribute("jakarta.servlet.forward.context_path");
        // jakarta.servlet.forward.servlet_path: 서블릿 경로
        String servletPath = (String) request.getAttribute("jakarta.servlet.forward.servlet_path");
        // request.getHttpServletMapping(): 서블릿 매핑 정보
        HttpServletMapping mapping = request.getHttpServletMapping();
        // jakarta.servlet.error.status_code: 에러 상태 코드 (예: 404, 500)
        Integer statusCode = (Integer) request.getAttribute("jakarta.servlet.error.status_code");
        // jakarta.servlet.error.message: 에러 메시지
        String message = (String) request.getAttribute("jakarta.servlet.error.message");
        // jakarta.servlet.error.servlet_name: 에러를 발생시킨 서블릿 이름
        String servletName = (String) request.getAttribute("jakarta.servlet.error.servlet_name");
        // jakarta.servlet.error.request_uri: 에러가 발생한 요청의 URI
        String errorRequestURI = (String) request.getAttribute("jakarta.servlet.error.request_uri");

        // 이 부분은 디버그 목적으로 사용한다.

        System.out.println(forwardRequestURI);
        System.out.println(contextPath);
        System.out.println(servletPath);
        System.out.println(mapping);
        System.out.println(mapping.getServletName());
        System.out.println(mapping.getMatchValue());
        System.out.println(mapping.getPattern());
        System.out.println(mapping.getMappingMatch());
        System.out.println(statusCode);
        System.out.println(message);
        System.out.println(servletName);
        System.out.println(errorRequestURI);

        // StringBuilder를 사용하여 HTML 형식의 에러페이지 생성 후 사용자에게 보여준다.
        StringBuilder errorPage = new StringBuilder();
        errorPage.append("<!doctype html>\n")
                .append("<html>\n")
                .append("<head>\n")
                .append("</head>\n")
                .append("<body>\n")
                .append("<h1>")
                .append(statusCode)
                .append(" - ")
                .append(message)
                .append("</h1>")
                .append("</body>\n")
                .append("</html>\n");
        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.print(errorPage.toString());
        out.flush();
        out.close();
    }
}
```