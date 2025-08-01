
문자열?

순서를 가진 문자들의 집합
- "쌍따옴표를 통해 나타낼 수 있음"
- 글자, 단어, 문장, 문서 등 문자로 구성된 자료형

자료형이란 정수, 실수, char, boolean...

String name = "" --> 빈 문자열로 가능

" " 대신 ' ' 쓰면 컴파일 에러 발생함.


Java.lang 패키지로 제공되는 Java 문자열 클래스
별도의 import 필요 없이 사용 가능하다.

한 번 인스턴스가 생성되면 수정할 수 없음 (Immutable Object)

String str_literal1 = "test";
String str_literal2 = "test";

공통 pool에 속하기 때문에 텍스트 값만 같다면 똑같다고 볼 수 있다.

반면
String str_object1 = new String("test");
String str_object2 = new String("test");

Heap 영역에 별개로 저장되기 때문에 텍스트 값이 같아도 값이 다르게 된다.

다만 문자열이 가지고 있는 값은 같기 때문에 equals로 비교한다면
value값은 4개 모두 같다.

[[대소문자 바꾸기]]
