---
title: .svelte files
---

컴포넌트란, 스벨트 어플리케이션들의 기본 조각이다. They are written into `.svelte` files, using a superset of HTML.

All three sections — script, styles and markup — are optional.

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

A `<script>` block contains JavaScript (or TypeScript, when adding the `lang="ts"` attribute) that runs when a component instance is created. Variables declared (or imported) at the top level can be referenced in the component's markup.

In addition to normal JavaScript, you can use _runes_ to declare [component props]($props) and add reactivity to your component. Runes are covered in the next section.

<!-- TODO describe behaviour of `export` -->

## `<script module>`

`module` 속성과 함께 사용한 `<script>` 태그는 각 컴포넌트 인스턴스마다 실행되는 것이 아니라 모듈이 처음 평가될 때 딱 한 번만 실행된다. 이 블록 안에 선언된 변수들은 컴포넌트의 다른 곳 아무데서나 참조할 수 있지만, 반대로는 되지 않는다.

```svelte
<script module>
	let total = 0;
</script>

<script>
	total += 1;
	console.log(`instantiated ${total} times`);
</script>
```

이 블록에서 바인딩들을 `export`할 수 있고, 컴파일된 모듈에서 그것들을 export한다. `export default`는 쓸 수 없는데, 컴포넌트 자체가 default export되기 때문이다.

> [!NOTE] 만일 타입스크립트를 사용하면서 `module` 블록의 export들을 `.ts` 파일 안으로 import하려고 한다면, If you are using TypeScript and import such exports from a `module` block into a `.ts` file, 에디터 설정에서 타입스크립트가 그들(.ts 파일)을 알고 있다고 설정되어 있는지 확인하세요. make sure to have your editor setup so that TypeScript knows about them. This is the case for our VS Code extension and the IntelliJ plugin, but in other cases you might need to setup our [TypeScript editor plugin](https://www.npmjs.com/package/typescript-svelte-plugin).

> [!LEGACY]
> 스벨트 4에서는, `<script context="module">`구문으로 이 스크립트 태그를 생성했다.

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
