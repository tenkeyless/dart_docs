---
# title: Extension types
title: 확장 타입
description: 기존 타입에 대한 정적 전용(static-only) 인터페이스를 작성하는 방법을 알아보세요.
prevpage:
  url: /language/extension-methods
  # title: Extension methods
  title: 확장 메서드
nextpage:
  url: /language/callable-objects
  # title: Callable objects
  title: 호출 가능한 객체
---

확장 타입은, 기존 타입을 다른 정적 전용(static-only) 인터페이스로 "래핑"하는, 컴파일 타임 추상화입니다. 
이는 실제 래퍼 비용을 들이지 않고도, 
기존 타입의 인터페이스(모든 종류의 상호 운용에 필수적)를 쉽게 수정할 수 있기 때문에, 
[정적 JS 상호 운용성][static JS interop]의 주요 구성 요소입니다.

확장 타입은 기본 타입의 객체에서 사용할 수 있는 연산 세트(또는 인터페이스)에 대한 규율을 적용하며, 
이를 *표현 타입(representation type)* 이라고 합니다. 
확장 타입의 인터페이스를 정의할 때, 
표현 타입의 일부 멤버를 재사용하고, 다른 멤버를 생략하고, 
다른 멤버를 대체하고, 새로운 기능을 추가할 수 있습니다.

다음 예제에서는 `int` 타입을 래핑하여, 
ID 번호에 적합한 연산만 허용하는 확장 타입을 만듭니다.

```dart
extension type IdNumber(int id) {
  // 'int' 타입의 '<' operator를 래핑합니다.
  operator <(IdNumber other) => id < other.id;
  // 예를 들어, ID 번호에 대한 덧셈은 의미가 없기 때문에, '+' operator를 선언하지 않습니다.
}

void main() {
  // 확장 타입의 규칙이 없으면, 'int'는 ID 번호를 안전하지 않은 연산에 노출시킵니다.
  int myUnsafeId = 42424242;
  myUnsafeId = myUnsafeId + 10; // 이것은 작동하지만, ID에는 허용되어서는 안 됩니다.

  var safeId = IdNumber(42424242);
  safeId + 10; // 컴파일 타임 오류: '+' operator가 없습니다.
  myUnsafeId = safeId; // 컴파일 타임 오류: 잘못된 타입입니다.
  myUnsafeId = safeId as int; // OK: 표현 타입으로 런타임 캐스트.
  safeId < IdNumber(42424241); // OK: 래핑된 '<' operator를 사용합니다.
}
```

:::note
확장 타입은 **래퍼 클래스**와 동일한 목적을 제공하지만, 
많은 객체를 래핑해야 할 때 비용이 많이 들 수 있는, 
추가 런타임 객체를 생성할 필요가 없습니다. 
확장 타입은 정적 전용(static-only)이며 런타임에 컴파일되므로, 본질적으로 비용이 없습니다.

[**확장 메서드**][ext](단순히 "확장(extensions)"이라고도 함)는 확장 타입과 유사한 정적 추상화입니다. 
그러나, 확장 메서드는 기본 타입의 모든 인스턴스에 기능을 *직접* 추가합니다. 
확장 타입은 다릅니다. 확장 타입의 인터페이스는 정적 타입이 해당 확장 타입인 *표현식에만* 적용됩니다. 
기본적으로 기본 타입의 인터페이스와 다릅니다.
:::

## 문법 {:#syntax}

### 선언 {:#declaration}

`extension type` 선언과 이름을 사용하여 새 확장 타입을 정의하고, 
그 뒤에 괄호 안에 *표현 타입 선언(representation type declaration)* 을 붙입니다.

```dart
extension type E(int i) {
  // 연산 세트를 정의합니다.
}
```

표현 타입 선언 `(int i)`는, 확장 타입 `E`의 기본 타입이 `int`이고, 
*표현 객체(representation object)* 에 대한 참조가 `i`로 명명됨을 지정합니다. 
이 선언은 또한 다음을 도입합니다.
- 표현 타입을 반환 타입으로 사용하는 표현 객체에 대한 암묵적 getter: `int get i`.
- 암묵적 생성자: `E(int i) : i = i`.

