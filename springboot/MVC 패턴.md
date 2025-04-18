# MVC 패턴
## 📊 구성 요소

### 1. 📦 모델 (Model)

- 데이터와 비즈니스 로직 관리
- 순수한 애플리케이션 데이터만 포함
- 데이터 표현 방식에 대한 로직은 제외

### 2. 🖼️ 뷰 (View)

- 사용자에게 정보 표시
- 모델의 데이터를 사용자에게 보여주는 방식 정의

### 3. 🎮 컨트롤러 (Controller)

- 모델과 뷰 사이의 중재자 역할
- 사용자 입력 처리
- 모델과 뷰 업데이트

## ✨ MVC의 특징

- 🔍 **관심사의 분리**: 애플리케이션 로직을 데이터, UI, 제어 로직으로 분리
- 🧩 **모듈성**: 각 구성 요소를 독립적으로 개발 및 테스트 가능
- 🛠️ **유지보수성**: 구성 요소 간 독립성으로 유지보수 용이
- 🔄 **재사용성**: 코드 재사용성과 확장성 향상

## 🔄 작동 방식

1. 👤 사용자가 뷰를 통해 요청 전송
2. 🎮 컨트롤러가 요청 수신 및 처리
3. 🔍 컨트롤러가 필요시 모델에 데이터 요청
4. 📊 모델이 데이터 처리 후 결과를 컨트롤러에 반환
5. 🔗 컨트롤러가 처리된 데이터를 뷰에 전달
6. 🖼️ 뷰가 받은 데이터를 사용자에게 표시

## 💡 MVC 패턴의 이점

- 복잡한 웹 애플리케이션 개발을 관리하기 쉬운 프로세스로 전환
- 여러 개발자의 동시 작업 가능
- 코드의 구조화로 인한 가독성 향상
- 테스트 용이성 증가

## 🚀 실제 적용 예시 (Spring MVC)

```java
// Model
public class User {
    private String name;
// getters, setters
}

// Controller
@Controller
public class UserController {
    @GetMapping("/user")
    public String getUser(Model model) {
        User user = new User("John Doe");
        model.addAttribute("user", user);
        return "user";
    }
}

// View (user.html)
<html>
    <body>
        <h1 th:text="${user.name}">User Name</h1>
    </body>
</html>
```

MVC 패턴을 활용하면 애플리케이션의 구조를 명확히 하고, 각 부분의 역할을 분리하여 개발 및 유지보수를 더욱 효율적으로 할 수 있다.