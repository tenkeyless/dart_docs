---
# title: Constructors
title: 생성자
description: Dart에서 생성자를 사용하는 방법에 대한 모든 것.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
prevpage:
  url: /language/classes
  # title: Classes
  title: 클래스
nextpage:
  url: /language/methods
  # title: Methods
  title: 메서드
---

생성자는 클래스 인스턴스를 만드는 특수 함수입니다.

Dart는 여러 타입의 생성자를 구현합니다. 
기본 생성자를 제외하고, 이러한 함수는 클래스와 동일한 이름을 사용합니다.

* [생성 생성자][generative]: 새 인스턴스를 만들고, 인스턴스 변수를 초기화합니다.
* [기본 생성자][default]: 생성자가 지정되지 않은 경우, 새 인스턴스를 만드는 데 사용됩니다. 
  인수를 받지 않으며, 이름이 지정되지 않습니다.
* [명명된 생성자][named]: 생성자의 목적을 명확히 하거나, 동일한 클래스에 대해 여러 생성자를 만들 수 있습니다.
* [상수 생성자][constant]: 컴파일 타입 상수로 인스턴스를 만듭니다.
* [팩토리 생성자][factory]: 하위 타입의 새 인스턴스를 만들거나, 캐시에서 기존 인스턴스를 반환합니다.
* [리디렉션 생성자][redirecting]: 동일한 클래스의 다른 생성자로 호출을 전달합니다.

[default]: #default-constructors
[generative]: #generative-constructors
[named]: #named-constructors
[constant]: #constant-constructors
[factory]: #factory-constructors
[redirecting]: #redirecting-constructors

<?code-excerpt path-base="misc/lib/language_tour/classes"?>

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

## 생성자의 타입 {:#types-of-constructors}

### 생성형 생성자 (Generative constructors) {:#generative-constructors}

클래스를 인스턴스화하려면, 생성형 생성자를 사용합니다.

<?code-excerpt "point_alt.dart (idiomatic-constructor)" plaster="none"?>
```dart
class Point {
  // 변수 및 값의 초기화자 리스트
  double x = 2.0;
  double y = 2.0;

  // 형식 매개변수를 초기화하는 생성형 생성자:
  Point(this.x, this.y);
}
```

### 기본 생성자 (Default constructors) {:#default-constructors}

생성자를 선언하지 않으면, Dart는 기본 생성자를 사용합니다.
기본 생성자는 인수나 이름이 없는 생성 생성자입니다.

### 명명된 생성자 (Named constructors) {:#named-constructors}

클래스에 대해 여러 생성자를 구현하거나 더 명확성을 제공하려면, 명명된 생성자를 사용합니다.

<?code-excerpt "point.dart (named-constructor)" replace="/Point\.\S*/[!$&!]/g" plaster="none"?>
```dart
const double xOrigin = 0;
const double yOrigin = 0;

class Point {
  final double x;
  final double y;

  // 생성자 본문이 실행되기 전에, x 및 y 인스턴스 변수를 설정합니다.
  Point(this.x, this.y);

  // 명명된 생성자
  [!Point.origin()!]
      : x = xOrigin,
        y = yOrigin;
}
```

서브클래스는 슈퍼클래스의 명명된 생성자를 상속하지 않습니다. 
슈퍼클래스에 정의된 명명된 생성자를 사용하여 서브클래스를 만들려면, 
서브클래스에서 해당 생성자를 구현합니다.

### 상수 생성자 (Constant constructors) {:#constant-constructors}

클래스가 변경되지 않는 객체를 생성하는 경우, 이러한 객체를 컴파일 타임 상수로 만듭니다. 
객체를 컴파일 타임 상수로 만들려면, 모든 인스턴스 변수를 `final`로 설정한 `const` 생성자를 정의합니다.

<?code-excerpt "immutable_point.dart"?>
```dart
class ImmutablePoint {
  static const ImmutablePoint origin = ImmutablePoint(0, 0);

  final double x, y;

  const ImmutablePoint(this.x, this.y);
}
```

상수 생성자는 항상 상수를 생성하지는 않습니다. 
`const`가 아닌 컨텍스트에서 호출될 수 있습니다. 
자세한 내용은 [생성자 사용][using constructors] 섹션을 참조하세요.

### 리다이렉트 생성자 (Redirecting constructors) {:#redirecting-constructors}

