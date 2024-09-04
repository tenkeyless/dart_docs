---
# title: Sound null safety
title: 사운드 널 세이프티
# description: Information about Dart's null safety feature
description: Dart의 null 세이프티 기능에 대한 정보
---

Dart 언어는 사운드 널 세이프티를 강제합니다.

널 세이프티는 `null`로 설정된 변수에 의도치 않게 액세스하여 발생하는 오류를 방지합니다.

예를 들어, 메서드가 정수를 기대하지만 `null`을 수신하는 경우, 앱에서 런타임 오류가 발생합니다. 
이러한 타입의 오류, 즉 null 역참조 오류는 디버깅하기 어려울 수 있습니다.

사운드 널 세이프티를 사용하면 모든 변수에 값이 필요합니다. 
즉, Dart는 모든 변수를 _null이 불가능하다고_ 간주합니다. 
(`int i=42`와 같이) 선언된 타입의 값만 할당할 수 있습니다. 
기본 변수 타입에는 `null` 값을 할당할 수 없습니다. 
변수 타입이 `null` 값을 가질 수 있도록 지정하려면, 타입 어노테이션 뒤에 `?`를 추가합니다. `int? i` 
이러한 특정 타입은 `null` _또는_ 정의된 타입의 값을 포함할 수 있습니다.

사운드 널 세이프티는 잠재적인 **런타임 오류**를 **편집 시간** 분석 오류로 변경합니다. 
널 세이프티를 사용하면, Dart 분석기와 컴파일러는, 
null이 불가능한 변수에 다음 중 하나가 있는 경우 플래그를 지정합니다.

* null이 아닌 값으로 초기화되지 않음
* `null` 값이 할당됨

이러한 검사를 통해 앱을 배포하기 _전에_ 이러한 오류를 수정할 수 있습니다.

## 예제를 통한 소개 {:#introduction-through-examples}

null 안전성을 사용하면, 다음 코드의 어떤 변수도 `null`이 될 수 없습니다.

```dart
// null 안전성을 고려하면, 이들 중 어느 것도 null이 될 수 없습니다.
var i = 42; // int로 추론됩니다.
String name = getFileName();
final b = Foo();
```

<a id="creating-variables"></a>
변수가 `null` 값을 가질 수 있음을 나타내려면, 해당 타입 선언에 `?`를 추가하기만 하면 됩니다.

```dart
int? aNullableInt = null;
```

- 상호작용 예제를 시도하려면, 
  [Dart 치트시트](/resources/dart-cheatsheet)에서 null-safety 지향 예제 중 일부를 시도해 보세요.
- null safety에 대해 자세히 알아보려면, 
  [null safety 이해](/null-safety/understanding-null-safety)를 확인하세요.


## 널 세이프티 원칙 {:#null-safety-principles}

Dart는 다음 두 가지 핵심 설계 원칙을 사용하여, null 안전성을 지원합니다.

* **기본적으로 null이 허용되지 않음**. 
  * Dart에 변수가 null이 될 수 있다고 명시적으로 알리지 않는 한, null이 허용되지 않는 것으로 간주됩니다. 
  * 이 기본값은 API에서 null이 아닌 것이 가장 일반적인 선택이라는 연구 결과가 나온 후 선택되었습니다.

* **완전히 sound**. 
  * Dart의 null 안전성은 sound이며, 컴파일러 최적화가 가능합니다. 
  * 타입 시스템에서 무언가가 null이 아니라고 판단하면, 그것은 _절대_ null이 될 수 없습니다. 
  * 전체 프로젝트와 종속성을 null 안전성으로 마이그레이션하면, soundness의 모든 이점을 얻을 수 있습니다. 
  * 버그가 줄어들 뿐만 아니라, 바이너리가 더 작고 실행 속도가 더 빠릅니다.

## Dart 3와 널 세이프티 {:#dart-3-and-null-safety}

Dart 3에는 사운드 널 안전 기능이 내장되어 있습니다. 
Dart 3는 이 기능이 없는 코드는 실행되지 않도록 합니다.

Dart 3으로 마이그레이션하는 방법을 알아보려면, 
[Dart 3 마이그레이션 가이드](/resources/dart-3-migration)를 확인하세요. 
널 안전 기능 지원 없이 개발된 패키지는 종속성을 해결할 때 문제를 일으킵니다.

