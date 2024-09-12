---
title: DartPad
# description: The tool that lets you interactively play with Dart in a browser.
description: 브라우저에서 Dart를 사용하여 대화형으로 플레이할 수 있는 도구입니다.
---

DartPad는 모든 최신 브라우저에서 Dart 언어로 플레이할 수 있는 [오픈 소스 도구]({{site.repo.dart.org}}/dart-pad)입니다. 
이 사이트의 많은 페이지, 특히 [튜토리얼](/tutorials)에는 [임베디드 DartPad](#embedding)가 있습니다. 
DartPad를 독립형 웹 페이지로 열려면, 
[DartPad 사이트(dartpad.dev)][DartPad]{:target="_blank" rel="noopener"}를 방문하세요.

:::tip
중국에 계시다면, [dartpad.cn](https://dartpad.cn)을 시도해 보세요.

DartPad 사용에 문제가 있는 경우, [DartPad 문제 해결 팁](/tools/dartpad/troubleshoot)을 확인하세요.
:::

Dart를 실행하도록 구성된 DartPad의 모습은 다음과 같습니다.

<img 
   src="/assets/img/dartpad-hello.png" 
   alt="Showcases what a Hello World app looks like in DartPad">


## 라이브러리 지원 {:#library-support}

DartPad는 [multi-platform][]으로 표시된 `dart:*` [코어 라이브러리](/libraries)를 지원합니다. 
Flutter 앱을 작성할 때, DartPad는 `package:flutter` 및 `dart:ui` 라이브러리도 지원합니다.

DartPad는 [지연된 로딩][deferred loading]을 지원하지 않으며, 
[현재 지원되는 패키지][currently supported packages] 외에 [pub.dev]({{site.pub}}) 패키지 저장소의 패키지를 사용하지 않습니다.

[multi-platform]: /libraries#multi-platform-libraries
[currently supported packages]: {{site.repo.dart.org}}/dart-pad/wiki/Package-and-plugin-support#currently-supported-packages

## 시작하기 {:#getting-started}

DartPad에 익숙해지려면, 몇 가지 샘플을 실행하고 간단한 명령줄 앱을 만들어 보세요.


### DartPad를 열고 샘플을 실행하세요 {:#step-1-open-and-run}

1. [DartPad][]{:target="_blank" rel="noopener"}로 이동합니다.

   왼쪽에 Dart 코드가 나타나고, 오른쪽에 출력을 위한 장소가 나타납니다.

2. 상단 메뉴의 **Samples** 버튼을 사용하여, **Sunflower**와 같은 Flutter 샘플을 선택합니다.

   렌더링된 출력이 오른쪽에 나타납니다.


### 명령줄 앱 만들기 {:#step-2-server}

간단한 명령줄 앱을 만들려면, 새 스니펫을 만드는 것으로 시작합니다.

1. **New** 버튼을 클릭하고, 현재 패드의 변경 사항을 취소할지 확인합니다.

2. Dart 로고가 있는 항목을 클릭합니다.

3. 코드를 변경합니다. 예를 들어, `main()` 함수를 변경하여 다음 코드를 포함합니다.

   ```dart
   for (final char in 'hello'.split('')) {
     print(char);
   }
   ``` 

   입력하는 동안, DartPad는 힌트, 문서, 자동 완성 제안을 표시합니다.

4. **Format** 버튼을 클릭합니다.

   DartPad는 [Dart 포매터](/tools/dart-format)를 사용하여, 
   코드에 적절한 들여쓰기, 공백, 줄 바꿈이 있는지 확인합니다.

5. 앱을 실행합니다.

6. 코드를 입력하는 동안 버그가 발생하지 않았다면, 버그를 도입해 보세요.

   예를 들어, `split`을 `spit`으로 변경하면, 창 오른쪽 하단에 경고가 표시됩니다. 
   앱을 실행하면, 콘솔에 컴파일 오류가 표시됩니다.


## Dart 버전 정보 확인 {:#checking-dart-version-info}

DartPad가 지원하는 언어 기능과 API는 DartPad가 현재 사용하는 **Dart SDK** 버전에 따라 달라집니다. 
이 SDK 버전은 DartPad의 오른쪽 하단에서 찾을 수 있습니다.

## 웹 페이지에 DartPad 임베드하기 {:#embedding}

웹 페이지 내부에 DartPad를 임베드하여 사용 사례에 맞게 커스터마이즈할 수 있습니다. 
예를 들어, [futures 튜토리얼][futures tutorial]에는 
_examples_ 및 _exercises_ 로 레이블이 지정된 여러 개의 임베드된 DartPad가 포함되어 있습니다.

DartPad 임베드에 대한 기술적 세부 정보는 [DartPad 임베딩 가이드][DartPad embedding guide.]를 참조하세요.

[DartPad]: {{site.dartpad}}
[DartPad embedding guide.]: {{site.repo.dart.org}}/dart-pad/wiki/Embedding-Guide
[deferred loading]: /language/libraries#lazily-loading-a-library
[futures tutorial]: /libraries/async/async-await
