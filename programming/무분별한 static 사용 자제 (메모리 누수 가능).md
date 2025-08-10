# Static이란?


static 키워드는 변수나 메서드가 클래스 레벨에서 관리되도록 하여,
인스턴스를 생성하지 않고도 접근 가능하게 한다.

이러한 static 요소들은 프로그램이 종료될 때까지 메모리에 유지된다.

## 메모리 누수가 발생하는 경우들

### 1. Static Collection에 객체 누적

java

```java
public class DataManager {
    private static List<UserData> userCache = new ArrayList<>();
    
    public static void addUser(UserData user) {
        userCache.add(user); // 계속 누적되어 메모리 증가
    }
    
    // 제거 로직이 없으면 메모리 누수 발생
}
```

### 2. Static 참조로 인한 객체 해제 불가

java

```java
public class EventHandler {
    private static SomeListener listener;
    
    public static void setListener(SomeListener l) {
        listener = l; // 이전 listener가 GC되지 않을 수 있음
    }
}
```

### 3. Static 내부 클래스 사용

java

```java
public class OuterClass {
    private String data = "important data";
    
    static class InnerClass {
        // 외부 클래스 인스턴스에 대한 암시적 참조로 인해
        // OuterClass 인스턴스가 GC되지 않을 수 있음
    }
}
```

## 왜 메모리 누수가 발생하는가?

1. **GC 대상에서 제외**: Static 변수는 클래스 로더가 언로드될 때까지 메모리에서 해제되지 않습니다.
2. **강한 참조 유지**: Static 변수가 객체를 참조하면 해당 객체는 GC되지 않습니다.
3. **생명주기 불일치**: 애플리케이션 전체 생명주기와 객체의 실제 필요 기간이 다를 수 있습니다.

## 올바른 사용법

### 1. 적절한 해제 로직 추가

java

```java
public class DataManager {
    private static Map<String, UserData> userCache = new ConcurrentHashMap<>();
    
    public static void addUser(String id, UserData user) {
        userCache.put(id, user);
    }
    
    public static void removeUser(String id) {
        userCache.remove(id); // 명시적 해제
    }
    
    public static void clearCache() {
        userCache.clear(); // 전체 정리
    }
}
```

### 2. WeakReference 사용

java

```java
public class EventManager {
    private static List<WeakReference<EventListener>> listeners = new ArrayList<>();
    
    public static void addListener(EventListener listener) {
        listeners.add(new WeakReference<>(listener));
    }
    
    private static void cleanupListeners() {
        listeners.removeIf(ref -> ref.get() == null);
    }
}
```

### 3. 싱글톤 패턴 대신 의존성 주입

java

```java
// 권장하지 않는 방식
public class DatabaseManager {
    private static DatabaseManager instance;
    private Connection connection;
    
    public static DatabaseManager getInstance() {
        if (instance == null) {
            instance = new DatabaseManager();
        }
        return instance;
    }
}

// 권장하는 방식 (Spring 등의 DI 컨테이너 사용)
@Component
@Scope("singleton")
public class DatabaseManager {
    // 컨테이너가 생명주기 관리
}
```

## 모니터링 및 디버깅

메모리 누수를 방지하려면:

- 힙 덤프 분석으로 static 변수의 메모리 사용량 모니터링
- 프로파일링 도구를 사용하여 메모리 증가 패턴 확인
- Static 변수에 대한 코드 리뷰 강화
- 단위 테스트에서 메모리 사용량 검증

Static은 편리하지만 신중하게 사용해야 하며, 특히 객체 참조를 저장하는 static 변수는 적절한 생명주기 관리가 필수입니다.



# Static을 써야하는 이유.

### 1. **유틸리티 메서드**

java

```java
// 인스턴스를 만들 필요가 없는 순수한 기능들
public class MathUtils {
    public static int max(int a, int b) {
        return a > b ? a : b;
    }
    
    public static boolean isEven(int number) {
        return number % 2 == 0;
    }
}

// MathUtils.max(5, 3) - 간단하고 직관적
```

### 2. **상수 정의**

java

```java
public class Constants {
    public static final String API_BASE_URL = "https://api.example.com";
    public static final int MAX_RETRY_COUNT = 3;
    public static final double PI = 3.14159;
}
```

### 3. **싱글톤이 필요한 경우**

java

```java
// 데이터베이스 커넥션 풀처럼 하나만 있어야 하는 경우
public class DatabaseConnectionPool {
    private static final DatabaseConnectionPool INSTANCE = new DatabaseConnectionPool();
    
    public static DatabaseConnectionPool getInstance() {
        return INSTANCE;
    }
}
```

### 4. **메모리 효율성**

java

```java
// 모든 인스턴스가 공유하는 데이터
public class User {
    private static final Logger logger = LoggerFactory.getLogger(User.class);
    private String name;
    
    // 1000개 User 인스턴스를 만들어도 Logger는 하나만 존재
}
```

## Static vs Non-static 비교

java

```java
// Non-static 방식 (비효율적)
class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}

// 매번 인스턴스 생성 필요
Calculator calc = new Calculator();
int result = calc.add(1, 2);

// Static 방식 (효율적)
class Calculator {
    public static int add(int a, int b) {
        return a + b;
    }
}

// 인스턴스 생성 불필요
int result = Calculator.add(1, 2);
```

## 메모리 누수 vs 정당한 메모리 사용

**메모리 누수가 되는 경우:**

java

```java
// 나쁜 예 - 계속 쌓이기만 함
public class BadCache {
    private static Map<String, Object> cache = new HashMap<>();
    
    public static void put(String key, Object value) {
        cache.put(key, value); // 영원히 쌓임
    }
}
```

**정당한 메모리 사용:**

java

```java
// 좋은 예 - 필요한 만큼만 사용
public class GoodCache {
    private static final Map<String, Object> cache = new LRUCache<>(1000);
    
    public static void put(String key, Object value) {
        cache.put(key, value); // 최대 1000개까지만
    }
}
```

## 실제 사용 사례들

1. **Java 표준 라이브러리**: `Math.max()`, `System.out.println()`, `Arrays.sort()`
2. **프레임워크**: Spring의 `@Component`, `@Service` 등
3. **로깅**: `LoggerFactory.getLogger()`
4. **팩토리 패턴**: `LocalDateTime.now()`

## 결론

Static은 **도구**입니다. 칼이 위험하다고 요리를 포기하지 않듯이, static도 올바르게 사용하면 코드를 더 효율적이고 읽기 쉽게 만들어줍니다.

핵심은:

- **상태를 가지지 않는 순수 함수**나 **상수**에는 static 사용
- **상태를 계속 쌓아두는** static 변수는 피하기
- **적절한 생명주기 관리**로 메모리 누수 방지

Static을 무조건 피하기보다는, 언제 사용해야 하고 언제 피해야 하는지 구분하는 게 중요해요!
