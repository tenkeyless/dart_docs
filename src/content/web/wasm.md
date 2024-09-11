---
# title: WebAssembly (Wasm) compilation
title: WebAssembly(Wasm) 컴파일
# description: Learn how to compile your Dart web app to WebAssembly.
description: Dart 웹앱을 WebAssembly로 컴파일하는 방법을 알아보세요.
---

Dart 팀은 웹용 Dart 및 [Flutter][] 애플리케이션을 빌드할 때, 
컴파일 대상으로 [WebAssembly](https://webassembly.org/)를 추가하게 되어 기쁩니다.

WebAssembly 지원 개발은 계속 진행 중이며, 잠재적으로 자주 변경될 수 있습니다. 
최신 업데이트는 이 페이지를 다시 방문하여 확인하세요.

:::note
**Wasm에 대한 지원이 이제 stable입니다!**

Dart 웹에 대한 WebAssembly 지원은 Dart *stable* [채널](/get-dart#release-channels)에서 제공됩니다.
:::

## WebAssembly 지원 {:#webassembly-support}

현재 버전의 Dart에서 WebAssembly로 컴파일하는 데는 여러 가지 제한이 있습니다.

1. 가비지 수집([WasmGC][])을 사용하여 WebAssembly를 대상으로 하므로, 
   현재 모든 브라우저가 지원되는 것은 아닙니다. 
   현재 브라우저 리스트는 [Flutter 문서][Flutter]에서 확인할 수 있습니다.

2. 컴파일된 Wasm 출력은 현재 JavaScript 환경(예: 브라우저)을 대상으로 하므로, 
   현재 wasmtime 및 wasmer와 같은 표준 Wasm 런타임에서 실행을 지원하지 않습니다. 
   자세한 내용은 [문제 #53884]({{site.repo.dart.sdk}}/issues/53884)를 참조하세요.

3. Wasm으로 컴파일할 때, 
   Dart의 [차세대 JS 상호 운용성 메커니즘](/interop/js-interop/)만 지원됩니다.

4. 현재 `webdev` 도구에서 실행(`webdev serve`) 또는 빌드(`webdev build`)를 지원하지 않습니다. 
   아래 단계에는 임시 해결 방법이 포함되어 있습니다. 
   자세한 내용은, [webdev 문제 2206]({{site.repo.dart.org}}/webdev/issues/2296)을 참조하세요.

### 지원되는 패키지 {:#supported-packages}

Wasm 호환 패키지를 찾으려면, [pub.dev][]에서 [`wasm-ready`][] 필터를 사용하세요.

(패키지가 `dart:html`, `dart:js` 등과 같은) Wasm 호환이 아닌 라이브러리를 import 하지 않으면, '
"wasm-ready"입니다. 
허용되지 않는 라이브러리의 전체 리스트는 [JS interop 페이지](/interop/js-interop/#next-generation-js-interop)에서 확인할 수 있습니다.

[`wasm-ready`]: {{site.pub-pkg}}?q=is%3Awasm-ready
[pub.dev]: {{site.pub}}

## 웹 앱을 Wasm으로 컴파일하기 {:#compiling-to-wasm}

Dart 및 [Flutter][]에서 Wasm 컴파일러를 호출하기 위한, `dart` CLI에 대한 지원이 추가되었습니다.

```console
$ dart help compile wasm
Compile Dart to a WebAssembly/WasmGC module.

Usage: dart compile wasm [arguments] <dart entry point>
-h, --help                  Print this usage information.
-o, --output                Write the output to <file name>.
                            This can be an absolute or relative path.
-v, --verbose               Print debug output during compilation
    --enable-asserts        Enable assert statements.
-D, --define=<key=value>    Define an environment declaration. To specify multiple declarations, use multiple
                            options or use commas to separate key-value pairs.
                            For example: dart compile wasm -Da=1,b=2 main.dart
```

Wasm 컴파일은 stable에서 사용할 수 있지만, 아직 미리보기 상태입니다. 
개발자 경험을 개선하기 위해 툴링을 계속 최적화하는 동안, 
여기에 설명된 임시 단계를 따라 오늘 Dart를 Wasm으로 컴파일해 보세요.

1. 웹 앱으로 시작: `dart create -t ​​web mywebapp`

   템플릿은 Wasm을 실행하는 데 필요한 [`package:web`][]을 사용하여 작은 웹 앱을 만듭니다. 
   웹 앱이 `dart:html`에서 `package:web`으로 [마이그레이션][migrated]되었는지 확인하세요.

2. Wasm으로 새 `site` 출력 디렉터리로 컴파일: 
   
   `mywebapp$ dart compile wasm web/main.dart -o site/main.wasm`

3. 웹 파일을 복사합니다: 
   
   `cp web/index.html web/styles.css site/`

4. Wasm 코드를 로드하기 위한 JS 부트스트랩 파일을 만듭니다:

   새 파일 `site/main.dart.js`를 추가하고, 
   이 [`main.dart.js` 샘플](https://gist.github.com/mit-mit/0fcb1247a9444b0cadf611aa5fc6f32e)의 내용으로 채웁니다.

5. 출력을 서비스합니다: `dart pub global run dhttpd` ([docs][dhttpd])

이 작은 예제를 [여기](https://github.com/mit-mit/sandbox)에서 시도할 수도 있습니다.

[WasmGC]: https://developer.chrome.com/blog/wasmgc/
[Flutter]: {{site.flutter}}/wasm
[`package:web`]: {{site.pub-pkg}}/web
[`dart:js_interop`]: {{site.dart.api}}/{{site.dart.sdk.channel}}/dart-js_interop 
[migrated]: /interop/js-interop/package-web/
[dhttpd]: {{site.pub-pkg}}/dhttpd
