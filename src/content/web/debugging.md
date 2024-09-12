---
# title: Debugging Dart web apps
title: Dart 웹 앱 디버깅
# description: Learn how to debug your Dart web app.
description: Dart 웹앱을 디버깅하는 방법을 알아보세요.
---

[Dart IDE][IDE], [Dart DevTools][] 및 [Chrome DevTools][]와 같은 브라우저 도구를 사용하여, 
Dart 웹 앱을 디버깅할 수 있습니다.

* 앱의 로직을 디버깅하려면, IDE, Dart DevTools 또는 브라우저 도구를 사용하세요. 
  Dart DevTools는 Dart 코드를 검사하고, 
  자동으로 다시 로드하는 데 있어 브라우저 도구보다 더 나은 지원을 제공합니다.
* 앱의 모양(HTML/CSS)과 성능을 디버깅하려면, 
  Chrome DevTools와 같은 IDE 또는 브라우저 도구를 사용하세요.

## 개요 {:#overview}

앱을 serve하려면, `webdev serve`를 사용하여(명령줄 또는 IDE를 통해), Dart 개발 컴파일러를 시작합니다. 
Dart DevTools를 활성화하려면, `--debug` 또는 `--debug-extension` 옵션을 추가합니다. (명령줄 또는 IDE를 통해):

```console
$ webdev serve --debug
```

`webdev`의 `--debug` 플래그를 사용하여 앱을 실행하는 경우, 
<kbd>Alt</kbd>+<kbd>D</kbd>(macOS에서는 <kbd>Option</kbd>+<kbd>D</kbd>)를 눌러, 
Dart DevTools를 열 수 있습니다.

Chrome DevTools를 열려면, 
<kbd>Control</kbd>+<kbd>Shift</kbd>+<kbd>I</kbd>(또는 macOS에서는 <kbd>Command</kbd>+<kbd>Option</kbd>+<kbd>I</kbd>)를 누릅니다. 
Chrome DevTools를 사용하여 앱을 디버깅하려면, [소스 맵][source maps]을 사용하여, 
컴파일러가 생성하는 JavaScript 대신 Dart 소스 파일을 표시할 수 있습니다. 
Chrome DevTools 사용에 대한 자세한 내용은 [Chrome DevTools 문서][Chrome DevTools]를 참조하세요.

[source maps]: https://developer.chrome.com/docs/devtools/javascript/source-maps/

Dart DevTools 또는 Chrome DevTools를 사용하여 Dart 웹 앱을 디버깅하려면, 다음 소프트웨어가 필요합니다.

* [Google Chrome][]
* [Dart SDK][], 버전 2.0.0 이상.
* 다음 개발 환경 중 하나:
  * 명령줄: webdev (Dart 및 Chrome DevTools에 모두 필요) 및 devtools(Dart DevTools에 필요)와 같은 [Dart 명령줄 도구 패키지][cl-tools] 
   <br>_또는_
  * 웹 개발을 지원하는 [Dart IDE 또는 편집기][IDE].
* 디버깅할 [Dart 웹 앱][Dart web app].

[cl-tools]: #getting-command-line-tool-packages

## Dart DevTools 시작하기 {:#using-dart-devtools}

<img src="/assets/img/dart-devtools-screenshot.png" alt="DevTools">

이 섹션에서는 Dart DevTools를 사용하여 웹 앱을 디버깅하는 기본 사항을 안내합니다. 
디버깅할 준비가 된 앱이 이미 있는 경우, 
테스트 앱(1단계)을 만드는 것을 건너뛸 수 있지만, 
앱에 맞게 지침을 조정해야 합니다.

1. _선택 사항:_ [webdev 저장소][webdev repo,]를 복제하여, 
   해당 예제 앱을 사용하여 Dart DevTools를 사용할 수 있습니다.

2. _선택 사항:_ [Dart Debug Extension][]을 설치하여, 
   앱을 실행하고 이미 실행 중인 Chrome 인스턴스에서 Dart DevTools를 열 수 있습니다.

3. 앱의 최상위 디렉터리에서 `dart pub get`을 실행하여 종속성을 가져옵니다.

   ```console
   $ cd example
   $ dart pub get
   ```

4. 명령줄에서 IDE나 `webdev`를 사용하여, 디버그 모드에서 앱을 컴파일하고 제공합니다.

   :::note
   첫 번째 컴파일은 전체 앱을 컴파일해야 하기 때문에 가장 오래 걸립니다. 
   그 후, 새로 고침은 훨씬 더 빠릅니다.
   :::

   명령줄에서 webdev를 사용하는 경우, 
   사용할 명령은 이미 실행 중인 Chrome 인스턴스에서 앱과 디버거를 실행할지(또는 실행해야 할지)에 따라 달라집니다.

   * [Dart Debug Extension][]이 설치되어 있고, 기존 Chrome 인스턴스를 사용하여 디버깅하려는 경우:

     ```console
     $ webdev serve --debug-extension
     ```

   * 그렇지 않은 경우, 다음 명령을 사용하여, Chrome의 새 인스턴스를 시작하고 앱을 실행합니다.

     ```console
     $ webdev serve --debug
     ```

