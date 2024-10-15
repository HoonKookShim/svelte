---
title: Svelte components
---

Components are the building blocks of Svelte applications. They are written into `.svelte` files, using a superset of HTML.
컴포넌트란, 스벨트 어플리케이션의 기본 구성요소(건물 지을 때 벽돌과 같은) 다. 컴포넌트는 '.svelte'파일로 작성되는데, 이 포맷은 기존 HTML을 포함하는 상위 포맷이다.

All three sections — script, styles and markup — are optional.
모두 - script, styles makrup - 세  부분으로 구성되는데,  필요한 부분만 있어도 된다.(모든 부분이 다 있어야 하는 것이 아니다.)

```svelte
<script>
	// logic goes here
</script>

<!-- markup (zero or more items) goes here -->

<style>
	/* styles go here */
</style>
```

## `<script>`

A `<script>` block contains JavaScript that runs when a component instance is created. Variables declared (or imported) at the top level are 'visible' from the component's markup. There are four additional rules:
<script> 블록은 컴포넌트의 인스턴스가 생성될 때 실행되는 자바스크립트 코드를 포함한다. 최상위 레벨에서 선언된(혹은 import된) 변수들은 해당 컴포넌트의 markup 부분에서 참조가 가능하다. 여기에 4개의 추가적인 규칙이 적용된다.

### 1. 'export' 명령어는 해당 컴포넌트의 prop(프로퍼티)를 성성한다.
	
Svelte uses the `export` keyword to mark a variable declaration as a _property_ or _prop_, which means it becomes accessible to consumers of the component (see the section on  for more information).

