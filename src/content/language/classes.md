---
# title: Classes
title: 클래스
# description: Summary of classes, class instances, and their members.
description: 클래스, 클래스 인스턴스와 멤버에 대한 요약입니다.
prevpage:
  url: /language/error-handling
  # title: Error handling
  title: 오류 처리
nextpage:
  url: /language/constructors
  # title: Constructors
  title: 생성자
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

Dart는 클래스와 믹스인 기반 상속을 갖춘 객체 지향 언어입니다. 
모든 객체는 클래스의 인스턴스이며, `Null`을 제외한 모든 클래스는 [`Object`][]에서 파생됩니다. 
*믹스인 기반 상속*은 모든 클래스([최상위 클래스][top-and-bottom], `Object?` 제외)에 정확히 하나의 슈퍼클래스가 있지만, 클래스 본문은 여러 클래스 계층에서 재사용할 수 있음을 의미합니다. 
[확장 메서드][Extension methods]는 클래스를 변경하거나 하위 클래스를 만들지 않고도, 클래스에 기능을 추가하는 방법입니다. 
[클래스 수정자][Class modifiers]를 사용하면, 라이브러리가 클래스를 하위 타입으로 지정하는 방법을 제어할 수 있습니다.

## 클래스 멤버 사용 {:#using-class-members}

객체는 함수와 데이터(각각 *메서드*와 *인스턴스 변수*)로 구성된 *멤버*를 갖습니다. 
메서드를 호출하면, 객체에서 *호출*합니다. 
메서드는 해당 객체의 함수와 데이터에 액세스할 수 있습니다.

점(`.`)을 사용하여 인스턴스 변수나 메서드를 참조합니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-members)"?>
```dart
var p = Point(2, 2);

// y의 값을 가져옵니다.
assert(p.y == 2);

// p에서 distanceTo()를 호출합니다.
double distance = p.distanceTo(Point(4, 4));
```

가장 왼쪽 피연산자가 null인 경우, 예외가 발생하지 않도록 하려면. `.` 대신 `?.`를 사용하세요.

<?code-excerpt "misc/test/language_tour/classes_test.dart (safe-member-access)"?>
```dart
// p가 null이 아니면, y 값과 동일한 변수를 설정합니다.
var a = p?.y;
```


## 생성자 사용 {:#using-constructors}

*생성자(constructor)*를 사용하여 객체를 생성할 수 있습니다. 
생성자 이름은 <code><em>ClassName</em></code> 또는 <code><em>ClassName</em></code> 일 수 있습니다. 
예를 들어, 다음 코드는 `Point()` 및 `Point.fromJson()` 생성자를 사용하여, `Point` 객체를 생성합니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-creation)" replace="/ as .*?;/;/g"?>
```dart
var p1 = Point(2, 2);
var p2 = Point.fromJson({'x': 1, 'y': 2});
```

다음 코드는 동일한 효과를 가지지만, 생성자 이름 앞에 선택적으로 `new` 키워드를 사용합니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-creation-new)" replace="/ as .*?;/;/g"?>
```dart
var p1 = new Point(2, 2);
var p2 = new Point.fromJson({'x': 1, 'y': 2});
```

일부 클래스는 [상수 생성자][constant constructors]를 제공합니다. 
상수 생성자를 사용하여 컴파일 타임 상수를 만들려면, 생성자 이름 앞에 `const` 키워드를 넣습니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (const)"?>
```dart
var p = const ImmutablePoint(2, 2);
```

두 개의 동일한 컴파일 타임 상수를 구성하면, 단일 표준(canonical) 인스턴스가 생성됩니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (identical)"?>
```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // 그것들은 동일한 인스턴스입니다!
```

_상수 컨텍스트_ 내에서, 생성자나 리터럴 앞에 `const`를 생략할 수 있습니다. 
예를 들어, const 맵을 만드는 이 코드를 살펴보세요.

<?code-excerpt "misc/test/language_tour/classes_test.dart (const-context-withconst)" replace="/pointAndLine1/pointAndLine/g"?>
```dart
// 여기에는 const 키워드가 많이 있습니다.
const pointAndLine = const {
  'point': const [const ImmutablePoint(0, 0)],
  'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
};
```

`const` 키워드의 첫 번째 사용을 제외한 모든 사용을 생략할 수 있습니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (const-context-noconst)" replace="/pointAndLine2/pointAndLine/g"?>
```dart
// 상수 컨텍스트를 설정하는 const는 단 하나입니다.
const pointAndLine = {
  'point': [ImmutablePoint(0, 0)],
  'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
};
```