생성자는 같은 클래스의 다른 생성자로 리디렉션할 수 있습니다. 
리디렉션하는 생성자는 빈 본문을 갖습니다. 
생성자는 콜론(:) 뒤에 클래스 이름 대신 `this`를 사용합니다.

<?code-excerpt "point_redirecting.dart"?>
```dart
class Point {
  double x, y;

  // 이 클래스의 메인 생성자입니다.
  Point(this.x, this.y);

  // 메인 생성자에게 위임합니다.
  Point.alongXAxis(double x) : this(x, 0);
}
```

### 팩토리 생성자 (Factory constructors) {:#factory-constructors}

생성자를 구현하는 다음 두 가지 경우 중 하나를 마주치면, `factory` 키워드를 사용하세요.

* 생성자는 항상 클래스의 새 인스턴스를 생성하지 않습니다.
  팩토리 생성자는 `null`을 반환할 수 없지만, 다음을 반환할 수 있습니다.

  * 새 인스턴스를 생성하는 대신, 캐시에서 기존 인스턴스
  * 하위 타입의 새 인스턴스

* 인스턴스를 생성하기 전에 사소하지 않은(non-trivial work) 작업을 수행해야 합니다.
  여기에는 인수 확인이나 초기화자 리스트에서 처리할 수 없는 다른 처리가 포함될 수 있습니다.

:::tip
[`late final`][late-final-ivar]를 사용하여, final 변수의 늦은 초기화를 처리할 수도 있습니다. (조심하세요!)
:::

다음 예에는 두 개의 팩토리 생성자가 포함됩니다.

* `Logger` 팩토리 생성자는 캐시에서 객체를 반환합니다.
* `Logger.fromJson` 팩토리 생성자는 JSON 객체에서 final 변수를 초기화합니다.

<?code-excerpt "logger.dart (constructors)"?>
```dart
class Logger {
  final String name;
  bool mute = false;

  // _cache는 이름 앞에 _가 붙어서, 라이브러리에서만 접근할 수 있습니다. (library-private)
  static final Map<String, Logger> _cache = <String, Logger>{};

  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }

  factory Logger.fromJson(Map<String, Object> json) {
    return Logger(json['name'].toString());
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

:::warning
팩토리 생성자는 `this`에 접근할 수 없습니다.
:::

다른 생성자와 마찬가지로, 팩토리 생성자를 사용합니다.

<?code-excerpt "logger.dart (logger)"?>
```dart
var logger = Logger('UI');
logger.log('Button clicked');

var logMap = {'name': 'UI'};
var loggerJson = Logger.fromJson(logMap);
```

### 리다이렉트 팩토리 생성자 (Redirecting factory constructors) {:#redirecting-factory-constructors}

리다이렉트 팩토리 생성자는 다른 클래스의 생성자에 대한 호출을 지정하여, 
누군가가 리다이렉트 생성자를 호출할 때마다 사용합니다.

```dart
factory Listenable.merge(List<Listenable> listenables) = _MergingListenable
```

일반적인 팩토리 생성자는 다른 클래스의 인스턴스를 생성하고 반환할 수 있는 것처럼 보일 수 있습니다. 
이렇게 하면 팩토리 리다이렉트이 불필요해집니다. 
팩토리 리다이렉트에는 여러 가지 장점이 있습니다.

* 추상 클래스는 다른 클래스의 상수 생성자를 사용하는 상수 생성자를 제공할 수 있습니다.
* 팩토리 리다이렉트 생성자는 포워더가 형식 매개변수와 기본값을 반복할 필요가 없습니다.

### 생성자 분리 (tear-offs) {:#constructor-tear-offs}

Dart에서는 생성자를 호출하지 않고 매개변수로 제공할 수 있습니다. 
_tear-off_ 라고 하는(괄호를 _tear-off_ 하는 것처럼) 생성자를, 
동일한 매개변수로 호출하는 클로저 역할을 합니다.

tear-off가 메서드가 허용하는 것과 동일한 서명과 반환 타입을 가진 생성자라면, 
매개변수나 변수로 tear-off를 사용할 수 있습니다.

tear-off는 람다나 익명 함수와 다릅니다. 
람다는 생성자의 래퍼 역할을 하는 반면 tear-off는 생성자입니다.

**Tear-Offs 사용**

```dart tag=good
// 명명된 생성자에 대해 tear-off를 사용합니다.
var strings = charCodes.map(String.fromCharCode);  

