---
# title: Libraries & imports
title: 라이브러리 & imports
# short-title: Libraries
short-title: 라이브러리
# description: Guidance on importing and implementing libraries.
description: 라이브러리 import 및 구현에 대한 지침.
prevpage:
  url: /language/metadata
  # title: Metadata
  title: 메타데이터
nextpage:
  url: /language/keywords
  # title: Keywords
  title: 키워드
---

`import` 및 `library` 지시문(directives)은 모듈식이고 공유 가능한 코드 베이스를 만드는 데 도움이 될 수 있습니다. 
라이브러리는 API를 제공할 뿐만 아니라, 개인 정보 보호의 단위입니다. 
밑줄(`_`)로 시작하는 식별자는 라이브러리 내부에서만 볼 수 있습니다. 
*모든 Dart 파일(및 해당 부분)은 [library][]*이며, 
[`library`](#library-directive) 지시문을 사용하지 않더라도 마찬가지입니다.

라이브러리는 [패키지](/guides/packages)를 사용하여 배포될 수 있습니다.

:::note
Dart가 `public` 또는 `private`와 같은 액세스 수정자 키워드 대신, 
밑줄을 사용하는 이유에 대해 알아보려면, 
[SDK issue 33383]({{site.repo.dart.sdk}}/issues/33383)을 참조하세요.
:::

[library]: /tools/pub/glossary#library

## 라이브러리 사용 {:#using-libraries}

`import`를 사용하여 한 라이브러리의 네임스페이스가 다른 라이브러리의 범위에서 어떻게 사용되는지 지정합니다.

예를 들어, Dart 웹 앱은 일반적으로 다음과 같이 import 할 수 있는 [dart:html][] 라이브러리를 사용합니다.

<?code-excerpt "misc/test/language_tour/browser_test.dart (dart-html-import)"?>
```dart
import 'dart:html';
```

`import`에 필요한 유일한 인수는 라이브러리를 지정하는 URI입니다. 
내장 라이브러리의 경우, URI에는 특수 `dart:` 체계가 있습니다. 
다른 라이브러리의 경우, 파일 시스템 경로나 `package:` 체계를 사용할 수 있습니다. 
`package:` 체계는 pub 도구와 같은 패키지 관리자가 제공하는 라이브러리를 지정합니다. 예를 들어:

<?code-excerpt "misc/test/language_tour/browser_test.dart (package-import)"?>
```dart
import 'package:test/test.dart';
```

:::note
*URI*는 uniform resource identifier(균일 자원 식별자)를 의미합니다.
*URL*(균일 자원 로케이터, uniform resource locators)은 일반적인 종류의 URI입니다.
:::

### 라이브러리 접두사(prefix) 지정 {:#specifying-a-library-prefix}

식별자가 충돌하는 두 라이브러리를 import 하는 경우, 
하나 또는 두 라이브러리에 대한 접두사를 지정할 수 있습니다. 
예를 들어, library1과 library2가 모두 Element 클래스를 가지고 있는 경우, 
다음과 같은 코드가 있을 수 있습니다.

<?code-excerpt "misc/lib/language_tour/libraries/import_as.dart" replace="/(lib\d)\.dart/package:$1\/$&/g"?>
```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

// lib1의 Element를 사용합니다.
Element element1 = Element();

// lib2의 Element를 사용합니다.
lib2.Element element2 = lib2.Element();
```

### 라이브러리의 일부만 import 하기 {:#importing-only-part-of-a-library}

라이브러리의 일부만 사용하고 싶다면, 라이브러리를 선택적으로 import 할 수 있습니다. 예를 들어:

<?code-excerpt "misc/lib/language_tour/libraries/show_hide.dart (imports)" replace="/(lib\d)\.dart/package:$1\/$&/g"?>
```dart
// foo만 import 합니다.
import 'package:lib1/lib1.dart' show foo;

// foo를 제외한 모든 이름을 import 합니다.
import 'package:lib2/lib2.dart' hide foo;
```

#### 라이브러리 지연(Lazily) 로딩 {:#lazily-loading-a-library}

*Deferred(지연된) 로딩*(*lazy 로딩*이라고도 함)은 웹 앱이 라이브러리가 필요할 때, 
필요에 따라 라이브러리를 로드할 수 있도록 합니다. 
다음 요구 사항 중 하나 이상을 충족하려는 경우 지연된 로딩을 사용합니다.

* 웹 앱의 초기 시작 시간을 줄입니다.
* A/B 테스트를 수행합니다. 예를 들어 알고리즘의 대체 구현을 시도합니다.
* 선택적 화면 및 대화 상자와 같이 거의 사용되지 않는 기능을 로드합니다.

그렇다고 해서 Dart가 시작 시 모든 지연된 구성 요소를 로드한다는 것은 아닙니다.
웹 앱은 필요할 때 웹을 통해 지연된 구성 요소를 다운로드할 수 있습니다.

`dart` 도구는 웹 외의 대상에 대한 지연된 로딩을 지원하지 않습니다. 
Flutter 앱을 빌드하는 경우, [지연된 구성 요소][flutter-deferred]에 대한 Flutter 가이드에서 지연된 로딩 구현을 참조하세요.

[flutter-deferred]: {{site.flutter-docs}}/perf/deferred-components

라이브러리를 지연 로드하려면, 먼저 `deferred as`를 사용하여 라이브러리를 import 합니다.

<?code-excerpt "misc/lib/language_tour/libraries/greeter.dart (import)" replace="/hello\.dart/package:greetings\/$&/g"?>
```dart
import 'package:greetings/hello.dart' deferred as hello;
```

라이브러리가 필요할 때, 라이브러리 식별자를 사용하여 `loadLibrary()`를 호출합니다.

<?code-excerpt "misc/lib/language_tour/libraries/greeter.dart (load-library)"?>
```dart
Future<void> greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```

이전 코드에서, `await` 키워드는 라이브러리가 로드될 때까지 실행을 일시 중지합니다. 
`async` 및 `await`에 대한 자세한 내용은 [비동기 지원](/language/async)을 참조하세요.

라이브러리에서 `loadLibrary()`를 문제 없이 여러 번 호출할 수 있습니다. 라이브러리는 한 번만 로드됩니다.

지연된 로드를 사용할 때 다음 사항에 유의하세요.

* 지연된 라이브러리의 상수는 import 하는 파일의 상수가 아닙니다. 
  이러한 상수는 지연된 라이브러리가 로드될 때까지 존재하지 않는다는 점을 기억하세요.
* import 하는 파일에서 지연된 라이브러리의 타입을 사용할 수 없습니다. 
  대신, 인터페이스 타입을 지연된 라이브러리와 import 하는 파일에서 모두에서 import 한 라이브러리로 이동하는 것을 고려하세요.
* Dart는 <code>deferred as <em>namespace</em></code>를 사용하여, 
  정의한 네임스페이스에 `loadLibrary()`를 암묵적으로 삽입합니다. 
  `loadLibrary()` 함수는 [`Future`](/libraries/dart-async#future)를 반환합니다.

### `library` 지시문(directive) {:#library-directive}

라이브러리 레벨의 [문서 주석][doc comments] 또는 [메타데이터 어노테이션][metadata annotations]을 지정하려면, 파일 시작 부분의 `library` 선언에 첨부합니다.

<?code-excerpt "misc/lib/effective_dart/docs_good.dart (library-doc)"?>
```dart
/// 정말 훌륭한 테스트 라이브러리예요.
@TestOn('browser')
library;
```

## 라이브러리 구현 {:#implementing-libraries}

패키지를 구현하는 방법에 대한 조언은 [패키지 생성](/guides/libraries/create-packages)을 참조하세요. 
여기에는 다음이 포함됩니다.

* 라이브러리 소스 코드를 구성하는 방법.
* `export` 지시문을 사용하는 방법.
* `part` 지시문을 사용하는 경우.
* 여러 플랫폼을 지원하는 라이브러리를 구현하기 위해, 조건부 imports 및 exports를 사용하는 방법.

[dart:html]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html
[doc comments]: /effective-dart/documentation#consider-writing-a-library-level-doc-comment
[metadata annotations]: /language/metadata
