---
title: '{@render ...}'
---

[snippet](snippet)을 렌더링하려면, `{@render ...}` 태그를 사용한다.

```svelte
{#snippet sum(a, b)}
	<p>{a} + {b} = {a + b}</p>
{/snippet}

{@render sum(1, 2)}
{@render sum(3, 4)}
{@render sum(5, 6)}
```

표현식은 The expression can be an identifier like `sum`, 아니면 임의의 자바스크립트 표현식이 될 수도 있다.or an arbitrary JavaScript expression:

```svelte
{@render (cool ? coolSnippet : lameSnippet)()}
```

## Optional snippets

If the snippet is potentially undefined — for example, because it's an incoming prop — then you can use optional chaining to only render it when it _is_ defined:

```svelte
{@render children?.()}
```

Alternatively, use an [`{#if ...}`](if) block with an `:else` clause to render fallback content:

```svelte
{#if children}
	{@render children()}
{:else}
	<p>fallback content</p>
{/if}
```
