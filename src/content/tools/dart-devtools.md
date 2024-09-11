---
title: Dart DevTools
# description: A suite of debugging and performance tools.
description: 디버깅 및 성능 도구 모음입니다.
---

Dart DevTools는 Dart와 Flutter를 위한 디버깅 및 성능 도구 모음입니다. 
이러한 도구는 `dart` 도구의 일부로 배포되며, 
IDE, `dart run`, `webdev`와 같은 도구와 상호 작용합니다.

<img src="/assets/img/tools/devtools.png" width="800" alt="Screenshot of DevTools' Memory page">

다음 표는 일반적인 Dart 앱 타입에서 사용할 수 있는 도구를 보여줍니다.

{% assign y = '<span class="material-symbols user-select-none" title="Supported" aria-label="Supported">done</span>' %}
{% assign b = '&nbsp;' %}
{% assign na = '&nbsp;' %}
{% comment %}
  Considered using this instead:
  assign b = '<span class="material-symbols" title="use browser tools instead">web</span>'
{% endcomment %}

| Tool                  | [Flutter 모바일 또는 데스크탑][Flutter devtools] | [Flutter 웹][Flutter devtools] | [기타 웹][Other web] | [명령줄][Command-line] |
|-----------------------|:---------------------------------------------:|:-------------------------------:|:-------------:|:----------------:|
| [디버거][Debugger]          |                     {{y}}                     |              {{y}}              |     {{y}}     |      {{y}}       |
| [로깅 뷰][Logging view]        |                     {{y}}                     |              {{y}}              |     {{y}}     |      {{y}}       |
| [앱 크기 툴][App size tool]     |                     {{y}}                     |                                 |               |      {{y}}       |
| [CPU 프로파일러][CPU profiler]      |                     {{y}}                     |                                 |               |      {{y}}       |
| [메모리 뷰][Memory view]       |                     {{y}}                     |                                 |               |      {{y}}       |
| [네트워크 뷰][Network view]      |                     {{y}}                     |                                 |               |      {{y}}       |
| [성능 뷰][Performance view]  |                     {{y}}                     |                                 |               |      {{y}}       |
| [Flutter 검사기][Flutter inspector] |                     {{y}}                     |              {{y}}              |               |                  |

{:.table .table-striped .nowrap}

각 앱 타입(예: 명령줄 앱)에서 Dart DevTools를 사용하는 방법에 대한 자세한 내용은, 
맨 위 행에서 앱 타입을 클릭합니다. 
개별 도구(예: 디버거)에 대한 자세한 내용은, 왼쪽 열에서 도구 이름을 클릭합니다.

표에서 볼 수 있듯이, 디버거와 로깅 뷰는 모든 앱 타입에서 사용할 수 있는 Dart DevTools의 유일한 부분입니다. 
웹 앱은 타임라인, 메모리 및 성능 뷰를 사용할 수 없습니다. 
대신 [Chrome DevTools][]와 같은 브라우저 도구를 사용할 수 있습니다. 
Flutter 검사기는 Flutter 앱에서만 작동합니다. 
다른 웹 앱은 Chrome DevTools와 같은 브라우저 도구를 사용해야 합니다.


## 명령줄 앱과 함께 DevTools 사용 {:#using-devtools-with-a-command-line-app}

DevTools를 사용하면 소스 레벨 디버깅을 수행하거나, 
실행 중인 명령줄 앱에 대한 일반 로그 및 진단 정보를 볼 수 있습니다.


### 1. 대상 앱 시작 {:#1-start-the-target-app}

`dart run --observe` 명령을 사용하여, 
디버깅하거나 관찰하려는 Dart 명령줄 앱의 메인 파일을 실행합니다. 
선택적으로 `--pause-isolates-on-start`를 추가하면, 
스크립트 시작 시 실행이 자동으로 중단됩니다.

```console
$ cd path/to/dart/app
$ dart run --pause-isolates-on-start --observe main.dart

The Dart VM service is listening on http://127.0.0.1:8181/afZySiNbDPg=/
The Dart DevTools debugger and profiler is available at: http://127.0.0.1:8181/afZySiNbDPg=/devtools/#/?uri=ws%3A%2F%2F127.0.0.1%3A8181%2FafZySiNbDPg%3D%2Fws
```

**Dart DevTools 디버거 및 프로파일러** URL을 기록해 두세요. 다음 단계에서 필요합니다.

:::important
이 URL에는 보안 토큰이 포함되어 있으며, 앱을 실행할 때마다 다릅니다. 
앱을 중지하고 다시 실행하면, 새 URL로 DevTools에 연결해야 합니다.
:::

### 2. DevTools를 열고 대상 앱에 연결합니다. {:#2-open-devtools-and-connect-to-the-target-app}

**Dart DevTools 디버거 및 프로파일러** URL을 복사하여, Chrome 브라우저 창의 주소창에 붙여넣습니다.

Chrome에서 해당 URL을 방문하면, 
Dart DevTools UI가 나타나 대상 앱에 대한 정보를 표시합니다. 
**Debugger**를 클릭하여 앱 디버깅을 시작합니다.


## Flutter 앱과 함께 DevTools 사용 {:#using-devtools-with-a-flutter-app}

웹을 포함한 모든 플랫폼의 Flutter 앱에서 DevTools를 사용하는 방법에 대한 자세한 내용은, 
[flutter.dev에 대한 DevTools 문서][Flutter devtools]를 참조하세요.

## Flutter가 아닌 웹 앱에서 DevTools 사용 {:#using-devtools-with-a-non-flutter-web-app}

Dart DevTools를 사용할 수 있도록 웹 앱을 시작하려면, 
`webdev serve` 명령을 `--debug` 또는 `--debug-extension` 플래그와 함께 사용합니다.

```console
$ webdev serve --debug
```

자세한 내용은 [Dart 웹앱 디버깅][Debugging Dart web apps]을 참조하세요.

[App size tool]: {{site.flutter-docs}}/tools/devtools/app-size
[Chrome DevTools.]: https://developer.chrome.com/docs/devtools/
[Command-line]: #using-devtools-with-a-command-line-app
[CPU profiler]: {{site.flutter-docs}}/tools/devtools/cpu-profiler
[Debugger]: {{site.flutter-docs}}/tools/devtools/debugger
[Debugging Dart web apps]: /web/debugging
[Flutter inspector]: {{site.flutter-docs}}/tools/devtools/inspector
[Flutter devtools]: {{site.flutter-docs}}/tools/devtools/overview
[Logging view]: {{site.flutter-docs}}/tools/devtools/logging
[Memory view]: {{site.flutter-docs}}/tools/devtools/memory
[Network view]: {{site.flutter-docs}}/tools/devtools/network
[Other web]: #using-devtools-with-a-non-flutter-web-app
[Performance view]: {{site.flutter-docs}}/tools/devtools/performance
[Timeline view]: {{site.flutter-docs}}/tools/devtools/timeline
