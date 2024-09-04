---
# title: Methods
title: 메서드
description: Dart의 메서드에 대해 알아보세요.
prevpage:
  url: /language/constructors
  # title: Constructors
  title: 생성자
nextpage:
  url: /language/extend
  # title: Extend a class
  title: 클래스 확장
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>

메서드는 객체에 대한 동작을 제공하는 함수입니다.

## 인스턴스 메서드 {:#instance-methods}

객체의 인스턴스 메서드는 인스턴스 변수와 `this`에 액세스할 수 있습니다. 
다음 샘플의 `distanceTo()` 메서드는 인스턴스 메서드의 예입니다.

<?code-excerpt "misc/lib/language_tour/classes/point.dart (class-with-distance-to)" plaster="none"?>
```dart
import 'dart:math';

class Point {
  final double x;
  final double y;

  // 생성자 본문이 실행되기 전에 x 및 y 인스턴스 변수를 설정합니다.
  Point(this.x, this.y);

  double distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

## 연산자 {:#operators}

대부분 연산자는 특별한 이름을 가진 인스턴스 메서드입니다.
Dart에서는 다음 이름으로 연산자를 정의할 수 있습니다.

|       |      |      |      |       |      |
|-------|------|------|------|-------|------|
| `<`   | `>`  | `<=` | `>=` | `==`  | `~`  |
| `-`   | `+`  | `/`  | `~/` | `*`   | `%`  |
| `\|`  | `ˆ`  | `&`  | `<<` | `>>>` | `>>` |
| `[]=` | `[]` |      |      |       |      |

{:.table}

:::note
일부 [연산자][operators](예: `!=`)가 이름 리스트에 없는 것을 알아차렸을 것입니다. 
이러한 연산자는 인스턴스 메서드가 아닙니다. 
해당 동작은 Dart에 내장되어 있습니다.
:::

{%- comment %}
  Internal note from https://github.com/dart-lang/site-www/pull/2691#discussion_r506184100:
  -  `??`, `&&` and `||` are excluded because they are lazy / short-circuiting operators
  - `!` is probably excluded for historical reasons
{% endcomment %}

연산자를 선언하려면, 빌트인 식별자 `operator`를 사용한 다음, 정의하려는 연산자를 사용합니다. 
다음 예에서는 벡터 덧셈(`+`), 뺄셈(`-`), 동등(`==`)을 정의합니다.

<?code-excerpt "misc/lib/language_tour/classes/vector.dart"?>
```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;

  @override
  int get hashCode => Object.hash(x, y);
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```

## Getters 및 setters {:#getters-and-setters}

getters와 setters는 객체의 속성에 대한 읽기 및 쓰기 액세스를 제공하는 특수 메서드입니다. 
각 인스턴스 변수에는 암묵적 getter와 적절한 경우, setter가 있다는 점을 기억하세요. 
`get` 및 `set` 키워드를 사용하여, getters와 setters를 구현하여 추가 속성을 만들 수 있습니다.

<?code-excerpt "misc/lib/language_tour/classes/rectangle.dart"?>
```dart
class Rectangle {
  double left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // 두 개의 계산된 속성(right 및 bottom)을 정의합니다.
  double get right => left + width;
  set right(double value) => left = value - width;
  double get bottom => top + height;
  set bottom(double value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```

getters와 setters를 사용하면, 
인스턴스 변수로 시작한 다음 나중에 메서드로 래핑할 수 있으며, 
클라이언트 코드를 변경하지 않아도 됩니다.

:::note
증가(++)와 같은 연산자는 getter가 명시적으로 정의되어 있든 아니든 예상대로 작동합니다. 
예상치 못한 부수 효과를 피하기 위해, 연산자는 getter를 정확히 한 번 호출하여, 임시 변수에 값을 저장합니다.
:::

## 추상 메서드 {:#abstract-methods}

인스턴스, getter, setter 메서드는 추상화할 수 있으며, 
인터페이스를 정의하지만, 구현은 다른 클래스에 맡길 수 있습니다. 
추상 메서드는 [추상 클래스][abstract classes] 또는 [믹스인][mixins]에만 존재할 수 있습니다.

메서드를 추상화하려면, 메서드 본문 대신 세미콜론(`;`)을 사용합니다.

<?code-excerpt "misc/lib/language_tour/classes/doer.dart"?>
```dart
abstract class Doer {
  // 인스턴스 변수와 메서드를 정의합니다...

  void doSomething(); // 추상 메서드를 정의합니다.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // 구현을 제공해서, 여기서는 메서드가 추상적이지 않도록 하세요...
  }
}
```

[operators]: /language/operators
[abstract classes]: /language/class-modifiers#abstract
[mixins]: /language/mixins
