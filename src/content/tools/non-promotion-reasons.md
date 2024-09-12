---
# title: Fixing type promotion failures
title: 타입 승격 실패 고치기
# description: >-
#   Solutions for cases where you know more about a
#   field's type than Dart can determine.
description: >-
  Dart가 판단할 수 있는 것보다 필드 타입에 대해 더 많이 알고 있는 경우에 대한 솔루션입니다.
---

[타입 승격][Type promotion]은 흐름 분석에서 [nullable 타입][nullable type]이 있는 변수가 *null이 아니며*, 
그 지점부터 변경되지 않을 것임을 확실히 확인할 수 있을 때 발생합니다. 
많은 상황에서 타입의 건전성(soundness)이 약해져 타입 승격이 실패할 수 있습니다.

이 페이지에서는 타입 승격이 실패하는 이유와 이를 수정하는 방법에 대한 팁을 나열합니다. 
흐름 분석과 타입 승격에 대해 자세히 알아보려면, [null 안전성 이해][Understanding null safety] 페이지를 확인하세요.

[Type promotion]: /null-safety/understanding-null-safety#type-promotion-on-null-checks
[nullable type]: /null-safety/understanding-null-safety#non-nullable-and-nullable-types
[Understanding null safety]: /null-safety/understanding-null-safety

## 필드 승격에 지원되지 않는 언어 버전 {:#language-version}

**원인:**
필드를 승격하려고 하지만, 필드 승격이 언어 버전으로 지정되어 있고, 
코드가 3.2 이전의 언어 버전으로 설정되어 있습니다.

이미 Dart 3.2 이상의 SDK 버전을 사용 중이라면, 
코드가 여전히 이전 [언어 버전][language version]을 명시적으로 대상으로 지정되어 있을 수 있습니다. 
이는 다음 중 하나의 이유로 발생할 수 있습니다.

* [`pubspec.yaml`][]이 3.2 미만의 하한을 가진 SDK 제약 조건을 선언하거나
* 파일 맨 위에 `// @dart=version` 주석이 있는데, 여기서 `version`은 3.2보다 낮습니다.

**예제:**

```dart tag=bad
// @dart=3.1

class C {
  final int? _i;
  C(this._i);

  void f() {
    if (_i != null) {
      int i = _i;  // ERROR
    }
  }
}
```

**메시지:**

```plaintext
'_i' refers to a field. It couldn't be promoted because field promotion is only available in Dart 3.2 and above.
```

**솔루션:**

라이브러리가 3.2 이전의 [언어 버전][language version]을 사용하지 않는지 확인하세요. 
파일 맨 위에서, 오래된 `// @dart=version` 주석을 확인하거나, 
`pubspec.yaml`에서 오래된 [SDK 제약 하한][SDK constraint lower-bound]을 확인하세요.

[`pubspec.yaml`]: /tools/pub/pubspec
[SDK constraint lower-bound]: /tools/pub/pubspec#sdk-constraints

## 지역 변수만 승격될 수 있음 (Dart 3.2 이전) {:#property}

**원인:**
속성을 승격하려고 하지만, 
Dart 3.2 이전 버전에서는 로컬 변수만 승격할 수 있으며, 
3.2 이전 버전을 사용하고 있습니다.

**예제:**

```dart tag=bad
class C {
  int? i;
  void f() {
    if (i == null) return;
    print(i.isEven);       // 에러
  }
}
```

**메시지:**

```plaintext
'i' refers to a property so it couldn't be promoted.
```

**솔루션:**

Dart 3.1 또는 이전 버전을 사용하는 경우, [3.2 이상으로 업그레이드][upgrade]하세요.