// 이름이 없는 생성자에 대해 tear-off를 사용합니다.
var buffers = charCodes.map(StringBuffer.new); 
```

**람다가 아닙니다**

```dart tag=bad
// 명명된 생성자에 대한 람다 대신:
var strings = charCodes.map((code) => String.fromCharCode(code));

// 이름이 없는 생성자에 대한 람다 대신:
var buffers = charCodes.map((code) => StringBuffer(code));
```

더 많은 논의를 원하시면, Flutter 디코딩 영상을 시청해 보세요.

{% ytEmbed "OmCaloD7sis", "Dart Tear-offs | Decoding Flutter" %}

## 인스턴스 변수 초기화 {:#instance-variable-initialization}

Dart는 세 가지 방법으로 변수를 초기화할 수 있습니다.

### (1) 선언에서 인스턴스 변수를 초기화합니다. {:#initialize-instance-variables-in-the-declaration}

변수를 선언할 때 인스턴스 변수도 초기화합니다.

<?code-excerpt "point_alt.dart (initialize-declaration)" plaster="none"?>
```dart
class PointA {
  double x = 1.0;
  double y = 2.0;

  // 암묵적 기본 생성자는 이러한 변수를 (1.0,2.0)으로 설정합니다.
  // PointA();

  @override
  String toString() {
    return 'PointA($x,$y)';
  }
}
```

### (2) 초기화 형식 매개변수 사용합니다. {:#use-initializing-formal-parameters}

인스턴스 변수에 생성자 인수를 할당하는 일반적인 패턴을 단순화하기 위해, 
Dart는 *형식 매개변수 초기화(initializing formal parameters)* 를 사용합니다.

생성자 선언에서, `this.<propertyName>`을 포함하고, 본문은 생략합니다. 
`this` 키워드는 현재 인스턴스를 참조합니다.

이름 충돌이 있는 경우 `this`를 사용합니다. 
그렇지 않으면, Dart 스타일은 `this`를 생략합니다. 
생성형 생성자의 경우, 초기화 형식 매개변수 이름 앞에 `this`를 접두사로 붙여야 하는 예외가 있습니다.

이 가이드의 앞부분에서 언급했듯이, 
특정 생성자와 생성자의 특정 부분은 `this`에 액세스할 수 없습니다. 여기에는 다음이 포함됩니다.

* 팩토리 생성자
* 초기화자 리스트의 오른쪽(right-hand side)
* 슈퍼클래스 생성자에 대한 인수

형식 매개변수 초기화를 통해 null이 불가능한 인스턴스 변수나 `final` 인스턴스 변수를 초기화할 수도 있습니다. 
이 두 타입의 변수 모두 초기화나 기본값이 필요합니다.

<?code-excerpt "point_alt.dart (initialize-formal)" plaster="none"?>
```dart
class PointB {
  final double x;
  final double y;

  // 생성자 본문이 실행되기 전에 x 및 y 인스턴스 변수를 설정합니다.
  PointB(this.x, this.y);

  // 형식 매개변수를 초기화하는 것도 선택 사항일 수 있습니다.
  PointB.optional([this.x = 0.0, this.y = 0.0]);
}
```

Private 필드는 명명된 초기화 형식으로 사용할 수 없습니다.

{% comment %}
Don't attach the following example to a code excerpt.
It doesn't work on purpose and will cause errors in CI.
{% endcomment %}

```dart
class PointB {
// ...

  PointB.namedPrivate({required double x, required double y})
      : _x = x,
        _y = y;

// ...
}
```

이는 명명된 변수에도 적용됩니다.

<?code-excerpt "point_alt.dart (initialize-named)" plaster="none"?>
```dart
class PointC {
  double x; // 생성자에서 설정해야 합니다
  double y; // 생성자에서 설정해야 합니다

  // 기본값을 사용하여 형식 매개변수를 초기화하는 생성형 생성자
  PointC.named({this.x = 1.0, this.y = 1.0});

  @override
  String toString() {
    return 'PointC.named($x,$y)';
  }
}

