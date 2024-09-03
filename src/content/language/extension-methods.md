---
# title: Extension methods
title: 확장 메서드
description: 기존 API에 추가하는 방법을 알아보세요.
prevpage:
  url: /language/enums
  title: Enums
nextpage:
  url: /language/extension-types
  # title: Extension types
  title: 확장 타입
---

확장 메서드는 기존 라이브러리에 기능을 추가합니다. 
확장 메서드를 알지 못한 채 사용할 수도 있습니다. 
예를 들어, IDE에서 코드 완성을 사용하면, 일반 메서드와 함께 확장 메서드를 제안합니다.

비디오를 시청하는 것이 학습에 도움이 된다면, 확장 메서드 개요를 확인하세요.

{% ytEmbed "D3j0OSfT9ZI", "Dart extension methods" %}

## 개요 {:#overview}

다른 사람의 API를 사용하거나 널리 사용되는 라이브러리를 구현할 때, 
API를 변경하는 것은 종종 비실용적이거나 불가능합니다. 
하지만 여전히 일부 기능을 추가하고 싶을 수 있습니다.

예를 들어, 문자열을 정수로 구문 분석하는 다음 코드를 고려해 보세요.

```dart
int.parse('42')
```

그 기능을 대신 `String`에 두는 게 더 좋을 듯합니다. 
도구를 사용하면, 더 짧고 사용하기 쉽습니다.

```dart
'42'.parseInt()
```

해당 코드를 활성화하려면, `String` 클래스의 확장을 포함하는 라이브러리를 import 할 수 있습니다.

<?code-excerpt "extension_methods/lib/string_extensions/usage_simple_extension.dart (basic)" replace="/  print/print/g"?>
```dart
import 'string_apis.dart';
// ···
print('42'.parseInt()); // 확장 메서드를 사용하세요.
```

확장은 메서드뿐만 아니라, getter, setter, operator와 같은 다른 멤버도 정의할 수 있습니다. 
또한, 확장은 이름을 가질 수 있으며, 이는 API 충돌이 발생할 경우 도움이 될 수 있습니다. 
다음은, 문자열에 대해 작동하는 확장(`NumberParsing`이라는 이름)을 사용하여, 
확장 메서드 `parseInt()`를 구현하는 방법입니다.

<?code-excerpt "extension_methods/lib/string_extensions/string_apis.dart (parseInt)"?>
```dart title="lib/string_apis.dart"
extension NumberParsing on String {
  int parseInt() {
    return int.parse(this);
  }
  // ···
}
```

다음 섹션에서는 확장 메서드를 _사용_ 하는 방법을 설명합니다. 
그 다음에는 확장 메서드 _구현_ 에 대한 섹션이 있습니다.

## 확장 메서드 사용 {:#using-extension-methods}

모든 Dart 코드와 마찬가지로, 확장 메서드는 라이브러리에 있습니다. 
확장 메서드를 사용하는 방법은 이미 살펴보았습니다. 
해당 메서드가 있는 라이브러리를 import 하고, 일반 메서드처럼 사용하면 됩니다.

<?code-excerpt "extension_methods/lib/string_extensions/usage_simple_extension.dart (import-and-use)" replace="/  print/print/g"?>
```dart
// String에 대한 확장이 포함된 라이브러리를 import 합니다.
import 'string_apis.dart';
// ···
print('42'.padLeft(5)); // String 메서드를 사용합니다.
print('42'.parseInt()); // 확장 메서드를 사용합니다.
```