표현 객체는 기본 타입의 객체에 대한 확장 타입 액세스를 제공합니다. 
객체는 확장 타입 본문의 범위에 있으며, getter로 이름을 사용하여 액세스할 수 있습니다.

- `i`(또는 생성자에서 `this.i`)를 사용하여, 확장 타입 본문 내에서.
- `e.i`를 사용하여 속성 추출을 사용하여 외부에서. (여기서 `e`는 확장 타입을 정적 타입으로 가짐)

확장 타입 선언은 클래스나 확장과 마찬가지로 [타입 매개변수](generics)를 포함할 수도 있습니다.

```dart
extension type E<T>(List<T> elements) {
  // ...
}
```

### 생성자 {:#constructors}

확장 유형의 본문에서 [생성자][constructors]를 선택적으로 선언할 수 있습니다. 
표현 선언 자체는 암묵적 생성자이므로, 기본적으로 확장 타입의 명명되지 않은 생성자를 대신합니다. 
추가적인 non-리다이렉팅 생성자는 초기화자 리스트나 타입 매개변수에서 `this.i`를 사용하여, 
표현 객체의 인스턴스 변수를 초기화해야 합니다.

```dart
extension type E(int i) {
  E.n(this.i);
  E.m(int j, String foo) : i = j + foo.length;
}

void main() {
  E(4); // 암묵적인 이름 없는 생성자.
  E.n(3); // 명명된 생성자.
  E.m(5, "Hello!"); // 추가 매개변수가 있는 명명된 생성자.
}
```

또는, 표현 선언 생성자의 이름을 지정할 수 있습니다. 
이 경우, 본문에 이름이 지정되지 않은 생성자를 위한 공간이 있습니다.

```dart
extension type const E._(int it) {
  E(): this._(42);
  E.otherName(this.it);
}

void main2() {
  E();
  const E._(2);
  E.otherName(3);
}
```

클래스에 대한 동일한 private 생성자 구문인 `_`를 사용하여, 
새 생성자를 정의하는 대신 생성자를 완전히 숨길 수도 있습니다. 
예를 들어, 기본 타입이 `int`인 경우에도, 
클라이언트가 `String`으로 `E`만 생성하기를 원하는 경우:

```dart
extension type E._(int i) {
  E.fromString(String foo) : i = int.parse(foo);
}
```

전달 생성형 생성자(forwarding generative constructors)나, 
[팩토리 생성자][factory]를 선언할 수도 있습니다. (이는 하위 확장 타입의 생성자로도 전달(forward) 가능)

### 멤버 {:#members}

확장 타입의 본문에서 멤버를 선언하여, 클래스 멤버와 같은 방식으로 인터페이스를 정의합니다. 
확장 타입 멤버는 메서드, getters, setters 또는 operator가 될 수 있습니다.
(non-[`외부`][`external`] [인스턴스 변수][instance variables] 및 [추상 멤버][abstract members]는 허용되지 않음)

```dart
extension type NumberE(int value) {
  // Operator:
  NumberE operator +(NumberE other) =>
      NumberE(value + other.value);
  // Getter:
  NumberE get myNum => this;
  // 메서드:
  bool isValid() => !value.isNegative;
}
```