// 명명된 변수를 사용하는 생성자입니다.
final pointC = PointC.named(x: 2.0, y: 2.0);
```

형식 매개변수를 초기화하는 데 도입된 모든 변수는 모두 final 변수이며, 초기화된 변수의 범위 내에만 있습니다.

초기화자 리스트에서 표현할 수 없는 논리를 수행하려면, 
해당 논리를 사용하여 [팩토리 생성자](#factory-constructors) 또는 [정적 메서드][static method]를 만듭니다. 
그런 다음 계산된 값을 일반 생성자에 전달할 수 있습니다.

생성자 매개변수는 nullable로 설정될 수 있으며, 초기화되지 않을 수 있습니다.

<?code-excerpt "point_alt.dart (initialize-null)" plaster="none"?>
```dart
class PointD {
  double? x; // 생성자에 설정되지 않으면 null
  double? y; // 생성자에 설정되지 않으면 null

  // 형식 매개변수를 초기화하는 생성형 생성자
  PointD(this.x, this.y);

  @override
  String toString() {
    return 'PointD($x,$y)';
  }
}
```

### (3) 초기화 리스트를 사용합니다. {:#use-an-initializer-list}

생성자 본문이 실행되기 전에, 인스턴스 변수를 초기화할 수 있습니다.
초기화자를 쉼표로 구분합니다.

<?code-excerpt "point_alt.dart (initializer-list)"?>
```dart
// 초기화자 리스트는 생성자 본문이 실행되기 전에 인스턴스 변수를 설정합니다.
Point.fromJson(Map<String, double> json)
    : x = json['x']!,
      y = json['y']! {
  print('In Point.fromJson(): ($x, $y)');
}
```

:::warning
초기화 리스트의 오른쪽(right-hand side)은 `this`에 접근할 수 없습니다.
:::

개발 중에 입력의 유효성을 검사하려면, 초기화 리스트에서 `assert`를 사용합니다.

<?code-excerpt "point_alt.dart (initializer-list-with-assert)" replace="/assert\(.*?\)/[!$&!]/g"?>
```dart
Point.withAssert(this.x, this.y) : [!assert(x >= 0)!] {
  print('In Point.withAssert(): ($x, $y)');
}
```

초기화자 리스트는 `final` 필드를 설정하는 데 도움이 됩니다.

다음 예는 초기화자 리스트에서 세 개의 `final` 필드를 초기화합니다. 
코드를 실행하려면, **Run**을 클릭합니다.

<?code-excerpt "point_with_distance_field.dart"?>
```dartpad
import 'dart:math';

class Point {
  final double x;
  final double y;
  final double distanceFromOrigin;

  Point(double x, double y)
      : x = x,
        y = y,
        distanceFromOrigin = sqrt(x * x + y * y);
}

void main() {
  var p = Point(2, 3);
  print(p.distanceFromOrigin);
}
```

## 생성자 상속 {:#constructor-inheritance}

_서브클래스(Subclasses)_ 또는 자식 클래스는 _슈퍼클래스(superclass)_ 또는 바로 위 부모 클래스에서 *생성자*를 상속하지 않습니다. 
클래스가 생성자를 선언하지 않으면, [기본 생성자](#default-constructors)만 사용할 수 있습니다.

클래스는 슈퍼클래스의 _매개변수(parameters)_ 를 상속할 수 있습니다. 
이를 [슈퍼 매개변수](#super-parameters)라고 합니다.

생성자는 정적 메서드 체인을 호출하는 방식과 다소 비슷한 방식으로 작동합니다. 
각 서브클래스는 슈퍼클래스의 생성자를 호출하여 인스턴스를 초기화할 수 있으며, 
서브클래스가 슈퍼클래스의 정적 메서드를 호출할 수 있는 것과 같습니다. 
이 프로세스는 생성자 본문이나 시그니처를 "상속"하지 않습니다.

### 디폴트가 아닌 슈퍼클래스 생성자 {:#non-default-superclass-constructors}

Dart는 다음 순서로 생성자를 실행합니다.

1. [초기화자 리스트](#use-an-initializer-list)
2. 슈퍼클래스의 명명되지 않은, 인수 없는 생성자
3. 메인 클래스의 인수 없는 생성자

슈퍼클래스에 명명되지 않은 인수 없는 생성자가 없는 경우, 
슈퍼클래스의 생성자 중 하나를 호출합니다. 
생성자 본문(있는 경우) 앞에, 콜론(`:`) 뒤에 슈퍼클래스 생성자를 지정합니다.

다음 예에서, `Employee` 클래스 생성자는 슈퍼클래스 `Person`의 명명된 생성자를 호출합니다. 
다음 코드를 실행하려면, **Run**을 클릭합니다.

<?code-excerpt "employee.dart (super)" plaster="none"?>
```dartpad
class Person {
  String? firstName;

  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // Person에는 디폴트 생성자가 없습니다.
  // super.fromJson()을 호출해야 합니다.
  Employee.fromJson(Map data) : super.fromJson(data) {
    print('in Employee');
  }
}

