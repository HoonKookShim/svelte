---
title: $state
---

`$state` 룬을 사용하면 _reactive state_ 를 생성할 수 있습니다. _reactive state_ 라는 건 그 값이 비뀌면, UI도 _반응_ 해서 바뀐다는 뜻입니다.

```svelte
<script>
	let count = $state(0);
</script>

<button onclick={() => count++}>
	clicks: {count}
</button>
```

지금까지 접해봤을 다른 프레임워크들과는 다르게, 상태를 다루는 API가 존재하지 않습니다. — `count` 는 객체나 함수라기 보다는 그냥 숫자이고, 다른 변수들의 값을 변경할 때와 똑같이 그 값을 변경할 수 있습니다.

### Deep state

만일 `$state` 문이 배열이나, 아니면 '단순 객체'에 사용된다면, 그 결과로 '깊은 반응성'을 가진 _state proxy_ 가 생성됩니다. [Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)를 사용하면, 그 프로퍼티의 값을 읽거나 변경할 때 스벨트가 세부 업데이트를 시작하게 하는 코드를 실행하게 합니다. 이는 array.push(...) 같은 메소드로 값을 변경할 때도 마찬가지 입니다.

> [!NOTE] `Set` 그리고 `Map` 같은 객체들은 프록시되지 않습니다. 하지만 스벨트는 이런것과 비슷한 반응성이 있는 다양한 객체들을 제공하며, [`svelte/reactivity`](./svelte-reactivity)에서 import 할 수 있습니다.

스벨트는 배열이나 단순 객체가 아닌 무엇가를 만날 때 까지, 재귀적으로 그 안의 state(내부 프로퍼티들, 혹은 배열의 원소) 들을 프록시합니다. 아래와 같은 케이스에서는...

```js
let todos = $state([
	{
		done: false,
		text: 'add more todos'
	}
]);
```

... 개별 todo의 프로퍼티를 변경하게 되면, 해당 프로퍼티에 의존하는 UI를 업데이트 하는 과정이 시작됩니다.

```js
// @filename: ambient.d.ts
declare global {
	const todos: Array<{ done: boolean, text: string }>
}

// @filename: index.js
// ---cut---
todos[0].done = !todos[0].done;
```

만일 새 객체를 배열 안에 push 하면, 그것도 프록시됩니다.:

```js
// @filename: ambient.d.ts
declare global {
	const todos: Array<{ done: boolean, text: string }>
}

// @filename: index.js
// ---cut---
todos.push({
	done: false,
	text: 'eat lunch'
});
```

> [!NOTE] 프록시의 프로퍼티 값을 변경해도, 원본 객체의 값은 바뀌지 않습니다.

### Classes

`$state` 는 클래스의 멤버 변수에도 사용할 수 있습니다. (public 이거나 private이거나 상관 없습니다.):

```js
// @errors: 7006 2554
class Todo {
	done = $state(false);
	text = $state();

	constructor(text) {
		this.text = text;
	}

	reset() {
		this.text = '';
		this.done = false;
	}
}
```

> [!NOTE] 스벨트 컴파일러는 `done`과 `text`를 해당 값을 참조하는 클래스 프로토타입의 `get`/`set` 메소드로 변환합니다.

## `$state.raw`

객체나 배열이 '깊은 반응성'을 가지지 않게 하고 싶다면, `$state.raw`를 사용할 수 있습니다.

`$state.raw` 안에 선언된 state들은 변형될 수 없습니다.; 그 state들은 _재할당_ 만 가능합니다. 다른 말로 하면, 객체의 프로퍼티에 값을 할당하거나, 배열의 메소드인 push같은 것을 사용하기 보다는, 그 값을 변경하고 싶으면 그 객체나 배열 전체를 교체해야 한다는 겁니다.:

```js
let person = $state.raw({
	name: 'Heraclitus',
	age: 49
});

// this will have no effect
person.age += 1;

// this will work, because we're creating a new person
person = {
	name: 'Heraclitus',
	age: 50
};
```

이렇게 하면 그 값이 변하지 않을 큰 배열이나 객체를 사용할 경우 성능을 향상시킬 수 있는데, 왜냐하면 이 방법으로 그들 모두를 반응성 있게 만드는 비용을 쓰지 않을 수 있기 때문입니다. raw state가 reactive state를 포함할 수 있다는 것에 주목합니다 (예를 들어서, reactive object들을 요소들로 가지고 있는 raw array).

## `$state.snapshot`

깊은 반응성을 가진 `$state` proxy의 정적 스냅샷을 얻기 위해서는, `$state.snapshot`을 사용합니다.:

```svelte
<script>
	let counter = $state({ count: 0 });

	function onclick() {
		// Will log `{ count: ... }` rather than `Proxy { ... }`
		console.log($state.snapshot(counter));
	}
</script>
```

이것은 `structuredClone`같은, 프록시를 입력받지 않는 외부 라이브러리나 API에게 state들의 정보를 전달하고자 할 때 유용합니다.