변수를 선언할 때 'export' 명령어를 주면, 스벨트는 그 해당 변수를 _property_, 또는 _prop_ 으로 등록한다. 이는 해당 컴포넌트를 사용하는 앱의 다른 부분에서 그 변수에 접근할 수 있게 된다는 뜻이다. (자세한 내용은 [attributes and props](/docs/basic-markup#attributes-and-props) 를 참조할 것)


```svelte
<script>
	export let foo;

	// Values that are passed in as props
	// are immediately available
	console.log({ foo });
</script>
```


You can specify a default initial value for a prop. It will be used if the component's consumer doesn't specify the prop on the component (or if its initial value is `undefined`) when instantiating the component. Note that if the values of props are subsequently updated, then any prop whose value is not specified will be set to `undefined` (rather than its initial value).
prop에는 초기값을 명시해 줄 수 있습니다. 이 기본값은 해당 컴포넌트의 인스턴트가 생성될 때, 컴포넌트를 사용하는 부분이 prop의 값을 전달하지 않을 경우(혹은 undefined일 경우)에 사용됩니다. 여기서 하나 알고 있어야 할 점은, prop에 저장된 값들이 추후에 업데이트될 경우, 값이 명시되지 않은 prop들의 값은 (초기값이 아니라) undefined로 초기화됩니다.

In development mode (see the ), a warning will be printed if no default initial value is provided and the consumer does not specify a value. To squelch this warning, ensure that a default initial value is specified, even if it is `undefined`.

개발 모드에서는([compiler options](/docs/svelte-compiler#compile) 참고), 만일 초기값도 없는데, 컴포넌트 사용시에도 값을 할당해 주지 않았다면, 경고가 표시됩니다.

```svelte
<script>
	export let bar = 'optional default initial value';
	export let baz = undefined;
</script>
```

If you export a `const`, `class` or `function`, it is readonly from outside the component. Functions are valid prop values, however, as shown below.

```svelte
<!--- file: App.svelte --->
<script>
	// these are readonly
	export const thisIs = 'readonly';

	/** @param {string} name */
	export function greet(name) {
		alert(`hello ${name}!`);
	}

	// this is a prop
	export let format = (n) => n.toFixed(2);
</script>
```

Readonly props can be accessed as properties on the element, tied to the component using [`bind:this` syntax](/docs/component-directives#bind-this).

You can use reserved words as prop names.

```svelte
<!--- file: App.svelte --->
<script>
	/** @type {string} */
	let className;

	// creates a `class` property, even
	// though it is a reserved word
	export { className as class };
</script>
```

### 2. Assignments are 'reactive'

컴포넌트의 상태를 바꾸고 페이지 리렌더링을 개시시키려면, 단지 지역 선언 변수에 값을 할당해 주기만 하면 됩니다.

갱신 명령어('count +=1')과 프로퍼티 할당('obj.x = y')는 동일한 효과를 나타냅니다.

```svelte
<script>
	let count = 0;

	function handleClick() {
		// calling this function will trigger an
		// update if the markup references `count`
		count = count + 1;
	}
</script>
```

스벨트의 반응성은 할당에 기반하기 때문에, .push() 나 '.splice()'같은 배열 메소드를 사용해서는 업데이트 처리가 되지 않습니다. 그래서 업데이트 처리를 개시시키기 위해서는 추후 할당 처리를 해 줘야 합니다. 여기에 대한 내용, 또 더 자세한 내용은 [tutorial](https://learn.svelte.dev/tutorial/updating-arrays-and-objects)를 참조하시기 바랍니다.

```svelte
<script>
	let arr = [0, 1];

	function handleClick() {
		// this method call does not trigger an update
		arr.push(2);
		// this assignment will trigger an update
		// if the markup references `arr`
		arr = arr;
	}
</script>
```

Svelte's `<script>` blocks are run only when the component is created, so assignments within a `<script>` block are not automatically run again when a prop updates. If you'd like to track changes to a prop, see the next example in the following section.

```svelte
<script>
	export let person;
	// this will only set `name` on component creation
	// it will not update when `person` does
	let { name } = person;
</script>
```

### 3. `$:` marks a statement as reactive

최상위 명령구문(블록이나 함수 블록 안에 있지 않은 명령 구문)은, 그 앞에 '$'를 붙여서 반응하게 만들 수 있습니다. [JS label syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label). 반응성 구문들은, 그 그문에서 참조하는 변수의 값이 변할 때 마다, 다른 스크립트 코드들이 실행된 후, 컴포넌트의 마크업들이 렌더링되기 전에 실행됩니다.

```svelte
<script>
	export let title;
	export let person;

	// this will update `document.title` whenever
	// the `title` prop changes
	$: document.title = title;

	$: {
		console.log(`multiple statements can be combined`);
		console.log(`the current title is ${title}`);
	}

	// this will update `name` when 'person' changes
	$: ({ name } = person);

	// don't do this. it will run before the previous line
	let name2 = name;
</script>
```

Only values which directly appear within the `$:` block will become dependencies of the reactive statement. For example, in the code below `total` will only update when `x` changes, but not `y`.

```svelte
<!--- file: App.svelte --->
<script>
	let x = 0;
	let y = 0;

	/** @param {number} value */
	function yPlusAValue(value) {
		return value + y;
	}

	$: total = yPlusAValue(x);
</script>

Total: {total}
<button on:click={() => x++}> Increment X </button>

<button on:click={() => y++}> Increment Y </button>
```

It is important to note that the reactive blocks are ordered via simple static analysis at compile time, and all the compiler looks at are the variables that are assigned to and used within the block itself, not in any functions called by them. This means that `yDependent` will not be updated when `x` is updated in the following example:

```svelte
<!--- file: App.svelte --->
<script>
	let x = 0;
	let y = 0;

	/** @param {number} value */
	function setY(value) {
		y = value;
	}

	$: yDependent = y;
	$: setY(x);
</script>
```

Moving the line `$: yDependent = y` below `$: setY(x)` will cause `yDependent` to be updated when `x` is updated.

If a statement consists entirely of an assignment to an undeclared variable, Svelte will inject a `let` declaration on your behalf.

```svelte
<!--- file: App.svelte --->
<script>
	/** @type {number} */
	export let num;

	// we don't need to declare `squared` and `cubed`
	// — Svelte does it for us
	$: squared = num * num;
	$: cubed = squared * num;
</script>
```

### 4. Prefix stores with `$` to access their values

A _store_ is an object that allows reactive access to a value via a simple _store contract_. The [`svelte/store` module](/docs/svelte-store) contains minimal store implementations which fulfil this contract.

Any time you have a reference to a store, you can access its value inside a component by prefixing it with the `$` character. This causes Svelte to declare the prefixed variable, subscribe to the store at component initialization and unsubscribe when appropriate.

Assignments to `$`-prefixed variables require that the variable be a writable store, and will result in a call to the store's `.set` method.

Note that the store must be declared at the top level of the component — not inside an `if` block or a function, for example.

Local variables (that do not represent store values) must _not_ have a `$` prefix.

```svelte
<script>
	import { writable } from 'svelte/store';

	const count = writable(0);
	console.log($count); // logs 0

	count.set(1);
	console.log($count); // logs 1

	$count = 2;
	console.log($count); // logs 2
</script>
```

#### Store contract

```ts
// @noErrors
store = { subscribe: (subscription: (value: any) => void) => (() => void), set?: (value: any) => void }
```

스벨트가 제공하는  [`svelte/store`](/docs/svelte-store)에 의존하지 않고도, _store contract_만 적용하면 자신만의 스토어를 생성할 수 있습니다.

1. 스토어는 `.subscribe` 라는 메소드를 가져야 하며, 이 메소드는 매개변수로 '구독 함수'(subscription function)를 받아야 합니다. '.subscribe'함수가 호출되면 이 '구독 함수'에 스토어의 현재 값을 주어 즉시, 그리고 동기적으로 실행되어야 합니다.This subscription function must be immediately and synchronously called with the store's current value upon calling `.subscribe`. 스토어에 등록된 '구독 함수'들 모두는 스토어의 값이 변할 때 마다 동기적으로 실행되어야 합니다.All of a store's active subscription functions must later be synchronously called whenever the store's value changes.
2. '.subscribe' 함수는 '구독 해지 함수'(unsubscribe function)을 반환합니다. The `.subscribe` method must return an unsubscribe function. Calling an unsubscribe function must stop its subscription, and its corresponding subscription function must not be called again by the store.
3. 스토어는, _선택적으로_ .'set' 메소드를 가질 수 있는데, 이 함수는 스토어에 저장할 새로운 값을 매개변수로 받아서 스토어에 등록된 모든 '구독 함수'들을 동기적으로 호출하는 함수입니다. 이렇게 '.set' 메소드가 있는 스토어를 _writable store_ 라고 합니다.

For interoperability with RxJS Observables, the `.subscribe` method is also allowed to return an object with an `.unsubscribe` method, rather than return the unsubscription function directly. Note however that unless `.subscribe` synchronously calls the subscription (which is not required by the Observable spec), Svelte will see the value of the store as `undefined` until it does.


## `<script context="module">`

'context="module";이 들어간 <script> 태그 안의 코드는, 컴포넌트 인스턴스가 생성될 때 매번 실행되는게 아니라, 앱 실행시 모듈이 처음 평가받을 때 한 번만 실행됩니다. 이 블럭 안에 선언된 변수들은 일반 <script> 태그 안( 그리고 markup 부분)에서 접근이 가능하지만, 반대로는 안 됩니다.

You can `export` bindings from this block, and they will become exports of the compiled module.


컴포넌트 자체가 default export 되기 때문에, 'export default'는 쓸 수 없습니다.

> 'module' script 안에서 정의된 변수들은 반응성이 없습니다. 그 변수들에 새로운 값을 할당하면 그 값은 바뀌지만 랜더링이 다시 개시되지는 않습니다. 여러개의 컴포넌트들에서 공유하는 변수들의 경우에는 [store](/docs/svelte-store)를 사용하는 것을 고려하는 것이 좋습니다.

```svelte
<script context="module">
	let totalComponents = 0;

	// the export keyword allows this function to be imported with e.g.
	// `import Example, { alertTotal } from './Example.svelte'`
	export function alertTotal() {
		alert(totalComponents);
	}
</script>

<script>
	totalComponents += 1;
	console.log(`total number of times this component has been created: ${totalComponents}`);
</script>
```

## `<style>`

CSS inside a `<style>` block will be scoped to that component.

This works by adding a class to affected elements, which is based on a hash of the component styles (e.g. `svelte-123xyz`).

```svelte
<style>
	p {
		/* this will only affect <p> elements in this component */
		color: burlywood;
	}
</style>
```

To apply styles to a selector globally, use the `:global(...)` modifier.

```svelte
<style>
	:global(body) {
		/* this will apply to <body> */
		margin: 0;
	}

	div :global(strong) {
		/* this will apply to all <strong> elements, in any
			 component, that are inside <div> elements belonging
			 to this component */
		color: goldenrod;
	}

	p:global(.red) {
		/* this will apply to all <p> elements belonging to this
			 component with a class of red, even if class="red" does
			 not initially appear in the markup, and is instead
			 added at runtime. This is useful when the class
			 of the element is dynamically applied, for instance
			 when updating the element's classList property directly. */
	}
</style>
```

If you want to make @keyframes that are accessible globally, you need to prepend your keyframe names with `-global-`.

The `-global-` part will be removed when compiled, and the keyframe then be referenced using just `my-animation-name` elsewhere in your code.

```svelte
<style>
	@keyframes -global-my-animation-name {
		/* code goes here */
	}
</style>
```

There should only be 1 top-level `<style>` tag per component.

However, it is possible to have `<style>` tag nested inside other elements or logic blocks.

In that case, the `<style>` tag will be inserted as-is into the DOM, no scoping or processing will be done on the `<style>` tag.

```svelte
<div>
	<style>
		/* this style tag will be inserted as-is */
		div {
			/* this will apply to all `<div>` elements in the DOM */
			color: red;
		}
	</style>
</div>
```
