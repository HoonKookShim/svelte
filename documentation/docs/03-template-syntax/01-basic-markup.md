---
title: Basic markup
---

스벨트 컴포넌트 안의 마크업은 일종의 HTML++로 생각할 수 있습니다.

## Tags

소문자 태그, 예를 들면`<div>`는 정규 HTML 요소를 나타냅니다.
대문자로 시작하는 태그나 점표기법(dot notation)을 사용한 태그, 예를 들면 `<Widget>` 이나 `<my.stuff>`는 _컴포넌트_ 를 나타냅니다.

```svelte
<script>
	import Widget from './Widget.svelte';
</script>

<div>
	<Widget />
</div>
```

## 요소의 속성들

기본적으로, 속성들은 그들의 원래 HTML 문법과 완전히 비슷하게 작동합니다.

```svelte
<div class="foo">
	<button disabled>can't touch this</button>
</div>
```

HTML에서와 동일하게, 값을 표시할 때는 따옴표를 생략할 수 있습니다.

<!-- prettier-ignore -->
```svelte
<input type=checkbox />
```

속성값에는 자바스크립트 표혁식이 포함될 수 있습니다.

```svelte
<a href="page/{p}">page {p}</a>
```

또는 속성값 자체가 자바스크립트 표현식이 될 수도 있습니다.

```svelte
<button disabled={!clickable}>...</button>
```

불른 값이 주어진 속성들은, 값이 [참](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)일 때 요소에 포함되며, 값이 [거짓](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)

그 값이 [널유사값(nullish)](https://developer.mozilla.org/en-US/docs/Glossary/Nullish)(`null` 또는 `undefined`)이 아닌 다른 모든 속성들은 포함됩니다.

```svelte
<input required={false} placeholder="This input field is not required" />
<div title={null}>This div has no title attribute</div>
```

> [!NOTE] Quoting a singular expression does not affect how the value is parsed, but in Svelte 6 it will cause the value to be coerced to a string:
>
> <!-- prettier-ignore -->
> ```svelte
> <button disabled="{number !== 42}">...</button>
> ```

속성의 이름과 그 값이 일치하는 경우에는(`name={name}`), 간단하게 `{name}`으로 줄일 수 있습니다.

```svelte
<button {disabled}>...</button>
<!-- equivalent to
<button disabled={disabled}>...</button>
-->
```

## Component props

정의에 의하면, 컴포넌트에 전달된 값들은 _속성_ 보다는 _프로퍼티_ 또는 _prop_ 라고 부르며, 그들은 DOM의 일부입니다.

As with elements, `name={name}` can be replaced with the `{name}` shorthand.

```svelte
<Widget foo={bar} answer={42} text="hello" />
```

_Spread attributes_ allow many attributes or properties to be passed to an element or component at once.

An element or component can have multiple spread attributes, interspersed with regular ones.

```svelte
<Widget {...things} />
```

## Events

Listening to DOM events is possible by adding attributes to the element that start with `on`. For example, to listen to the `click` event, add the `onclick` attribute to a button:

```svelte
<button onclick={() => console.log('clicked')}>click me</button>
```

Event attributes are case sensitive. `onclick` listens to the `click` event, `onClick` listens to the `Click` event, which is different. This ensures you can listen to custom events that have uppercase characters in them.

Because events are just attributes, the same rules as for attributes apply:

- you can use the shorthand form: `<button {onclick}>click me</button>`
- you can spread them: `<button {...thisSpreadContainsEventAttributes}>click me</button>`

Timing-wise, event attributes always fire after events from bindings (e.g. `oninput` always fires after an update to `bind:value`). Under the hood, some event handlers are attached directly with `addEventListener`, while others are _delegated_.

When using `ontouchstart` and `ontouchmove` event attributes, the handlers are [passive](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#using_passive_listeners) for better performance. This greatly improves responsiveness by allowing the browser to scroll the document immediately, rather than waiting to see if the event handler calls `event.preventDefault()`.

In the very rare cases that you need to prevent these event defaults, you should use [`on`](svelte-events#on) instead (for example inside an action).

### Event delegation

To reduce memory footprint and increase performance, Svelte uses a technique called event delegation. This means that for certain events — see the list below — a single event listener at the application root takes responsibility for running any handlers on the event's path.

There are a few gotchas to be aware of:

- when you manually dispatch an event with a delegated listener, make sure to set the `{ bubbles: true }` option or it won't reach the application root
- when using `addEventListener` directly, avoid calling `stopPropagation` or the event won't reach the application root and handlers won't be invoked. Similarly, handlers added manually inside the application root will run _before_ handlers added declaratively deeper in the DOM (with e.g. `onclick={...}`), in both capturing and bubbling phases. For these reasons it's better to use the `on` function imported from `svelte/events` rather than `addEventListener`, as it will ensure that order is preserved and `stopPropagation` is handled correctly.

The following event handlers are delegated:

- `beforeinput`
- `click`
- `change`
- `dblclick`
- `contextmenu`
- `focusin`
- `focusout`
- `input`
- `keydown`
- `keyup`
- `mousedown`
- `mousemove`
- `mouseout`
- `mouseover`
- `mouseup`
- `pointerdown`
- `pointermove`
- `pointerout`
- `pointerover`
- `pointerup`
- `touchend`
- `touchmove`
- `touchstart`

## Text expressions

A JavaScript expression can be included as text by surrounding it with curly braces.

```svelte
{expression}
```

Curly braces can be included in a Svelte template by using their [HTML entity](https://developer.mozilla.org/docs/Glossary/Entity) strings: `&lbrace;`, `&lcub;`, or `&#123;` for `{` and `&rbrace;`, `&rcub;`, or `&#125;` for `}`.

If you're using a regular expression (`RegExp`) [literal notation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp#literal_notation_and_constructor), you'll need to wrap it in parentheses.

<!-- prettier-ignore -->
```svelte
<h1>Hello {name}!</h1>
<p>{a} + {b} = {a + b}.</p>

<div>{(/^[A-Za-z ]+$/).test(value) ? x : y}</div>
```

The expression will be stringified and escaped to prevent code injections. If you want to render HTML, use the `{@html}` tag instead.

```svelte
{@html potentiallyUnsafeHtmlString}
```

> [!NOTE] Make sure that you either escape the passed string or only populate it with values that are under your control in order to prevent [XSS attacks](https://owasp.org/www-community/attacks/xss/)

## Comments

컴포넌트 안에 HTML 주석을 사용할 수 있습니다.

```svelte
<!-- this is a comment! --><h1>Hello world</h1>
```

`svelte-ignore`로 시작하는 주석은 다음 마크업 블록에서 발생하는 경고를 disable합니다.
보통, 접근성 경고입니다/warnings for the next block of markup. Usually, these are accessibility warnings; make sure that you're disabling them for a good reason.

```svelte
<!-- svelte-ignore a11y-autofocus -->
<input bind:value={name} autofocus />
```

`@component`로 시작하는 특수 주석을 추가하면, 다른 파일들에서 해당 컴포넌트 위에서 마우스 커서가 호버링할 때 그 주석을 보여줍니다.

````svelte
<!--
@component
- You can use markdown here.
- You can also use code blocks here.
- Usage:
  ```html
  <Main name="Arethra">
  ```
-->
<script>
	let { name } = $props();
</script>

<main>
	<h1>
		Hello, {name}
	</h1>
</main>
````
