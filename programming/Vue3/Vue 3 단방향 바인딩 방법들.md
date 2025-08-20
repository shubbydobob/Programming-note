## 단방향 바인딩 방법들

**1. `{{ }}` (머스태시 구문)**

- 가장 기본적이고 안전한 방법
- HTML을 자동으로 이스케이프해서 XSS 공격 방지
- 텍스트 콘텐츠만 렌더링

vue

```vue
<template>
  <p>{{ message }}</p>
</template>
```

**2. `v-text`**

- `{{ }}`와 동일한 기능
- 엘리먼트의 textContent를 업데이트
- 주로 조건부 렌더링이나 특수한 상황에서 사용

vue

```vue
<template>
  <p v-text="message"></p>
</template>
```

**3. `v-html`**

- HTML을 파싱해서 렌더링
- **보안 위험** - XSS 공격에 취약
- 신뢰할 수 있는 콘텐츠에만 사용

vue

```vue
<template>
  <div v-html="htmlContent"></div>
</template>
```

## 실무에서의 권장사항

**우선순위: `{{ }}` > `v-text` > `v-html`**

1. **일반적인 텍스트**: `{{ }}` 사용
2. **HTML 렌더링이 필요한 경우**:
    - 가능하면 컴포넌트로 분리
    - 불가피한 경우에만 `v-html` + DOMPurify 같은 라이브러리로 sanitize
3. **성능이 중요한 대량 렌더링**: `v-text` 고려 (아주 미미한 차이)

vue

```vue
<template>
  <!-- 권장 -->
  <p>{{ userMessage }}</p>
  
  <!-- 주의해서 사용 -->
  <div v-html="sanitizedHtml"></div>
</template>

<script setup>
import DOMPurify from 'dompurify'

const userMessage = ref('사용자 입력')
const sanitizedHtml = computed(() => DOMPurify.sanitize(rawHtml.value))
</script>
```

보안을 항상 우선순위에 두고, HTML 렌더링이 정말 필요한 경우가 아니라면 `{{ }}`를 사용하는 것이 안전합니다.