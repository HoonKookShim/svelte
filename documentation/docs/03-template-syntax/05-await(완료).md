---
title: \{#await ...}
---

```svelte
<!--- copy: false  --->
{#await expression}...{:then name}...{:catch name}...{/await}
```

```svelte
<!--- copy: false  --->
{#await expression}...{:then name}...{/await}
```

```svelte
<!--- copy: false  --->
{#await expression then name}...{/await}
```

```svelte
<!--- copy: false  --->
{#await expression catch name}...{/await}
```

Await 블록을 사용하면 [`프라미스`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 의 세가지 가능한 상태 — 대기중, 이행완료 또는 거절됨 - 에 따라 랜더링될 내용을 나눌 수 있습니다.

```svelte
{#await promise}
	<!-- promise is pending -->
	<p>waiting for the promise to resolve...</p>
{:then value}
	<!-- promise was fulfilled or not a Promise -->
	<p>The value is {value}</p>
{:catch error}
	<!-- promise was rejected -->
	<p>Something went wrong: {error.message}</p>
{/await}
```

> [!NOTE] 서버 사이드 렌더링 동안에는 대기중(pending) 부분만 렌더링됩니다.
>
> 만일 주어직 표현식이 `프라미스`가 아니라면, `:then` 부분의 내용만 렌더링됩니다. 서버서이드 렌더링 중에도 마찬가지입니다.

`catch` 블록은, 프라미스가 이행하든 거절되든 따로 표시할 내용이 없다면, 생략할 수 있습니다.(또는 에러가 발생할 수 없는  조건이라던지)

```svelte
{#await promise}
	<!-- promise is pending -->
	<p>waiting for the promise to resolve...</p>
{:then value}
	<!-- promise was fulfilled -->
	<p>The value is {value}</p>
{/await}
```

대기 상태에 대해서 신경쓰지 않아도 된다면, 역시 초기 블록도 생략할 수 있습니다.

```svelte
{#await promise then value}
	<p>The value is {value}</p>
{/await}
```

비슷하게, 에러가 발생한 상태만 보여주고 싶다면, `then` 블록을 생략할 수 있습니다.

```svelte
{#await promise catch error}
	<p>The error is {error}</p>
{/await}
```

> [!NOTE] `#await`를 [`import(...)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import)와 함께 사용하면 컴포넌트를 천천히 렌더링할 수 있습니다.
>
> ```svelte
> {#await import('./Component.svelte') then { default: Component }}
> 	<Component />
> {/await}
> ```

