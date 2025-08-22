`ResponseEntity`는 스프링에서 **HTTP 응답 전체를 직접 제어할 수 있게 해주는 클래스**.  

리턴 타입으로 쓰여서 상태 코드, 헤더, 바디까지 명시적으로 지정할 수 있습니다.

```
@GetMapping("/hello")
public ResponseEntity<String> hello() {
    return ResponseEntity
            .status(HttpStatus.OK)           // 상태 코드
            .header("Custom-Header", "test") // 헤더
            .body("Hello World");            // 바디
}

```

스프링은 `@RestController` + 객체 반환을 쓰면 `MappingJackson2HttpMessageConverter`가 알아서 `Content-Type: application/json` 붙여줍니다. 그래서 **직접 헤더 지정은 특수한 경우에만** 필요합니다.


기본 `ResponseEntity`는 **상태 코드 + 바디 객체**만 넘길 수 있으니까,  `success` 같은 플래그를 같이 주고 싶으면 **커스텀 응답 DTO**를 만들 수 있다.


### 예시: 커스텀 응답 DTO

```
public class ApiResponse<T> {     
private boolean success;     
private T data;     
private String message;      
// 생성자, getter/setter }

```


컨트롤러 사용

```
@GetMapping("/user/{id}")
public ResponseEntity<ApiResponse<UserDto>> getUser(@PathVariable Long id) {
    UserDto user = userService.findById(id);

    ApiResponse<UserDto> response = new ApiResponse<>(
            true,
            user,
            "조회 성공"
    );

    return ResponseEntity.ok(response);
}
```

응답 예시(JSON):
```
{
  "success": true,
  "data": {
    "id": 1,
    "name": "얌마"
  },
  "message": "조회 성공"
}
```

