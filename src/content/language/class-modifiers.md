---
# title: Class modifiers
title: 클래스 수정자
# description: >-
#   Modifier keywords for class declarations to control external library access.
description: >-
  외부 라이브러리 액세스를 제어하기 위한, 클래스 선언에 대한 수정자 키워드입니다.
prevpage:
  url: /language/callable-objects
  # title: Callable objects
  title: 호출 가능한 객체
nextpage:
  url: /language/class-modifiers-for-apis
  # title: Class modifiers for API maintainers
  title: API 유지 관리자를 위한 클래스 수정자
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore: (stable|beta|dev)[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore: (stable|beta|dev)[^\n]+\n/$1\n/g; /. • (lib|test)\/\w+\.dart:\d+:\d+//g"?>

:::version-note
`abstract` 외의 클래스 수정자는, 최소 3.0의 [언어 버전][language version]이 필요합니다.
:::

클래스 수정자는 클래스 또는 믹스인이 [자체 라이브러리 내부에서](#abstract)와, 
정의된 라이브러리 외부에서 어떻게 사용될 수 있는지 제어합니다.

수정자(Modifier) 키워드는 클래스 또는 믹스인 선언 앞에 옵니다. 
예를 들어, `abstract class`를 쓰면 추상 클래스가 정의됩니다. 
클래스 선언 앞에 나타날 수 있는 수정자의 전체 세트는 다음과 같습니다.

- `abstract`
- `base`
- `final`
- `interface`
- `sealed`
- [`mixin`][class, mixin, or mixin class]

`base` 수정자만 믹스인 선언 앞에 나타날 수 있습니다. 
수정자는 `enum`, `typedef`, `extension` 또는 `extension type`과 같은 다른 선언에는 적용되지 않습니다.

클래스 수정자를 사용할지 여부를 결정할 때, 
클래스의 의도된 용도와 클래스가 의존할 수 있어야 하는 동작을 고려하세요.

:::note
라이브러리를 유지 관리하는 경우, 
[API 유지 관리자를 위한 클래스 수정자](/language/class-modifiers-for-apis) 페이지를 읽어, 
라이브러리에 대한 이러한 변경 사항을 탐색하는 방법에 대한 지침을 확인하세요.
:::

## 수정자 없음 {:#no-modifier}

라이브러리에서 생성하거나 하위 타입에 무제한 권한을 허용하려면, 
수정자 없이 `class` 또는 `mixin` 선언을 사용합니다. 
기본적으로 다음을 수행할 수 있습니다.

- 클래스의 새 인스턴스를 [생성][Construct]합니다.
- 클래스를 [확장][Extend]하여 새 하위 타입을 만듭니다.
- 클래스 또는 믹스인의 인터페이스를 [구현][Implement]합니다.
- 믹스인 또는 믹스인 클래스를 [믹스인][mixin]합니다.

## `abstract` {:#abstract}

전체 인터페이스의, 완전하고 구체적인 구현이 필요하지 않은 클래스를 정의하려면, `abstract` 수정자를 사용합니다.

추상 클래스는 자체 라이브러리나 외부 라이브러리에서 생성할 수 없습니다. 
추상 클래스는 종종 [추상 메서드][abstract methods]를 갖습니다.

<?code-excerpt "language/lib/class_modifiers/ex1/a.dart"?>
```dart title="a.dart"
abstract class Vehicle {
  void moveForward(int meters);
}
```

<?code-excerpt "language/lib/class_modifiers/ex1/b.dart (abstract-usages)"?>
```dart title="b.dart"
import 'a.dart';

// 오류: 생성할 수 없습니다.
Vehicle myVehicle = Vehicle();

// 확장 가능합니다.
class Car extends Vehicle {
  int passengers = 4;

  @override
  void moveForward(int meters) {
    // ...
  }
}

// 구현 가능합니다.
class MockVehicle implements Vehicle {
  @override
  void moveForward(int meters) {
    // ...
  }
}
```

추상 클래스가 인스턴스화 가능한 것처럼 보이게 하려면, [팩토리 생성자][factory constructor]를 정의하세요.

## `base` {:#base}

클래스 또는 믹스인 구현의 상속을 강제하려면, `base` 수정자를 사용합니다. 
베이스 클래스는 자체 라이브러리 외부에서 구현하는 것을 허용하지 않습니다. 
이렇게 하면 다음을 보장합니다.

- 베이스 클래스 생성자는 클래스 하위 타입의 인스턴스가 생성될 때마다 호출됩니다.
- 구현된 모든 private 멤버는 하위 타입에 존재합니다.
- `base` 클래스에서 새로 구현된 멤버는, 모든 하위 타입이 새 멤버를 상속하기 때문에, 하위 타입을 깨지 않습니다.
  - 하위 타입이 이미 같은 이름과 호환되지 않는 서명을 가진 멤버를 선언하지 않는 한, 이는 사실입니다.

베이스 클래스를 구현하거나 확장하는 모든 클래스는 `base`, `final` 또는 `sealed`로 표시해야 합니다. 
이렇게 하면 외부 라이브러리가 베이스 클래스 보장을 깨는 것을 방지할 수 있습니다.

<?code-excerpt "language/lib/class_modifiers/ex2/a.dart"?>
```dart title="a.dart"
base class Vehicle {
  void moveForward(int meters) {
    // ...
  }
}
```

<?code-excerpt "language/lib/class_modifiers/ex2/b.dart"?>
```dart title="b.dart"
import 'a.dart';

// 생성할 수 있습니다.
Vehicle myVehicle = Vehicle();

// 확장 가능합니다.
base class Car extends Vehicle {
  int passengers = 4;
  // ...
}

// 오류: 구현할 수 없습니다.
base class MockVehicle implements Vehicle {
  @override
  void moveForward() {
    // ...
  }
}
```

## `interface` {:#interface}

인터페이스를 정의하려면, `interface` 수정자를 사용합니다. 
인터페이스 자체 정의 라이브러리 외부의 라이브러리는 인터페이스를 구현할 수 있지만, 확장할 수는 없습니다.
 이렇게 하면 다음을 보장합니다.

- 클래스의 인스턴스 메서드 중 하나가 `this`에서 다른 인스턴스 메서드를 호출하면, 
  항상 동일한 라이브러리에서 메서드의 알려진 구현을 호출합니다.
- 다른 라이브러리는 인터페이스 클래스 자체 메서드가 
  나중에 예상치 못한 방식으로 호출할 수 있는 메서드를 재정의할 수 없습니다. 
  이렇게 하면 [취약한 베이스 클래스 문제][fragile base class problem]가 줄어듭니다.

<?code-excerpt "language/lib/class_modifiers/ex3/a.dart"?>
```dart title="a.dart"
interface class Vehicle {
  void moveForward(int meters) {
    // ...
  }
}
```

<?code-excerpt "language/lib/class_modifiers/ex3/b.dart"?>
```dart title="b.dart"
import 'a.dart';

// 생성할 수 있습니다.
Vehicle myVehicle = Vehicle();

// 오류: 상속받을 수 없습니다.
class Car extends Vehicle {
  int passengers = 4;
  // ...
}

// 구현 가능합니다.
class MockVehicle implements Vehicle {
  @override
  void moveForward(int meters) {
    // ...
  }
}
```

### `abstract interface` {:#abstract-interface}

`interface` 수정자의 가장 일반적인 용도는 순수 인터페이스를 정의하는 것입니다. 
`abstract interface class`은 `interface`와 [`abstract`](#abstract) 수정자를 [결합](#combining-modifiers)하는 것입니다.

`interface` 클래스와 마찬가지로, 다른 라이브러리는 순수 인터페이스를 구현할 수 있지만, 상속할 수는 없습니다. 
`abstract` 클래스와 마찬가지로, 순수 인터페이스는 추상 멤버를 가질 수 있습니다.

## `final` {:#final}

타입 계층을 닫으려면, `final` 수정자를 사용합니다. 
이렇게 하면 현재 라이브러리 외부의 클래스에서 하위 타입 지정이 방지됩니다. 
상속과 구현을 모두 허용하지 않으면, 하위 타입 지정이 완전히 방지됩니다. 
이렇게 하면 다음을 보장합니다.

- API에 증분 변경 사항(incremental changes)을 안전하게 추가할 수 있습니다.
- 타사 하위 클래스에서 덮어쓰이지 않았다는 것을 알고, 인스턴스 메서드를 호출할 수 있습니다.

Final 클래스는 동일한 라이브러리 내에서 확장되거나 구현될 수 있습니다. 
`final` 수정자는 `base`의 효과를 포함하므로, 
모든 하위 클래스도 `base`, `final` 또는 `sealed`로 표시되어야 합니다.

<?code-excerpt "language/lib/class_modifiers/ex4/a.dart"?>
```dart title="a.dart"
final class Vehicle {
  void moveForward(int meters) {
    // ...
  }
}
```

<?code-excerpt "language/lib/class_modifiers/ex4/b.dart"?>
```dart title="b.dart"
import 'a.dart';

// 생성할 수 있습니다.
Vehicle myVehicle = Vehicle();

// 오류: 상속받을 수 없습니다.
class Car extends Vehicle {
  int passengers = 4;
  // ...
}

class MockVehicle implements Vehicle {
  // 오류: 구현할 수 없습니다.
  @override
  void moveForward(int meters) {
    // ...
  }
}
```

## `sealed` {:#sealed}

알려진 열거형 하위 타입 세트를 만들려면, `sealed` 수정자를 사용합니다. 
이렇게 하면, 정적으로 [_완전함(exhaustive)_][exhaustive]이 보장되는, 
하위 타입에 대한 스위치를 만들 수 있습니다.

`sealed` 수정자는 클래스가 자체 라이브러리 외부에서 확장되거나 구현되는 것을 방지합니다. 
Sealed 클래스는 암묵적으로 [추상](#abstract)입니다.

- 스스로 생성할 수 없습니다.
- [팩토리 생성자](/language/constructors#factory-constructors)를 가질 수 있습니다.
- 하위 클래스에서 사용할 생성자를 정의할 수 있습니다.

그러나, Sealed 클래스의 하위 클래스는, 암묵적으로 추상적이지 않습니다.

컴파일러는, 동일한 라이브러리에만 존재할 수 있기 때문에, 가능한 모든 직접 하위 타입을 인식합니다. 
이를 통해, 컴파일러는 스위치가 해당 케이스에서 가능한 모든 하위 타입을 철저히 처리하지 못할 때 경고할 수 있습니다.

<?code-excerpt "language/lib/class_modifiers/ex5/sealed.dart"?>
```dart
sealed class Vehicle {}

class Car extends Vehicle {}

class Truck implements Vehicle {}

class Bicycle extends Vehicle {}

// 오류: 인스턴스화할 수 없습니다.
Vehicle myVehicle = Vehicle();

// 하위 클래스를 인스턴스화할 수 있습니다.
Vehicle myCar = Car();

String getVehicleSound(Vehicle vehicle) {
  // 오류: 스위치에 Bicycle 하위 타입이나 디폴트 케이스가 없습니다.
  return switch (vehicle) {
    Car() => 'vroom',
    Truck() => 'VROOOOMM',
  };
}
```

[철저한(exhaustive) 스위치][exhaustive]를 원하지 않거나, 
나중에 API를 손상시키지 않고 하위 타입을 추가할 수 있게 하려는 경우, 
[`final`](#final) 수정자를 사용하세요. 
더 자세한 비교는 [`sealed` vs `final`](/language/class-modifiers-for-apis#sealed-versus-final)을 읽어보세요.

## 수정자 결합 {:#combining-modifiers}

레이어 제한에 대한 일부 수정자를 결합할 수 있습니다. 클래스 선언은 다음과 같습니다.

1. (선택 사항) `abstract`
   * 클래스가 추상 멤버를 포함할 수 있는지 여부를 설명하고, 인스턴스화를 방지합니다.
2. (선택 사항) `base`, `interface`, `final` 또는 `sealed` 중 하나
   * 클래스를 하위 타입으로 지정하는 다른 라이브러리에 대한 제한 사항을 설명합니다.
3. (선택 사항) `mixin`
   * 선언을 혼합(mixed in)할 수 있는지 여부를 설명합니다.
4. `class` 
   * 키워드 자체.

일부 수정자는 모순되거나, 중복되거나, 상호 배타적이기 때문에 결합할 수 없습니다.

* `abstract`와 `sealed`. 
  * [sealed](#sealed) 클래스는 암묵적으로 [abstract](#abstract)입니다.
* `interface`, `final` 또는 `sealed`와 `mixin`. 
  * 이러한 액세스 수정자는 [혼합(mixed in)][mixin]을 방지합니다.

클래스 수정자를 결합하는 방법에 대한 자세한 내용은, 
[클래스 수정자 참조][Class modifiers reference]를 확인하세요.

[Class modifiers reference]: /language/modifier-reference


[language version]: /guides/language/evolution#language-versioning
[class, mixin, or mixin class]: /language/mixins#class-mixin-or-mixin-class
[mixin]: /language/mixins
[fragile base class problem]: https://en.wikipedia.org/wiki/Fragile_base_class
[`noSuchMethod`]: /language/extend#nosuchmethod
[construct]: /language/constructors
[extend]: /language/extend
[implement]: /language/classes#implicit-interfaces
[factory constructor]: /language/constructors#factory-constructors
[exhaustive]: /language/branches#exhaustiveness-checking
[abstract methods]: /language/methods#abstract-methods
[syntax specification]: {{site.repo.dart.lang}}/blob/main/accepted/3.0/class-modifiers/feature-specification.md#syntax
