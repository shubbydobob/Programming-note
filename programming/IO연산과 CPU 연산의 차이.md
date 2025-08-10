## 1. CPU 연산

**CPU 연산**은 말 그대로 **프로세서(CPU)**가 메모리(RAM)에 올라온 데이터를 가지고 직접 계산하는 작업.

- **예시**
    
    - 산술 연산: `a + b`, `x * y`
        
    - 논리 연산: `if`, `&&`, `||`
        
    - 메모리 내 데이터 복사

    - StringBuilder

    - 정렬, 탐색 같은 알고리즘 실행
        
- **특징**
    
    - 속도가 매우 빠름 (나노초 ~ 마이크로초 단위)
        
    - 병렬화/멀티코어 최적화 가능
        
    - CPU 클럭 속도에 직접적으로 영향 받음
        
    - 병목이 적음 (단, 데이터가 메모리에 있어야 함)
        

---

## 2. I/O 연산

**I/O(입출력) 연산**은 CPU가 아닌 **외부 장치**와 데이터를 주고받는 작업.  
I/O에는 디스크, 네트워크, 콘솔(키보드/모니터) 출력, 프린터 등이 포함.

- **예시**
    
    - 파일 읽기/쓰기
        
    - 콘솔 출력 (`System.out.println`)
        
    - 네트워크 요청/응답
        
    - 데이터베이스 쿼리 실행
        
    - 키보드 입력 받기
        
- **특징**
    
    - CPU 연산보다 훨씬 느림 (마이크로초 ~ 밀리초, 경우에 따라 초 단위)
        
    - 데이터 전송 경로(디스크 → 메모리 → CPU)가 길고 장치 속도에 의존
        
    - 대량 호출 시 병목 발생 가능
        
    - 네트워크 I/O는 특히 지연(latency)이 큼


## 3. 왜 I/O가 훨씬 느린가?

1. **물리 장치 속도 한계**
    
    - CPU: 초당 수십억 회 연산 가능
        
    - SSD: 초당 수백 MB 처리
        
    - HDD: 초당 수십~백 MB 처리
        
    - 네트워크: 지연 수~수백 ms 발생 가능
        
2. **컨텍스트 스위칭 비용**
    
    - CPU가 I/O 요청을 하면, OS는 요청을 대기열에 넣고 다른 작업을 처리.
        
    - 데이터가 준비되면 CPU로 다시 전달 → 이 과정에서 문맥 전환 비용 발생.
        
3. **버퍼링 필요**
    
    - I/O 장치는 한 번에 많은 데이터를 처리하지 못해, 버퍼에 쌓아 두고 순차적으로 처리.

## 5. 결론

- **CPU 연산**: 빠름, 메모리 내에서 처리 → 수 ns~μs
    
- **I/O 연산**: 느림, 외부 장치 의존 → 수 μs~~ms (네트워크는 수십~~수백 ms)
    
- **실무 최적화 팁**
    
    - I/O 호출 최소화 (`StringBuilder`/버퍼링)
        
    - 가능하면 비동기 I/O 사용
        
    - 네트워크 요청은 병렬화 또는 캐싱
        
    - CPU-bound vs I/O-bound 구분하고 병목 원인을 먼저 찾기


* System.out.println()이 느린 이유


     *  콘솔(표준 출력)로 데이터를 보내는 I/O 연산
     *  호출 할 때 마다
     1.  JVM -> OS 표춘 출력 버퍼에 문자열 전달
     2.  OS가 그걸 화면 장치로 전송
     3. 커서 이동 / 개행 처리

     *  이 과정은 CPU 메모리 연산에 비해 수천~수만 배 느림

     *  호출이 많을 수록 이 지연이 누적돼 병목이 생긴다.

*  StringBuilder가 유리한 이유

     *  - `StringBuilder.append()`는 단순히 **메모리(RAM)** 안에서 문자열 배열에 데이터를 이어 붙이는 **CPU 연산**.
    
     - 매우 빠르고, 호출 횟수가 많아도 부담이 적음.
    
     -  마지막에 한 번만 `System.out.print(sb)`로 I/O를 하면  
        → **I/O 호출 횟수를 1번**으로 줄일 수 있음.


<<<< System.out.println VS StringBuilder 연산 속도 비교 코드 >>>>

package com.project.codingtest;  
  
public class Main {  
    public static void main(String[] args) {  
        int N = 1_000_000;  
  
        // 1. System.out.println() 반복  
        long start1 = System.currentTimeMillis();  
        for (int i = 0; i < N; i++) {  
            System.out.println(i);  
        }  
        long end1 = System.currentTimeMillis();  
        System.out.println("println 반복: " + (end1 - start1) + " ms");  
  
        // 2. StringBuilder + 마지막에 한 번 출력  
        long start2 = System.currentTimeMillis();  
        StringBuilder sb = new StringBuilder();  
        for (int i = 0; i < N; i++) {  
            sb.append(i).append('\n');  
        }  
        System.out.print(sb);  
        long end2 = System.currentTimeMillis();  
        System.out.println("StringBuilder 사용: " + (end2 - start2) + " ms");  
    }  
}


System.out.printlin 실행 결과 : 3556ms
StringBuilder 실행 결과 : 700 ~ 1000ms
