---
title: Introduction
---

Svelte 참조 문서에 오신 것을 환영합니다! 이 문서는 이미 Svelte에 어느 정도 익숙한 사람들을 위한 자료로, Svelte을 사용하는 방법에 대해 더 자세히 알고 싶은 분들을 위한 것입니다.

아직 Svelte에 익숙하지 않다면, 이 문서를 참고하기 전에 [대화식 튜토리얼](https://learn.svelte.dev)이나 [예제](/examples)를 살펴보시는 것이 좋습니다. [REPL](/repl)을 사용하여 온라인에서 Svelte를 시도해볼 수도 있습니다. 또는 보다 다양한 기능을 제공하는 환경으로 [StackBlitz](https://sveltekit.new)에서 Svelte를 시도해볼 수도 있습니다.

## 프로젝트 시작하기

Svelte 팀에서 공식적으로 제공하는 응용 프로그램 프레임워크인 [SvelteKit](https://kit.svelte.dev/)을 사용하는 것을 권장합니다.

```
npm create svelte@latest myapp
cd myapp
npm install
npm run dev
```

SvelteKit은 [Svelte컴파일러](https://www.npmjs.com/package/svelte)를 호출하여 `.svelte` 파일을 DOM을 생성하는 `.js` 파일과 스타일을 적용하는 `.css` 파일로 변환하는 작업을 처리합니다. 또한 개발 서버, 라우팅, 배포, SSR 지원과 같은 웹 애플리케이션 구축에 필요한 모든 구성 요소를 제공합니다. [SvelteKit](https://kit.svelte.dev/)은 코드를 빌드하기 위해 [Vite](https://vitejs.dev/)를 사용합니다.

### SvelteKit의 대안

만약 어떤 이유로 인해 SvelteKit을 사용하지 않고 싶다면, `npm init vite`를 실행하고 `svelte` 옵션을 선택하여 Svelte를 Vite와 함께 (SvelteKit 없이) 사용할 수도 있습니다. 이렇게 하면 `npm run build` 를 통해 HTML, JS 및 CSS 파일이 dist 디렉토리 내에 생성됩니다. 대부분의 경우, 라우팅 라이브러리를 선택해야 할 필요도 있을 것입니다. [라우팅 라이브러리 선택](/faq#is-there-a-router)을 참고하시기 바랍니다.

Alternatively, there are [plugins for all the major web bundlers](https://sveltesociety.dev/tools#bundling) to handle Svelte compilation — which will output `.js` and `.css` that you can insert into your HTML — but most others won't handle SSR.

## Editor tooling

The Svelte team maintains a [VS Code extension](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode) and there are integrations with various other [editors](https://sveltesociety.dev/tools#editor-support) and tools as well.

## Getting help

Don't be shy about asking for help in the [Discord chatroom](https://svelte.dev/chat)! You can also find answers on [Stack Overflow](https://stackoverflow.com/questions/tagged/svelte).
