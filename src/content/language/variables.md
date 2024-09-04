---
# title: Variables
title: 변수
# description: Learn about variables in Dart.
description: Dart의 변수에 대해 알아보세요.
prevpage:
  url: /language
  # title: Basics
  title: 기초
nextpage:
  url: /language/operators
  # title: Operators
  title: 연산자
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

다음은 변수를 생성하고 초기화하는 예입니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (var-decl)"?>
```dart
var name = 'Bob';
```

변수는 참조를 저장합니다. 
`name`이라는 변수는 "Bob"이라는 값을 가진 `String` 객체에 대한 참조를 포함합니다.

`name` 변수의 타입은 `String`으로 추론되지만, 지정하여 해당 타입을 변경할 수 있습니다. 
객체가 단일 타입으로 제한되지 않은 경우, `Object` 타입(또는 필요한 경우 `dynamic`)을 지정합니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (type-decl)"?>
```dart
Object name = 'Bob';
```

또 다른 옵션은 추론될 타입을 명시적으로 선언하는 것입니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (static-types)"?>
```dart
String name = 'Bob';
```

:::note
이 페이지는 로컬 변수에 대해, 
타입 어노테이션 대신 `var`를 사용하는 [스타일 가이드 권장 사항](/effective-dart/design#types)을 따릅니다.
:::

## 널 세이프티 (null safety) {:#null-safety}

Dart 언어는 사운드 널 세이프티(sound null safety)를 적용합니다.

널 세이프티는 `null`로 설정된 변수에 의도치 않게 액세스하여 발생하는 오류를 방지합니다. 
이 오류를 null 역참조 오류(null dereference error)라고 합니다. 
null 역참조 오류는 `null`로 평가되는 표현식에서, 속성에 액세스하거나 메서드를 호출할 때 발생합니다. 
이 규칙의 예외는 `null`이 (`toString()` 또는 `hashCode`와 같이) 속성이나 메서드를 지원하는 경우입니다. 
널 세이프티를 통해, Dart 컴파일러는 컴파일 타임에 이러한 잠재적 오류를 감지합니다.

예를 들어, `int` 변수 `i`의 절대값을 찾고 싶다고 가정해 보겠습니다. 
`i`가 `null`인 경우, `i.abs()`를 호출하면, null 역참조 오류가 발생합니다. 
다른 언어에서는, 이를 시도하면 런타임 오류가 발생할 수 있지만, Dart 컴파일러는 이러한 동작을 금지합니다. 
따라서, Dart 앱은 런타임 오류를 일으킬 수 없습니다.

널 세이프티는 세 가지 주요 변경 사항을 도입합니다.

1. 변수, 매개변수 또는 다른 관련 구성 요소에 대한 타입을 지정할 때, 
   타입이 `null`을 허용하는지 여부를 제어할 수 있습니다. 
   Null 허용을 활성화하려면, 타입 선언 끝에 `?`를 추가합니다.

   ```dart
   String? name  // Nullable 타입입니다. `null` 또는 문자열일 수 있습니다.

   String name   // null이 될 수 없는 타입입니다. `null`이 될 수 없지만, 문자열은 가능합니다.
   ```

1. 변수를 사용하기 전에 초기화해야 합니다. 
   Null 허용 변수는 기본적으로 `null`이므로, 기본적으로 초기화됩니다. 
   Dart는 null을 허용하지 않는 타입에 초기값을 설정하지 않습니다. 초기값을 설정하도록 강제합니다. 
   Dart는 초기화되지 않은 변수를 관찰하는 것을 허용하지 않습니다. 
   이렇게 하면 수신자의 타입이 `null`일 수 있지만, 
   `null`이 사용된 메서드나 속성을 지원하지 않는 속성에, 
   액세스하거나 메서드를 호출할 수 없습니다.

2. nullable 타입이 있는 표현식에서 속성에 액세스하거나 메서드를 호출할 수 없습니다. 
   (`hashCode` 또는 `toString()`와 같이) `null`이 지원하는 속성이나 메서드인 경우에도, 
   동일한 예외가 적용됩니다.

사운드 널 세이프티는 잠재적인 **런타임 오류**를 **편집 시** 분석 오류로 변경합니다. 널 세이프티는 다음 중 하나일 때 널이 아닌 변수를 플래그로 표시합니다.

* 널이 아닌 값으로 초기화되지 않음.
* `null` 값이 할당됨.

이 검사를 통해 앱을 배포하기 _전에_ 이러한 오류를 수정할 수 있습니다.

## 기본값 {:#default-value}

nullable 타입을 가진 초기화되지 않은 변수는 초기 값이 `null`입니다. 
숫자 타입의 변수조차도 처음에는 null입니다. 
숫자는 Dart의 다른 모든 것과 마찬가지로 객체이기 때문입니다.

<?code-excerpt "misc/test/language_tour/variables_test.dart (var-null-init)"?>
```dart
int? lineCount;
assert(lineCount == null);
```

:::note
프로덕션 코드는 `assert()` 호출을 무시합니다. 
반면, 개발 중에. <code>assert(<em>condition</em>)</code>는 _condition_ 이 거짓이면, 
예외를 throw합니다. 
자세한 내용은 [Assert][]를 확인하세요.
:::

널 세이프티를 사용하려면, 사용하기 전에 non-nullable 변수의 값을 초기화해야 합니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (var-ns-init)"?>
```dart
int lineCount = 0;
```

선언된 곳에서 로컬 변수를 초기화할 필요는 없지만 사용하기 전에 값을 할당해야 합니다. 
예를 들어, 다음 코드는 Dart가 `lineCount`가 `print()`에 전달될 때까지, 
null이 아님을 감지할 수 있기 때문에 유효합니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (var-ns-flow)"?>
```dart
int lineCount;

if (weLikeToCount) {
  lineCount = countLines();
} else {
  lineCount = 0;
}

print(lineCount);
```

최상위 수준 변수와 클래스 변수는 지연(lazily) 초기화됩니다. 
초기화 코드는 변수가 처음 사용될 때 실행됩니다.

## Late 변수 {:#late-variables}

`late` 수정자는 두 가지 사용 사례가 있습니다.

* 선언 후 초기화되는 null이 아닌 변수 선언.
* 변수 지연(Lazily) 초기화.

종종 Dart의 흐름 제어 분석은 null이 아닌 변수가 사용되기 전에 null이 아닌 값으로 설정되는 경우를 감지할 수 있지만, 때로는 분석이 실패합니다. 
두 가지 일반적인 사례는 최상위 변수와 인스턴스 변수입니다. Dart는 종종 설정되었는지 확인할 수 없으므로, 시도하지 않습니다.

변수가 사용되기 전에 설정되었다고 확신하지만, Dart가 동의하지 않는 경우, 
변수를 `late`로 표시하여 오류를 수정할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (var-late-top-level)" replace="/late/[!$&!]/g"?>
```dart
[!late!] String description;

void main() {
  description = 'Feijoada!';
  print(description);
}
```

:::warning 공지
`late` 변수를 초기화하지 못하면, 해당 변수가 사용될 때 런타임 오류가 발생합니다.
:::

변수를 `late`로 표시하지만 선언 시 초기화하면, 초기화자는 변수가 처음 사용될 때 실행됩니다. 
이 지연 초기화는 몇 가지 경우에 유용합니다.

* 변수가 필요하지 않을 수 있으며, 초기화하는 데 비용이 많이 듭니다.
* 인스턴스 변수를 초기화하고 있으며, 초기화자는 `this`에 액세스해야 합니다.

다음 예에서, `temperature` 변수가 전혀 사용되지 않으면, 
비용이 많이 드는 `readThermometer()` 함수가 호출되지 않습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (var-late-lazy)" replace="/late/[!$&!]/g"?>
```dart
// 이것은 이 프로그램의 readThermometer()에 대한 유일한 호출입니다.
[!late!] String temperature = readThermometer(); // 지연(Lazily) 초기화되었습니다.
```

## Final 및 const {:#final-and-const}

변수를 변경할 생각이 없다면, `var` 대신 또는 타입에 추가하여, `final` 또는 `const`를 사용하세요. 
final 변수는 한 번만 설정할 수 있습니다. 
const 변수는 컴파일 타임 상수입니다. (Const 변수는 암묵적으로 final입니다.)

:::note
[인스턴스 변수][Instance variables]는 `final`이 될 수 있지만, `const`가 될 수는 없습니다.
:::

다음은 `final` 변수를 생성하고 설정하는 예입니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (final)"?>
```dart
final name = 'Bob'; // 타입 어노테이션 없이
final String nickname = 'Bobby';
```

`final` 변수의 값은 변경할 수 없습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (cant-assign-to-final)"?>
```dart tag=fails-sa
name = 'Alice'; // 오류: final 변수는 한 번만 설정할 수 있습니다.
```

**컴파일 타임 상수**가 되기를 원하는 변수에는, `const`를 사용합니다. 
const 변수가 클래스 레벨에 있는 경우, `static const`로 표시합니다. 
변수를 선언하는 곳에서는, 숫자나 문자열 리터럴, const 변수 또는 상수 숫자에 대한 산술 연산 결과와 같은 
컴파일 타임 상수로 값을 설정합니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (const)"?>
```dart
const bar = 1000000; // 압력의 단위 (dynes/cm2)
const double atm = 1.01325 * bar; // 표준 대기
```

`const` 키워드는 상수 변수를 선언하기 위한 것만은 아닙니다. 
상수 _값_ 을 생성하고, 상수 값을 _생성_ 하는 생성자를 선언하는 데, 사용할 수도 있습니다. 
모든 변수는 상수 값을 가질 수 있습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (const-vs-final)"?>
```dart
var foo = const [];
final bar = const [];
const baz = []; // `const []`와 동일함
```

`const` 선언의 초기화 표현식에서 `const`를 생략할 수 있습니다. (위의 `baz`와 같이) 
자세한 내용은 [const를 중복해서 사용하지 마세요][DON'T use const redundantly]를 참조하세요.

`const` 값이 있던 경우에도, final이 아닌, const가 아닌 변수의 값을 변경할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (reassign-to-non-final)"?>
```dart
foo = [1, 2, 3]; // const [] 였습니다.
```

`const` 변수의 값은 변경할 수 없습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (cant-assign-to-const)"?>
```dart tag=fails-sa
baz = [42]; // 오류: 상수 변수에 값을 할당할 수 없습니다.
```

[타입 검사 및 캐스트][type checks and casts] (`is` 및 `as`), [컬렉션 `if`][collection `if`], [스프레드 연산자][spread operators] (`...` 및 `...?`)를 사용하는 상수를 정의할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/variables.dart (const-dart-25)"?>
```dart
const Object i = 3; // 여기서 i는 int 값을 갖는 const 객체입니다.
const list = [i as int]; // 타입캐스트를 사용하세요.
const map = {if (i is int) i: 'int'}; // is와 collection if를 사용합니다.
const set = {if (list is List<int>) ...list}; // ...그리고 스프레드를 사용합니다.
```

:::note
`final` 객체는 수정할 수 없지만, 필드는 변경할 수 있습니다. 
반면, `const` 객체와 필드는 변경할 수 없습니다. _변경 불가능(immutable)_ 합니다.
:::

`const`를 사용하여 상수 값을 생성하는 방법에 대한 자세한 내용은, 
[리스트][Lists] [맵][Maps] 및 [클래스][Classes]를 참조하세요.

[Assert]: /language/error-handling#assert
[Instance variables]: /language/classes#instance-variables
[DON'T use const redundantly]: /effective-dart/usage#dont-use-const-redundantly
[type checks and casts]: /language/operators#type-test-operators
[collection `if`]: /language/collections#control-flow-operators
[spread operators]: /language/collections#spread-operators
[Lists]: /language/collections#lists
[Maps]: /language/collections#maps
[Classes]: /language/classes
