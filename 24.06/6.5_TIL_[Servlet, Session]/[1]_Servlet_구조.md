# 2024.06.05 TIL
<br>

# 1. Servlet 코드 구조

### 1-1. HTML 부분 구조
```html
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP - Hello World</title>
</head>

<body>
    <h1>Request Parameter</h1>
    <h3>GET 방식의 요청</h3>
    <h4>form태그를 이용한 get 방식 요청</h4>
    <form action="querystring" method="get">
        <label>이름 : </label><input type="text" name="name">
        <br>
        <label>나이 : </label><input type="number" name="age">
        <br>
        <label>생일 : </label><input type="date" name="birthday">
        <br>
        <label>성별 : </label>
        <input type="radio" name="gender" id="male" value="M"><label for="male">남자</label>
        <input type="radio" name="gender" id="female" value="F"><label for="female">여자</label>
        <br>
        <label>국적 : </label>
        <select name="national">
            <option value="ko">한국</option>
            <option value="ch">중국</option>
            <option value="jp">일본</option>
            <option value="etc">기타</option>
        </select>
        <br>
        <label>취미 : </label>
        <input type="checkbox" name="hobbies" id="movie" value="movie"><label for="movie">영화</label>
        <input type="checkbox" name="hobbies" id="music" value="music"><label for="music">음악</label>
        <input type="checkbox" name="hobbies" id="sleep" value="sleep"><label for="sleep">취침</label>
        <br>
        <input type="submit" value="GET 요청">
    </form>
</body>
</html>
```
[코드 설명]<br>

action : <br>
method : 방식을 뜻한다. get, post

> [ GET과 POST의 다른 점 ] <br>
> 
> GET : <br>
> POST : 

<br>

### 1-2. Servlet Parameter

- GET 방식
```java
package com.ohgiraffers.section01.queryString;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;
import java.time.LocalDate;

@WebServlet("/querystring")
public class QueryStringTestServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        /**
         * HttpServlet클래스의 service method는 요청 방식에 따라 GET요청에 대해서는 doGet()메소드를 호출하고 request, response를 전달한다.
         * -> 톰캣 서블릿 컨테이넉가 요청 URL로 매핑된 Servlet클래스의 인스턴스를 생성하여 service 메소드를 호출하고
         * HttpServlet을 상속받아 오버라이딩한 현재 클래스의 doGet() 메소드가 동적바인딩에 의해 호출된다.
         */

        // service로부터 전달받은 HttpServletRequest는 요청 시 전달한 값을 getParameter() 메소드로 추출해온다.
        String name = request.getParameter("name");
        System.out.println("name = " + name);

        // 넘겨받은 문자열을 프로그램에서 사용할 수 있게 파싱을 해야한다.
        int age = Integer.parseInt(request.getParameter("age"));
        System.out.println("age = " + age);

        java.sql.Date birthday = java.sql.Date.valueOf(request.getParameter("birthday"));
        LocalDate localDate = LocalDate.parse(request.getParameter("birthday"));
        System.out.println("birthday = " + birthday);
        System.out.println("localDate = " + localDate);

        String gender = request.getParameter("gender");
        System.out.println("gender = " + gender);

        String national = request.getParameter("national");
        System.out.println("national = " + national);

        // 체크 박스는 문자열 배열 형태로 전해준다.
        String[] hobbies = request.getParameterValues("hobbies");
        for(String hobbie : hobbies) {
            System.out.println(hobbie);
        }
    }
}
```
<br>

- POST 방식
```java
package com.ohgiraffers.section02.formdata;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

@WebServlet("/formData")
public class FormDataTestServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // POST 방식으로 요청한 내용의 값도 GET요청으로 파라미터를 처리하는 방식은 동일하다.

        String name = request.getParameter("name");
        System.out.println("name = " + name);

        Map<String, String[]> requestMap = request.getParameterMap();
        Set<String> keySet = requestMap.keySet();
        Iterator<String> iterator = keySet.iterator();

        while(iterator.hasNext()) {
            String key = iterator.next();
            String[] values = requestMap.get(key);

            System.out.println("key = " + key);

            for(int i = 0; i < values.length; i++) {
                System.out.println("value = " + values[i]);
            }
        }
    }
}
```

