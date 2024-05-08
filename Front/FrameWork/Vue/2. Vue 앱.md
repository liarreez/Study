# Vue 앱

<br>

## 1. 인스턴스 생성

---

```Vue
import { createApp } from 'vue'

const app = createApp({
  /* 컴포넌트 */
})
```

<br>

## 2. 최상위 컴포넌트에 포함

---

```Vue
import { createApp } from 'vue'
// 싱글 파일 컴포넌트에서 최상위 컴포넌트 앱을 가져옴
import App from './App.vue'

const app = createApp(App)
```

<br>

## 3. 앱 마운트
최상위 컴포넌트 컨텐츠를 컨테이너 엘리먼트 내에서 렌더링
```html
<div id="app"></div>
```

```js
app.mount('#app')
```

 - 마운트 컴포넌트 내부에 최상위 컴포넌트 작성 가능
 - 최상위 컴포넌트에 template 옵션이 없으면 Vue는 컨테이너의 innerHtml을 템플릿으로 사용
 - 대규모 페이지의 특정 부분을 제어하고자 할 때는 페이지 전체를 단일 Vue 앱 인스턴스로 마운트 하지 말고 여러 개의 작은 앱 인스턴스로 담당하는 요소에 마운트
