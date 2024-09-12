---
# title: Dart testing
title: Dart 테스트
# description: How to test Flutter, web, and VM applications.
description: Flutter, 웹, VM 애플리케이션을 테스트하는 방법.
---

앱 개발의 중요한 부분인 소프트웨어 테스트는 출시하기 전에 앱이 올바르게 작동하는지 확인하는 데 도움이 됩니다. 
이 Dart 테스트 가이드는 여러 타입의 테스트를 설명하고, 
[Flutter]({{site.flutter}}), [web](/web), [server-side 앱 및 스크립트](/server)를 테스트하는 방법을 배울 수 있는 곳을 알려줍니다.

[`dart test`][] 명령(또는 Flutter 앱의 경우 [`flutter test`][])을 사용하여, 
명령줄에서 테스트를 실행할 수 있습니다.

[`dart test`]: /tools/dart-test
[`flutter test`]: {{site.flutter-docs}}/reference/flutter-cli

## 테스트 종류 {:#kinds-of-testing}

Dart 테스트 문서는 여러분이 익숙할 수 있는 [많은 종류의 테스트](https://en.wikipedia.org/wiki/Software_testing) 중에서 세 가지 종류의 테스트에 초점을 맞춥니다. 
유닛, 구성 요소, 엔드투엔드(통합 테스트의 한 형태)입니다. 
테스트 용어는 다양하지만, Dart 기술을 사용할 때 접하게 될 용어와 개념은 다음과 같습니다.

* _유닛_ 테스트
  * _유닛_ 테스트는 함수, 메서드 또는 클래스와 같이, 
    테스트 가능한 소프트웨어의 가장 작은 부분을 확인하는 데 초점을 맞춥니다. 
  * 테스트 모음에는 다른 종류의 테스트보다 더 많은 유닛 테스트가 있어야 합니다.

* _구성 요소_ 테스트(Flutter에서는 _위젯_ 테스트라고 함)
  * _구성 요소_ 테스트는 구성 요소(일반적으로 여러 클래스로 구성됨)가 예상대로 작동하는지 확인합니다. 
  * 구성 요소 테스트는 종종 사용자 작업, 이벤트를 모방하고, 레이아웃을 수행하고, 
    자식 구성 요소를 인스턴스화할 수 있는 mock 객체를 사용해야 합니다.

* _통합_ 및 _엔드투엔드_ 테스트
  * _통합_ 및 _엔드투엔드_ 테스트는 전체 앱 또는 앱의 큰 청크의 동작을 확인합니다. 
  * 통합 테스트는 일반적으로 시뮬레이션 또는 실제 기기 또는 브라우저(웹용)에서 실행되며, 
    앱 자체와 앱을 테스트하는 테스트 앱의 두 부분으로 구성됩니다. 
  * 통합 테스트는 종종 성능을 측정하므로, 
    테스트 앱은 일반적으로 테스트되는 앱과 다른 기기 또는 OS에서 실행됩니다.

## 일반적으로 유용한 라이브러리 {:#generally-useful-libraries}

테스트는 코드가 사용될 플랫폼(예: Flutter, 웹 또는 서버 측)에 따라 부분적으로 달라지지만, 
다음 패키지는 Dart 플랫폼 전반에 유용합니다.

* [package:test]({{site.pub-pkg}}/test)<br>
  Dart에서 테스트를 작성하는 표준 방식을 제공합니다. 
  테스트 패키지를 사용하여 다음을 수행할 수 있습니다.
  * 단일 테스트 또는 테스트 그룹을 작성합니다.
  * `@TestOn` 어노테이션을 사용하여, 특정 환경에서 실행되도록 테스트를 제한합니다.
  * 동기 테스트를 작성하는 것처럼, 비동기 테스트를 작성합니다.
  * `@Tag` 주석어노테이션을 사용하여, 테스트에 태그를 지정합니다. 
    예를 들어, 태그를 정의하여 일부 테스트에 대한 커스텀 구성을 만들거나,
    일부 테스트를 완료하는 데 더 많은 시간이 필요한 것으로 식별합니다.
  * `dart_test.yaml` 파일을 만들어, 여러 파일 또는 전체 패키지에 태그가 지정된 테스트를 구성합니다.

* [package:mockito]({{site.pub-pkg}}/mockito)<br>
  고정된 시나리오에서 사용하도록 쉽게 구성할 수 있는 [mock 객체](https://en.wikipedia.org/wiki/Mock_object)를 생성하고, 
  테스트 중인 시스템이 예상한 방식으로 mock 객체와 상호 작용하는지 확인하는 방법을 제공합니다. 
  package:test와 package:mockito를 모두 사용하는 예는 
  [mockito 패키지]({{site.repo.dart.org}}/mockito)의 
  [International Space Station API 라이브러리와 해당 유닛 테스트]({{site.repo.dart.org}}/mockito)를 참조하세요.

## Flutter 테스트 {:#flutter-testing}

다음 리소스를 사용하여, Flutter 앱 테스트에 대해 자세히 알아보세요.

* [Flutter 앱 테스트]({{site.flutter-docs}}/testing)<br>
  Flutter 앱에서 유닛, 위젯 또는 통합 테스트를 수행하는 방법.
* [flutter_test]({{site.flutter-api}}/flutter/flutter_test/flutter_test-library.html)<br>
  package:test 위에 빌드된 Flutter용 테스트 라이브러리.
* [flutter_driver]({{site.flutter-api}}/flutter/flutter_driver/flutter_driver-library.html)<br>
  실제 기기와 에뮬레이터에서 Flutter 애플리케이션을 테스트하기 위한 테스트 라이브러리. (별도의 프로세스에서)
* [flutter_gallery](https://github.com/flutter/gallery)<br>
  Flutter 갤러리 예제의 소스 코드와 테스트.
* [flutter/dev/manual_tests](https://github.com/flutter/flutter/tree/master/dev/manual_tests)<br>
  Flutter SDK의 테스트에 대한 많은 예.

## 기타 도구 및 리소스 {:#other-tools-and-resources}

Dart 애플리케이션을 개발하고 디버깅하는 데 다음 리소스도 유용할 수 있습니다.

### IDE {:#ide}

디버깅에 관해서, 첫 번째 방어선은 IDE입니다. 
Dart 플러그인은 많은 [일반적으로 사용되는 IDE](/tools#editors)에 존재합니다.

### Dart DevTools {:#dart-devtools}

Dart DevTools는 Dart 및 Flutter를 위한 성능 도구 모음입니다. 
자세한 내용은 [Dart DevTools 문서](/tools/dart-devtools)를 참조하세요.

### Continuous integration {:#continuous-integration}

프로젝트를 빌드하고 모든 커밋 후에 테스트를 실행하려면, 
CI(Continuous Integration)를 사용하는 것을 고려하세요. 
GitHub의 두 가지 CI 서비스는 [GitHub Actions](https://github.com/features/actions)와 
[AppVeyor](https://www.appveyor.com/)입니다.

GitHub Actions에 대해 자세히 알아보세요.

* [GitHub Actions로 Dart 패키지 테스트][gha-article]는 
  GitHub Actions를 사용하여 Flutter 앱이나 Dart 패키지를 테스트하는 간단한 워크플로를 보여줍니다.
* Dart 팀에서 제공하는 많은 패키지는 GitHub Actions를 사용합니다. 
  예를 들어, 마크다운 패키지의 리포지토리에서 [`test-package.yml`][markdown-ci]를 참조하세요. 
  해당 리포지토리가 Travis CI에서 GitHub Actions로 어떻게 마이그레이션되었는지 보려면, 
  [PR #353]({{site.repo.dart.org}}/markdown/pull/353)을 살펴보세요.

[gha-article]: https://poetryincode.dev/testing-dart-packages-with-github-actions
[markdown-ci]: {{site.repo.dart.org}}/markdown/blob/master/.github/workflows/test-package.yml

