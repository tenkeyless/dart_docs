---
title: 열거형 타입
description: Dart의 열거형 타입에 대해 알아보세요.
short-title: Enums
prevpage:
  url: /language/mixins
  title: Mixins
nextpage:
  url: /language/extension-methods
  # title: Extension methods
  title: 확장 메서드
---

열거형은 종종 _열거형(enumerations)_ 또는 _열거형(enums)_ 이라고도 하며, 
고정된 수의 상수 값을 나타내는 데 사용되는 특수한 종류의 클래스입니다.

:::note
모든 열거형은 자동으로 [`Enum`][] 클래스를 확장합니다. 
또한 봉인(sealed)되어 있으므로, 하위 클래스화, 구현, 혼합(mixed in) 또는 명시적으로 인스턴스화할 수 없습니다.

추상 클래스와 믹스인은 `Enum`을 명시적으로 구현하거나 확장할 수 있지만, 
열거형 선언에 의해 구현되거나 혼합(mixed)되지 않는 한, 
어떤 객체도 실제로 해당 클래스 또는 믹스인의 타입을 구현할 수 없습니다.
:::

## 간단한 열거형 선언 {:#declaring-simple-enums}

간단한 열거형을 선언하려면, `enum` 키워드를 사용하고 열거할 값을 나열합니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (enum)"?>
```dart
enum Color { red, green, blue }
```

:::tip
복사-붙여넣기 오류를 방지하기 위해 열거형 타입을 선언할 때, 
[끝 쉼표][trailing commas]를 사용할 수도 있습니다.
:::

## 향상된 열거형 선언 {:#declaring-enhanced-enums}

Dart는 또한 필드, 메서드 및 (고정된 수의 알려진 상수 인스턴스로 제한된) const 생성자가 있는 클래스를 선언하는 enum 선언을 허용합니다.

향상된 enum을 선언하려면, 일반적인 [클래스][classes]와 유사한 구문을 따르지만, 몇 가지 추가 요구 사항이 있습니다.

* 인스턴스 변수는 [mixins][]에서 추가한 변수를 포함하여, `final`이어야 합니다.
* 모든 [생성형 생성자][generative constructors]는 상수여야 합니다.
* [팩토리 생성자][Factory constructors]는 고정된 알려진 enum 인스턴스 중 하나만 반환할 수 있습니다.
* 다른 클래스는 [`Enum`]이 자동으로 확장되므로, 확장할 수 없습니다.
* `index`, `hashCode`, 등식 연산자 `==`에 대한 재정의가 있을 수 없습니다.
* `values`라는 이름의 멤버는, 자동으로 생성된 정적 `values` getter와 충돌하므로, enum에서 선언할 수 없습니다.
* enum의 모든 인스턴스는 선언 시작 부분에서 선언해야 하며, 최소한 하나의 인스턴스가 선언되어야 합니다.

향상된 열거형의 인스턴스 메서드는, `this`를 사용하여 현재 열거형 값을 참조할 수 있습니다.

다음은 여러 인스턴스, 인스턴스 변수, getters 및 구현된 인터페이스가 있는, 향상된 열거형을 선언하는 예입니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (enhanced)"?>
```dart
enum Vehicle implements Comparable<Vehicle> {
  car(tires: 4, passengers: 5, carbonPerKilometer: 400),
  bus(tires: 6, passengers: 50, carbonPerKilometer: 800),
  bicycle(tires: 2, passengers: 1, carbonPerKilometer: 0);

  const Vehicle({
    required this.tires,
    required this.passengers,
    required this.carbonPerKilometer,
  });

  final int tires;
  final int passengers;
  final int carbonPerKilometer;

  int get carbonFootprint => (carbonPerKilometer / passengers).round();

  bool get isTwoWheeled => this == Vehicle.bicycle;

  @override
  int compareTo(Vehicle other) => carbonFootprint - other.carbonFootprint;
}
```

:::version-note
향상된 열거형에는 최소 2.17의 [언어 버전][language version]이 필요합니다.
:::

## 열거형 사용 {:#using-enums}

다른 [정적 변수][static variable]와 마찬가지로 열거된 값에 액세스합니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (access)"?>
```dart
final favoriteColor = Color.blue;
if (favoriteColor == Color.blue) {
  print('Your favorite color is blue!');
}
```

열거형의 각 값에는 `index` getter가 있는데, 
이는 열거형 선언에서 값의 0 기반 위치를 반환합니다. 
예를 들어, 첫 번째 값의 인덱스는 0이고, 두 번째 값의 인덱스는 1입니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (index)"?>
```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```

열거된 모든 값의 리스트를 얻으려면. 열거형의 `values` 상수를 사용합니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (values)"?>
```dart
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```

[switch 문][switch statements]에서 열거형을 사용할 수 있으며, 
열거형의 모든 값을 처리하지 않으면 경고가 표시됩니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (switch)"?>
```dart
var aColor = Color.blue;

switch (aColor) {
  case Color.red:
    print('Red as roses!');
  case Color.green:
    print('Green as grass!');
  default: // 이것이 없으면 경고가 표시됩니다.
    print(aColor); // 'Color.blue'
}
```

`Color.blue`에서 `'blue'`와 같이, 열거형 값의 이름에 액세스해야 하는 경우, `.name` 속성을 사용합니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (name)"?>
```dart
print(Color.blue.name); // 'blue'
```

일반 객체에서와 마찬가지로 열거형 값의 멤버에 액세스할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (method-call)"?>
```dart
print(Vehicle.car.carbonFootprint);
```

[`Enum`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Enum-class.html
[trailing commas]: /language/collections#lists
[classes]: /language/classes
[mixins]: /language/mixins
[generative constructors]: /language/constructors#constant-constructors
[Factory constructors]: /language/constructors#factory-constructors
[language version]: /guides/language/evolution#language-versioning
[static variable]: /language/classes#class-variables-and-methods
[switch statements]: /language/branches#switch
