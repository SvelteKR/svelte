---
title: Introduction
---

Svelte 참조 문서에 오신 것을 환영합니다! 이 문서는 이미 Svelte에 어느 정도 익숙한 사람들을 위한 자료로, Svelte을 사용하는 방법에 대해 더 자세히 알고 싶은 분들을 위한 문서입니다.

아직 Svelte에 익숙하지 않다면, 이 문서를 참고하기 전에 [대화식 튜토리얼](https://learn.svelte.dev)이나 [예제](/examples)를 살펴보시는 것이 좋습니다. [REPL](/repl)을 사용하여 온라인에서 Svelte를 시험해볼 수도 있습니다. 또는 보다 다양한 기능을 제공하는 환경으로 [StackBlitz](https://sveltekit.new)에서 Svelte를 시험해볼 수도 있습니다.

## 프로젝트 시작하기

Svelte 팀에서 공식적으로 제공하는 어플리케이션 프레임워크인 [SvelteKit](https://kit.svelte.dev/)을 사용하는 것을 권장합니다.

```
npm create svelte@latest myapp
cd myapp
npm install
npm run dev
```

SvelteKit은 [Svelte컴파일러](https://www.npmjs.com/package/svelte)를 호출하여 `.svelte` 파일을 DOM을 생성하는 `.js` 파일과 스타일을 적용하는 `.css` 파일로 변환하는 작업을 처리합니다. 또한 개발 서버, 라우팅, 배포, SSR 지원과 같은 웹 애플리케이션 구축에 필요한 모든 구성 요소를 제공합니다. [SvelteKit](https://kit.svelte.dev/)은 코드를 빌드하기 위해 [Vite](https://vitejs.dev/)를 사용합니다.

### SvelteKit의 대안

만약 어떤 이유로 인해 SvelteKit을 사용하지 않고 싶다면, `npm init vite`를 실행하고 `svelte` 옵션을 선택하여 Svelte를 Vite와 함께 (SvelteKit 없이) 사용할 수도 있습니다. 이렇게 하면 `npm run build` 를 통해 HTML, JS 및 CSS 파일이 dist 디렉토리 내에 생성됩니다. 대부분의 경우, 라우팅 라이브러리를 선택해야 할 필요도 있을 것입니다. [라우팅 라이브러리 선택](/faq#is-there-a-router)을 참고하시기 바랍니다.

또한, Svelte 컴파일을 처리하기 위한 [번들링 플러그인](https://sveltesociety.dev/tools#bundling)들이 있습니다. 이런 플러그인들은 HTML에 들어갈 `.js` 와 `.css` 를 생성해 주지만 대부분 SSR을 지원하지는 않습니다.

## 에디터 도구

Svelte 팀은 [VS Code extension](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode)을 유지 관리하며, 다른 [에디터](https://sveltesociety.dev/tools#editor-support) 및 도구와의 통합도 가능합니다.

## 도움 받기

[Discord chatroom](https://svelte.dev/chat)에서 도움을 받는것을 망설이지 마세요! 또한 [Stack Overflow](https://stackoverflow.com/questions/tagged/svelte)에서도 답변을 찾을수 있습니다.
