---
# title: Patterns
title: 패턴
# description: Summary of patterns in Dart.
description: Dart의 패턴 요약.
prevpage:
  url: /language/type-system
  # title: Type system
  title: 타입 시스템
nextpage:
  url: /language/pattern-types
  # title: Pattern types
  title: 패턴 타입
---

:::version-note
패턴에는 최소 3.0의 [언어 버전][language version]이 필요합니다.
:::

패턴은 (구문과 표현식과 같은) Dart 언어의 구문적 카테고리입니다. 
패턴은 실제 값과 일치할 수 있는 값 세트의 모양을 나타냅니다.

이 페이지에서는 다음을 설명합니다.

- 패턴의 기능.
- Dart 코드에서 패턴이 허용되는 곳.
- 패턴의 일반적인 사용 사례.

다양한 종류의 패턴에 대해 알아보려면 [패턴 타입][types] 페이지를 방문하세요.

## 패턴의 기능 {:#what-patterns-do}

일반적으로, 패턴은 패턴의 컨텍스트과 모양에 따라, 
값과 **일치(match)**하거나, 값과 **구조 분해(destructure)**하거나, 둘 다 할 수 있습니다.

먼저, _패턴 매칭(pattern matching)_ 을 사용하면, 주어진 값이 다음과 같은지 확인할 수 있습니다.

- 특정 모양을 가지고 있는지.
- 특정 상수(constant)인지.
- 다른 것과 같은지.
- 특정 타입을 가지고 있는지.

그런 다음, _패턴 구조 분해(pattern destructuring)_ 는 해당 값을 구성 요소로 분해하는, 
편리한 선언적 구문을 제공합니다. 
동일한 패턴을 사용하면, 프로세스에서 해당 부분 중 일부 또는 전체에 변수를 바인딩할 수도 있습니다.

### 매칭 {:#matching}

패턴은 항상 값에 대해 테스트하여, 값이 예상하는 형태인지 확인합니다. 
즉, 값이 패턴과 _일치_ 하는지 확인하는 것입니다.

일치를 구성하는 것은 당신이 사용하는 [어떤 종류의 패턴][types]에 따라 달라집니다. 
예를 들어, 상수 패턴은 값이 패턴의 상수와 같으면 일치합니다.

<?code-excerpt "language/lib/patterns/switch.dart (constant-pattern)"?>
```dart
switch (number) {
  // 1 == number인 경우, 상수 패턴이 일치합니다.
  case 1:
    print('one');
}
```

많은 패턴은 때때로 각각 _outer_ 및 _inner_ 패턴이라고도 하는 하위 패턴을 사용합니다. 
패턴은 하위 패턴에서 재귀적으로 일치합니다. 
예를 들어, 모든 [collection-type][] 패턴의 개별 필드는, 
[변수 패턴][variable] 또는 [상수 패턴][constant]이 될 수 있습니다.

<?code-excerpt "language/lib/patterns/switch.dart (list-pattern)"?>
```dart
const a = 'a';
const b = 'b';
switch (obj) {
  // 리스트 패턴 [a, b]는 obj가 두 개의 필드를 갖는 리스트인 경우 먼저 obj와 일치하고, 
  // 해당 필드가 상수 하위 패턴 'a' 및 'b'와 일치하는 경우입니다.
  case [a, b]:
    print('$a, $b');
}
```

일치하는 값의 일부를 무시하려면, [와일드카드 패턴][wildcard pattern]을 플레이스홀더로 사용할 수 있습니다. 
리스트 패턴의 경우, [나머지 요소][rest element]를 사용할 수 있습니다.

### 구조 분해 {:#destructuring}

객체와 패턴이 일치하면, 패턴은 객체의 데이터에 액세스하여, 부분적으로 추출할 수 있습니다. 
즉, 패턴은 객체를 _구조 분해(destructures)_ 합니다.

<?code-excerpt "language/lib/patterns/destructuring.dart (list-pattern)"?>
```dart
var numList = [1, 2, 3];
// 리스트 패턴 [a, b, c]는 numList...의 세 요소를 구조 분해합니다.
var [a, b, c] = numList;
// ...그리고 이를 새로운 변수에 할당합니다.
print(a + b + c);
```

구조 분해 패턴 안에 [모든 종류의 패턴][types]을 중첩할 수 있습니다. 
예를 들어, 이 케이스 패턴은 첫 번째 요소가 `'a'` 또는 `'b'`인 두 요소 리스트를 매치하고 구조 분해합니다.

<?code-excerpt "language/lib/patterns/destructuring.dart (nested-pattern)"?>
```dart
switch (list) {
  case ['a' || 'b', var c]:
    print(c);
}
```

## 패턴이 나타날 수 있는 곳 {:#places-patterns-can-appear}

