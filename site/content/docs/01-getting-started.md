---
title: Getting started
---

---

Svelte를 대화형 온라인 환경에서 확인해보려면 [the REPL](https://svelte.dev/repl)이나 [StackBlitz](https://node.new/svelte)를 사용해볼 수 있습니다.

로컬에서 프로젝트를 생성하려면 Svelte 팀에서 공식적으로 제공하는 애플리케이션 프레임워크인 [SvelteKit](https://kit.svelte.dev/)을 사용하는 것이 좋습니다:
```
npm create svelte@latest myapp
cd myapp
npm install
npm run dev
```

SvelteKit은 [Svelte 컴파일러](https://www.npmjs.com/package/svelte)를 이용하여 `.svelte`파일을 DOM을 구축하는 `.js`와 그것을 스타일링하는 `.css`파일로 변환합니다. 또한 개발 서버, 라우팅, 배포와 같은 웹 애플리케이션 구축에 필요한 다른 구성 요소도 제공합니다. [SvelteKit](https://kit.svelte.dev/)은 [Vite](https://vitejs.dev/)를 활용하여 코드를 빌드하고 서버 사이드 렌더링(SSR)을 처리합니다. Svelte의 컴파일을 처리하는 [모든 주요 웹 번들러용 플러그인](https://sveltesociety.dev/tools#bundling)이 있어 HTML에 삽입할`.js`와`.css`파일을 출력할 수 있지만 대부분은 SSR을 다루지는 않습니다.

만약 완전한 앱 프레임워크가 필요하지 않고 프론트엔드만으로 간단한 사이트나 앱을 만들고자 한다면, Svelte (Kit 없이)와 Vite를 함께 사용할 수도 있습니다. 이 경우 `npm init vite`명령을 실행하고 svelte 옵션을 선택합니다. 이후`npm run build`명령을 실행하면 `dist` 디렉토리 안에 HTML, JS, CSS 파일이 생성됩니다.

Svelte 팀은 [VS Code용 확장 프로그램](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode)을 유지보수하고 있으며, 다양한 다른 [에디터](https://sveltesociety.dev/tools#editor-support)와 도구와의 통합도 가능합니다.

도움이 필요한 경우 [Discord](https://svelte.dev/chat) 또는 [StackOverflow](https://stackoverflow.com/questions/tagged/svelte)에서 도움을 받을 수 있습니다.