이전 버전을 계속 사용해야 하는 경우, 
[기타 원인 및 해결 방법](#other-causes-and-workarounds)을 읽어보세요.

[upgrade]: /get-dart

## 기타 원인 및 해결 방법 {:#other-causes-and-workarounds}

이 페이지의 나머지 예제는 버전 불일치와 관련 없는 승격 실패의 이유, 
필드 및 로컬 변수 실패에 대한 이유, 예제 및 해결 방법을 문서화합니다.

일반적으로 승격 실패에 대한 일반적인 수정 사항은 다음 중 하나 이상입니다.

* 필요한 non-nullable 타입으로 로컬 변수에 속성 ​​값을 할당합니다.
* 명시적 null 검사를 추가합니다. (예: `i == null`)
* 표현식이 `null`이 될 수 없다고 확신하는 경우, `!` 또는 `as`를 [중복 검사](#redundant-check)로 사용합니다.

다음은 `i` 값을 보유하는 로컬 변수(`i`로 명명할 수 있음)를 만드는 예입니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (property-copy)" replace="/final.*/[!$&!]/g"?>
```dart tag=good
class C {
  int? i;
  void f() {
    [!final i = this.i;!]
    if (i == null) return;
    print(i.isEven);
  }
}
```

이 예제에서는 인스턴스 필드가 등장하지만, 
대신 인스턴스 getter, 정적 필드 또는 getter, 최상위 변수 또는 getter 또는 [`this`](#this)를 사용할 수 있습니다.

:::tip
필드 값을 보관하기 위한 로컬 변수를 생성할 때, **변수를 `final`로 만드세요.** 
이렇게 하면 필드를 업데이트하려고 할 때, 실수로 로컬 변수를 업데이트할 수 없습니다.
:::

`i!`를 사용하는 예는 다음과 같습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (property-bang)" replace="/!/[!!!]/g"?>
```dart tag=good
print(i[!!!].isEven);
```


<a id="redundant-check" aria-hidden="true"></a>

:::note
이러한 모든 비승격 사례를 해결하려면, 이미 검사된 조건을 확인하는 코드인 _중복 검사(redundant check)_ 를 추가합니다. 
실패한 승격이 null 검사인 경우 `!`를 사용하고, 타입 검사인 경우 `as`를 사용할 수 있습니다.

중복 검사는 타입 승격 실패에 대한 간단하지만 오류가 발생하기 쉬운 솔루션입니다. 
컴파일러를 무시하기 때문에, 다른 솔루션에서는 발생하지 않는 방식으로 실수를 유발할 수 있습니다.

타입을 승격시키기 위해 추가 작업을 수행할지(코드가 정확하다는 확신을 제공) 아니면 
중복 검사를 수행할지(추론이 잘못되었을 경우 버그가 발생할 수 있음)는 사용자에게 달려 있습니다.
:::


### `이것`을 승격할 수 없습니다. {:#this}

**원인:**
`this`를 승격시키려고 하지만, `this`에 대한 타입 승격은 아직 지원되지 않습니다.

일반적인 `this` 승격 시나리오 중 하나는 [확장 메서드][extension methods]를 작성할 때입니다. 
확장 메서드의 [`on` 타입][`on` type]이 nullable 타입인 경우, 
`this`가 `null`인지 확인하기 위해 null 검사를 수행해야 합니다.

**예제:**

```dart tag=bad
extension on int? {
  int get valueOrZero {
    return this == null ? 0 : this; // 오류
  }
}
```

**메시지:**

```plaintext
`this` can't be promoted. 
```

**솔루션:**

`this`의 값을 저장할 로컬 변수를 생성한 다음, null 검사를 수행합니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (this)" replace="/final.*/[!$&!]/g"?>
```dart tag=good
extension on int? {
  int get valueOrZero {
    [!final self = this;!]
    return self == null ? 0 : self;
  }
}
```

[extension methods]: /language/extension-methods
[`on` type]: /language/extension-methods#implementing-extension-methods

### private 필드만 승격 가능합니다. {:#private}

**원인:**
필드를 승격하려고 하지만, 필드가 private이 아닙니다.

프로그램의 다른 라이브러리가 getter로 공개 필드를 재정의할 수 있습니다. 
[getter가 안정적인 값을 반환하지 않을 수 있고](#not-field), 
컴파일러가 다른 라이브러리가 무엇을 하는지 알 수 없기 때문에, 
private이 아닌 필드는 승격할 수 없습니다.

**예제:**

```dart tag=bad
class Example {
  final int? value;
  Example(this.value);
}

void test(Example x) {
  if (x.value != null) {
    print(x.value + 1); // 오류
  }
}
```

**메시지:**

```plaintext
'value' refers to a public property so it couldn't be promoted.
```

**솔루션:**

필드를 private으로 만들면 컴파일러는 외부 라이브러리가 해당 값을 재정의할 수 없음을 확신할 수 있으므로, 
안전하게 승격할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (private)" replace="/_val/_value/g; /_value;/[!_value!];/g; /PrivateFieldExample/Example/g;"?>
```dart tag=good
class Example {
  final int? [!_value!];
  Example(this._value);
}

void test(Example x) {
  if (x._value != null) {
    print(x._value + 1);
  }
}
```

### final 필드만 승격될 수 있습니다. {:#final}

**원인:**
필드를 승격하려고 하지만, 필드가 final이 아닙니다.

컴파일러에 따르면, final이 아닌 필드는 원칙적으로 테스트되는 시간과 사용되는 시간 사이에 언제든지 수정될 수 있습니다. 
따라서, 컴파일러가 final이 아닌 nullable 타입을 non-nullable 타입으로 승격하는 것은 안전하지 않습니다.

**예제:**

```dart tag=bad
class Example {
  int? _mutablePrivateField;
  Example(this._mutablePrivateField);

  void f() {
    if (_mutablePrivateField != null) {
      int i = _mutablePrivateField; // 오류
    }
  }
}
```

**메시지:**

```plaintext
'_mutablePrivateField' refers to a non-final field so it couldn't be promoted.
```

**솔루션:**

필드를 `final`로 만듭니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (final)" replace="/final/[!$&!]/g; /FinalExample/Example/g;"?>
```dart tag=good
class Example {
  [!final!] int? _immutablePrivateField;
  Example(this._immutablePrivateField);

  void f() {
    if (_immutablePrivateField != null) {
      int i = _immutablePrivateField; // OK
    }
  }
}
```

### getter는 승격될 수 없습니다. {:#not-field}

**원인:**
Getters를 승격시키려고 하지만, 인스턴스 *필드*만 승격시킬 수 있고, 인스턴스 Getters는 승격시킬 수 없습니다.

컴파일러는 Getters가 항상 같은 결과를 반환하도록 보장할 방법이 없습니다. 
안정성을 확인할 수 없기 때문에 Getters는 승격시키기에 안전하지 않습니다.

**예제:**

```dart tag=bad
import 'dart:math';

abstract class Example {
  int? get _value => Random().nextBool() ? 123 : null;
}

void f(Example x) {
  if (x._value != null) {
    print(x._value.isEven); // 오류
  }
}
```

**메시지:**

```plaintext
'_value' refers to a getter so it couldn't be promoted.
```

**솔루션:**

getter를 로컬 변수에 할당합니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (not-field)" plaster="" replace="/final.*/[!$&!]/g; /NotFieldExample/Example/g;"?>
```dart tag=good
import 'dart:math';

abstract class Example {
  int? get _value => Random().nextBool() ? 123 : null;
}

void f(Example x) {
  [!final value = x._value;!]
  if (value != null) {
    print(value.isEven); // OK
  }
}
```

:::note
흐름 분석은 충돌하는 선언이 없는 한, 
`abstract` getters가 타입 승격을 허용할 만큼 충분히 안정적이라고 간주합니다.
:::

### external 필드는 승격될 수 없습니다. {:#external}

**원인:**
필드를 승격시키려고 하지만, 필드가 `external`로 표시되어 있습니다.

외부 필드는 본질적으로 외부 getters이기 때문에 승격되지 않습니다. 
구현은 Dart 외부의 코드이므로, 컴파일러가 외부 필드가 호출될 때마다 동일한 값을 반환할 것이라는 보장이 없습니다.

**예제:**

```dart tag=bad
class Example {
  external final int? _externalField;

  void f() {
    if (_externalField != null) {
      print(_externalField.isEven); // 오류
    }
  }
}
```

**메시지:**

```plaintext
'_externalField' refers to an external field so it couldn't be promoted.
```

**솔루션:**

외부 필드의 값을 로컬 변수에 할당합니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (external)" replace="/final i =.*/[!$&!]/g; /ExternalExample/Example/g;"?>
```dart tag=good
class Example {
  external final int? _externalField;

  void f() {
    [!final i = _externalField;!]
    if (i != null) {
      print(i.isEven); // OK
    }
  }
}
```

### 라이브러리의 다른 곳에서 getter와 충돌합니다. {:#getter-name}

**원인:**
필드를 승격하려고 하는데, 같은 라이브러리의 다른 클래스에 같은 이름의 구체적 getter가 포함되어 있습니다.

**예제:**

```dart tag=bad
import 'dart:math';

class Example {
  final int? _overridden;
  Example(this._overridden);
}

class Override implements Example {
  @override
  int? get _overridden => Random().nextBool() ? 1 : null;
}

void testParity(Example x) {
  if (x._overridden != null) {
    print(x._overridden.isEven); // 오류
  }
}
```

**메시지:**

```plaintext
'_overriden' couldn't be promoted because there is a conflicting getter in class 'Override'.
```

**솔루션**:

getter와 필드가 관련되어 있고 이름을 공유해야 하는 경우,
(위의 예에서처럼 둘 중 하나가 다른 하나를 재정의하는 경우) 
로컬 변수에 값을 할당하여 타입 승격을 활성화할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (conflicting-getter)" plaster="" replace="/final i =.*/[!$&!]/g; /GetterExample/Example/g;"?>
```dart tag=good
import 'dart:math';

class Example {
  final int? _overridden;
  Example(this._overridden);
}

class Override implements Example {
  @override
  int? get _overridden => Random().nextBool() ? 1 : null;
}

void testParity(Example x) {
  [!final i = x._overridden;!]
  if (i != null) {
    print(i.isEven); // OK
  }
}
```

#### 관련되지 않은 클래스에 대한 참고 사항 {:#note-about-unrelated-classes}

위의 예에서 `_overridden` 필드를 승격하는 것이 안전하지 않은 이유는 명확합니다. 
필드와 getter 사이에 오버라이드 관계가 있기 때문입니다. 
그러나, 충돌하는 getter는 클래스가 관련이 없더라도 필드 승격을 방지합니다. 예를 들어:

```dart tag=bad
import 'dart:math';

class Example {
  final int? _i;
  Example(this._i);
}

class Unrelated {
  int? get _i => Random().nextBool() ? 1 : null;
}

void f(Example x) {
  if (x._i != null) {
    int i = x._i; // 오류
  }
}
```

다른 라이브러리에는 두 개의 관련 없는 클래스를 동일한 클래스 계층으로 결합하는 클래스가 포함되어 있을 수 있으며, 
이는 함수 `f`에서 `x._i`에 대한 참조가 `Unrelated._i`로 전송되도록 합니다. 예를 들어:

```dart tag=bad
class Surprise extends Unrelated implements Example {}

void main() {
  f(Surprise());
}
```

**솔루션:**

필드와 충돌하는 엔터티가 실제로 관련이 없다면, 
다음과 같이 서로 다른 이름을 지정하여 문제를 해결할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (unrelated)" replace="/get _j/[!$&!]/g; /UnrelatedExample/Example/g; /f2/f/g;"?>
```dart tag=good
class Example {
  final int? _i;
  Example(this._i);
}

class Unrelated {
  int? [!get _j!] => Random().nextBool() ? 1 : null;
}

void f(Example x) {
  if (x._i != null) {
    int i = x._i; // OK
  }
}
```

### 라이브러리의 다른 곳에 있는 non-promotable 필드와 충돌합니다. {:#field-name}

**원인:**
필드를 승격하려고 하지만, 같은 라이브러리의 다른 클래스에, 
승격할 수 없는 같은 이름의 필드가 있습니다. (이 페이지에 나열된 다른 이유로 인해)

**예제:**

```dart tag=bad
class Example {
  final int? _overridden;
  Example(this._overridden);
}

class Override implements Example {
  @override
  int? _overridden;
}

void f(Example x) {
  if (x._overridden != null) {
    print(x._overridden.isEven); // 오류
  }
}
```

이 예제는 런타임에 `x`가 실제로 `Override`의 인스턴스일 수 있기 때문에 실패합니다. 
따라서 승격이 적절하지 않습니다.

**메시지:**

```plaintext
'overridden' couldn't be promoted because there is a conflicting non-promotable field in class 'Override'.
```

**솔루션:**

필드가 실제로 관련되어 있고 이름을 공유해야 하는 경우, 
값을 final 로컬 변수에 할당하여 타입 승격을 활성화할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (conflicting-field)" replace="/final i =.*/[!$&!]/g; /FieldExample/Example/g; /f3/f/g; /Override2/Override/g;"?>
```dart tag=good
class Example {
  final int? _overridden;
  Example(this._overridden);
}

class Override implements Example {
  @override
  int? _overridden;
}

void f(Example x) {
  [!final i = x._overridden;!]
  if (i != null) {
    print(i.isEven); // OK
  }
}
```

필드가 관련이 없다면, 필드 중 하나의 이름을 바꿔서 충돌하지 않도록 하세요. 
[관련 없는 클래스에 대한 참고사항](#note-about-unrelated-classes)을 읽어보세요.


### 암묵적 `noSuchMethod` 전달자(forwarder)와 충돌합니다. {:#nosuchmethod}

**원인:**
private이고 final인 필드를 승격하려고 하지만, 
같은 라이브러리의 다른 클래스에 필드와 같은 이름을 가진 
[암묵적 `noSuchMethod` 포워더(forwarder)][nosuchmethod]가 포함되어 있습니다.

`noSuchMethod`가 한 호출에서 다음 호출로 안정적인 값을 반환한다는 보장이 없기 때문에 이는 타당하지 않습니다.

**예제:**

```dart tag=bad
import 'package:mockito/mockito.dart';

class Example {
  final int? _i;
  Example(this._i);
}

class MockExample extends Mock implements Example {}

void f(Example x) {
  if (x._i != null) {
    int i = x._i; // 오류
  }
}
```

이 예에서, `_i`는 컴파일러가 `MockExample` 내부에서 생성하는, 
unsound 암묵적 `noSuchMethod` 포워더(`_i`라고도 함)로 해결(resolve)될 수 있기 때문에, 승격될 수 없습니다.

컴파일러는 `MockExample`이 선언에서, `Example`을 구현할 때 `_i`에 대한 getter를 지원하겠다고 약속하지만, 
그 약속을 이행하지 않기 때문에 `_i`의 암묵적 구현을 ​​만듭니다. 
따라서 정의되지 않은 getter 구현은, 
[`Mock`의 `noSuchMethod` 정의][`Mock`'s `noSuchMethod` definition]에서 처리되며, 
이는 같은 이름의 암묵적 `noSuchMethod` 포워더를 생성합니다.

실패는 [관련 없는 클래스](#note-about-unrelated-classes)의 필드 사이에서도 발생할 수 있습니다.

**메시지:**

```plaintext
'_i' couldn't be promoted because there is a conflicting noSuchMethod forwarder in class 'MockExample'.
```

**솔루션:**

문제의 getter를 정의하면, `noSuchMethod`가 암묵적으로 구현을 처리할 필요가 없습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (mock)" plaster="" replace="/late.*/[!$&!]/g; /MockingExample/Example/g; /f4/f/g;"?>
```dart tag=good
import 'package:mockito/mockito.dart';

class Example {
  final int? _i;
  Example(this._i);
}

class MockExample extends Mock implements Example {
  @override
  [!late final int? _i;!]
}

void f(Example x) {
  if (x._i != null) {
    int i = x._i; // OK
  }
}
```

getter는 일반적으로 mock 객체가 사용되는 방식과 일관성을 유지하기 위해 `late` 선언됩니다. 
mock 객체와 관련이 없는 시나리오에서, 이러한 타입 승격 실패를 해결하기 위해, 
getter를 `late` 선언할 필요는 없습니다.

:::note
위의 예는 `Mock`에 이미 `noSuchMethod` 정의가 포함되어 있기 때문에, 
[mocks]({{site.pub-pkg}}/mockito)를 단순하게 사용하므로, 
임의의 정의를 정의할 필요가 없고, 예제 코드를 짧게 유지할 수 있습니다.

일반적으로 mock 객체는, mock 객체가 모의하는 클래스와 다른 라이브러리에서 선언되기 때문에, 
mock 객체에서 이와 같은 문제가 자주 발생하지 않을 것으로 예상합니다. 
문제의 클래스가 다른 라이브러리에서 선언된 경우, 
private 이름은 `noSuchMethod`로 전달되지 않으므로(개인 정보 보호 기대 사항을 위반하기 때문), 
필드를 승격하는 것은 안전합니다.
:::

[nosuchmethod]: /language/extend#nosuchmethod
[`Mock`'s `noSuchMethod` definition]: {{site.pub-api}}/mockito/latest/mockito/Mock/noSuchMethod.html

### 승격 후에 쓰여졌을 가능성이 있습니다. {:#write}

**원인:**
승격된 이후 작성되었을 수 있는 변수를 승격하려고 합니다.

**예제:**

```dart tag=bad
void f(bool b, int? i, int? j) {
  if (i == null) return;
  if (b) {
    i = j;           // (1)
  }
  if (!b) {
    print(i.isEven); // (2) 오류
  }
}
```

**솔루션**:

이 예에서, 흐름 분석이 (1)에 도달하면, `i`를 non-nullable `int`에서 nullable `int?`로 강등합니다. 
(1)과 (2)를 모두 포함하는 코드 경로가 없기 때문에, 사람은 (2)에서의 액세스가 안전하다고 알 수 있지만, 
흐름 분석은 별도의 `if` 문에서 조건 간의 상관 관계를 추적하지 않기 때문에, 
이를 알아차릴 만큼 똑똑하지 않습니다.

두 개의 `if` 문을 결합하여 문제를 해결할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (write-combine-ifs)" replace="/else/[!$&!]/g"?>
```dart tag=good
void f(bool b, int? i, int? j) {
  if (i == null) return;
  if (b) {
    i = j;
  } [!else!] {
    print(i.isEven);
  }
}
```

이러한 직선 제어 흐름 사례(루프 없음)에서, 
흐름 분석은 강등 여부를 결정할 때 할당의 오른쪽을 고려합니다. 
결과적으로, 이 코드를 수정하는 또다른 방법은 `j`의 타입을 `int`로 변경하는 것입니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (write-change-type)" replace="/int j/[!$&!]/g"?>
```dart tag=good
void f(bool b, int? i, [!int j!]) {
  if (i == null) return;
  if (b) {
    i = j;
  }
  if (!b) {
    print(i.isEven);
  }
}
```

### 이전 루프 반복에서 작성되었을 가능성이 있습니다. {:#loop-or-switch}

**원인:**
이전 루프 반복에서 작성되었을 수 있는 것을, 승격하려고 했기 때문에 승격이 무효화되었습니다.

**예제:**

```dart tag=bad
void f(Link? p) {
  if (p != null) return;
  while (true) {    // (1)
    print(p.value); // (2) 오류
    var next = p.next;
    if (next == null) break;
    p = next;       // (3)
  }
}
```

흐름 분석이 (1)에 도달하면, 앞을 보고(looking ahead) (3)에서 `p`에 대한 쓰기를 봅니다(sees). 
하지만 앞을 보고 있기(looking ahead) 때문에, 아직 할당의 오른쪽 타입을 파악하지 못했으므로, 
승격을 유지하는 것이 안전한지 알 수 없습니다. 
안전을 위해, 승격을 무효화합니다.

**솔루션**:

루프의 맨 위로 null 검사를 옮기면, 이 문제를 해결할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (loop)" replace="/p != null/[!$&!]/g"?>
```dart tag=good
void f(Link? p) {
  while ([!p != null!]) {
    print(p.value);
    p = p.next;
  }
}
```

`case` 블록에 레이블이 있는 경우 `switch` 문에서도 이러한 상황이 발생할 수 있습니다. 
레이블이 지정된 `switch` 문을 사용하여, 루프를 구성할 수 있기 때문입니다.

```dart tag=bad
void f(int i, int? j, int? k) {
  if (j == null) return;
  switch (i) {
    label:
    case 0:
      print(j.isEven); // 오류
      j = k;
      continue label;
  }
}
```

다시, 루프의 맨 위로 null 검사를 옮겨서 문제를 해결할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (switch-loop)" replace="/if .*/[!$&!]/g"?>
```dart tag=good
void f(int i, int? j, int? k) {
  switch (i) {
    label:
    case 0:
      [!if (j == null) return;!]
      print(j.isEven);
      j = k;
      continue label;
  }
}
```

### catch에서, try 이후에 쓰여졌을 가능성이 있습니다. {:#catch}

**원인:**
변수가 `try` 블록에 쓰여졌을 수 있으며, 실행은 이제 `catch` 블록에 있습니다.

**예제:**

```dart tag=bad
void f(int? i, int? j) {
  if (i == null) return;
  try {
    i = j;                 // (1)
    // ... 추가적인 코드 ...
    if (i == null) return; // (2)
    // ... 추가적인 코드 ...
  } catch (e) {
    print(i.isEven);       // (3) 오류
  }
}
```

이 경우, 흐름 분석은 `i.isEven` (3)이 안전하다고 생각하지 않습니다. 
`try` 블록에서 예외가 언제 발생했을지 알 수 있는 방법이 없기 때문입니다. 
따라서, 보수적으로 `i`가 잠재적으로 `null`일 때, (1)과 (2) 사이에 발생했을 것이라고 가정합니다.

`try`와 `finally` 블록 사이, `catch`와 `finally` 블록 사이에서도 비슷한 상황이 발생할 수 있습니다. 
구현 방법에 대한 역사적 유물(historical artifact) 때문에 이러한 `try`/`catch`/`finally` 상황은, 
루프에서 발생하는 것과 유사하게 할당의 오른쪽을 고려하지 않습니다.

**솔루션**:

문제를 해결하려면, `catch` 블록이 `try` 블록 내부에서 변경되는 변수의 상태에 대한 가정에 의존하지 않도록 해야 합니다. 
예외는 `try` 블록 중 언제든지 발생할 수 있으며, `i`가 `null`일 수도 있습니다.

가장 안전한 해결책은 `catch` 블록 내부에 null 체크를 추가하는 것입니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (catch-null-check)" replace="/if.*/[!$&!]/g;/(} else {|  \/\/ H.*)/[!$&!]/g;/  }/  [!}!]/g"?>
```dart tag=good
try {
  // ···
} catch (e) {
  [!if (i != null) {!]
    print(i.isEven); // (3) OK 바로 위 줄의 null 검사로 인해.
  [!} else {!]
  [!  // i가 null인 경우를 처리합니다.!]
  [!}!]
}
```

또는, `i`가 `null`인 동안 예외가 발생할 수 없다고 확신하는 경우에는, `!` 연산자를 사용하면 됩니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (catch-bang)" replace="/i!/i[!!!]/g"?>
```dart
try {
  // ···
} catch (e) {
  print(i[!!!].isEven); // (3) OK `!` 때문입니다.
}
```

### 하위 타입 불일치 {:#subtype-mismatch}

**원인:**
변수의 현재 승격된 타입의 하위 타입이 아닌 타입으로 승격하려고 합니다. (또는 승격 시도 시점에 하위 타입이 아니었습니다)

**예제:**

```dart tag=bad
void f(Object o) {
  if (o is Comparable /* (1) */) {
    if (o is Pattern /* (2) */) {
      print(o.matchAsPrefix('foo')); // (3) 오류
    }
  }
}
```

이 예에서, `o`는 (1)에서 `Comparable`로 승격되지만, (2)에서는 `Pattern`으로 승격되지 않습니다. 
`Pattern`이 `Comparable`의 하위 타입이 아니기 때문입니다. 
(그 이유는 승격된다면 `Comparable`에서 메서드를 사용할 수 없기 때문입니다.) 
`Pattern`이 `Comparable`의 하위 타입이 아니라는 것이, (3)의 코드가 죽었다는 것을 의미하지는 않습니다. 
`o`에는 `Comparable`과 `Pattern`을 모두 구현하는 `String`과 같은 타입이 있을 수 있습니다.

**솔루션**:

가능한 해결책 중 하나는 새 로컬 변수를 생성하여, 
원본 변수가 `Comparable`로 승격되고, 
새 변수가 `Pattern`으로 승격되는 것입니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (subtype-variable)" replace="/Object o2.*/[!$&!]/g;/(o2)(\.| is)/[!$1!]$2/g"?>
```dart
void f(Object o) {
  if (o is Comparable /* (1) */) {
    [!Object o2 = o;!]
    if ([!o2!] is Pattern /* (2) */) {
      print(
          [!o2!].matchAsPrefix('foo')); // (3) OK; o2가 `Pattern`으로 승격되었습니다.
    }
  }
}
```

하지만, 나중에 코드를 편집하는 사람은 `Object o2`를 `var o2`로 변경하고 싶어할 수 있습니다. 
이 변경은 `o2`에 `Comparable` 타입을 부여하여, 
객체가 `Pattern`으로 승격될 수 없다는 문제를 다시 일으킵니다.

중복된 타입 검사가 더 나은 해결책일 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (subtype-redundant)" replace="/\(o as Pattern\)/[!$&!]/g"?>
```dart tag=good
void f(Object o) {
  if (o is Comparable /* (1) */) {
    if (o is Pattern /* (2) */) {
      print([!(o as Pattern)!].matchAsPrefix('foo')); // (3) OK
    }
  }
}
```

가끔 작동하는 또다른 해결책은 더 정확한 타입을 사용할 수 있는 경우입니다. 
3번째 줄이 문자열에 대해서만 신경 쓰는 경우 타입 검사에서 `String`을 사용할 수 있습니다. 
`String`은 `Comparable`의 하위 타입이므로 승격이 작동합니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (subtype-string)" replace="/is String/is [!String!]/g"?>
```dart tag=good
void f(Object o) {
  if (o is Comparable /* (1) */) {
    if (o is [!String!] /* (2) */) {
      print(o.matchAsPrefix('foo')); // (3) OK
    }
  }
}
```

### 로컬 함수에 의해 쓰기가 캡쳐됩니다. {:#captured-local}

**원인:**
변수가 로컬 함수 또는 함수 표현식에 의해 쓰기 캡처되었습니다.

**예제:**

```dart tag=bad
void f(int? i, int? j) {
  var foo = () {
    i = j;
  };
  // ... foo 사용 ... 
  if (i == null) return; // (1)
  // ... 추가적인 코드 ...
  print(i.isEven);       // (2) 오류
}
```

흐름 분석은 `foo`의 정의에 도달하자마자 언제든지 호출될 수 있으므로, 
`i`를 전혀 승격시키는 것이 더 이상 안전하지 않다고 추론합니다. 
루프와 마찬가지로 이 강등은 할당의 오른쪽 타입과 관계없이 발생합니다.

**솔루션**:

때로는 승격이 쓰기 캡처보다 앞서도록, 논리를 재구성할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (local-write-capture-reorder)" replace="/(  )((var foo|  i = j|\}\;|\/\/ ... Use foo).*)/$1[!$2!]/g"?>
```dart tag=good
void f(int? i, int? j) {
  if (i == null) return; // (1)
  // ... 추가적인 코드 ...
  print(i.isEven); // (2) OK
  [!var foo = () {!]
  [!  i = j;!]
  [!};!]
  [!// ... foo 사용 ...!]
}
```

또다른 옵션은 로컬 변수를 생성하여, 쓰기 캡처되지 않도록 하는 것입니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (local-write-capture-copy)" replace="/var i2.*/[!$&!]/g;/(i2)( ==|\.)/[!$1!]$2/g"?>
```dart tag=good
void f(int? i, int? j) {
  var foo = () {
    i = j;
  };
  // ... foo 사용 ...
  [!var i2 = i;!]
  if ([!i2!] == null) return; // (1)
  // ... 추가적인 코드 ...
  print([!i2!].isEven); // (2) OK `i2`가 쓰기 캡처되지 않았기 때문입니다.
}
```

또는 중복 검사를 수행할 수 있습니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (local-write-capture-bang)" replace="/i!/i[!!!]/g"?>
```dart
void f(int? i, int? j) {
  var foo = () {
    i = j;
  };
  // ... foo 사용 ...
  if (i == null) return; // (1)
  // ... 추가적인 코드 ...
  print(i[!!!].isEven); // (2) OK `!` 체크로 인해.
}
```


### 현재 클로저 또는 함수 표현식 외부에 작성되었습니다. {:#write-outer}

**원인:**
변수가 클로저 또는 함수 표현식 외부에 작성되고, 타입 승격 위치가 클로저 또는 함수 표현식 내부에 있습니다.

**예제:**

```dart tag=bad
void f(int? i, int? j) {
  if (i == null) return;
  var foo = () {
    print(i.isEven); // (1) 오류
  };
  i = j;             // (2)
}
```

흐름 분석은 `foo`가 언제 호출될지 결정할 방법이 없기 때문에, 
((2)에서 할당 후에 호출될 수 있고) 
따라서, 승격이 더 이상 유효하지 않을 수 있습니다. 
루프와 마찬가지로, 이 강등은 할당의 오른쪽 타입과 관계없이 발생합니다.

**솔루션**:

솔루션은 로컬 변수를 만드는 것입니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (closure-new-var)" replace="/var i2.*/[!$&!]/g;/i2\./[!i2!]./g"?>
```dart tag=good
void f(int? i, int? j) {
  if (i == null) return;
  [!var i2 = i;!]
  var foo = () {
    print([!i2!].isEven); // (1) OK `i2`는 나중에 변경되지 않기 때문입니다.
  };
  i = j; // (2)
}
```

**예제:**

특히 심각한 사례는 다음과 같습니다.

```dart tag=bad
void f(int? i) {
  i ??= 0;
  var foo = () {
    print(i.isEven); // 오류
  };
}
```

이 경우, 사람은 `i`에 대한 유일한 쓰기가 null이 아닌 값을 사용하고, 
`foo`가 생성되기 전에 발생하기 때문에, 
승격이 안전하다는 것을 알 수 있습니다. 
하지만 [흐름 분석은 그렇게 똑똑하지 않습니다][1536].

[1536]: {{site.repo.dart.lang}}/issues/1536

**솔루션**:

다시, 해결책은 로컬 변수를 만드는 것입니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (closure-new-var2)" replace="/var j.*/[!$&!]/g;/j\./[!j!]./g"?>
```dart tag=good
void f(int? i) {
  [!var j = i ?? 0;!]
  var foo = () {
    print([!j!].isEven); // OK
  };
}
```

이 솔루션은 `j`가 초기 값(`i ?? 0`)으로 인해 non-nullable 타입(`int`)을 갖는 것으로 추론되기 때문에 작동합니다. 
`j`가 나중에 할당되든 안 되든, null이 불가능한 타입을 갖기 때문에, 
`j`는 결코 null이 아닌 값을 가질 수 없습니다.


### 현재 클로저 또는 함수 표현식 외부에서 캡처된 내용을 작성합니다. {:#captured-outer}

**원인:**
승격시키려는 변수는 클로저 또는 함수 표현식 외부에서 캡처되었지만, 
이 변수 ​​사용은 이를 승격시키려는 클로저 또는 함수 표현식 내부에 있습니다.

**예제:**

```dart tag=bad
void f(int? i, int? j) {
  var foo = () {
    if (i == null) return;
    print(i.isEven); // 오류
  };
  var bar = () {
    i = j;
  };
}
```

흐름 분석은 `foo`와 `bar`가 어떤 순서로 실행될지 알 수 있는 방법이 없다고 추론합니다. 
사실, `bar`는 `foo`를 실행하는 중간에 실행될 수도 있습니다.
(`foo`가 `bar`를 호출하는 것을 호출하기 때문입니다)
따라서, `foo` 내부에서 `i`를 전혀 승격시키는 것은 안전하지 않습니다.

**솔루션**:

아마도 가장 좋은 해결책은 로컬 변수를 만드는 것입니다.

<?code-excerpt "non_promotion/lib/non_promotion.dart (closure-write-capture)" replace="/var i2.*/[!$&!]/g;/(i2)( ==|\.)/[!i2!]$2/g"?>
```dart tag=good
void f(int? i, int? j) {
  var foo = () {
    [!var i2 = i;!]
    if ([!i2!] == null) return;
    print([!i2!].isEven); // OK i2가 이 클로저에서 로컬이기 때문입니다.
  };
  var bar = () {
    i = j;
  };
}
```

[language version]: /guides/language/evolution#language-versioning