Dart 언어의 여러 곳에서 패턴을 사용할 수 있습니다.

<a id="pattern-uses"></a>

- 지역 변수 [선언](#variable-declaration) 및 [할당](#variable-assignment)
- [for 및 for-in 루프][for]
- [if-case][if] 및 [switch-case][switch]
- [컬렉션 리터럴][collection literals]의 흐름 제어

이 섹션에서는, 패턴을 사용한 매칭 및 구조 분해의 일반적인 사용 사례를 설명합니다.

### 변수 선언 {:#variable-declaration}

Dart가 로컬 변수 선언을 허용하는 모든 곳에서 _패턴 변수 선언_ 을 사용할 수 있습니다. 
패턴은 선언 오른쪽의 값과 일치합니다. 
일치하면, 값을 구조 분해하고 새 로컬 변수에 바인딩합니다.

<?code-excerpt "language/lib/patterns/destructuring.dart (variable-declaration)"?>
```dart
// 새로운 변수 a, b, c를 선언합니다.
var (a, [b, c]) = ('str', [1, 2]);
```

패턴 변수 선언은 `var` 또는 `final`로 시작해야 하며, 그 뒤에 패턴이 와야 합니다.

### 변수 할당 {:#variable-assignment}

_변수 할당 패턴_ 은 할당의 왼쪽에 있습니다. 
먼저, 일치하는 객체를 구조 분해합니다. 
그런 다음, 새 변수를 바인딩하는 대신, _기존_ 변수에 값을 할당합니다.

변수 할당 패턴을 사용하여, 세 번째 임시 변수를 선언하지 않고, 두 변수의 값을 바꿉니다. 

<?code-excerpt "language/lib/patterns/destructuring.dart (variable-assignment)"?>
```dart
var (a, b) = ('left', 'right');
(b, a) = (a, b); // 바꾸기.
print('$a $b'); // "right left"을 출력.
```

### 스위치 문과 표현식 {:#switch-statements-and-expressions}

모든 case 절에는 패턴이 포함됩니다. 
이는 [switch 문][switch] 및 [표현식][expressions]과 [if-case 문][if]에 적용됩니다. 
case에서 [모든 종류의 패턴][types]을 사용할 수 있습니다.

_Case 패턴_ 은 [반박 가능(refutable)][refutable]합니다. 
이를 통해, 흐름 제어가 다음 중 하나를 수행할 수 있습니다.

- 스위치에 오는 객체를 일치시키고, 구조 분해합니다.
- 객체가 일치하지 않으면, 실행을 계속합니다.

패턴이 case에서 구조 분해하는 값은 로컬 변수가 됩니다. 
해당 범위는 해당 case의 body 내에만 있습니다.

<?code-excerpt "language/lib/patterns/switch.dart (switch-statement)"?>
```dart
switch (obj) {
  // 1 == obj인 경우, 일치합니다.
  case 1:
    print('one');

  // obj의 값이 'first'와 'last'의 상수 값 사이에 있는 경우, 일치합니다.
  case >= first && <= last:
    print('in range');

  // obj가 두 개의 필드가 있는 레코드인 경우 일치하며, 해당 필드를 'a'와 'b'에 할당합니다.
  case (var a, var b):
    print('a = $a, b = $b');

  default:
}
```

<a id="or-pattern-switch"></a>

[Logical-or 패턴][logical-or]은 스위치 표현식이나 명령문에서, 
여러 케이스가 본문을 공유하는 데 유용합니다.

<?code-excerpt "language/lib/patterns/switch.dart (or-share-body)"?>
```dart
var isPrimary = switch (color) {
  Color.red || Color.yellow || Color.blue => true,
  _ => false
};
```

Switch 문은 [Logical-or 패턴 공유를 사용하지 않고도][share] 여러 케이스가 본문을 공유할 수 있지만, 
여전히 여러 케이스가 [guard][]를 공유할 수 있도록 하는 데 고유하게 유용합니다.

<?code-excerpt "language/lib/patterns/switch.dart (or-share-guard)"?>
```dart
switch (shape) {
  case Square(size: var s) || Circle(size: var s) when s > 0:
    print('Non-empty symmetric shape');
}
```

[Guard 절][guard]는 조건이 거짓인 경우 switch를 종료하지 않고, 
케이스의 일부로 임의의 조건을 평가합니다. (케이스 본문에서 `if` 문을 사용하는 것과 같음)

<?code-excerpt "language/lib/control_flow/branches.dart (guard)"?>
```dart
switch (pair) {
  case (int a, int b):
    if (a > b) print('First element greater');
  // false이면, 아무것도 출력하지 않고 스위치를 종료합니다.
  case (int a, int b) when a > b:
    // false이면, 아무것도 출력하지 않고 다음 케이스로 넘어갑니다.
    print('First element greater');
  case (int a, int b):
    print('First element not greater');
}
```

### For 및 for-in 루프 {:#for-and-for-in-loops}

[for 및 for-in 루프][for]에서 패턴을 사용하여, 컬렉션의 값을 반복(iterate-over)하고 구조 분해할 수 있습니다.

이 예제에서는 for-in 루프에서 [객체 구조 분해][object]를 사용하여, 
`<Map>.entries` 호출이 반환하는 [`MapEntry`][] 객체를 구조 분해합니다.

<?code-excerpt "language/lib/patterns/for_in.dart (for-in-pattern)"?>
```dart
Map<String, int> hist = {
  'a': 23,
  'b': 100,
};

for (var MapEntry(key: key, value: count) in hist.entries) {
  print('$key occurred $count times');
}
```

객체 패턴은 `hist.entries`에 명명된 타입 `MapEntry`가 있는지 확인한 다음, 
명명된 필드 하위 패턴 `key`와 `value`로 재귀합니다. 
각 반복에서 `MapEntry`에서 `key` getter와 `value` getter를 호출하고, 
결과를 각각 로컬 변수 `key`와 `count`에 바인딩합니다.

getter 호출의 결과를 같은 이름의 변수에 바인딩하는 것은 일반적인 사용 사례이므로, 
객체 패턴은 [변수 하위 패턴][variable]에서 getter 이름을 유추할 수도 있습니다. 
이렇게 하면, `key: key`와 같은 중복된 것에서, `:key`로 변수 패턴을 단순화할 수 있습니다.

<?code-excerpt "language/lib/patterns/for_in.dart (for-in-short)"?>
```dart
for (var MapEntry(:key, value: count) in hist.entries) {
  print('$key occurred $count times');
}
```

## 패턴의 사용 사례 {:#use-cases-for-patterns}

[이전 섹션](#places-patterns-can-appear)은 패턴이 다른 Dart 코드 구성에 _어떻게_ 들어맞는지 설명합니다. 
두 변수의 값을 [바꾸기](#variable-assignment) 또는 맵에서 [키-값 쌍을 구조 분해](#for-and-for-in-loops)와 같이, 몇 가지 흥미로운 사용 사례를 예로 들었습니다. 
이 섹션에서는 다음에 대한 답변을 제공하는 더 많은 사용 사례를 설명합니다.

- 패턴을 사용해야 하는 _시기 및 이유_.
- 어떤 종류의 문제를 해결하는지.
- 어떤 관용구에 가장 적합한지.

### 다중 반환 구조 분해 {:#destructuring-multiple-returns}

레코드는 단일 함수 호출에서 [여러 값을 집계하고 반환][returning multiple values]할 수 있습니다. 
패턴은 함수 호출과 함께 인라인으로, 레코드의 필드를 로컬 변수로 직접 구조 분해할 수 있는 기능을 추가합니다.

다음과 같이, 각 레코드 필드에 대해 새 로컬 변수를 개별적으로 선언하는 대신:

<?code-excerpt "language/lib/patterns/destructuring.dart (destructure-multiple-returns-1)"?>
```dart
var info = userInfo(json);
var name = info.$1;
var age = info.$2;
```

[변수 선언](#variable-declaration) 또는 [할당 패턴](#variable-assignment)과 [레코드 패턴][record]를 하위 패턴으로 사용하여, 
함수가 반환하는 레코드의 필드를 로컬 변수로 구조 분해할 수 있습니다.

<?code-excerpt "language/lib/patterns/destructuring.dart (destructure-multiple-returns-2)"?>
```dart
var (name, age) = userInfo(json);
```

패턴을 사용하여, 명명된 필드가 있는 레코드를 구조 분해하려면:

<?code-excerpt "language/lib/patterns/destructuring.dart (destructure-multiple-returns-3)"?>
```dart
final (:name, :age) =
    getData(); // 예를 들어, (name: 'doug', age: 25)를 반환합니다;
```

### 클래스 인스턴스 구조 분해 {:#destructuring-class-instances}

[객체 패턴][object]는 명명된 객체 타입과 일치하여, 
객체 클래스가 이미 노출한 getters를 사용하여 데이터를 구조 분해할 수 있습니다.

클래스 인스턴스를 구조 분해하려면, 명명된 타입을 사용하고, 
그 뒤에 괄호로 묶은 구조 분해할 속성을 사용합니다.

<?code-excerpt "language/lib/patterns/destructuring.dart (destructure-class-instances)"?>
```dart
final Foo myFoo = Foo(one: 'one', two: 2);
var Foo(:one, :two) = myFoo;
print('one $one, two $two');
```

### 대수적 데이터 타입 {:#algebraic-data-types}

객체 구조 분해와 스위치 케이스는 [대수적 데이터 타입][algebraic data type] 스타일로 코드를 작성하는 데, 
도움이 됩니다.
이 메서드를 사용하는 경우:
- 관련된 타입의 패밀리가 있는 경우
- 각 타입에 대해 특정 동작이 필요한 연산이 있는 경우
- 모든 다른 타입 정의에 걸쳐, 동작을 분산하는 대신 한곳에서 동작을 그룹화하려는 경우

모든 타입에 대한 인스턴스 메서드로 작업을 구현하는 대신, 
하위 타입을 전환하는 단일 함수에 작업의 변형을 유지합니다.

<?code-excerpt "language/lib/patterns/algebraic_datatypes.dart (algebraic-datatypes)"?>
```dart
sealed class Shape {}

class Square implements Shape {
  final double length;
  Square(this.length);
}

class Circle implements Shape {
  final double radius;
  Circle(this.radius);
}

double calculateArea(Shape shape) => switch (shape) {
      Square(length: var l) => l * l,
      Circle(radius: var r) => math.pi * r * r
    };
```

### 들어오는 JSON 검증 {:#validating-incoming-json}

[Map][] 및 [list][] 패턴은 JSON 데이터의 키-값 쌍을 구조 분해하는 데 적합합니다.

<?code-excerpt "language/lib/patterns/json.dart (json-1)"?>
```dart 
var json = {
  'user': ['Lily', 13]
};
var {'user': [name, age]} = json;
```

JSON 데이터가 예상한 구조를 가지고 있다는 것을 알고 있다면, 이전 예는 현실적입니다. 
하지만, 데이터는 일반적으로 네트워크와 같은 외부 소스에서 제공됩니다. 
먼저, 유효성 검사를 통해 구조를 확인해야 합니다.

패턴이 없으면, 유효성 검사가 장황합니다.

<?code-excerpt "language/lib/patterns/json.dart (json-2)"?>
```dart
if (json is Map<String, Object?> &&
    json.length == 1 &&
    json.containsKey('user')) {
  var user = json['user'];
  if (user is List<Object> &&
      user.length == 2 &&
      user[0] is String &&
      user[1] is int) {
    var name = user[0] as String;
    var age = user[1] as int;
    print('User $name is $age years old.');
  }
}
```

단일 [case 패턴](#switch-statements-and-expressions)은 동일한 검증을 달성할 수 있습니다. 
단일 케이스는 [if-case][if] 문으로 가장 잘 작동합니다. 
패턴은 JSON을 검증하는 더 선언적이고, 훨씬 덜 자세한 방법을 제공합니다.

<?code-excerpt "language/lib/patterns/json.dart (json-3)"?>
```dart
if (json case {'user': [String name, int age]}) {
  print('User $name is $age years old.');
}
```

이 케이스 패턴은 다음을 동시에 검증합니다.

- `json`은 맵입니다. 진행하려면 먼저 외부 [맵 패턴][map]과 일치해야 하기 때문입니다.
  - 그리고 맵이므로, `json`이 null이 아님을 확인합니다.
- `json`에는 키 `user`가 있습니다.
- 키 `user`는 두 값의 리스트와 쌍을 이룹니다.
- 리스트 값의 타입은 `String`과 `int`입니다.
- 값을 보관하는 새 로컬 변수는 `name`과 `age`입니다.

[language version]: /guides/language/evolution#language-versioning
[types]: /language/pattern-types
[collection-type]: /language/collections
[wildcard pattern]: /language/pattern-types#wildcard
[rest element]: /language/pattern-types#rest-element
[null-check pattern]: /language/pattern-types#null-check
[for]: /language/loops#for-loops
[if]: /language/branches#if-case
[switch]: /language/branches#switch-statements
[expressions]: /language/branches#switch-expressions
[collection literals]: /language/collections#control-flow-operators
[null-assert pattern]: /language/pattern-types#null-assert
[record]: /language/pattern-types#record
[returning multiple values]: /language/records#multiple-returns
[refutable]: /resources/glossary#refutable-pattern
[constant]: /language/pattern-types#constant
[list]: /language/pattern-types#list
[map]: /language/pattern-types#map
[variable]: /language/pattern-types#variable
[logical-or]: /language/pattern-types#logical-or
[share]: /language/branches#switch-share
[guard]: /language/branches#guard-clause
[relational]: /language/pattern-types#relational
[check]: /language/pattern-types#null-check
[assert]: /language/pattern-types#null-assert
[object]: /language/pattern-types#object
[`MapEntry`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/MapEntry-class.html
[algebraic data type]: https://en.wikipedia.org/wiki/Algebraic_data_type
