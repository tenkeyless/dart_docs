---
# title: Class modifiers for API maintainers
title: API 유지 관리자를 위한 클래스 수정자
# description: >-
#  How to use the class modifiers added in Dart 3.0
#  to make your package's API more robust and maintainable.
description: >-
 Dart 3.0에 추가된 클래스 수정자를 사용하여, 패키지의 API를 보다 강력하고 유지 관리하기 쉽게 만드는 방법.
prevpage:
  url: /language/class-modifiers
  # title: Class modifiers
  title: 클래스 수정자
nextpage:
  url: /language/modifier-reference
  # title: Class modifiers reference
  title: 클래스 수정자 참조
---

Dart 3.0은 클래스와 [믹스인 선언][mixin]에 배치할 수 있는, 
몇 가지 [새로운 수정자][class modifiers]를 추가합니다. 
라이브러리 패키지의 작성자인 경우, 
이러한 수정자를 사용하면 당신의 패키지에서 export 하는 타입으로, 
사용자가 수행할 수 있는 작업을 더 많이 제어할 수 있습니다. 
이를 통해 패키지를 더 쉽게 개발하고, 
코드 변경으로 인해 사용자에게 문제가 발생할 수 있는지 더 쉽게 알 수 있습니다.

[class modifiers]: /language/class-modifiers
[mixin]: /language/mixins

