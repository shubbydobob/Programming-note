ex) 시작년도를 임의로 설정하고 원하는 범위의 년도까지 select 또는 dropdwon에 나타내기

```
<DropdownItem v-for="y in Array.from({ length: 2025 - 2005 + 1 }, (_, i) => 2005 + i)" :key="y" :value="y" :text="y" />
```

부모 컴포넌트에서는 'yyyy-MM-dd'형태로 받지만 자식 컴포넌트에서는 
년, 월, 일 독립적으로 select할 때

자식 컴포넌트에서 computed를 사용하여 v-model을 각각 year, month, day로 받은 후 넘길 때 합친 형태로 부모에 넘겨주기.


// 갱신기간 만료일 처리
const licenseRefreshExpireDate = computed({
  get: () => props.param?.licenseRefreshExpireDate || '',
  set: value => emit('update:licenseRefreshExpireDate', value),
});

const expireYear = computed({
  get: () => licenseRefreshExpireDate.value.split('-')[0] || '',
  set: val => {
    const [, m = '', d = ''] = licenseRefreshExpireDate.value.split('-');
    licenseRefreshExpireDate.value = `${val}-${m}-${d}`;
  },
});

const expireMonth = computed({
  get: () => licenseRefreshExpireDate.value.split('-')[1] || '',
  set: val => {
    const [y = '', , d = ''] = licenseRefreshExpireDate.value.split('-');
    licenseRefreshExpireDate.value = `${y}-${val}-${d}`;
  },
});

const expireDay = computed({
  get: () => licenseRefreshExpireDate.value.split('-')[2] || '',
  set: val => {
    const [y = '', m = ''] = licenseRefreshExpireDate.value.split('-');
    licenseRefreshExpireDate.value = `${y}-${m}-${val}`;
  },
});


props는 부모컴포넌트에서 받은 데이터를 사용한다.

만약 부모가 아닌 자식 컴포넌트에서 자체 데이터를 입력 후 부모 컴포넌트로넘겨줘야한다면
자식 컴포넌트에서 ref를 이용해서 데이터 생성 후 사용.