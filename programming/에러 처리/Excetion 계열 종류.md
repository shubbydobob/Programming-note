
# 1. 자바 예외 계층 구조

```
Throwable
 ├── Error                // 시스템 레벨, 복구 불가
 └── Exception
      ├── Checked Exception     // 컴파일 시점 체크
      └── RuntimeException      // 언체크, 실행 중 발생

```


# 2. 주요 예외 분류표

| 구분                         | 대표 클래스                                                                                           | 특징 / 역할                                   | 롤백 기본 동작(@Transactional)           |
| -------------------------- | ------------------------------------------------------------------------------------------------ | ----------------------------------------- | ---------------------------------- |
| **Error**                  | OutOfMemoryError, StackOverflowError                                                             | JVM/시스템 오류, 코드로 복구 불가                     | 롤백 발생 (자동)                         |
| **Checked Exception**      | IOException, SQLException, ParseException, TimeoutException                                      | **컴파일러가 처리 강제** (`throws` or `try-catch`) | 롤백 안 함 (기본). 필요 시 `rollbackFor` 지정 |
| **RuntimeException (언체크)** | NullPointerException, IllegalArgumentException, IndexOutOfBoundsException, IllegalStateException | 실행 중 발생, `throws` 선언 불필요                  | 롤백 발생 (자동)                         |


# 3. 세부 예시

### (1) Checked Exception

- **IOException**: 파일, 네트워크 입출력 실패.
    
- **SQLException**: JDBC 통신/SQL 오류.
    
- **ParseException**: 문자열 날짜 파싱 실패.  
    → 반드시 try-catch 하거나 `throws`로 던져야 함.
    

### (2) Unchecked(RuntimeException)

- **프로그래밍 오류**에서 자주 발생.
    
- `throws` 선언 없이 발생 가능.
    
- 복구보다는 **개발자가 코드 수정으로 해결**해야 하는 문제.

- **NullPointerException (NPE)**: null 참조 접근.
    
- **IllegalArgumentException**: 잘못된 인자 전달.
    
- **IndexOutOfBoundsException**: 배열/리스트 범위 벗어남.
    
- **IllegalStateException**: 메서드 호출 시점 불일치.  
    → 컴파일러 강제 없음. 런타임 시점에만 터짐.
    

### (3) Error

- **OutOfMemoryError**: JVM 힙 부족.
    
- **StackOverflowError**: 무한 재귀 등으로 스택 초과.
    
- **NoClassDefFoundError**: 클래스 로딩 실패.  
    → 코드로 복구 불가. 예외 처리 시도 자체가 무의미.



커스텀 Exception 예외 처리

```
public class DuplicateEmailException extends RuntimeException {
    public DuplicateEmailException(String message) { super(message); }
}

public class InvalidPasswordException extends RuntimeException {
    public InvalidPasswordException(String message) { super(message); }
}

```


# 서비스 계층에서 사용

```
@Service
@RequiredArgsConstructor
public class MemberService {
    private final MemberRepository memberRepository;

    @Transactional
    public Long signUp(String email, String password) {
        // 1. 이메일 중복 체크
        if (memberRepository.existsByEmail(email)) {
            throw new DuplicateEmailException("이미 사용 중인 이메일: " + email);
        }

        // 2. 비밀번호 정책 검사
        if (!isValidPassword(password)) {
            throw new InvalidPasswordException("비밀번호 정책 위반: " + password);
        }

        // 3. 회원 생성
        Member member = new Member(email, password);
        return memberRepository.save(member).getId();
    }

    private boolean isValidPassword(String pw) {
        return pw != null && pw.length() >= 8 && pw.matches(".*[!@#$%^&*].*");
    }
}

```

# 컨트롤러에서 처리

```
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(DuplicateEmailException.class)
    public ResponseEntity<String> handleDup(DuplicateEmailException e) {
        return ResponseEntity.status(HttpStatus.CONFLICT).body(e.getMessage());
    }

    @ExceptionHandler(InvalidPasswordException.class)
    public ResponseEntity<String> handleInvalidPw(InvalidPasswordException e) {
        return ResponseEntity.badRequest().body(e.getMessage());
    }
}

```

