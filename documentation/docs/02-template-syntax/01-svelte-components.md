---
title: Svelte components
---

컴포넌트는 Svelte 애플리케이션의 구성 요소입니다. 컴포넌트는 HTML의 슈퍼셋인 `.svelte` 파일로 작성됩니다.

컴포넌트는 스크립트(script), 스타일(styles), 마크업(markup) 세 가지 섹션으로 구성되며 모두 선택 사항입니다.

```svelte
<script>
	// 로직은 이쪽에
</script>

<!-- 마크업은 (한개 또는 그 이상의 아이템) 이쪽에 -->

<style>
	/* 스타일은 이쪽에 */
</style>
```

## &lt;script&gt;

`<script>` 블록은 컴포넌트 인스턴스가 생성될 때 실행되는 JavaScript 코드를 작성하는 곳입니다. 블록 최상위에서 선언되는 변수는 컴포넌트의 마크업 부분에 표시할수 있습니다. 변수에는 네 가지 추가 규칙이 있습니다:

### 1. `export` 구문은 컴포넌트의 프로퍼티를 만듭니다

스벨트는 변수의 선언을 _property_ 또는 _prop_ 로써 마크하기 위해 `export` 키워드를 사용합니다. 그걸로 인해서 컴포넌트를 사용할 때 선언한 변수에 접근 가능하게 됩니다. (보다 더 자세한 내용은 [attributes and props](/docs/basic-markup#attributes-and-props)를 참고).

```svelte
<script>
	export let foo;

	// 프로퍼티로써 전달받은 변수는
	// 바로 사용할 수 있습니다
	console.log({ foo });
</script>
```

prop(프로퍼티)에는 기본값을 지정할 수 있습니다. 기본값은 컴포넌트가 인스턴스화 될 때 컴포넌트의 prop값을 지정하지 않았을 경우(또는 지정한값이 `undefined` 인 경우)에 사용됩니다.

개발 모드에서는 ([compiler options](/docs/svelte-compiler#compile)), 만약 기본값이 지정되어 있지 않고 사용할 때도 값을 지정하지 않으면 경고가 출력됩니다. 경고를 해소하기 위해서는 초기값이 `undefined` 라도 기본값을 지정해야 합니다.

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

	// 예약어이지만 `class` 프로퍼티를 생성할수 있습니다.
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
		// 이 메서드 호출은 변경을 트리거하지 않습니다.
		arr.push(2);
		// 이 할당은 `arr` 이 마크업에서 참조되면
		// 변경이 트리거 됩니다.
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

### 3. `$:` 구문은 상태를 반응형으로 표시합니다

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

	// 이렇게 하지마세요. 이전줄보다 먼저 실행됩니다.
	let name2 = name;
</script>
```

`$:` 블록 내에서 직접적으로 표현되는 값들만이 반응형 statement가 의존하는 값이 됩니다. 예를 들어, 아래 코드에서 `total`은 `x`가 변경될 때만 변경되고, `y`는 변경되지 않습니다.

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

반응형 블록은 컴파일할 때 간단한 정적 분석을 통해 순서가 결정됩니다. 이때 컴파일러가 보는 것은 블록 자체에서 할당되어 사용되는 변수들뿐이며, 해당 블록에서 호출하는 함수안에서 사용되는 변수까지는 보지 않는다는 사실을 알아두는게 중요합니다. 따라서 다음 예시에서 `x`가 업데이트될 때 `yDependent`가 업데이트되지 않습니다.

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

`$: setY(x)` 아래에 `$: yDependent = y`라인을 옮기면, `x`가 업데이트될 때 `yDependent`도 업데이트됩니다.

만약 문장이 선언되지 않은 변수에 할당으로만 구성되어 있다면, Svelte는 대신 `let` 선언을 삽입합니다.

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

### 4. 스토어의 값에 접근 하기 위한 `$` 접두사

스토어(store) 는 간단한 스토어 계약(store contract) 을 통해 반응적인 값 접근을 가능하게 하는 객체입니다. [`svelte/store` module](/docs/svelte-store)  모듈은 이 계약(contract)을 충족하는 최소한의 스토어 구현을 포함하고 있습니다.

스토어에 대한 참조가 있을 때마다 `$` 문자를 접두사로 사용하여 컴포넌트 내에서 해당 값을 액세스할 수 있습니다. 이렇게 하면 Svelte가 접두사가 붙은 변수를 선언하고, 컴포넌트 초기화 시 스토어를 구독하고 적절한 때에 구독을 해제합니다.

`$` 접두사 변수에 대한 할당은 쓰기 가능한 스토어(writable store)여야하며, 이는 스토어의  `.set` 메서드를 호출하게 됩니다.

스토어는 컴포넌트의 최상위 레벨에서 선언되어야합니다. if 블록이나 함수 내부에는 선언할 수 없습니다.

스토어 값이 아닌 로컬 변수에는 `$` 접두사를 사용해서는 안 됩니다.

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
ストアコントラクト(store contract) を実装すれば、svelte/store に依存しない独自のストアを作ることができます:

ストアは、サブスクリプション関数を引数に取る .subscribe メソッドを持つ必要があります。このサブスクリプション関数は、.subscribe が呼ばれたら即座に・同期的に、ストアの現在の値を渡して呼び出されるようにする必要があります。ストアのアクティブなサブスクリプション関数は全て、ストアの値が変更されるたびに同期的に呼び出される必要があります。
.subscribe メソッドは、サブスクリプションを解除する関数 (unsubscribe function) を返す必要があります。サブスクリプションを解除する関数が呼ばれたら、そのサブスクリプションを停止し、それに対応するサブスクリプション関数がそのストアから呼び出されないようにする必要があります。
ストアは オプションで .set メソッドを持つことができます。.set メソッドは、引数としてストアの新しい値を受けとる必要があり、全てのアクティブなサブスクリプション関数を同期的に呼び出します。このようなストアは 書き込み可能なストア (writable store) と呼ばれます。
RxJS の Observables との相互運用性のため、.subscribe メソッドはサブスクリプションを解除する関数を直接返すのではなく、.unsubscribe メソッドを持つオブジェクトを返すこともできます。ただし、.subscribe が同期的にサブスクリプションを呼び出さない限り(これはObservableの仕様で要求されていませんが)、サブスクリプションを呼び出すまでは、Svelte がストアの値を undefined とみなすことに注意してください。

스토어 계약(store contract) 을 이용하면, [`svelte/store`](/docs/svelte-store)에 의존하지 않는 스토어를 만들 수 있습니다.

1. 스토어는 구독 함수를 인수로 받는 `.subscribe` 메서드를 포함해야합니다. 이 메서드는 구독 함수 (subscription function) 를 인수로 받아야합니다. 구독 함수는 `.subscribe`를 호출할 때 즉시 동기적으로 호출되어 스토어의 현재 값으로 호출되어야합니다. 스토어의 모든 활성화된 구독 함수들은 스토어의 값이 변경될 때마다 나중에 동기적으로 호출되어야합니다.
2. `.subscribe` 메서드는 구독 해제 함수 (unsubscribe function) 를 반환해야합니다. 구독 해제 함수를 호출하면 해당 구독이 중지되고, 해당 구독 함수는 스토어에 의해 다시는 호출되지 않습니다.
3. 스토어는 선택적으로  `.set` 메서드를 포함할 수 있습니다. 이 메서드는 스토어의 새 값을 인수로 받아야하며, 스토어의 모든 활성화된 구독 함수들을 동기적으로 호출해야합니다. 이러한 스토어를 쓰기 가능한 스토어 (writable store) 라고합니다.

RxJS Observables와의 상호 운용성을 위해 .subscribe 메서드는 구독 함수를 직접 반환하는 대신 .unsubscribe 메서드가있는 객체를 반환 할 수도 있습니다. 그러나 Observable 사양에서 요구하지 않는 구독을 동기적으로 호출하지 않는 한 (이 경우 Svelte는 스토어의 값을 undefined로 볼 수 있음), 스토어의 값을 볼 수 없습니다.

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

`<style>` 블록 내부의 CSS는 해당 컴포넌트에 대해 스코프가 지정됩니다.

이는 영향을 받는 요소에 클래스를 추가하여 작동합니다. 클래스는 컴포넌트 스타일의 해시를 기반으로 합니다 (예: `svelte-123xyz`).

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
