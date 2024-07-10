# 2024.06.17 - TIL
<br>

# 1. Exception Handler

- spring boot에서 예외 처리를 담당하는 메소드를 만들 수 있다. <br>
- @ExceptionHandler("예외 클래스 명") 어노테이션을 사용하여 해당 예외가 발생시 이 메소드로 처리를<br>
  넘길 수 있다.
- @ExceptionHandler 어노테이션은 해당 클래스 안에서만 동작한다.
- 전역으로 처리하고 싶은 경우 클래스에 @ControllerAdvice 어노테이션을 달아주면 된다.

  <br>

# 2. 예제 코드
<br>

## 2-1. main.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>main</title>
</head>

<body>
    <h1>Exception Handler 사용하기</h1>

    <h3>@ExceptionHandler</h3>
    <button onclick="location.href='/controller-null'">NullPointerException 테스트</button>
    <button onclick="location.href='/controller-user'">사용자 정의 Exception 테스트</button>

    <h3>@ControllerAdvice</h3>
    <button onclick="location.href='other-controller-null'">다른 컨트롤러 NullPointerException 테스트</button>
</body>
</html>
```

## 2-2. MainController
```java
@Controller
public class MainController {

    @RequestMapping(value={"/", "/main"})
    public String main() {
        return "main";
    }
}
```

## 2-3. MemberRegistException
```java
public class MemberRegistException extends Exception {

    public MemberRegistException(String message) {
        super(message);
    }
}
```


## 2-4. ExceptionHandlerController
```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class ExceptionHandlerController {

    @GetMapping("/controller-null")
    public String nullPointerExceptionTest() {

        String str = null;
        System.out.println(str.charAt(0));  // nullpointerException 발생

        return "/";
    }

    @ExceptionHandler(NullPointerException.class)
    public String nullPointerExceptionHandler(NullPointerException exception) {

        System.out.println("controller 레벨의 exception 처리");
        return "error/nullPointer";
    }

    @GetMapping("/controller-user")
    public String userExceptionTest() throws MemberRegistException {
        boolean check = true;
        if (check) {
            throw new MemberRegistException("당신 같은 사람은 회원으로 받을 수 없습니다..");
        }
        return "/"; // forward -> (리다이렉트로 변경) -> redirect:/
    }

    @ExceptionHandler(MemberRegistException.class)
    public String userExceptionHandler(Model model, MemberRegistException exception) {
        System.out.println("controller 레벨의 exception 처리");
        model.addAttribute("exception", exception);

        return "error/memberRegist";
    }

}

```

## 2-5. GlobalExceptionHandler
```java
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

// 전역 예외 처리를 담당
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(NullPointerException.class)
    public String nullPointer(NullPointerException exception) {

        System.out.println("Global Exception Handler 처리");

        return "error/nullPointer";
    }

    @ExceptionHandler({MemberRegistException.class})
    public String userExceptionHandler(Model model, MemberRegistException exception) {
        System.out.println("Global 레벨의 exception 처리");
        model.addAttribute("exception", exception);

        return "error/memberRegist";
    }
}

```


## 2-6. OtherController
- 다른 컨트롤러(클래스)에서 
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class OtherController {


    @GetMapping("/other-controller-null")
    public String otherControllerNullTest() {
        String str = null;
        System.out.println(str.charAt(0));

        return "/";
    }

    @GetMapping("/other-controller-user")
    public String otherControllerUserTest() throws MemberRegistException {
        boolean check = true;
        if(check) {
            throw new MemberRegistException("넌 회원이 될 수 없다!!!!!!!!!!!!!!!!!!");
        }

        return "/";
    }
}
```