상수 생성자가 상수 컨텍스트 외부에 있고 `const` 없이 호출되면, 
**비상수 객체(non-constant object)**가 생성됩니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (nonconst-const-constructor)"?>
```dart
var a = const ImmutablePoint(1, 1); // 상수를 생성합니다
var b = ImmutablePoint(1, 1); // 상수를 생성하지 않습니다

assert(!identical(a, b)); // 동일한 인스턴스가 아닙니다!
```


## 객체의 타입 가져오기 {:#getting-an-objects-type}

런타임에 객체의 타입을 얻으려면, 
`Object` 속성인 `runtimeType`을 사용하면 됩니다. 
이 속성은 [`Type`][] 객체를 반환합니다.

<?code-excerpt "misc/test/language_tour/classes_test.dart (runtime-type)"?>
```dart
print('The type of a is ${a.runtimeType}');
```

:::warning
`runtimeType` 대신 [타입 테스트 연산자][type test operator]를 사용하여 객체의 타입을 테스트합니다. 
프로덕션 환경에서, `object is Type` 테스트는 `object.runtimeType == Type` 테스트보다 더 안정적입니다.
:::

지금까지 클래스를 _사용_ 하는 방법을 살펴보았습니다. 
이 섹션의 나머지 부분에서는 클래스를 _구현_ 하는 방법을 보여줍니다.

## 인스턴스 변수 {:#instance-variables}

인스턴스 변수를 선언하는 방법은 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/classes/point_with_main.dart (class)"?>
```dart
class Point {
  double? x; // 인스턴스 변수 x를 선언합니다. 초기에는 null입니다.
  double? y; // y를 선언합니다. 초기에는 null입니다.
  double z = 0; // z를 선언합니다. 초기에는 0입니다.
}
```

[nullable 타입][nullable type]으로 선언된 초기화되지 않은 인스턴스 변수는 `null` 값을 갖습니다. 
null이 불가능한 인스턴스 변수는 선언 시 [반드시 초기화되어야 합니다][must be initialized].

모든 인스턴스 변수는 암묵적인 *getter* 메서드를 생성합니다. 
초기화자가 없는 비 final 인스턴스 변수와 `late final` 인스턴스 변수도 암묵적인 *setter* 메서드를 생성합니다. 
자세한 내용은 [Getter 및 setter][Getters and setters]를 확인하세요.

<?code-excerpt "misc/lib/language_tour/classes/point_with_main.dart (class-main)" replace="/(double .*?;).*/$1/g" plaster="none"?>
```dart
class Point {
  double? x; // 인스턴스 변수 x를 선언합니다. 초기에는 null입니다.
  double? y; // y를 선언합니다. 초기에는 null입니다.
}

void main() {
  var point = Point();
  point.x = 4; // x에 대한 setter 메서드를 사용합니다.
  assert(point.x == 4); // x에 대한 getter 메서드를 사용합니다.
  assert(point.y == null); // 기본값은 null입니다.
}
```

선언된 위치에서 `late`가 아닌 인스턴스 변수를 초기화하면, 
생성자와 초기화자 리스트가 실행되기 전에, 인스턴스가 생성될 때 값이 설정됩니다. 
결과적으로, `late`가 아닌 인스턴스 변수의 초기화 표현식(`=` 뒤)은 `this`에 액세스할 수 없습니다.

<?code-excerpt "misc/lib/language_tour/classes/point_this.dart"?>
```dart
double initialX = 1.5;

class Point {
  // OK, `this`에 의존하지 않는 선언에 액세스할 수 있습니다.
  double? x = initialX;

  // ERROR, `late`가 아닌 초기화자에서 `this`에 액세스할 수 없습니다.
  double? y = this.x;

  // OK, `late` 초기화자에서 `this`에 액세스할 수 있습니다.
  late double? z = this.x;

  // OK, `this.x`와 `this.y`는 표현식이 아니라, 매개변수 선언입니다.
  Point(this.x, this.y);
}
```

인스턴스 변수는 `final`일 수 있으며, 이 경우 정확히 한 번만 설정해야 합니다. 
선언 시, 생성자 매개변수를 사용하거나, 생성자의 [초기화자 리스트][initializer list]를 사용하여, 
`final`, `late`가 아닌 인스턴스 변수를 초기화합니다.

