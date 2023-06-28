---
title: Svelte components
---

컴포넌트는 Svelte 애플리케이션의 구성 요소입니다. 컴포넌트는 HTML의 슈퍼셋인 `.svelte` 파일로 작성됩니다. 

컴포넌트는 스크립트(script), 스타일(styles), 마크업(markup) 세 가지 섹션으로 구성되며 모두 선택 사항입니다.

```svelte
<script>
	// logic goes here
</script>

<!-- markup (zero or more items) goes here -->

<style>
	/* styles go here */
</style>
```

## &lt;script&gt;

`<script>` 블록은 컴포넌트 인스턴스가 생성될 때 실행되는 JavaScript 코드를 작성하는 곳입니다. 블록 최상위에서 선언되는 변수는 컴포넌트의 마크업 부분에 표시할수 있습니다. 변수에는 네 가지 추가 규칙이 있습니다:

### 1. `export` creates a component prop

스벨트는 변수의 선언을 _property_ 또는 _prop_ 로써 마크하기 위해 `export` 키워드를 사용합니다. 그걸로 인해서 컴포넌트를 사용할 때 선언한 변수에 접근 가능하게 됩니다. (보다 더 자세한 내용은 [attributes and props](/docs/basic-markup#attributes-and-props)를 참고).

```svelte
<script>
	export let foo;

	// 프로퍼티로써 전달받은 변수는 
	// 바로 사용할 수 있습니다
	console.log({ foo });
</script>
```

prop에는 기본값을 지정할 수 있습니다. 기본값은 컴포넌트가 인스턴스화 될 때 컴포넌트의 prop값을 지정하지 않았을 경우(또는 지정한값이 `undefined` 인 경우)에 사용됩니다.  

개발모드에서는 ([compiler options](/docs/svelte-compiler#compile)), 만약 기본값이 지정되어 있지 않고 사용할 때도 값을 지정하지 않을경우 경고가 출력됩니다. 경고를 해소하기 위해서는 초기값이 `undefined` 라도 기본값을 지정해야 합니다.

```svelte
<script>
	export let bar = 'optional default initial value';
	export let baz = undefined;
</script>
```

만약 `const`, `class` 또는 `function` 을 export 하면, 컴포넌트 외부에서 읽기전용이 됩니다. 그러나 아래와 같이 Functions는 유효한 prop값이 됩니다.

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

읽기전용 프로퍼티는 요소의 프로퍼티로써 접근이 가능하여 [`bind:this` 구문]을 사용하여 컴포넌트에 연결할수 있습니다. 

예약어도 프로퍼티의 이름으로써 사용할수 있습니다.

```svelte
<!--- file: App.svelte --->
<script>
	/** @type {string} */
	let className;

	// 예약어이지만 `class` 프로퍼티를 생성할수 있습니다.
	export { className as class };
</script>
```

### 2. Assignments are 'reactive' (할당은 '반응적' 입니다.)

컴포넌트의 상태를 변경해서 재렌더링을 트리거 하기 위해 필요한 것은 로컬에 정의한 변수에 대입하는 것 뿐입니다.

업데이트 표현식 (`count += 1`) 과 프로퍼티 할당 (`obj.x = y`) 은 같은 효과를 가집니다.

```svelte
<script>
	let count = 0;

	function handleClick() {
		// 이 함수의 호출은 마크업에서 `count`를 참조하고 있다면
    // 변경처리를 트리거합니다.
		count = count + 1;
	}
</script>
```

Svelte의 반응성은 할당에 기반하므로, `.push()`나 `.splice()`와 같은 배열 메서드를 사용해도 자동으로 변경처리가 트리거되지 않습니다. 변경처리를 트리거하기 위해서는 이후에 할당 작업이 필요합니다. 이와 관련된 자세한 내용은 [튜토리얼](https://learn.svelte.dev/tutorial/updating-arrays-and-objects)에서도 확인할 수 있습니다.

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

Svelte의 `<script>` 블록은 컴포넌트가 생성될 때에만 실행되므로, `<script>` 블록 내의 할당은 프로퍼티가 변경될 때 자동으로 다시 실행되지 않습니다. 프로퍼티의 변경을 추적하려면 다음 섹션의 예제를 참조하십시오.

```svelte
<script>
	export let person;
	// 이것은 컴포넌트가 생성될 때에만 `name`을 설정합니다.
	// `person`이 갱신될 때 `name`은 갱신되지 않습니다.
	let { name } = person;
</script>
```

### 3. `$:` marks a statement as reactive

top-level의 statement(블록이나 함수 내부가 아닌) 는 `$:` [JS 레이블 구문](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)을 접두사로 붙여 반응형으로 만듭니다. 반응형 statement는 다른 스크립트 코드가 실행된 후 혹은 컴포넌트 마크업이 렌더링되기 전에 실행되며, 이에 의존하는 값이 변경될 때마다 실행됩니다.

```svelte
<script>
	export let title;
	export let person;

	// `document.title` 은 
	// `title` prop이 변경 될때마다 변경 됩니다.
	$: document.title = title;

	$: {
		console.log(`multiple statements can be combined`);
		console.log(`the current title is ${title}`);
	}

	// 'person' 에 변경이 발생하면 `name` 이 변경됩니다. 
	$: ({ name } = person);

	// 이렇게 하지마세요. 이전줄보다 먼저 실행됩니다.
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

You can create your own stores without relying on [`svelte/store`](/docs/svelte-store), by implementing the _store contract_:

1. A store must contain a `.subscribe` method, which must accept as its argument a subscription function. This subscription function must be immediately and synchronously called with the store's current value upon calling `.subscribe`. All of a store's active subscription functions must later be synchronously called whenever the store's value changes.
2. The `.subscribe` method must return an unsubscribe function. Calling an unsubscribe function must stop its subscription, and its corresponding subscription function must not be called again by the store.
3. A store may _optionally_ contain a `.set` method, which must accept as its argument a new value for the store, and which synchronously calls all of the store's active subscription functions. Such a store is called a _writable store_.

For interoperability with RxJS Observables, the `.subscribe` method is also allowed to return an object with an `.unsubscribe` method, rather than return the unsubscription function directly. Note however that unless `.subscribe` synchronously calls the subscription (which is not required by the Observable spec), Svelte will see the value of the store as `undefined` until it does.

## &lt;script context="module"&gt;

A `<script>` tag with a `context="module"` attribute runs once when the module first evaluates, rather than for each component instance. Values declared in this block are accessible from a regular `<script>` (and the component markup) but not vice versa.

You can `export` bindings from this block, and they will become exports of the compiled module.

You cannot `export default`, since the default export is the component itself.

> Variables defined in `module` scripts are not reactive — reassigning them will not trigger a rerender even though the variable itself will update. For values shared between multiple components, consider using a [store](/docs/svelte-store).

```svelte
<script context="module">
	let totalComponents = 0;

	// the export keyword allows this function to imported with e.g.
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

## &lt;style&gt;

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