```console
$ dart pub get

Because pkg1 doesn't support null safety, version solving failed.
The lower bound of "sdk: '>=2.9.0 <3.0.0'" must be 2.12.0 or higher to enable null safety.
```

Dart 3와 호환되지 않는 라이브러리로 인해 분석 또는 컴파일 오류가 발생합니다.


```console
$ dart analyze .
Analyzing ....                         0.6s

  error • lib/pkg1.dart:1:1 • The language version must be >=2.12.0. 
  Try removing the language version override and migrating the code.
  • illegal_language_version_override
```

```console
$ dart run bin/my_app.dart
../pkg1/lib/pkg1.dart:1:1: Error: Library doesn't support null safety.
// @dart=2.9
^^^^^^^^^^^^
```

이러한 문제를 해결하려면:

1. pub.dev에서 설치한 모든 패키지의 [null safe 버전](/null-safety/migration-guide#check-dependency-status)을 확인하세요.
2. 모든 소스 코드를 [마이그레이션](#migrate)하여, 사운드 null 안전성을 사용하세요.

Dart 3는 Dart 및 Flutter의 stable 채널에서 찾을 수 있습니다. 
자세한 내용은 [다운로드 페이지][the download page]에서 확인하세요. 
Dart 3 호환성을 위해 코드를 테스트하려면, Dart 3 이상을 사용하세요.

```console
$ dart --version                     # 이것이 3.0.0-417.1.beta 이상을 보고하는지 확인하세요.
$ dart pub get / flutter pub get     # 이것은 문제없이 해결되어야 합니다.
$ dart analyze / flutter analyze     # 이것은 오류 없이 통과되어야 합니다.
```

`pub get` 단계가 실패하면, [종속성 상태][status of the dependencies]를 확인하세요.

`analyze` 단계가 실패하면, 분석기에서 나열한 문제를 해결하기 위해 코드를 업데이트하세요.

[the download page]: /get-dart/archive
[status of the dependencies]: /null-safety/migration-guide#check-dependency-status

## Dart 2.x와 널 세이프티 {:#enable-null-safety}

Dart 2.12에서 2.19까지는 null 안전성을 활성화해야 합니다. 
Dart 2.12 이전 SDK 버전에서는 null 안전성을 사용할 수 없습니다.

<a id="constraints"></a>
사운드 널 안전성을 활성화하려면, 
[SDK 제약 하한](/tools/pub/pubspec#sdk-constraints)을 [언어 버전][language version] 2.12 이상으로 설정합니다. 
예를 들어, `pubspec.yaml` 파일에는 다음과 같은 제약이 있을 수 있습니다.

```yaml
environment:
  sdk: '>=2.12.0 <3.0.0'
```

[language version]: /guides/language/evolution#language-versioning

## 기존 코드 마이그레이션 {:#migrate}

:::warning
Dart 3에서는 `dart migrate` 도구가 제거되었습니다. 
코드 마이그레이션에 도움이 필요하면, 2.19 SDK로 도구를 실행한 다음, Dart 3으로 업그레이드하세요.

도구 없이도 마이그레이션할 수 있지만, 코드를 직접 편집해야 합니다.
:::

null 안전성 지원 없이 작성된 Dart 코드는 null 안전성을 사용하도록 마이그레이션할 수 있습니다. 
Dart SDK 버전 2.12~2.19에 포함된 `dart migrate` 도구를 사용하는 것이 좋습니다.

```console
$ cd my_app
$ dart migrate
```

코드를 null 안전으로 마이그레이션하는 방법을 알아보려면, 
[마이그레이션 가이드][migration guide]를 확인하세요.

## 더 자세히 알아볼 수 있는 곳 {:#where-to-learn-more}

null 안전성에 대해 자세히 알아보려면 다음 리소스를 확인하세요.

* [null 안전성 이해][Understanding null safety]
* [기존 코드 마이그레이션 가이드][migration guide]
* [null 안전성 FAQ][Null safety FAQ]
* [null 안전성 샘플 코드][calculate_lix]

[calculate_lix]: {{site.repo.dart.org}}/samples/tree/main/null_safety/calculate_lix
[migration guide]: /null-safety/migration-guide
[Null safety FAQ]: /null-safety/faq
[Understanding null safety]: /null-safety/understanding-null-safety
[#34233]: {{site.repo.dart.sdk}}/issues/34233
[#49529]: {{site.repo.dart.sdk}}/issues/49529
[#2357]: {{site.repo.dart.lang}}/issues/2357

