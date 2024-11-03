---
title: 'class:'
---

`class:` 지시어를 사용하면 편리하게 요소들의 클래스들을 조건에 따라 세팅할 수 있습니다. 클래스 속성 안에서 조건 표현식을 사용하는 것과 동일한 결과를 만듭니다.:

```svelte
<!-- 아래의 두 코드들은 동일한 결과를 나타냅니다. -->
<div class={isCool ? 'cool' : ''}>...</div>
<div class:cool={isCool}>...</div>
```

다른 지시어들과 마찬가지로, 클래스의 이름이 값과 동일할 경우에는 클래스 이름을 줄여서 쓸 수 있습니다.

```svelte
<div class:cool>...</div>
```

하나의 요소 안에 여러개의 `class:` 지시어들이 추가될 수 있습니다.:

```svelte
<div class:cool class:lame={!cool} class:potato>...</div>
```
