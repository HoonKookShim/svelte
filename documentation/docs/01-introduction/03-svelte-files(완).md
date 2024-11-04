---
title: .svelte files
---

컴포넌트란, 스벨트 어플리케이션들의 기본 단위입니다. 컴포넌트는 HTML의 상위 버전 문법을 사용해서 `.svelte` 파일에 작성합니다.

세 부분 — script, styles and markup — 모두 선택적으로, 필요한 것 외에는 없어도 상관 없습니다.

<!-- prettier-ignore -->
```svelte
/// file: MyComponent.svelte
<script module>
	// module-level logic goes here
	// (you will rarely use this)
</script>

<script>
	// instance-level logic goes here
</script>

<!-- markup (zero or more items) goes here -->

<style>
	/* styles go here */
</style>
```

## `<script>`

`<script>` 블록은 자바스크립트(또는, `lang="ts"` 속성을 붙인 경우에는 타입스크립트)를 포함하며 컴포넌트의 인스턴스가 생성될 때 실행됩니다. 최상위에 선언된 변수(또는 import된 변수)들은 컴포넌트의 마크업 부분에서 참조할 수 있습니다.

보통 자바스크립트에 추가해서, _runes_ 을 이용해서 [component props]($props)을 선언할 수 있고, 컴포넌트에 반응성을 줄 수 있습니다. '룬'은 다음 섹션에서 다룹니다.

<!-- TODO describe behaviour of `export` -->

## `<script module>`

`module` 속성과 함께 사용한 `<script>` 태그는 각 컴포넌트 인스턴스마다 실행되는 것이 아니라 모듈이 처음 평가될 때 딱 한 번만 실행됩니다. 이 블록 안에 선언된 변수들은 컴포넌트의 다른 곳 아무데서나 참조할 수 있지만, 반대 방향으로는 불가능합니다.

```svelte
<script module>
	let total = 0;
</script>

<script>
	total += 1;
	console.log(`instantiated ${total} times`);
</script>
```

이 블록에서 바인딩들을 `export`할 수 있는데, 그러면 컴파일된 모듈의 export가 됩니다. 참고로 `export default`는 쓸 수 없는데, default export되는 것은 컴포넌트 자체이기 때문입니다.

> [!NOTE] 만일 타입스크립트를 사용하면서 `module` 블록의 export들을 `.ts` 파일 안에서 import하려고 한다면, 에디터 설정에서 타입스크립트가 그들을 알고 있다고 설정되어 있는지 확인해야 합니다. 이것은 저희가 제공하는 VS Code 확장과 IntelliJ 플러그인에는 구현되어 있지만, 그 외의 경우에는 아마도 [TypeScript editor plugin](https://www.npmjs.com/package/typescript-svelte-plugin)가 필요할 것입니다.

> [!LEGACY]
> 스벨트 4에서는, `<script context="module">`구문으로 이 스크립트 태그를 생성했습니다.

## `<style>`

`<style>` 블록 안의 CSS 들은 그 범위가 해당 컴포넌트 내로 한정됩니다.

```svelte
<style>
	p {
		/* this will only affect <p> elements in this component */
		color: burlywood;
	}
</style>
```

더 자세한 정보를 보려면, [styling](scoped-styles)부분을 참고하시기 바랍니다.
