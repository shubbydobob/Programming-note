
JVM(Java Virtual Machine)은 **Java 바이트코드(Bytecode)** 를 실행하기 위한 가상의 실행 환경이다.

Java 프로그램이 "한 번 작성하면 어디서나 실행된다"라는 특징을
가질 수 있는 핵심 이유가 JVM 덕분이다.

## 1. 동작 구조

JVM의 주요 역할은 **바이트코드 → 기계어 변환 → 실행**입니다.

1. **Java Compiler (javac)**  
    `.java` 소스 파일을 **바이트코드(.class)** 로 컴파일.
    
2. **클래스 로더(Class Loader)**  
    `.class` 파일을 메모리로 로딩.
    
3. **실행 엔진(Execution Engine)**
    
    - **인터프리터**: 바이트코드를 한 줄씩 해석 후 실행. (속도 느림)
        
    - **JIT(Just-In-Time) 컴파일러**: 자주 실행되는 코드(Hotspot)를 기계어로 변환해 성능 향상.
        
4. **메모리 관리(Memory Management)**
    
    - **힙(Heap)**: 객체 인스턴스 저장.
        
    - **스택(Stack)**: 메서드 호출 시 프레임 저장.
        
    - **메소드 영역(Method Area)**: 클래스, 메서드, static 변수 정보 저장.
        
    - **GC(Garbage Collector)**: 더 이상 참조되지 않는 객체 메모리 해제.

## 2. 특징

- **플랫폼 독립성**  
    OS마다 JVM 구현체만 있으면 같은 바이트코드 실행 가능.
    
- **자동 메모리 관리**  
    개발자가 직접 메모리 해제하지 않아도 GC가 처리.
    
- **보안성**  
    클래스 로더와 바이트코드 검증기를 통해 안전한 코드만 실행.
    
- **최적화 실행**  
    JIT 컴파일과 Hotspot 최적화로 실행 속도 향상.



```
 .java 소스 → javac → .class 바이트코드 → JVM
      ↓ 클래스 로더
      ↓ 실행 엔진(인터프리터/JIT)
      ↓ OS/CPU에서 실행
```

## 4. 실무 관점 핵심

- **성능**: JIT 튜닝, GC 방식 선택(Serial, Parallel, G1 등)으로 최적화 가능.
    
- **메모리**: JVM 옵션(`-Xms`, `-Xmx`, `-XX:MaxMetaspaceSize` 등)으로 힙/메타스페이스 크기 조정.
    
- **모니터링**: `jconsole`, `jvisualvm`, `GC 로그` 분석으로 문제 진단.
    
- **이식성**: 서버 이전이나 환경 변경 시 소스 수정 없이 배포 가능.


# **JVM 메모리 구조**

## 1. **JVM이 프로그램 실행 시 사용하는 메모리**는 크게 **5영역**으로 나뉩니다.

|영역|내용|특징|활용/주의점|
|---|---|---|---|
|**Heap**|객체(instance)와 배열 저장 공간|GC가 관리, 전역 공유|메모리 부족 시 `OutOfMemoryError: Java heap space` 발생|
|**Method Area** (Java 8 이후 Metaspace)|클래스 정보, static 변수, 메서드 코드 저장|JVM 시작 시 로드, 모든 스레드 공유|너무 크면 `OutOfMemoryError: Metaspace` 발생|
|**Stack**|각 스레드의 메서드 호출 정보(지역변수, 매개변수) 저장|스레드별로 독립, LIFO 구조|재귀 무한 호출 시 `StackOverflowError`|
|**PC Register**|현재 실행 중인 JVM 명령 주소 저장|스레드별 독립|일반적으로 개발자가 직접 다루지 않음|
|**Native Method Stack**|JNI 호출 시 네이티브 코드 실행 공간|OS 의존|C/C++ 연동 시 사용|


## **2. 실행 시 메모리 동작 흐름**

1. **클래스 로드 시** → Method Area에 클래스 메타데이터, static 변수 저장
    
2. **new 객체 생성 시** → Heap에 저장, 참조 변수는 Stack에 저장
    
3. **메서드 호출 시** → Stack에 호출 프레임(지역변수, 파라미터, 연산 중 데이터) 생성
    
4. **메서드 종료 시** → 해당 Stack Frame 제거
    
5. **참조 끊긴 객체** → GC가 Heap에서 제거


## **3. 핵심 요소 & 실무 활용**

- **Heap**
    
    - **활용**: 모든 객체 저장 → 성능 병목 원인 중 하나
        
    - **튜닝**: `-Xms`, `-Xmx` 로 초기/최대 Heap 크기 설정
        
    - **문제 예시**: 컬렉션에 너무 많은 데이터 적재 시 GC 과부하
        
- **Method Area(Metaspace)**
    
    - **활용**: 클래스, static 변수 관리
        
    - **튜닝**: `-XX:MaxMetaspaceSize` 로 크기 제한
        
    - **문제 예시**: 동적 클래스 로딩 반복 시 OOM (예: 리플렉션 과다 사용)
        
- **Stack**
    
    - **활용**: 메서드 실행 시 지역변수·연산 처리
        
    - **문제 예시**: 무한 재귀 → `StackOverflowError`
        
    - **특징**: GC 영향 없음, 메서드 종료 시 자동 해제