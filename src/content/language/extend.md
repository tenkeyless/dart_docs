---
# title: Extend a class
title: 클래스 확장
description: 슈퍼클래스에서 하위 클래스를 만드는 방법을 알아보세요.
prevpage:
  url: /language/methods
  # title: Methods
  title: 메서드
nextpage:
  url: /language/mixins
  title: Mixins
---

`extends`를 사용하여 하위 클래스를 생성하고, 
`super`를 사용하여 상위 클래스를 참조합니다.

<?code-excerpt "misc/lib/language_tour/classes/extends.dart (smart-tv)" replace="/extends|super/[!$&!]/g"?>
```dart
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ···
}

class SmartTelevision [!extends!] Television {
  void turnOn() {
    [!super!].turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ···
}
```

`extends`의 다른 사용법은, 
제네릭 페이지에서 [매개변수화된 타입][parameterized types]에 대한 논의를 참조하세요.

## 멤버 재정의 {:#overriding-members}

하위 클래스는 인스턴스 메서드([operators][] 포함), getters, setters를 오버라이드할 수 있습니다. 
`@override` 주석을 사용하여 멤버를 의도적으로 오버라이드한다는 것을 나타낼 수 있습니다.

<?code-excerpt "misc/lib/language_tour/metadata/television.dart (override)" replace="/@override/[!$&!]/g"?>
```dart
class Television {
  // ···
  set contrast(int value) {...}
}

class SmartTelevision extends Television {
  [!@override!]
  set contrast(num value) {...}
  // ···
}
```

오버라이딩 메서드 선언은 여러 가지 방법으로 오버라이딩하는 메서드(들)와 일치해야 합니다.

* 반환 타입은 오버라이딩된 메서드의 반환 타입과 같은 타입(또는 하위 타입)이어야 합니다.
* 매개변수 타입은 오버라이딩된 메서드의 매개변수 타입과 같은 타입(또는 상위 타입)이어야 합니다. 
  앞의 예에서, `SmartTelevision`의 `contrast` setter는 매개변수 타입을, 
  `int`에서 상위 타입인 `num`으로 변경합니다.
* 오버라이딩된 메서드가 _n_ 개의 위치 매개변수를 허용하는 경우, 
  오버라이딩 메서드도 _n_ 개의 위치 매개변수를 허용해야 합니다.
* [제네릭 메서드][generic method]는 제네릭이 아닌 메서드를 오버라이딩할 수 없고, 
  제네릭이 아닌 메서드는 제네릭 메서드를 오버라이딩할 수 없습니다.

때로는 메서드 매개변수나 인스턴스 변수의 타입을 좁히고 싶을 수 있습니다. 
이는 일반적인 규칙을 위반하며, 런타임에 타입 오류를 일으킬 수 있다는 점에서 다운캐스트와 비슷합니다. 
그래도, 코드가 타입 오류가 발생하지 않을 것이라고 보장할 수 있다면, 타입을 좁히는 것이 가능합니다. 
이 경우, 매개변수 선언에서 [`covariant` 키워드](/guides/language/sound-problems#the-covariant-keyword)를 사용할 수 있습니다. 
자세한 내용은 [Dart 언어 사양][Dart language specification]을 참조하세요.

:::warning
`==`를 오버라이드하는 경우, Object의 `hashCode` getter도 오버라이드해야 합니다. 
`==`와 `hashCode`를 오버라이드하는 예는 [맵 키 구현](/libraries/dart-core#implementing-map-keys)을 확인하세요.
:::

## noSuchMethod() {:#nosuchmethod}

코드가 존재하지 않는 메서드나 인스턴스 변수를 사용하려고 할 때마다 이를 감지하거나 대응하려면, 
`noSuchMethod()`를 재정의할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/classes/no_such_method.dart (no-such-method-impl)" replace="/noSuchMethod(?!,)/[!$&!]/g"?>
```dart
class A {
  // noSuchMethod를 재정의하지 않는 한, 
  // 존재하지 않는 멤버를 사용하면 NoSuchMethodError가 발생합니다.
  @override
  void [!noSuchMethod!](Invocation invocation) {
    print('You tried to use a non-existent member: '
        '${invocation.memberName}');
  }
}
```

다음 중 **하나**가 참이 아니면, 구현되지 않은 메서드를 **호출할 수 없습니다**.

* 수신자(receiver)는 정적 타입 `dynamic`을 갖습니다.

* 수신자는 구현되지 않은 메서드를 정의하는 정적 타입(추상도 가능)을 갖고, 
  수신자의 동적 타입은 `Object` 클래스의 구현과 다른 `noSuchMethod()` 구현을 갖습니다.

자세한 내용은 비공식적인 [noSuchMethod 전달 사양]({{site.repo.dart.lang}}/blob/main/archive/feature-specifications/nosuchmethod-forwarding.md)을 참조하세요.

[parameterized types]: /language/generics#restricting-the-parameterized-type
[operators]: /language/methods#operators
[generic method]: /language/generics#using-generic-methods
[Dart language specification]: /guides/language/spec