표현 타입의 인터페이스 멤버는 [기본적으로](#transparency) 확장 타입의 인터페이스 멤버가 아닙니다. 
표현 타입의 단일 멤버를 확장 타입에서 사용할 수 있게 하려면, 
(`NumberE`의 `operator +`와 같이) 확장 타입 정의에 선언을 작성해야 합니다. 
(`i` getter 및 `isValid` 메서드와 같이) 표현 타입과 관련 없는 새 멤버를 정의할 수도 있습니다.

### 구현 {:#implements}

선택적으로 `implements` 절을 사용하여 다음을 수행할 수 있습니다.

- 확장 타입에 하위 타입 관계를 도입하고,
- 표현 객체의 멤버를 확장 타입 인터페이스에 추가합니다.

`implements` 절은 ([extension method][ext]와 해당 `on` 타입 간의 관계와 같은) 
[applicability (적용 가능성)][applicability] 관계를 도입합니다. 
슈퍼 타입에 적용 가능한 멤버는 하위 타입에도 적용되며, 
하위 타입에 동일한 멤버 이름이 있는 선언이 있는 경우는 예외입니다.

확장 타입은 다음만 구현할 수 있습니다.

- **그것의 표현 타입**.
  이를 통해 표현 타입의 모든 멤버를 확장 타입에서 암묵적으로 사용할 수 있습니다.
  
  ```dart
  extension type NumberI(int i) implements int {
    // 'NumberI'는 'int'의 모든 멤버를 호출할 수 있으며, 
    // 여기에 선언된 다른 모든 것도 호출할 수 있습니다.
  }
  ```

- **표현 타입의 슈퍼타입**.
  이는 슈퍼타입의 멤버를 사용할 수 있게 하지만, 
  반드시 표현 타입의 모든 멤버를 사용할 수 있는 것은 아닙니다.

  ```dart
  extension type Sequence<T>(List<T> _) implements Iterable<T> {
    // List보다 더 나은 연산.
  }
  
  extension type Id(int _id) implements Object {
    // 확장 타입을 null을 허용하지 않게 만듭니다.
    static Id? tryParse(String source) => int.tryParse(source) as Id?;
  }
  ```

- **다른 확장 타입** 동일한 표현 타입에 대해 유효합니다.
  이를 통해, 여러 확장 타입에 걸쳐 연산을 재사용할 수 있습니다. (다중 상속과 유사)
  
  ```dart
  extension type const Opt<T>._(({T value})? _) { 
    const factory Opt(T value) = Val<T>;
    const factory Opt.none() = Non<T>;
  }
  extension type const Val<T>._(({T value}) _) implements Opt<T> { 
    const Val(T value) : this._((value: value));
    T get value => _.value;
  }
  extension type const Non<T>._(Null _) implements Opt<Never> {
    const Non() : this._(null);
  }
  ```

다양한 시나리오에서 `implements`의 효과에 대해 자세히 알아보려면, [사용법](#usage) 섹션을 읽어보세요.

#### `@redeclare` {:#redeclare}

슈퍼타입의 멤버와 이름을 공유하는 확장 타입 멤버를 선언하는 것은, 
클래스 간의 오버라이드 관계가 *아니라*, *재선언*입니다. 
확장 타입 멤버 선언은, 동일한 이름을 가진 모든 슈퍼타입 멤버를, *완전히 대체*합니다. 
동일한 함수에 대한 대체 구현을 제공하는 것은 불가능합니다.

`@redeclare` 주석을 사용하여, 
컴파일러에게 슈퍼타입의 멤버와 동일한 이름을 사용하도록 *의도적으로* 선택했다는 것을 알릴 수 있습니다. 
그러면, 분석기가 실제로 그렇지 않은 경우(예: 이름 중 하나가 잘못 입력된 경우), 경고합니다.

```dart
extension type MyString(String _) implements String {
  // 'String.operator[]'를 대체합니다.
  @redeclare
  int operator [](int index) => codeUnitAt(index);
}
```

또한, 슈퍼인터페이스 멤버를 숨기고 `@redeclare`로 어노테이션되지 않은 확장 타입 메서드를 선언하는 경우, 
경고를 받도록 lint [`annotate_redeclares`][lint]를 활성화할 수도 있습니다.

## 사용법 {:#usage}

확장 타입을 사용하려면, 클래스에서와 마찬가지로 생성자를 호출하여 인스턴스를 만듭니다.

```dart
extension type NumberE(int value) {
  NumberE operator +(NumberE other) =>
      NumberE(value + other.value);

  NumberE get next => NumberE(value + 1);
  bool isValid() => !value.isNegative;
}

void testE() { 
  var num = NumberE(1);
}
```

그런 다음, 클래스 객체에서와 마찬가지로 객체에서 멤버를 호출할 수 있습니다.

확장 타입에 대한 두 가지 동등하게 유효하지만, 실질적으로 다른 핵심 사용 사례가 있습니다.

1. 기존 타입에 *확장된* 인터페이스 제공.
2. 기존 타입에 *다른* 인터페이스 제공.

:::note
어떤 경우에도, 확장 타입의 표현 타입은 결코 하위 타입이 아니므로, 
확장 타입이 필요한 곳에서 표현 타입을 서로 바꿔 사용할 수 없습니다.
:::

<a id="transparency"></a>

### 1. 기존 타입에 *확장된* 인터페이스 제공 {:#1-provide-an-extended-interface-to-an-existing-type}

확장 타입이 표현 타입을 [구현](#implements)할 때, 
확장 타입이 기본 타입을 "볼" 수 있으므로, "투명"하다고 간주할 수 있습니다.

투명한 확장 타입은 표현 타입의 ([재선언](#redeclare)되지 않은) 모든 멤버와, 
정의하는 모든 보조 멤버를 호출할 수 있습니다. 
이렇게 하면, 기존 타입에 대한 새롭고 *확장된* 인터페이스가 생성됩니다. 
새 인터페이스는 정적 타입이 확장 타입인 표현식에서 사용할 수 있습니다.

즉, 다음과 같이 표현 타입의 멤버를 호출*할 수* 있습니다.
([비투명](#2-provide-a-different-interface-to-an-existing-type) 확장 타입과 달리):

```dart
extension type NumberT(int value) implements int {
  // 'int'의 멤버를 명시적으로 선언하지 않습니다.
  NumberT get i => this;
}

void main () {
  // 전부 OK: 투명성을 통해 확장 타입에서 `int` 멤버를 호출할 수 있습니다.
  var v1 = NumberT(1); // v1 타입: NumberT
  int v2 = NumberT(2); // v2 타입: int
  var v3 = v1.i - v1;  // v3 타입: int
  var v4 = v2 + v1; // v4 타입: int
  var v5 = 2 + v1; // v5 타입: int
  // 오류: 확장 타입 인터페이스는 표현 타입에 사용할 수 없습니다.
  v2.i;
}
```

또한, 슈퍼타입에서 주어진 멤버 이름을 다시 선언하여, 
새로운 멤버를 추가하고 다른 멤버를 조정하는, 
"대부분 투명한" 확장 타입을 가질 수 있습니다. 
이를 통해 메서드의 일부 매개변수에 더 엄격한 타입을 사용하거나, 예를 들어 다른 기본값을 사용할 수 있습니다.

또 다른 대부분 투명한 확장 타입 접근 방식은 표현 타입의 슈퍼타입인 타입을 구현하는 것입니다. 
예를 들어, 표현 타입이 private이지만, 슈퍼타입이 클라이언트에 중요한 인터페이스 부분을 정의하는 경우입니다.

### 2. 기존 타입에 *다른* 인터페이스 제공 {:#2-provide-a-different-interface-to-an-existing-type}

[투명하지 않은](#transparency) 확장 타입(표현 타입을 [`implement`](#구현)하지 않는 확장 타입)은, 
표현 타입과 별개인 완전히 새로운 타입으로 정적으로 처리됩니다. 
표현 타입에 할당할 수 없으며, 표현 타입의 멤버를 노출하지 않습니다.

예를 들어, [사용법](#usage)에서 선언한, `NumberE` 확장 타입을 살펴보겠습니다.

```dart
void testE() { 
  var num1 = NumberE(1);
  int num2 = NumberE(2); // 오류: 'NumberE'를 'int'에 할당할 수 없습니다.
  
  num1.isValid(); // OK: 확장 멤버 호출.
  num1.isNegative(); // 오류: 'NumberE'는 'int' 멤버 'isNegative'를 정의하지 않습니다.
  
  var sum1 = num1 + num1; // OK: 'NumberE'는 '+'를 정의합니다.
  var diff1 = num1 - num1; // 오류: 'NumberE'는 'int' 멤버 '-'를 정의하지 않습니다.
  var diff2 = num1.value - 2; // OK: 참조를 통해 표현 객체에 접근할 수 있습니다.
  var sum2 = num1 + 2; // 오류: 매개변수 타입 'NumberE'에 'int'를 할당할 수 없습니다.
  
  List<NumberE> numbers = [
    NumberE(1), 
    num1.next, // OK: 'next' getter는 'NumberE' 타입을 반환합니다.
    1, // 오류: 'Number' 리스트 타입에 'int' 요소를 할당할 수 없습니다.
  ];
}
```

이런 방식으로 확장 타입을 사용하여 기존 타입의 인터페이스를 *대체*할 수 있습니다. 
이를 통해 (소개의 `IdNumber` 예제와 같이) 새로운 타입의 제약 조건에 맞는 인터페이스를 모델링할 수 있으며, 
`int`와 같은 간단한 미리 정의된 타입의 성능과 편의성도 활용할 수 있습니다.

이 사용 사례는 래퍼 클래스의 완전한 캡슐화에 최대한 가깝습니다.
(하지만 현실적으로는 [*어느 정도* 보호된](#type-considerations) 추상화에 불과합니다)

## 타입 고려사항 {:#type-considerations}

확장 타입은 컴파일 타임 래핑 구조입니다. 
런타임에는, 확장 타입의 흔적이 전혀 없습니다. 
모든 타입 쿼리 또는 유사한 런타임 연산은 표현 타입에서 작동합니다.

이는 확장 타입을 *안전하지 않은* 추상화로 만듭니다. 
왜냐하면, 런타임에 항상 표현 타입을 찾아, 기본 객체에 액세스할 수 있기 때문입니다.

동적 타입 테스트(`e is T`), 캐스트(`e as T`) 및 
(`switch (e) ...` 또는 `if (e case ...)`와 같은) 기타 런타임 타입 쿼리는 모두 기본 표현 객체로 평가되고, 
해당 객체의 런타임 타입과 비교합니다. 
이는 `e`의 정적 타입이 확장 타입이고, 확장 타입(`case MyExtensionType(): ...`)과 비교 테스트할 때 해당됩니다.

```dart
void main() {
  var n = NumberE(1);

  // 'n'의 런타임 타입은 'int' 표현 타입입니다.
  if (n is int) print(n.value); // Prints 1.

  // 런타임에 'n'에 'int' 메서드를 사용할 수 있습니다.
  if (n case int x) print(x.toRadixString(10)); // 1을 출력합니다.
  switch (n) {
    case int(:var isEven): print("$n (${isEven ? "even" : "odd"})"); // 1 (odd)을 출력합니다.
  }
}
```

마찬가지로, 일치하는 값의 정적 타입은 이 예에서 확장 타입의 정적 타입과 같습니다.

```dart
void main() {
  int i = 2;
  if (i is NumberE) print("It is"); // 'It is'을 출력합니다..
  if (i case NumberE v) print("value: ${v.value}"); // 'value: 2'을 출력합니다..
  switch (i) {
    case NumberE(:var value): print("value: $value"); // 'value: 2'을 출력합니다..
  }
}
```

확장 타입을 사용할 때 이러한 품질을 인식하는 것이 중요합니다. 
확장 타입은 컴파일 시에 존재하고 중요하지만, 컴파일 _중에_ 지워진다는 점을 항상 명심하세요.

예를 들어, 정적 타입이 확장 타입 `E`이고, `E`의 표현 타입이 `R`인 표현식 `e`를 생각해 보세요. 
그러면, `e` 값의 런타임 타입은 `R`의 하위 타입입니다. 타입 자체도 지워집니다. 
`List<E>`는 런타임에 `List<R>`와 정확히 동일합니다.

즉, 실제 래퍼 클래스는 래핑된 객체를 캡슐화할 수 있는 반면, 
확장 타입은 래핑된 객체에 대한 컴파일 타임 뷰일 뿐입니다. 
실제 래퍼가 더 안전하지만, 확장 타입은 래퍼 객체를 피할 수 있는 옵션을 제공하여,
일부 시나리오에서 성능을 크게 향상시킬 수 있다는 trade-off가 있습니다.

[static JS interop]: /go/next-gen-js-interop
[ext]: /language/extension-methods
[generics]: /language/generics
[constructors]: /language/constructors
[factory]: /language/constructors#factory-constructors
[applicability]: {{site.repo.dart.lang}}/blob/main/accepted/2.7/static-extension-methods/feature-specification.md#examples
[more specific]: {{site.repo.dart.lang}}/blob/main/accepted/2.7/static-extension-methods/feature-specification.md#specificity
[lint]: /tools/linter-rules/annotate_redeclares
[instance variables]: /language/classes#instance-variables
[`external`]: /language/functions#external
[abstract members]: /language/methods#abstract-methods
[`is` or `as` check]: /language/operators#type-test-operators
