HttpMediaTypeNotAcceptableException: 
Could not find acceptable representation

이 에러는 **"클라이언트가 원하는 형식(Accept 헤더)에 맞춰서 
응답을 만들어 줄 방법이 없다"** 라는 뜻

- 컨트롤러에서 `@ResponseBody`가 붙어 있음 → 스프링은 **리턴값을 JSON 같은 걸로 변환해서 보내야 한다고 생각**함.
    
- 그런데 이미 `HttpServletResponse`로 **엑셀 파일을 직접 써서 응답을 다 보냈음**(커밋됨).
    
- 스프링이 "이제 리턴값(ResponseVo)도 JSON으로 변환해야지" 하다가,
    
    - 이미 응답이 끝나 있어서 변환 불가 → **"응답 형식을 맞출 수 없다"**(406) 예외 발생.
        
- 그래서 `Could not find acceptable representation`이 뜸.

파일 다운로드처럼 직접 응답을 쓸 때는 `@ResponseBody`나 리턴값을 쓰면 안 됌.

그냥 `void`로 하고, 헤더 + 파일 내용만 직접 내려주면 된다.  
아니면 `ResponseEntity<Resource>`로 안전하게 내려주는 방법을 써야함.
