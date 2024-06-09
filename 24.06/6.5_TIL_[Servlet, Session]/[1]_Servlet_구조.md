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

action : form 데이터를 제출할 URL을 지정한다. <u>빈 값으로 설정하면 현재 URL</u>에 보내게 된다.<br>
        

method : 데이터 전송 방식을 결정한다. 주로 사용하는 것은 GET, POST이고 그 외에 PUT, DELETE도 있음.

<pre>
[ 데이터 전송 방식 ]
 
 GET : 주로 서버에서 데이터를 조회할 때 사용
 POST : 데이터를 서버로 전송하기 위해 사용
 PUT : 서버에 있는 리소스를 생성하거나 전체적으로 교체하기 위해 사용 = 특정 리소스 업데이트
 DELETE : 서버에 있는 리소스를 삭제하기 위해 사용

- 주로 GET과 POST 방식을 많이 사용한다.

[ GET과 POST의 차이점 ]

GET : 데이터가 쿼리 스트링으로 URL에 포함되어 전송된다. 
ex) http://example.com/resource?id=123
- GET 방식은 데이터가 URL에 노출이 되기 때문에 보안이 취약하다.
- URL의 길이 제한이 있기 때문에 대용량의 데이터를 전송하지는 못한다.
- 주로 html의 < a > 태그에 사용하거나 간단한 데이터 전송에 사용, `페이지 연결`을 주로 한다.

POST : 데이터가 HTTP 헤더에 내용(본문)으로 전송한다.
- POST 방식은 URL에 데이터를 포함시키지 않기 때문에 보안적인 측면에서 GET보다 좋다.
- 그러므로 보안이 필요한 데이터(아이디 , 비밀번호) 등의 `데이터 전송`을 주로 담당한다.

- GET 방식은 브라우저에서 캐싱되어 동일한 요청이 반복될 때 서버 부하를 줄일 수 있다.
   반대로 POST 방식은 캐싱되지 않기 때문에 새로운 요청을 매번 보내야 한다.

[ 캐싱이란? ]
> 자주 사용하는 데이터나 리소스를 미리 저장해 두었다가 다시 필요할 때 
> 빠르게 불러올 수 있게 해주는 기술, 서버의 부하를 줄이고 데이터 요청에 대한
> 응답시간을 줄일 수 있다.
</pre>


### 1-2. Servlet Parameter

- **GET 방식 servlet 예제코드**
```java
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

- **POST 방식 servlet 코드**
```java
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

        // 모든 요청 파라미터를 Map 형태로 가져오기
        Map<String, String[]> requestMap = request.getParameterMap();

        // keySet = 이 Map의 Key(파라미터 이름)을 저장
        Set<String> keySet = requestMap.keySet();
        Iterator<String> iterator = keySet.iterator();

        while(iterator.hasNext()) {
            String key = iterator.next();
            
        // 이 곳이 배열인 이유는 key에 해당하는 값이 여러개일 수 있기 때문이다.
        // ex) 체크박스 등등..
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


        /*
        - 사용자 브라우저에 응답하기 위해서는 HttpServletResponse의 getWriter() method로 
          PrintWriter 인스턴스를 반환받는다.
        - PrintWriter는 BufferedWriter와 형제격인 클래스이지만 더 많은 형태의 생성자를 
          제공하고 있는 범용성으로 인해 더 많이 사용된다.
        */
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