5. 앱이 아직 실행 중이 아니라면, Chrome 브라우저 창에서 엽니다.
   <br>
   예를 들어, 인수 없이 `webdev serve --debug-extension`을 사용하는 경우, 
   [http://127.0.0.1:8080](http://127.0.0.1:8080)을 엽니다.

6. Dart DevTools를 열어, 현재 창에서 실행 중인 앱을 디버깅합니다.

   * Dart Debug Extension이 설치되어 있고, 
     `--debug-extension` 플래그를 `webdev`에 사용한 경우, 
     브라우저 창 오른쪽 상단에 있는 Dart 로고 <img src="/assets/img/logo/dart-64.png" alt="Dart logo" class="align-baseline text-icon">를 클릭합니다.

   * `--debug` 플래그를 `webdev`에 사용한 경우, 
     <kbd>Alt</kbd>+<kbd>D</kbd>(또는 macOS에서는 <kbd>Option</kbd>+<kbd>D</kbd>)를 누릅니다.

   Dart DevTools 창이 나타나고 앱의 메인 파일에 대한 소스 코드가 표시됩니다.

1. 코드 줄 중 하나의 왼쪽을 클릭하여, 타이머 또는 이벤트 처리기 내부에 중단점을 설정합니다.
   <br>
   예를 들어, 이벤트 핸들러나 타이머 콜백 내부의 첫 번째 줄에 대한 줄 번호를 클릭합니다.

1. 함수 호출을 발생시키는 이벤트를 트리거합니다. 실행은 중단점에서 멈춥니다.

2. **Variables** 창에서, 변수 값을 검사합니다.

3. 스크립트 실행을 재개하고, 이벤트를 다시 트리거하거나 **Pause**를 누릅니다.
   실행이 다시 일시 중지됩니다.

4. **Step In**, **Step Over**, **Step Out** 버튼을 사용하여, 줄별로 코드를 단계별로 실행해 보세요.

   :::note
   Dart DevTools는 SDK 코드로 들어가지 않습니다. 
   예를 들어, `print()`를 호출할 때 **Step In**을 누르면, 
   `print()`를 구현하는 SDK 코드로 들어가지 않고, 다음 줄로 이동합니다.
   :::

5. 소스 코드를 변경하고 앱을 실행 중인 Chrome 창을 다시 로드합니다. 
   앱이 빠르게 재구축되고 다시 로드됩니다. 
   [문제 1925][issue 1925]가 수정될 때까지, 앱을 다시 로드할 때 중단점이 손실됩니다.

6. **Logging** 버튼을 클릭하여 stdout, stderr 및 시스템 로그를 확인합니다.


## 명령줄 도구 패키지 가져오기 {:#getting-command-line-tool-packages}

IDE 또는 Dart 지원 편집기 대신 명령줄을 사용하는 경우, [webdev 도구][webdev]가 필요합니다. 
Dart DevTools는 SDK에서 제공됩니다.

```console
$ dart pub global activate webdev
```

PATH 환경 변수가 올바르게 설정된 경우, 
이제 명령줄에서 다음 도구를 사용할 수 있습니다.

```console
$ webdev --help
A tool to develop Dart web projects.
...
```

PATH 설정에 대한 정보는, [`dart pub global` 문서][dart pub global documentation]를 참조하세요.

Dart SDK를 업데이트할 때마다, 도구를 다시 활성화하여 업데이트하세요.

```console
$ dart pub global activate webdev     # webdev 업데이트
```

{% include 'tools/debug-prod-js-code.md' %}

## 리소스 {:#resources}

자세한 내용은 다음을 참조하세요.

* [당신의 IDE][IDE]에 대한 문서
* [Dart DevTools 문서][Dart DevTools]
* [webdev 도구 문서][webdev]
* [webdev 패키지 문서][webdev-pkg]

[Chrome DevTools]: https://developer.chrome.com/docs/devtools/
[Dart Debug Extension]: https://chrome.google.com/webstore/detail/dart-debug-extension/eljbmlghnomdjgdjmbdekegdkbabckhm
[Dart DevTools]: /tools/dart-devtools
[IDE]: /tools#editors
[Dart SDK]: /get-dart
[Dart web app]: /web
[Google Chrome]: https://www.google.com/chrome
[issue 1925]: https://github.com/flutter/devtools/issues/1925
[JavaScript debugging reference]: https://developer.chrome.com/docs/devtools/javascript/reference/
[dart pub global documentation]: /tools/pub/cmd/pub-global
[webdev]: /tools/webdev
[webdev repo,]: {{site.repo.dart.org}}/webdev
[webdev-pkg]: {{site.pub-pkg}}/webdev