확장 메서드를 사용하는 데 일반적으로 알아야 할 모든 내용입니다. 
코드를 작성할 때, 확장 메서드가 정적 타입(`동적`과 대조적으로)에 어떻게 의존하는지, 
[API 충돌](#api-conflicts)을 해결하는 방법도 알아야 할 수 있습니다.

### 정적 타입과 동적 타입 {:#static-types-and-dynamic}

`dynamic` 타입의 변수에 확장 메서드를 호출할 수 없습니다. 
예를 들어, 다음 코드는 런타임 예외를 발생시킵니다.

<?code-excerpt "extension_methods/lib/string_extensions/usage_simple_extension.dart (dynamic)" plaster="none" replace="/  \/\/ print/print/g"?>
```dart
dynamic d = '2';
print(d.parseInt()); // 런타임 오류: NoSuchMethodError
```

확장 메서드는 Dart의 타입 추론과 함께 _작동합니다_. 
다음 코드는 변수 `v`가 `String` 타입으로 추론되기 때문에 괜찮습니다.

<?code-excerpt "extension_methods/lib/string_extensions/usage_simple_extension.dart (var)"?>
```dart
var v = '2';
print(v.parseInt()); // Output: 2
```

`dynamic`이 작동하지 않는 이유는, 
확장 메서드가 수신자의 정적 타입에 대해 해결되기 때문입니다. 
확장 메서드는 정적으로 해결되므로, 정적 함수를 호출하는 것만큼 빠릅니다.

정적 타입과 `dynamic`에 대한 자세한 내용은, [Dart 타입 시스템](/language/type-system)을 참조하세요.

### API 충돌 {:#api-conflicts}

확장 멤버가 인터페이스 또는 다른 확장 멤버와 충돌하는 경우, 몇 가지 옵션이 있습니다.

한 가지 옵션은 충돌하는 확장을 import 하는 방법을 변경하여,
`show` 또는 `hide`를 사용하여 노출된 API를 제한하는 것입니다.

<?code-excerpt "extension_methods/lib/string_extensions/usage_import.dart (hide-conflicts)" replace="/  //g"?>
```dart
// String 확장 메서드 parseInt()를 정의합니다.
import 'string_apis.dart';

// parseInt()도 정의하지만, NumberParsing2를 숨기면, 해당 확장 메서드가 숨겨집니다.
import 'string_apis_2.dart' hide NumberParsing2;

// ···
// 'string_apis.dart'에 정의된, parseInt()를 사용합니다.
print('42'.parseInt());
```

또 다른 옵션은 확장을 명시적으로 적용하는 것인데, 
그러면 확장이 래퍼 클래스인 것처럼 보이는 코드가 생성됩니다.

<?code-excerpt "extension_methods/lib/string_extensions/usage_explicit.dart (conflicts-explicit)" replace="/  //g"?>
```dart
// 두 라이브러리 모두 parseInt()를 포함하는 String에 대한 확장을 정의하며, 
// 확장의 이름이 서로 다릅니다.
import 'string_apis.dart'; // NumberParsing 확장을 포함합니다.
import 'string_apis_2.dart'; // NumberParsing2 확장을 포함합니다.

// ···
// print('42'.parseInt()); // 작동하지 않습니다.
print(NumberParsing('42').parseInt());
print(NumberParsing2('42').parseInt());
```

두 확장자의 이름이 같은 경우, 접두사를 사용하여 가져와야 할 수도 있습니다.

<?code-excerpt "extension_methods/lib/string_extensions/usage_prefix.dart (conflicts-prefix)" replace="/  //g"?>
```dart
// 두 라이브러리 모두 확장 메서드 parseInt()를 포함하는 NumberParsing이라는 확장을 정의합니다. 
// 한 NumberParsing 확장('string_apis_3.dart'에 있음)도 parseNum()을 정의합니다.
import 'string_apis.dart';
import 'string_apis_3.dart' as rad;

// ···
// print('42'.parseInt()); // 작동하지 않습니다.

// string_apis.dart의 ParseNumbers 확장을 사용하세요.
print(NumberParsing('42').parseInt());

// string_apis_3.dart의 ParseNumbers 확장을 사용하세요.
print(rad.NumberParsing('42').parseInt());

// parseNum()은 string_apis_3.dart에만 있습니다.
print('42'.parseNum());
```

예에서 보듯이, 접두사를 사용하여 import 하더라도 확장 메서드를 암묵적으로 호출할 수 있습니다. 
접두사를 사용해야 하는 유일한 경우는, 확장을 명시적으로 호출할 때 이름 충돌을 피하기 위한 것입니다.


## 확장 메서드 구현 {:#implementing-extension-methods}

다음 구문을 사용하여 확장을 만드세요.

```plaintext
extension <extension name>? on <type> { // <extension-name> 은 선택 사항입니다.
  (<member definition>)* // 하나 이상의 <member definition>를 제공할 수 있습니다.
}
```

예를 들어, `String` 클래스에 확장을 구현하는 방법은 다음과 같습니다.

<?code-excerpt "extension_methods/lib/string_extensions/string_apis.dart"?>
```dart title="lib/string_apis.dart"
extension NumberParsing on String {
  int parseInt() {
    return int.parse(this);
  }

  double parseDouble() {
    return double.parse(this);
  }
}
```

확장의 멤버는 메서드, getters, setters 또는 연산자일 수 있습니다. 
확장은 정적 필드와 정적 헬퍼 메서드를 가질 수도 있습니다. 
확장 선언 외부의 정적 멤버에 액세스하려면, 
[클래스 변수 및 메서드][class variables and methods]와 같은 선언 이름을 통해 호출합니다.

[class variables and methods]: /language/classes#class-variables-and-methods

### 이름 없는 확장 {:#unnamed-extensions}

확장을 선언할 때, 이름을 생략할 수 있습니다. 
이름 없는 확장은 선언된 라이브러리에서만 볼 수 있습니다. 
이름이 없으므로, [API 충돌](#api-conflicts)을 해결하기 위해 명시적으로 적용할 수 없습니다.

<?code-excerpt "extension_methods/lib/string_extensions/string_apis_unnamed.dart (unnamed)"?>
```dart
extension on String {
  bool get isBlank => trim().isEmpty;
}
```

:::note
명명되지 않은 확장의 정적 멤버는, 확장 선언 내에서만 호출할 수 있습니다.
:::

## 제네릭 확장 구현 {:#implementing-generic-extensions}

확장에는 제네릭 타입 매개변수가 있을 수 있습니다. 
예를 들어, 다음은 getter, 연산자 및 메서드로 빌트인 `List<T>` 타입을 확장하는 코드입니다.

<?code-excerpt "extension_methods/lib/fancylist.dart (generic)"?>
```dart
extension MyFancyList<T> on List<T> {
  int get doubleLength => length * 2;
  List<T> operator -() => reversed.toList();
  List<List<T>> split(int at) => [sublist(0, at), sublist(at)];
}
```

타입 `T`는 메서드가 호출되는 리스트의 정적 타입에 따라 바인딩됩니다.
{% comment %}
TODO (https://github.com/dart-lang/site-www/issues/2171):
Add more info about generic extensions. 
For example, in the following code, `T` is `PENDING` because PENDING:

[PENDING: example]

[PENDING: Explain why it matters in normal usage.]
{% endcomment %}

## 리소스 {:#resources}

확장 메서드에 대한 자세한 내용은 다음을 참조하세요.

* [글: Dart 확장 메서드 기본][article]
* [기능 사양][specification]
* [확장 메서드 샘플][sample]

[specification]: {{site.repo.dart.lang}}/blob/main/accepted/2.7/static-extension-methods/feature-specification.md#dart-static-extension-methods-design
[article]: https://medium.com/dartlang/extension-methods-2d466cd8b308
[sample]: {{site.repo.dart.org}}/samples/tree/main/extension_methods
