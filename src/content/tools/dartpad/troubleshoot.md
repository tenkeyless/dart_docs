---
# title: Troubleshooting DartPad
title: DartPad 문제 해결
# description: Common problems with using DartPad
description: DartPad 사용 시 일반적인 문제
---

이 페이지에서는 [dartpad.dev]({{site.dartpad}})에서든, 내장된 DartPad가 있는 페이지에서든, 
DartPad를 사용하려고 할 때 발생할 수 있는 문제에 대한 솔루션을 설명합니다. 
DartPad 개요는 [DartPad 페이지](/tools/dartpad)를 참조하세요.

## 내장된 DartPad가 나타나지 않습니다. {:#embedded-dartpads-dont-appear}

dart.dev 홈페이지와 많은 튜토리얼에는 DartPad가 내장되어 있습니다. 
이러한 DartPad가 전혀 나타나지 않으면, 다음을 시도해 보세요.

* Chrome, Edge, Firefox 또는 Safari의 최신 두 버전 중 하나를 사용하고 있는지 확인하세요. 
  DartPad는 다른 브라우저에서는 작동하지 않을 수 있으며, 
  Brave 브라우저의 기본 구성에서는 작동하지 않는 것으로 알려져 있습니다.

* 광고 차단기를 사용하고 있다면, 비활성화하세요.

* 타사 추적 쿠키를 비활성화했는지 확인하세요. ([Chrome 지침][chrome-cookies])
  쿠키를 비활성화한 경우, 설정을 변경하여 **dartpad.dev에 대한 쿠키 허용**으로 변경하세요. 
  [Dart cheatsheet](/resources/dart-cheatsheet) 또는 [implicit animations codelab]({{site.flutter-docs}}/codelabs/implicit-animations)와 같이 내장된 DartPad를 사용하는 경우, 
  내장된 사이트 도메인에 대한 쿠키도 허용해야 할 수 있습니다.
  (이 경우 각각 **dart.dev** 및 **docs.flutter.dev**)

* 내장된 DartPad가 포함된 페이지를 반복해서 다시 로드하면, 
  [GitHub 속도 제한][GitHub rate limiting.]이 발생할 수 있습니다. 
  60분 이내에 페이지를 다시 로드하고, 내장된 DartPad에서 코드를 볼 수 있어야 합니다.

DartPad는 쿠키를 사용하지 않지만 로컬 스토리지에 의존하며, 
쿠키가 비활성화되면 브라우저가 일반적으로 로컬 스토리지를 비활성화합니다.

## DartPad 외부에서는 코드가 작동하지 않습니다. {:#code-doesnt-work-outside-dartpad}

DartPad에서 다른 환경으로 코드를 복사하면, 코드가 성공적으로 실행되지 않을 수 있습니다. 
다음은 몇 가지 가능한 원인과 해결 방법입니다.

* Dart에서 import 한 라이브러리를 찾을 수 없는 경우, 
  필요한 모든 패키지 종속성을 추가했는지 확인하세요. 
  DartPad에는 기본적으로 많은 기본 제공 패키지가 포함되어 있지만, 
  자체 프로젝트에서는 종속된 패키지를 명시적으로 나열해야 합니다. 
  DartPad에 포함된 패키지를 보려면 [dartpad.dev]({{site.dartpad}})로 이동하여, 
  창 오른쪽 하단에 있는 **i** 아이콘을 클릭하세요. 
  자체 프로젝트에 패키지 종속성을 추가하는 방법에 대한 자세한 내용은, 
  [`dart pub add`](/tools/pub/cmd/pub-add)에 대한 문서를 참조하세요.

* Dart에서 특정 메서드나 속성을 찾을 수 없는 경우, 연습과 함께 제공된 포함된 코드를 확인하세요.

* 코드에 다른 컴파일 오류가 있는 경우, 최신 stable 버전의 Dart SDK를 사용하고 있는지 확인하세요. 
  DartPad와 임베디드 샘플은 일반적으로 SDK의 최신 stable 릴리스를 사용하며, 
  이전 버전에는 필요한 언어 또는 라이브러리 기능이 없을 수 있습니다. 
  Dart SDK를 업데이트하는 방법을 알아보려면, [Dart SDK 받기](/get-dart)를 참조하세요.

* 웹 앱을 만드는 경우, 적절한 프로젝트 설정이 있는지 확인하세요. 
  DartPad는 Dart 코드를 실행하는 데 필요한 모든 마크업을 표시하지 않습니다. 
  Dart로 웹 앱을 만들기 시작하려면, [Dart로 웹 앱 빌드](/web/get-started)를 확인하세요.

## 이전에 작동하던 코드에 이제 오류가 있습니다. {:#previously-working-code-now-has-errors}

코드가 최신 stable Dart 릴리스에서 더 이상 지원되지 않는 언어 또는 라이브러리 기능을 사용하는 경우, 
해당 코드는 더 이상 오류 없이 분석하거나 실행되지 않을 수 있습니다.

* 가능하다면 DartPad가 **stable** 및 **베타** 채널만 지원하므로, 
  최신 Dart 릴리스에서 작동하도록 코드를 업데이트하세요.
* 코드가 이전 Dart 릴리스에서 작동한 경우 해당 버전의 Dart를 로컬에서 사용할 수 있습니다. 
  특정 버전의 Dart를 설치하는 방법을 알아보려면, [Dart SDK 받기](/get-dart)를 확인하세요.

## DartPad가 중국에서 작동하지 않습니다 {:#dartpad-doesnt-work-in-china}
  
[dartpad.cn](https://dartpad.cn)을 시도해 보세요.

## 기타 문제 {:#other-issues}

DartPad 사용 시 다른 문제가 발생하는 경우, [GitHub에 이슈를 생성하세요][new-issue].

[GitHub rate limiting.]: https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting
[browser]: /resources/faq#q-what-browsers-do-you-support-as-javascript-compilation-targets
[chrome-cookies]: https://support.google.com/chrome/answer/95647
[new-issue]: {{site.repo.dart.org}}/dart-pad/issues/new