void main() {
  var employee = Employee.fromJson({});
  print(employee);
  // 다음을 출력합니다.:
  // in Person
  // in Employee
  // Instance of 'Employee'
}
```

Dart는 생성자를 호출하기 *전에* 슈퍼클래스 생성자의 인수를 평가하므로, 
인수는 함수 호출과 같은 표현식이 될 수 있습니다.

<?code-excerpt "employee.dart (method-then-constructor)"?>
```dart
class Employee extends Person {
  Employee() : super.fromJson(fetchDefaultData());
  // ···
}
```

:::warning
슈퍼클래스 생성자에 대한 인수는 `this`에 액세스할 수 없습니다. 
예를 들어, 인수는 *정적* 메서드를 호출할 수 있지만, *인스턴스* 메서드는 호출할 수 없습니다.
:::

### 슈퍼 매개변수 {:#super-parameters}

각 매개변수를 생성자의 슈퍼 호출에 전달하는 것을 피하려면, 
슈퍼 초기화자 매개변수를 사용하여 매개변수를 지정되거나, 
기본 슈퍼클래스 생성자로 전달합니다. 
이 기능은 [리다이렉트 생성자](#redirecting-constructors)와 함께 사용할 수 없습니다. 
슈퍼 초기화자 매개변수는 [형식 매개변수 초기화](#use-initializing-formal-parameters)와 같은 구문과 의미를 갖습니다.

:::version-note
슈퍼 초기화 매개변수를 사용하려면 최소 2.17의 [언어 버전][language version]이 필요합니다. 
이전 언어 버전을 사용하는 경우, 모든 슈퍼 생성자 매개변수를 수동으로 전달해야 합니다.
:::

슈퍼 생성자 호출에 위치 인수가 포함된 경우, 
슈퍼 초기화자 매개변수는 위치 인수일 수 없습니다.

<?code-excerpt "super_initializer_positional_parameters.dart (positional)" plaster="none"?>
```dart
class Vector2d {
  final double x;
  final double y;

  Vector2d(this.x, this.y);
}

class Vector3d extends Vector2d {
  final double z;

  // x 및 y 매개변수를 다음과 같이 기본 슈퍼 생성자에 전달합니다.
  // Vector3d(final double x, final double y, this.z) : super(x, y);
  Vector3d(super.x, super.y, this.z);
}
```

더 자세히 설명하기 위해, 다음 예를 살펴보겠습니다.

```dart
  // 위치 인수와 함께 슈퍼 생성자(`super(0)`)를 호출하는 경우, 
  // 슈퍼 매개변수(`super.x`)를 사용하면 오류가 발생합니다.
  Vector3d.xAxisError(super.x): z = 0, super(0); // BAD
```

이 명명된 생성자는 `x` 값을 두 번 설정하려고 합니다. 
(1) 한 번은 슈퍼 생성자에서, (2) 한 번은 위치 슈퍼 매개변수로 설정하려고 합니다. 
둘 다 `x` 위치 매개변수를 처리하기 때문에, 오류가 발생합니다.

슈퍼 생성자에 명명된 인수가 있는 경우, 
명명된 슈퍼 매개변수(다음 예에서 `super.y`)와, 
슈퍼 생성자 호출에 대한 명명된 인수(`super.named(x: 0)`)로 분할할 수 있습니다.

<?code-excerpt "super_initializer_named_parameters.dart (named)" plaster="none"?>
```dart
class Vector2d {
  // ...
  Vector2d.named({required this.x, required this.y});
}

class Vector3d extends Vector2d {
  final double z;

  // 다음과 같이 y 매개변수를 지정된 슈퍼 생성자에 전달합니다.
  // Vector3d.yzPlane({required double y, required this.z})
  //       : super.named(x: 0, y: y);
  Vector3d.yzPlane({required super.y, required this.z}) : super.named(x: 0);
}
```

[language version]: /guides/language/evolution#language-versioning
[using constructors]: /language/classes#using-constructors
[late-final-ivar]: /effective-dart/design#avoid-public-late-final-fields-without-initializers
[static method]: /language/classes#static-methods
