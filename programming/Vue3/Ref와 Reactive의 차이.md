ref()는 내부 값을 받아서 반응형이며 변경 가능한 ref 객체를 
반환합니다. 

이 객체는 내부 값을 가리키는 단일 속성 `.value`를 가집니다.

- ## 타입

```
function ref<T>(value: T): Ref<UnwrapRef<T>>

interface Ref<T> {
  value: T
}
```

- **세부사항**
    
    ref 객체는 변경 가능합니다. 즉, `.value`에 새로운 값을 할당할 수 있습니다. 또한 반응형이기도 하여, `.value`에 대한 읽기 작업은 추적되고, 쓰기 작업은 관련된 효과를 트리거합니다.
    
    객체가 ref의 값으로 할당되면, 해당 객체는 [reactive()](https://ko.vuejs.org/api/reactivity-core.html#reactive)로 깊게 반응형이 됩니다. 이는 객체에 중첩된 ref가 있을 경우, 이들도 깊게 언랩된다는 의미입니다.
    
    깊은 변환을 피하려면 [`shallowRef()`](https://ko.vuejs.org/api/reactivity-advanced.html#shallowref)를 사용하세요.


## reactive()[​](https://ko.vuejs.org/api/reactivity-core.html#reactive)

객체의 반응형 프록시를 반환합니다.

- **타입**

```
function reactive<T extends object>(target: T):         UnwrapNestedRefs<T>
```


- **세부사항**
    
    반응형 변환은 "깊게" 이루어집니다: 모든 중첩 속성에 영향을 미칩니다. 반응형 객체는 [ref](https://ko.vuejs.org/api/reactivity-core.html#ref) 속성도 깊게 언랩하면서 반응성을 유지합니다.
    
    또한 ref가 반응형 배열이나 `Map`과 같은 네이티브 컬렉션 타입의 요소로 접근될 때는 ref 언래핑이 수행되지 않는다는 점에 유의해야 합니다.
    
    깊은 변환을 피하고 루트 레벨에서만 반응성을 유지하려면 [shallowReactive()](https://ko.vuejs.org/api/reactivity-advanced.html#shallowreactive)를 사용하세요.
    
    반환된 객체와 그 중첩 객체들은 [ES Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)로 래핑되며, **원본 객체와 동일하지 않습니다**. 반응형 프록시만을 사용하고 원본 객체에 의존하지 않는 것이 권장됩니다.
    
- **예시**
    
    반응형 객체 생성:
    
  ```
    const obj = reactive({ count: 0 })
    obj.count++
    ```
    
    ref 언래핑:
    
    
    
    ```
    const count = ref(1)
    const obj = reactive({ count })
    
    // ref가 언랩됩니다
    console.log(obj.count === count.value) // true
    
    // `obj.count`가 업데이트됩니다
    count.value++
    console.log(count.value) // 2
    console.log(obj.count) // 2
    
    // `count` ref도 업데이트됩니다
    obj.count++
    console.log(obj.count) // 3
    console.log(count.value) // 3
    ```
    
    ref가 배열이나 컬렉션 요소로 접근될 때는 **언랩되지 않습니다**:

    ```
    const books = reactive([ref('Vue 3 Guide')])
    // 여기서는 .value가 필요합니다
    console.log(books[0].value)
    
    const map = reactive(new Map([['count', ref(0)]]))
    // 여기서도 .value가 필요합니다
    console.log(map.get('count').value)
    ```
    
    [ref](https://ko.vuejs.org/api/reactivity-core.html#ref)를 `reactive` 속성에 할당하면, 해당 ref도 자동으로 언랩됩니다:

    ```
    const count = ref(1)
    const obj = reactive({})
    
    obj.count = count
    
    console.log(obj.count) // 1
    console.log(obj.count === count.value) // true
    ```