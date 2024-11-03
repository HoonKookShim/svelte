---
title: 'style:'
---

`style:` 지시어는 하나의 요소에 여러개의 스타일을 세팅하는 편리한 방법을 제공합니다.

```svelte
<!-- 아래 두 줄은 동일한 효과를 냅니다. -->
<div style:color="red">...</div>
<div style="color: red;">...</div>
```

속성값에는 어떤 표현식이라도 사용할 수 있습니다.:

```svelte
<div style:color={myColor}>...</div>
```

줄임형태도 허용됩니다.:

```svelte
<div style:color>...</div>
```

여러개의 스타일들도 하나의 요소에 적용할 수 있습니다.:

```svelte
<div style:color style:width="12rem" style:background-color={darkMode ? 'black' : 'white'}>...</div>
```

하나의 스타일에 중요하다고 표시하고 싶으면, `|important` 변형자를 사용합니다.:

```svelte
<div style:color|important="red">...</div>
```

`style:` 지시어와 `style` 속성을 함께 사용하면, 지시어에 우선권이 있습니다.

```svelte
<div style="color: blue;" style:color="red">This will be red</div>
```
