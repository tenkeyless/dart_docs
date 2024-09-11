---
# title: Tools
title: 도구
# description: The tools that support the Dart language.
description: Dart 언어를 지원하는 도구.
---

앱을 만들 준비가 되면 앱 타입에 맞는 SDK와 도구를 받으세요. 
어떤 도구가 필요한지 잘 모르겠다면, **Flutter SDK를 받으세요.**

| 앱 타입                  | 시작하기 지침                                     | 도구 정보                                           |
|---------------------------|--------------------------------------------------------------|------------------------------------------------------------|
| Flutter (모바일 및 기타) | [Flutter 설치]({{site.flutter-docs}}/get-started/install) | [Flutter 도구]({{site.flutter-docs}}/using-ide)           |
| (Flutter가 아닌) 웹 앱     | [Dart SDK 설치](/tools/sdk)                           | [일반 목적 도구][General-purpose tools] 및 [웹 도구](#web)            |
| 서버 또는 명령줄    | [Dart SDK 설치](/tools/sdk)                           | [일반 목적 도구][General-purpose tools] 및 [특수 도구](#server) |

{:.table .table-striped}

[General-purpose tools]: #general-purpose-tools

:::note
  Flutter SDK에는 전체 Dart SDK가 포함되어 있습니다.
:::

## 일반 목적 도구 {:#general-purpose-tools}

다음 도구는 모든 플랫폼에서 Dart 언어를 지원합니다.

* [DartPad](#dartpad)
* [IDE 및 편집기](#editors)
* [명령줄 도구](#cli)


### DartPad {:#dartpad}

<img src="/assets/img/dartpad-hello.png" alt="DartPad Hello World" width="200px" align="right" />

[DartPad](/tools/dartpad)는, Dart 구문을 배우고 Dart 언어 기능을 실험할 수 있는, 
훌륭한, 다운로드가 필요 없는 방법입니다. 
`dart:io`와 같은 VM 라이브러리를 제외한, Dart의 코어 라이브러리를 지원합니다.


<a id="ides-and-editors"></a>
### IDE 및 편집기 {:#editors}

일반적으로 사용되는 이러한 IDE에는 Dart 플러그인이 있습니다.

<ul class="cols2">
<li>
<img src="/assets/img/tools/android_studio.svg" class="list-image" alt="Android Studio logo">
<a href="/tools/jetbrains-plugin"><b>Android Studio</b></a>
</li>
<li>
<img src="/assets/img/tools/intellij-idea.svg" class="list-image" alt="IntelliJ logo">
<a href="/tools/jetbrains-plugin"><b>IntelliJ IDEA<br>
(및 기타 JetBrains IDE)</b></a>
</li>
<li>
<img src="/assets/img/tools/vscode.svg" class="list-image" alt="Visual Studio Code logo">
<a href="/tools/vs-code"><b>Visual Studio Code</b></a>
</li>
</ul>

Dart 커뮤니티 덕분에, 다음에서도 Dart 플러그인을 사용할 수 있습니다.

<ul class="cols2">
<li>
<img src="/assets/img/tools/emacs.png" alt="Emacs logo" class="list-image">
<a href="https://github.com/nex3/dart-mode"><b>Emacs</b></a>
</li>
<li>
<img src="/assets/img/tools/vim.png" alt="Vim logo" class="list-image">
<a href="{{site.repo.dart.org}}/dart-vim-plugin"><b>Vim</b></a>
</li>
<li>
<img src="/assets/img/tools/eclipse-logo.png" alt="Eclipse logo" class="list-image">
<a href="https://github.com/eclipse/dartboard"><b>Eclipse</b></a>
</li>
</ul>

[LSP 지원 편집기][LSP-capable editors]에는 특정 Dart 확장 기능이 없는, 
[언어 서버 프로토콜(Language Server Protocol, LSP) 구현][LSP]도 사용할 수 있습니다.

[LSP]: {{site.repo.dart.sdk}}/blob/main/pkg/analysis_server/tool/lsp_spec/README.md
[LSP-capable editors]: https://microsoft.github.io/language-server-protocol/implementors/tools/

### 명령줄 도구 {:#cli}

Dart SDK에는 다음과 같은 범용 `dart` 도구가 포함되어 있습니다.

[`dart`](/tools/dart-tool)
: Dart 코드를 만들고, 포맷하고, 분석하고, 테스트하고, 문서화하고, 컴파일하고, 
  실행하고, [pub 패키지 관리자](/guides/packages)와 작업하기 위한 명령줄 인터페이스(CLI).


### 디버깅 {:#debugging}

[Dart DevTools](/tools/dart-devtools)
: 디버깅 및 성능 도구 모음입니다.


## 웹 앱 개발 도구 {:#web}

다음 도구는 웹 앱 개발을 지원합니다.

[`webdev`](/tools/webdev)
: Dart 웹 앱을 빌드하고 제공하는 CLI입니다.

## 명령줄 앱 및 서버 개발을 위한 도구 {:#server}

다음 도구는 명령줄 앱과 서버 개발 또는 실행을 지원합니다.

[`dart run`](/tools/dart-run)
: `dart run` 명령을 사용하여, 컴파일되지 않은 Dart 명령줄 앱과 일부 종류의 스냅샷을 실행합니다.

[`dartaotruntime`](/tools/dartaotruntime)
: 이 Dart 런타임을 사용하여, AOT 스냅샷을 실행합니다.

