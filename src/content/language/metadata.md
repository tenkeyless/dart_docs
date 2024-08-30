---
# title: Metadata
title: 메타데이터
# description: Metadata and annotations in Dart.
description: Dart의 메타데이터와 어노테이션.
toc: false
prevpage:
  url: /language/comments
  # title: Comments
  title: 주석
nextpage:
  url: /language/libraries
  # title: Libraries
  title: 라이브러리
---

메타데이터를 사용하여 코드에 대한 추가 정보를 제공합니다. 
메타데이터 어노테이션은 `@` 문자로 시작하고, 
그 뒤에 컴파일 타임 상수에 대한 참조(예: `deprecated`) 또는 상수 생성자에 대한 호출이 옵니다.

모든 Dart 코드에서 네 가지 주석을 사용할 수 있습니다. 
[`@Deprecated`][], [`@deprecated`][], [`@override`][], 및 [`@pragma`][]. 
`@override` 사용의 예는 [클래스 확장][Extending a class]을 참조하세요. 
`@Deprecated` 어노테이션을 사용하는 예는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/metadata/television.dart (deprecated)" replace="/@Deprecated.*/[!$&!]/g"?>
```dart
class Television {
  /// 이것 대신 [turnOn]을 사용하여 전원을 켜세요.
  [!@Deprecated('Use turnOn instead')!]
  void activate() {
    turnOn();
  }

  /// TV의 전원을 켭니다.
  void turnOn() {...}
  // ···
}
```

메시지를 지정하고 싶지 않으면 `@deprecated`를 사용할 수 있습니다. 
그러나, 우리는 항상 `@Deprecated`로 메시지를 지정하는 것을 [권장합니다][dep-lint].

자신의 메타데이터 어노테이션을 정의할 수 있습니다. 
두 개의 인수를 취하는 `@Todo` 어노테이션을 정의하는 예는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/metadata/todo.dart"?>
```dart
class Todo {
  final String who;
  final String what;

  const Todo(this.who, this.what);
}
```

다음은 `@Todo` 어노테이션을 사용하는 예입니다.

<?code-excerpt "misc/lib/language_tour/metadata/misc.dart (usage)"?>
```dart
@Todo('Dash', 'Implement this function')
void doSomething() {
  print('Do something');
}
```

메타데이터는 라이브러리, 클래스, typedef, 타입 매개변수, 생성자, 팩토리, 함수, 필드, 매개변수 또는 변수 선언 그리고 import 또는 export 지시문 앞에 나타날 수 있습니다.

[`@Deprecated`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Deprecated-class.html
[`@deprecated`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/deprecated-constant.html
[`@override`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/override-constant.html
[`@pragma`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/pragma-class.html
[dep-lint]: /tools/linter-rules/provide_deprecation_message
[Extending a class]: /language/extend