<?code-excerpt "misc/lib/effective_dart/usage_good.dart (field-init-at-decl)"?>
```dart
class ProfileMark {
  final String name;
  final DateTime start = DateTime.now();

  ProfileMark(this.name);
  ProfileMark.unnamed() : name = '';
}
```

생성자 본문이 시작된 후에 `final` 인스턴스 변수의 값을 할당해야 하는 경우, 다음 중 하나를 사용할 수 있습니다.

* [팩토리 생성자][factory constructor]를 사용합니다.
* `late final`을 사용하지만, [_조심하세요:_][late-final-ivar] 
  초기화자가 없는 `late final`은 API에 setter를 추가합니다.

## 암묵적 인터페이스 {:#implicit-interfaces}

모든 클래스는 클래스의 모든 인스턴스 멤버와 구현하는 모든 인터페이스를 포함하는 인터페이스를 암묵적으로 정의합니다. 
B의 구현을 상속하지 않고 클래스 B의 API를 지원하는 클래스 A를 만들려면 클래스 A가 B 인터페이스를 구현해야 합니다.

클래스는 `implements` 절에서 선언한 다음, 
인터페이스에 필요한 API를 제공하여 하나 이상의 인터페이스를 구현합니다. 예를 들어:

<?code-excerpt "misc/lib/language_tour/classes/impostor.dart"?>
```dart
// 사람. 암묵적 인터페이스에는 greet()가 포함되어 있습니다.
class Person {
  // 인터페이스에 있지만, 이 라이브러리에서만 볼 수 있습니다.
  final String _name;

  // 인터페이스에는 없습니다. 이것은 생성자이기 때문입니다.
  Person(this._name);

  // 인터페이스에 있습니다.
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// Person 인터페이스의 구현.
class Impostor implements Person {
  String get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```

다음은 클래스가 여러 인터페이스를 구현하도록 지정하는 예입니다.

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (point-interfaces)"?>
```dart
class Point implements Comparable, Location {...}
```


## 클래스 변수와 메서드 {:#class-variables-and-methods}

클래스 전체 변수와 메서드를 구현하려면, `static` 키워드를 사용하세요.

### Static 변수 {:#static-variables}

정적 변수(클래스 변수)는 클래스 전체 상태 및 상수에 유용합니다.

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (static-field)"?>
```dart
class Queue {
  static const initialCapacity = 16;
  // ···
}

void main() {
  assert(Queue.initialCapacity == 16);
}
```

정적 변수는 사용되기 전까지 초기화되지 않습니다.

:::note
이 페이지는 상수 이름에 대해, 
`lowerCamelCase`를 선호하는 [스타일 가이드 권장 사항](/effective-dart/style#identifiers)을 따릅니다.
:::

### Static 메서드 {:#static-methods}

정적 메서드(클래스 메서드)는 인스턴스에서 작동하지 않으므로, `this`에 액세스할 수 없습니다. 
그러나 정적 변수에는 액세스할 수 있습니다. 
다음 예제에서 보듯이, 클래스에서 직접 정적 메서드를 호출합니다.

<?code-excerpt "misc/lib/language_tour/classes/point_with_distance_method.dart"?>
```dart
import 'dart:math';

class Point {
  double x, y;
  Point(this.x, this.y);

  static double distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

void main() {
  var a = Point(2, 2);
  var b = Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(2.8 < distance && distance < 2.9);
  print(distance);
}
```

:::note
일반적이거나 널리 사용되는 유틸리티와 기능에는, 정적 메서드 대신, 최상위 함수를 사용하는 것을 고려하세요.
:::

정적 메서드를 컴파일 타임 상수로 사용할 수 있습니다. 
예를 들어, 정적 메서드를 상수 생성자에 매개변수로 전달할 수 있습니다.


[`Object`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Object-class.html
[top-and-bottom]: /null-safety/understanding-null-safety#top-and-bottom
[Extension methods]: /language/extension-methods
[Class modifiers]: /language/class-modifiers
[constant constructors]: /language/constructors#constant-constructors
[`Type`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Type-class.html
[type test operator]: /language/operators#type-test-operators
[Getters and setters]: /language/methods#getters-and-setters
[initializer list]: /language/constructors#use-an-initializer-list
[factory constructor]: /language/constructors#factory-constructors
[late-final-ivar]: /effective-dart/design#avoid-public-late-final-fields-without-initializers
[nullable type]: /null-safety/understanding-null-safety#using-nullable-types
[must be initialized]: /null-safety/understanding-null-safety#uninitialized-variables