<br>


### 1-3. Servlet Request & Response

<pre>[ Header ]
1. General header : 요청 및 응답 모두에 적용되지만 최종적으로는 body에 전송되는 것과 관련이 없는 헤더이다. <br>
2. Request header : Fetch될 리소스나 클라이언트 자체에 대한 상세 정보를 포함하는 헤더이다.<br>
3. Response header : 위치나 서버 자체와 같은 응답에 대한 부가적인 정보를 갖는 헤더이다.<br>
4. Entity header : 컨텐츠 길이나 MIME타입과 같은 엔티티 body에 대한 상세 정보를 포함하는 헤더이다.
                 (요청 응답 모두 사용되며, 메시지 바디의 컨텐츠를 나타내기에 GET요청은 해당하지 않는다.)                 
-------------------------------------------------------------------------------------
[ HTTP 요청 헤더들 ]
> 서버에서 요청할 응답 타입, 인코딩 방식, 언어, 네트워크 유지 여부, 등등을 명시하는 헤더

accept : 요청을 보낼 때 서버에게 요청할 응답 타입 명시<br>
accept-encoding : 응답 시 원하는 인코딩 방식<br>
accept-language : 웅답 시 원하는 언어<br>
connection : HTTP 통신이 완료된 후에 네트워크 접속을 유지할지 결정 (기본값: keep-alive = 연결을 열린 상태로 유지)<br>
host : 서버의 도메인 네임과 서버가 현재 Listening 중인 TCP포트 지정 (반드시 하나가 존재. 없거나 둘 이상이면 404)<br>
referer : 이 페이지 이전에 대한 주소<br>
sec-fetch-dest : 요청 대상<br>
sec-fetch-mode : 요청 모드<br>
sec-fetch-site : 출처(origin)와 요청된 resource 사이의 관계<br>
sec-fetch-user : 사용자가 시작한 요청일 때만 보내짐 (항상 ?1 값 가짐)<br>
cache-control : 캐시 설정<br>
upgrade-insecure-requests<br>
user-agent : 현재 사용자가 어떤 클라이언트(OS, browser 포함)을 이용해 보낸 요청인지 명시<br>
</pre>

<br>

- **Servlet Request 예제 코드**
```java
package com.ohgiraffers.section01;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;

import java.io.IOException;
import java.util.Enumeration;

@WebServlet("/headers")
public class RequestHeaderPrintServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        Enumeration<String> headerNames = request.getHeaderNames();
        while(headerNames.hasMoreElements()) {
            System.out.println(headerNames.nextElement());
        }

        System.out.println("accept : " + request.getHeader("accept"));
        System.out.println("host : " + request.getHeader("host"));
    }
}
```
<br>

- **Servlet Response 예제 코드** 
```java
@WebServlet("/response")
public class ResponseTestServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {


        // PrintWriter out = response.getWriter();

        StringBuilder responseBuilder = new StringBuilder();
        responseBuilder.append("<!DOCTYPE html>")
                .append("<html>")
                .append("<head>")
                .append("<meta charset=\"UTF-8\">")
                .append("<title>Response</title>")
                .append("</head>")
                .append("<body>")
                .append("<h1>hello servlet response</h1>")
                .append("</body>")
                .append("</html>");


        // text/html -> MIME 타입 설정
        response.setContentType("text/html");

        // content-type 헤더의 값은 null이면 수정을 해줘야 한다.
        System.out.println("default response type : " + response.getContentType());
        System.out.println("default response encoding : " + response.getCharacterEncoding());

        // 만약에 getCharacterEncoding이 null이면 현재코드처럼 작성해야 한다.
        response.setCharacterEncoding("UTF-8");

        // 한번에 하기
        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();

        // out.print : 응답 객체로 내보내기
        out.print(responseBuilder.toString());
        out.flush(); // 버퍼공간에 있는 데이터를 내보내기
        out.close(); // close 하면 flush + close가 된다.
    }
```