Dart 3.0에는 클래스를 믹스인으로 사용하는 것과 관련된 [중단 변경 사항](/resources/dart-3-migration#mixin)도 포함되어 있습니다.
이 변경은 *당신의* 클래스를 손상시키지는 않지만, 당신의 클래스의 *사용자*를 손상시킬 수 있습니다.

이 가이드는 이러한 변경 사항을 안내하여 새로운 수정자를 사용하는 방법과, 
라이브러리 사용자에게 어떤 영향을 미치는지 알 수 있도록 합니다.

## 클래스의 `mixin` 수정자 {:#the-mixin-modifier-on-classes}

가장 중요한 수정자는 `mixin`입니다. 
Dart 3.0 이전의 언어 버전에서는, 
클래스가 다른 클래스의 `with` 절에서 믹스인으로 사용될 수 있도록 허용합니다. 
단, 클래스가 다음과 같은 경우는 _예외입니다._

* 팩토리가 아닌 생성자를 선언합니다.
* `Object`가 아닌 클래스를 확장합니다.

이렇게 하면 다른 사람이 `with` 절에서 사용하고 있다는 사실을 깨닫지 못한 채 생성자나 `extends` 절을 클래스에 추가하여, 
다른 사람의 코드를 실수로 손상시키기 쉽습니다.

Dart 3.0에서는 더 이상 기본적으로 클래스를 믹스인으로 사용할 수 없습니다. 
대신 `mixin class`를 선언하여, 해당 동작을 명시적으로 선택해야(opt-in) 합니다.

```dart
mixin class Both {}

class UseAsMixin with Both {}
class UseAsSuperclass extends Both {}
```

패키지를 Dart 3.0으로 업데이트하고 코드를 변경하지 않으면, 오류가 표시되지 않을 수 있습니다. 
하지만, 사용자가 클래스를 믹스인으로 사용하는 경우, 실수로 패키지 사용자를 망가뜨릴 수 있습니다.

### 믹스인으로 클래스 마이그레이션 {:#migrating-classes-as-mixins}

클래스에 팩토리가 아닌 생성자, `extends` 절 또는 `with` 절이 있는 경우, 이미 믹스인으로 사용할 수 없습니다. 
동작은 Dart 3.0에서 변경되지 않습니다. 걱정할 것도 없고, 할 일도 없습니다.

실제로, 이는 기존 클래스의 약 90%를 설명합니다. 
믹스인으로 사용할 수 있는 나머지 클래스의 경우, 무엇을 지원할지 결정해야 합니다.

다음은 결정하는 데 도움이 되는 몇 가지 질문입니다. 첫 번째는 실용적(pragmatic)입니다.

* **사용자를 중단시킬 위험을 감수하고 싶습니까?** 
  * 답이 단호하게 "아니오"인 경우, 
    [믹스인으로 사용할 수 있는](#the-mixin-modifier-on-classes) 모든 클래스 앞에 `mixin`을 붙입니다. 
    이렇게 하면, API의 기존 동작이 그대로 유지됩니다.

반면, API가 제공하는 기능을 재고할 기회를 잡고 싶다면, 
그것을 `mixin class`로 바꾸지 *않는* 것이 좋습니다. 
다음 두 가지 디자인 질문을 고려하세요.

* **사용자가 직접 인스턴스를 구성할 수 있기를 원하십니까?**
  * 다시 말해, 클래스가 의도적으로 추상적이지 않습니까?

* **사람들이 선언을 믹스인으로 사용할 수 있기를 *원하십니까?***
  * 다시 말해, `with` 절에서 사용할 수 있기를 원하십니까?

두 질문에 대한 답이 모두 "예"이면 믹스인 클래스로 만드세요. 
두 번째 질문에 대한 답이 "아니요"이면, 그냥 클래스로 두세요. 
첫 번째 질문에 대한 답이 "아니요"이고, 두 번째 질문에 대한 답이 "예"이면, 클래스에서 믹스인 선언으로 변경하세요.

마지막 두 가지 옵션, 즉 클래스로 두거나, 순수한 믹스인으로 바꾸는 것은 API 변경을 깨는 것입니다. 
이렇게 하면 패키지의 major 버전을 올려야 합니다.

## 기타 선택형(opt-in) 수정자 {:#other-opt-in-modifiers}

클래스를 믹스인으로 처리하는 것은 패키지의 API에 영향을 미치는 Dart 3.0의 유일한 중요한 변경 사항입니다. 
여기까지 왔다면, 패키지에서 사용자가 할 수 있는 다른 변경 사항을 만들고 싶지 않으면 중단할 수 있습니다.

아래에 설명된 수정자를 계속 사용하고 사용하는 경우, 
패키지의 API에 대한 잠재적인 중단 변경이 되어, major 버전 증가가 필요합니다.

## `interface` 수정자 {:#the-interface-modifier}

Dart에는 순수 인터페이스를 선언하기 위한 별도의 구문이 없습니다. 
대신, 추상 메서드만 포함하는 추상 클래스를 선언합니다. 
사용자가 패키지의 API에서 해당 클래스를 볼 때, 
클래스를 확장하여 재사용할 수 있는 코드가 포함되어 있는지, 
또는 인터페이스로 사용하도록 의도된 것인지 알 수 없습니다.

클래스에 [`interface`](/language/class-modifiers#interface) 수정자를 넣어 이를 명확히 할 수 있습니다. 
그러면 `implements` 절에서 클래스를 사용할 수 있지만, `extends`에서 사용할 수는 없습니다.

클래스에 추상이 아닌 메서드가 *있는 경우에도*, 
사용자가 클래스를 확장하지 못하도록 할 수 있습니다. 
상속은 코드 재사용을 가능하게 하기 때문에, 소프트웨어에서 가장 강력한 결합 중 하나입니다. 
하지만 이러한 결합은 [위험하고 취약합니다][dangerous and fragile]. 
상속이 패키지 경계를 넘을 때, 
하위 클래스를 깨지 않고 슈퍼클래스를 진화시키기 어려울 수 있습니다.

[dangerous and fragile]: https://en.wikipedia.org/wiki/Fragile_base_class

클래스를 `interface`로 표시하면, 
사용자가 클래스를 구성하고(`abstract`로 표시된 경우 제외), 
클래스의 인터페이스를 구현할 수 있지만, 
해당 클래스의 코드를 재사용할 수는 없습니다.

클래스가 `interface`로 표시되면, 
클래스가 선언된 라이브러리 내에서 제한을 무시할 수 있습니다. 
라이브러리 내부에서는, 모든 코드가 자신의 코드이고, 
무엇을 하는지 알고 있으므로 자유롭게 확장할 수 있습니다. 
이 제한은 다른 패키지와, 자신의 패키지 내의 다른 라이브러리에도 적용됩니다.

## `base` 수정자 {:#the-base-modifier}

[`base`](/language/class-modifiers#base) 수정자는 `interface`와 다소 반대입니다. 
`extends` 절에서 클래스를 사용하거나, 
`with` 절에서 mixin 또는 mixin 클래스를 사용할 수 있습니다. 
하지만, 클래스 라이브러리 외부의 코드가, 
`implements` 절에서 클래스 또는 mixin을 사용하는 것을, 
허용하지 않습니다.

이렇게 하면 클래스 또는 mixin 인터페이스의 인스턴스인 모든 객체가 실제 구현을 상속합니다. 
특히, 이는 모든 인스턴스에 클래스 또는 mixin이 선언하는 모든 private 멤버가 포함된다는 것을 의미합니다. 
이렇게 하면 그렇지 않으면 발생할 수 있는 런타임 오류를 방지하는 데 도움이 될 수 있습니다.

다음 라이브러리를 고려하세요.

```dart title="a.dart"
class A {
  void _privateMethod() {
    print('I inherited from A');
  }
}

void callPrivateMethod(A a) {
  a._privateMethod();
}
```

이 코드는 그 자체로는 괜찮아 보이지만, 
사용자가 이와 같이 또 다른 라이브러리를 만드는 것을 막을 수는 없습니다.

```dart title="b.dart"
import 'a.dart';

class B implements A {
  // _privateMethod()를 구현하지 않았습니다!
}

main() {
  callPrivateMethod(B()); // 런타임 예외!
}
```

클래스에 `base` 수정자를 추가하면, 이러한 런타임 오류를 방지하는 데 도움이 될 수 있습니다. 
`interface`와 마찬가지로, `base` 클래스나 믹스인이 선언된 동일한 라이브러리에서 이 제한을 무시할 수 있습니다. 그러면 동일한 라이브러리의 하위 클래스에 private 메서드를 구현하라는 알림이 표시됩니다. 
하지만 다음 섹션은 *적용됩니다*.

### 베이스 전이성 {:#base-transitivity}

클래스를 `base`로 표시하는 목적은 해당 타입의 모든 인스턴스가 구체적으로 상속하도록 하는 것입니다. 
이를 유지하기 위해, base 제한은 "전염성(contagious)"이 있습니다. 
`base`로 표시된 타입의 모든 하위 타입(*직접적이든 간접적이든*)도 구현되지 않아야 합니다. 
즉, `base`(또는 다음에 다룰 `final` 또는 `sealed`)로 표시되어야 합니다.

따라서, 타입에 `base`를 적용하려면 약간의 주의가 필요합니다. 
이는 사용자가 클래스나 믹스인으로 무엇을 할 수 있는지에 영향을 미칠 뿐만 아니라, 
*그들의* 하위 클래스가 제공할 수 있는 가능성에도 영향을 미칩니다. 
타입에 `base`를 적용하면, 그 아래의 전체 계층 구조가 구현되지 않습니다.

이는 강렬한 것처럼 들리지만, 대부분의 다른 프로그래밍 언어는 항상 이런 방식으로 작동했습니다. 
대부분은 암묵적 인터페이스가 전혀 없으므로, 
Java, C# 또는 다른 언어에서 클래스를 선언하면, 
사실상 동일한 제약 조건이 적용됩니다.

## `final` 수정자 {:#the-final-modifier}

`interface`와 `base`의 모든 제한을 원하면, 
클래스나 믹스인 클래스를 [`final`](/language/class-modifiers#final)로 표시할 수 있습니다. 
이렇게 하면, 라이브러리 외부의 누구도 어떤 종류의 하위 타입도 만들 수 없습니다. 
`implements`, `extends`, `with` 또는 `on` 절에서 사용할 수 없습니다.

이는 클래스 사용자에게 가장 제한적입니다. 
할 수 있는 일은 클래스를 구성하는 것뿐입니다. (`abstract`로 표시되지 않는 한)
그 대가로, 클래스 유지 관리자로서 가장 적은 제한을 받습니다. 
하위 사용자를 망가뜨릴 걱정 없이, 새로운 메서드를 추가하고, 
생성자를 팩토리 생성자로 전환할 수 있습니다.

<a id="the-sealed-modifer"></a>
## `sealed` 수정자 {:#the-sealed-modifier}

마지막 수정자, [`sealed`](/language/class-modifiers#sealed)는 특별합니다. 
주로 패턴 매칭에서 [철저성(exhaustiveness) 검사][exhaustiveness checking]를 가능하게 하기 위해 존재합니다. 
스위치에 `sealed`로 표시된 유형의 모든 직접 하위 타입에 대한 케이스가 있는 경우, 
컴파일러는 스위치가 철저(exhaustive)하다는 것을 알고 있습니다.

[exhaustiveness checking]: /language/branches#exhaustiveness-checking

<?code-excerpt "language/lib/class_modifiers/sealed_exhaustiveness.dart"?>
```dart title="amigos.dart"
sealed class Amigo {}

class Lucky extends Amigo {}

class Dusty extends Amigo {}

class Ned extends Amigo {}

String lastName(Amigo amigo) => switch (amigo) {
      Lucky _ => 'Day',
      Dusty _ => 'Bottoms',
      Ned _ => 'Nederlander',
    };
```

이 스위치는 `Amigo`의 각 하위 타입에 대한 케이스를 갖습니다. 
컴파일러는 `Amigo`의 모든 인스턴스가 해당 하위 타입 중 하나의 인스턴스여야 한다는 것을 알고 있으므로, 
스위치가 안전하게 철저(exhaustive)하며, 최종 디폴트 케이스가 필요하지 않다는 것을 알고 있습니다.

이를 sound하게 하기 위해, 컴파일러는 두 가지 제한을 적용합니다.

1. sealed 클래스 자체는 직접 생성할 수 없습니다. 
   그렇지 않으면, 하위 타입의 *어떤* 인스턴스도 아닌 `Amigo`의 인스턴스가 있을 수 있습니다. 
   따라서, 모든 `sealed` 클래스는 암묵적으로 `abstract`이기도 합니다.

2. sealed 타입의 모든 직접 하위 타입은 sealed 타입이 선언된 동일한 라이브러리에 있어야 합니다. 
   이렇게 하면 컴파일러가 모든 하위 타입을 찾을 수 있습니다. 
   케이스와 일치하지 않는 다른 숨겨진 하위 타입이 떠다니지 않는다는 것을 알고 있습니다.

두 번째 제한은 `final`과 비슷합니다. 
`final`과 마찬가지로, `sealed`로 표시된 클래스는, 
선언된 라이브러리 외부에서 직접 확장(extended), 구현(implemented) 또는 혼합(mixed in)될 수 없음을 의미합니다. 
그러나, `base` 및 `final`과 달리 *전이적* 제한은 없습니다.

```dart title="amigo.dart"
sealed class Amigo {}
class Lucky extends Amigo {}
class Dusty extends Amigo {}
class Ned extends Amigo {}
```

```dart title="other.dart"
// 이것은 오류입니다:
class Bad extends Amigo {}

// 하지만 이들은 둘 다 괜찮습니다.
class OtherLucky extends Lucky {}
class OtherDusty implements Dusty {}
```

물론, sealed 타입의 하위 타입도 제한되기를 원한다면, 
`interface`, `base`, `final` 또는 `sealed`를 사용하여 표시하면 됩니다.

### `sealed` vs `final` {:#sealed-versus-final}

사용자가 직접 하위 타입을 지정할 수 없게 하려는 클래스가 있는 경우, 
언제 `sealed`를 사용하고 언제 `final`을 사용해야 할까요? 
몇 가지 간단한 규칙이 있습니다.:

* 사용자가 클래스 인스턴스를 직접 생성할 수 있게 하려는 경우, 
  sealed 타입이 암묵적으로 추상화되므로 `sealed`를 사용할 수 없습니다.

* 클래스에 라이브러리에 하위 타입이 없는 경우, 
  완전성(exhaustiveness) 검사의 이점을 얻을 수 없으므로, `sealed`를 사용할 필요가 없습니다.

그렇지 않으면, 클래스에 정의한 하위 타입이 있는 경우, `sealed`가 원하는 것일 가능성이 큽니다. 
사용자가 클래스에 몇 가지 하위 타입이 있는 것을 알게 되면, 
각각을 스위치 케이스로 별도로 처리하고, 
컴파일러가 전체 타입이 포함된다는 것을 알 수 있으면 편리합니다.

`sealed`를 사용하면, 나중에 라이브러리에 다른 하위 타입을 추가하면, 
API 변경이 중단된다는 것을 의미합니다. 
새 하위 타입이 나타나면, 모든 기존 스위치는 새 타입을 처리하지 않으므로 비철저(non-exhaustive)해집니다. 
열거형에 새 값을 추가하는 것과 똑같습니다.

이러한 비철저(non-exhaustive)한 스위치 컴파일 오류는 사용자에게 *유용합니다*. 
사용자가 코드에서 새 타입을 처리해야 하는 위치에 주의를 기울이기 때문입니다.

하지만 새 하위 타입을 추가할 때마다, 중요한 변경사항(breaking change)이라는 것을 의미합니다. 
중단되지 않는(non-breaking) 방식으로 새 하위 타입을 추가할 수 있는 자유를 원한다면, 
`sealed` 대신 `final`을 사용하여 슈퍼타입을 표시하는 것이 좋습니다. 
즉, 사용자가 해당 슈퍼타입의 값을 switches on 하면, 
모든 하위 타입에 대한 케이스가 있더라도, 
컴파일러가 다른 디폴트 케이스를 추가하도록 강제합니다. 
그러면 나중에 하위 타입을 더 추가하면, 해당 기본 케이스가 실행됩니다.

## 요약 {:#summary}

API 설계자로서, 이러한 새로운 수정자는 사용자가 코드를 사용하는 방식을 제어할 수 있게 해주고, 
반대로 사용자의 코드를 손상시키지 않고 코드를 어떻게 발전시킬 수 있는지 제어할 수 있게 해줍니다.

하지만 이러한 옵션은 복잡성을 수반합니다. 이제 API 설계자로서 더 많은 선택권을 갖게 되었습니다. 
또한 이러한 기능이 새롭기 때문에 모범 사례가 무엇인지 아직 알 수 없습니다. 
모든 언어의 생태계는 다르고 요구 사항도 다릅니다.

다행히도, 한꺼번에 모든 것을 알아낼 필요는 없습니다. 
아무것도 하지 않더라도 클래스가 대부분 3.0 이전과 동일한 기능을 갖도록, 의도적으로 기본값을 선택했습니다. 
API를 원래대로 유지하고 싶다면, 이미 해당 기능을 지원하는 클래스에 `mixin`을 추가하면 됩니다.

시간이 지나면서, 더 세밀하게 제어하고 싶은 부분이 어디인지 알게 되면, 
다른 수정자를 적용하는 것을 고려할 수 있습니다.

* `interface`를 사용하면, 사용자가 클래스의 코드를 재사용하지 못하게 하면서, 
  인터페이스를 다시 구현할 수 있습니다.

* `base`를 사용하면, 
  사용자가 클래스의 코드를 재사용하도록 요구하고, 
  클래스 타입의 모든 인스턴스가 해당 실제 클래스 또는 하위 클래스의 인스턴스인지 확인할 수 있습니다.

* `final`을 사용하면, 클래스가 확장되는 것을 완전히 방지할 수 있습니다.

* `sealed`를 사용하면, 하위 타입 패밀리에 대한 완전성 검사(exhaustiveness checking)를 선택할 수 있습니다.

그럴 경우, 패키지를 게시할 때 major 버전을 증가시킵니다. 
이러한 수정자는 모두 중요한 변경사항인 제한을 의미하기 때문입니다.