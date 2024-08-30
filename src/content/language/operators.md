---
# title: Operators
title: 연산자
# description: Learn about the operators Dart supports.
description: Dart가 지원하는 연산자에 대해 알아보세요.
prevpage:
  url: /language/variables
  # title: Variables
  title: 변수
nextpage:
  url: /language/comments
  # title: Comments
  title: 주석
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

<a name="operators"></a>

Dart는 다음 표에 표시된 연산자를 지원합니다. 
이 표는 Dart의 연산자 결합성(Associativity)과 [연산자 우선순위](#operator-precedence-example)를 가장 높은 것부터 가장 낮은 것까지 보여줍니다. 
이는 Dart의 연산자 관계에 대한 **근사치(approximation)** 입니다. 
이러한 [연산자 중 다수를 클래스 멤버로][operators as class members] 구현할 수 있습니다.

| 설명                             | 연산자                                                                                           | 결합성(Associativity) |
|-----------------------------------------|----------------------------------------------------------------------------------------------------|---------------|
| unary postfix                           | *`expr`*`++`    *`expr`*`--`    `()`    `[]`    `?[]`    `.`    `?.`    `!`                        | 없음          |
| unary prefix                            | `-`*`expr`*    `!`*`expr`*    `~`*`expr`*    `++`*`expr`*    `--`*`expr`*      `await` *`expr`*    | 없음          |
| multiplicative                          | `*`    `/`    `%`    `~/`                                                                          | 왼쪽          |
| additive                                | `+`    `-`                                                                                         | 왼쪽          |
| shift                                   | `<<`    `>>`    `>>>`                                                                              | 왼쪽          |
| bitwise AND                             | `&`                                                                                                | 왼쪽          |
| bitwise XOR                             | `^`                                                                                                | 왼쪽          |
| bitwise OR                              | <code>&#124;</code>                                                                                | 왼쪽          |
| 관계 및 타입 테스트                | `>=`    `>`    `<=`    `<`    `as`    `is`    `is!`                                                | 없음          |
| equality                                | `==`    `!=`                                                                                       | 없음          |
| logical AND                             | `&&`                                                                                               | 왼쪽          |
| logical OR                              | <code>&#124;&#124;</code>                                                                          | 왼쪽          |
| if-null                                 | `??`                                                                                               | 왼쪽          |
| conditional                             | *`expr1`*    `?`    *`expr2`*    `:`    *`expr3`*                                                  | 오른쪽         |
| cascade                                 | `..`    `?..`                                                                                      | 왼쪽          |
| assignment                              | `=`    `*=`    `/=`    `+=`    `-=`    `&=`    `^=`    *etc.*                                      | 오른쪽         |
| spread  ([참고사항 참조](#spread-operators)) | `...`    `...?`                                                                                    | 없음          |

{:.table .table-striped}

:::warning
이전 표는 도움이 되는 가이드로만 사용해야 합니다. 
연산자 우선순위와 결합성의 개념은 언어 문법에서 발견되는 진실의 근사치입니다. 
Dart 연산자 관계의 권한 있는 동작은 [Dart 언어 사양][Dart language specification]에 정의된 문법에서 찾을 수 있습니다.
:::

연산자를 사용하면, 표현식을 만듭니다. 연산자 표현식의 몇 가지 예는 다음과 같습니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (expressions)" replace="/,//g"?>
```dart
a++
a + b
a = b
a == b
c ? a : b
a is T
```

## 연산자 우선순위 예 {:#operator-precedence-example}

[연산자 테이블](#operators)에서, 각 연산자는 그 뒤에 오는 행의 연산자보다 우선순위가 높습니다. 
예를 들어, 곱셈 연산자 `%`는 등식 연산자 `==`보다 우선순위가 높고(따라서 먼저 실행됨), 
논리 AND 연산자 `&&`보다 우선순위가 높습니다. 
이 우선순위는 다음 두 줄의 코드가 같은 방식으로 실행됨을 의미합니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (precedence)"?>
```dart
// 괄호를 사용하면 가독성이 향상됩니다.
if ((n % i == 0) && (d % i == 0)) ...

// 읽기는 어렵지만, 동등합니다.
if (n % i == 0 && d % i == 0) ...
```

:::warning
두 개의 피연산자를 취하는 연산자의 경우, 가장 왼쪽의 피연산자가 어떤 방법을 사용할지 결정합니다. 
예를 들어, `Vector` 객체와 `Point` 객체가 있는 경우, `aVector + aPoint`는 `Vector` 덧셈(`+`)을 사용합니다.
:::


## 산술 연산자 {:#arithmetic-operators}

Dart는 다음 표에 표시된 것처럼 일반적인 산술 연산자를 지원합니다.

| 연산자    | 의미                                                                  |
|-------------|--------------------------------------------------------------------------|
| `+`         | 덧셈                                                                      |
| `-`         | 뺄셈                                                                 |
| `-`*`expr`* | 단항 마이너스, 부정이라고도 함(표현식의 부호를 반전) |
| `*`         | 곱셈                                                                 |
| `/`         | 나눗셈                                                                   |
| `~/`        | 나눗셈, 정수 결과 반환                                    |
| `%`         | 정수 나누기의 나머지를 구합니다. (modulo)   |

{:.table .table-striped}

예:

<?code-excerpt "misc/test/language_tour/operators_test.dart (arithmetic)"?>
```dart
assert(2 + 3 == 5);
assert(2 - 3 == -1);
assert(2 * 3 == 6);
assert(5 / 2 == 2.5); // 결과는 double 입니다.
assert(5 ~/ 2 == 2); // 결과는 int 입니다.
assert(5 % 2 == 1); // 나머지

assert('5/2 = ${5 ~/ 2} r ${5 % 2}' == '5/2 = 2 r 1');
```

Dart는 접두사(prefix) 및 접미사(postfix) 증가 및 감소 연산자도 지원합니다.

| 연산자                    | 의미                                            |
|-----------------------------|----------------------------------------------------|
| `++`*`var`* | *`var`*  `=`  *`var `*  `+ 1` (표현식 값은 *`var`* ` + 1` 입니다) |
| *`var`*`++` | *`var`*  `=`  *`var `*  `+ 1` (표현식 값은 *`var`* 입니다)        |
| `--`*`var`* | *`var`*  `=`  *`var `*  `- 1` (표현식 값은 *`var`* ` - 1` 입니다) |
| *`var`*`--` | *`var`*  `=`  *`var `*  `- 1` (표현식 값은 *`var`* 입니다)       |

{:.table .table-striped}

예:

<?code-excerpt "misc/test/language_tour/operators_test.dart (increment-decrement)"?>
```dart
int a;
int b;

a = 0;
b = ++a; // b가 값을 얻기 전에 a를 증가시킵니다.
assert(a == b); // 1 == 1

a = 0;
b = a++; // b가 값을 얻은 후 a를 증가시킵니다.
assert(a != b); // 1 != 0

a = 0;
b = --a; // b가 값을 얻기 전에 a를 감소시킵니다.
assert(a == b); // -1 == -1

a = 0;
b = a--; // b가 값을 얻은 후 a를 감소시킵니다.
assert(a != b); // -1 != 0
```

## 동등 연산자와 관계 연산자 {:#equality-and-relational-operators}

다음 표는 동등 연산자와 관계 연산자의 의미를 나열한 것입니다.

| 연산자  | 의미                                   |
|-----------|-------------------------------------------|
| `==`      | 동일함; 아래 토론 참조               |
| `!=`      | 동일하지 않음                                 |
| `>`       | 보다 큼                              |
| `<`       | 보다 작음                                 |
| `>=`      | 보다 크거나 같음                  |
| `<=`      | 보다 작거나 같음                     |

{:.table .table-striped}

두 객체 x와 y가 같은 것을 나타내는지 테스트하려면, `==` 연산자를 사용합니다. 
(두 객체가 정확히 같은 객체인지 알아야 하는 드문 경우에는, 대신 [identical()][] 함수를 사용합니다.) 
`==` 연산자의 작동 방식은 다음과 같습니다.

1. *x* 또는 *y*가 null인 경우, 둘 다 null이면 true를 반환하고, 하나만 null이면 false를 반환합니다.

2. 인수 *y*를 사용하여 *x*에서 `==` 메서드를 호출한 결과를 반환합니다. 
   (맞습니다. `==`와 같은 연산자는 첫 번째 피연산자에서 호출되는 메서드입니다. 
   자세한 내용은 [연산자][Operators]를 참조하세요.)

다음은 각 동등 및 관계 연산자를 사용하는 예입니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (relational)"?>
```dart
assert(2 == 2);
assert(2 != 3);
assert(3 > 2);
assert(2 < 3);
assert(3 >= 3);
assert(2 <= 3);
```

## 타입 테스트 연산자 {:#type-test-operators}

`as`, `is`, `is!` 연산자는 런타임에 타입을 확인하는 데 유용합니다.

| 연산자  | 의미                                              |
|-----------|------------------------------------------------------|
| `as`      | 타입캐스트([라이브러리 접두사][library prefixes] 지정에도 사용됨) |
| `is`      | 객체가 지정된 타입을 가지고 있는 경우 True        |
| `is!`     | 객체에 지정된 타입을 가지고 있지 않은 경우 True   |

{:.table .table-striped}

`obj is T`의 결과는 `obj`가 `T`에서 지정한 인터페이스를 구현하는 경우 true 입니다. 
예를 들어, `obj is Object?`는 항상 true 입니다.

`as` 연산자를 사용하여 객체가 해당 타입인지 확신하는 경우에만, 객체를 특정 타입으로 캐스팅합니다. 예:

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (emp-as-person)"?>
```dart
(employee as Person).firstName = 'Bob';
```

객체가 `T` 타입인지 확실하지 않으면, 객체를 사용하기 전에 `is T`를 사용하여 타입을 확인합니다.

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (emp-is-person)"?>
```dart
if (employee is Person) {
  // 타입 체크
  employee.firstName = 'Bob';
}
```

:::note
코드는 동일하지 않습니다. 
`employee`가 null이거나 `Person`이 아니면, 
첫 번째 예는 예외를 발생시키고 두 번째 예는 아무것도 하지 않습니다.
:::

## 할당 연산자 {:#assignment-operators}

이미 보셨듯이, `=` 연산자를 사용하여 값을 할당할 수 있습니다. 
할당된 변수가 null인 경우에만 할당하려면, `??=` 연산자를 사용합니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (assignment)"?>
```dart
// a에 값을 할당합니다
a = value;
// b가 null인 경우 b에 값을 할당합니다. 그렇지 않으면, b는 동일하게 유지됩니다.
b ??= value;
```

`+=`와 같은 복합 할당 연산자는 연산과 할당을 결합합니다.

|      |       |       |        |                      |
|------|-------|-------|--------|----------------------|
| `=`  | `*=`  | `%=`  | `>>>=` | `^=`                 |
| `+=` | `/=`  | `<<=` | `&=`   | <code>&#124;=</code> |
| `-=` | `~/=` | `>>=` |        |                      |

{:.table}

복합 할당 연산자의 작동 방식은 다음과 같습니다.

|                           | 복합 할당 | 동일한 표현 |
|---------------------------|---------------------|-----------------------|
| **연산자 *op*에 대해:** | `a `  *`op`*`= b`   | `a = a ` *`op `* `b`  |
| **예제:**              | `a += b`            | `a = a + b`           |

{:.table}

다음 예에서는 할당 연산자와 복합 할당 연산자를 사용합니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (op-assign)"?>
```dart
var a = 2; // =를 사용하여 할당
a *= 3; // 할당하고 곱합니다: a = a * 3
assert(a == 6);
```

## 논리 연산자 {:#logical-operators}

논리 연산자를 사용하여 boolean 표현식을 반전하거나 결합할 수 있습니다.

| 연산자                   | 의미                                                                  |
|----------------------------|--------------------------------------------------------------------------|
| `!`*`expr`*                | 다음 표현식을 반전합니다. (false를 true로, true를 false로 변경) |
| <code>&#124;&#124;</code>  | 논리적 OR                                                               |
| `&&`                       | 논리적 AND                                                              |

{:.table .table-striped}

논리 연산자를 사용하는 예는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/operators.dart (op-logical)"?>
```dart
if (!done && (col == 0 || col == 3)) {
  // ...무언가를 합니다...
}
```


## 비트 연산자와 시프트 연산자 {:#bitwise-and-shift-operators}

Dart에서 숫자의 개별 비트를 조작할 수 있습니다. 
보통, 정수에 이러한 비트와 시프트 연산자를 사용합니다.

| 연산자            | 의미                                               |
|---------------------|-------------------------------------------------------|
| `&`                 | AND                                                   |
| <code>&#124;</code> | OR                                                    |
| `^`                 | XOR                                                   |
| `~`*`expr`*         | 단항 비트 보수(0은 1이 되고, 1은 0이 됨) |
| `<<`                | 왼쪽으로 이동(시프트)                                            |
| `>>`                | 오른쪽으로 이동(시프트)                                           |
| `>>>`               | 부호 없는 오른쪽 이동(시프트)                                  |

{:.table .table-striped}

:::note
크거나 음수 피연산자를 사용하는 비트 연산의 동작은 플랫폼마다 다를 수 있습니다. 
자세한 내용은 [비트 연산 플랫폼 차이점][Bitwise operations platform differences]을 확인하세요.
:::

다음은 비트 연산자와 이동(시프트) 연산자를 사용하는 예입니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (op-bitwise)"?>
```dart
final value = 0x22;
final bitmask = 0x0f;

assert((value & bitmask) == 0x02); // AND
assert((value & ~bitmask) == 0x20); // AND NOT
assert((value | bitmask) == 0x2f); // OR
assert((value ^ bitmask) == 0x2d); // XOR

assert((value << 4) == 0x220); // 왼쪽으로 이동(시프트) 
assert((value >> 4) == 0x02); // 오른쪽으로 이동(시프트) 

// 피연산자 값이 32비트로 마스크 처리되면 변경되기 때문에, 
// 웹에서 다른 동작이 발생하는 오른쪽 이동(시프트) 예제:
assert((-value >> 4) == -0x03);

assert((value >>> 4) == 0x02); // 부호 없는 오른쪽 이동(시프트)
assert((-value >>> 4) > 0); // 부호 없는 오른쪽 이동(시프트)
```

:::version-note
`>>>` 연산자(_삼중 이동(triple-shift)_ 또는 _부호 없는 이동(unsigned shift)_ 이라고도 함)를 사용하려면, 
최소 2.14의 [언어 버전][language version]이 필요합니다.
:::

[Bitwise operations platform differences]: /guides/language/numbers#bitwise-operations

## 조건부 표현식 {:#conditional-expressions}

Dart에는 [if-else][] 문이 필요할 수 있는 표현식을 간결하게 평가할 수 있는 두 가지 연산자가 있습니다.

*`condition `* `? ` *`expr1 `* `: ` *`expr2`*
: _condition_ 이 참이면, _expr1_ 을 평가하고(값을 반환함); 그렇지 않으면, _expr2_ 의 값을 평가하고 반환합니다.

*`expr1 `* `?? ` *`expr2`*
: _expr1_ 이 null이 아니면 값을 반환하고; 그렇지 않으면 _expr2_ 의 값을 평가하고 반환합니다.

boolean 표현식에 따라 값을 할당해야 하는 경우, 조건 연산자 `?`와 `:`를 사용하는 것을 고려하세요.

<?code-excerpt "misc/lib/language_tour/operators.dart (if-then-else-operator)"?>
```dart
var visibility = isPublic ? 'public' : 'private';
```

boolean 표현식이 null인지 테스트하는 경우, 
if-null 연산자 `??` (null 병합 연산자라고도 함)를 사용하는 것이 좋습니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (if-null)"?>
```dart
String playerName(String? name) => name ?? 'Guest';
```

이전 예는 적어도 두 가지 다른 방식으로 작성될 수 있었지만, 간결하게 작성되지는 않았습니다.

<?code-excerpt "misc/test/language_tour/operators_test.dart (if-null-alt)"?>
```dart
// 조금 더 긴 버전은 ?: 연산자를 사용합니다.
String playerName(String? name) => name != null ? name : 'Guest';

// 매우 긴 버전은 if-else 문을 사용합니다.
String playerName(String? name) {
  if (name != null) {
    return name;
  } else {
    return 'Guest';
  }
}
```

## 캐스케이드 표기법 {:#cascade-notation}

캐스케이드(`..`, `?..`)를 사용하면 동일한 객체에서 일련의 연산을 수행할 수 있습니다. 
인스턴스 멤버에 액세스하는 것 외에도, 동일한 객체에서 인스턴스 메서드를 호출할 수도 있습니다. 
이렇게 하면 임시 변수를 만드는 단계를 절약하고, 보다 유연한 코드를 작성할 수 있습니다.

다음 코드를 고려하세요.

<?code-excerpt "misc/lib/language_tour/cascades.dart (cascade)"?>
```dart
var paint = Paint()
  ..color = Colors.black
  ..strokeCap = StrokeCap.round
  ..strokeWidth = 5.0;
```

생성자 `Paint()`는 `Paint` 객체를 반환합니다. 
캐스케이드 표기법을 따르는 코드는 이 객체에서 연산하며 반환될 수 있는 모든 값을 무시합니다.

이전 예제는 이 코드와 동일합니다.

<?code-excerpt "misc/lib/language_tour/cascades.dart (cascade-expanded)"?>
```dart
var paint = Paint();
paint.color = Colors.black;
paint.strokeCap = StrokeCap.round;
paint.strokeWidth = 5.0;
```

캐스케이드가 작동하는 객체가 null일 수 있는 경우, 
첫 번째 연산에 _null-shorting_ 캐스케이드(`?..`)를 사용합니다. 
`?..`로 시작하면, 해당 null 객체에 캐스케이드 연산이 시도되지 않습니다.

<?code-excerpt "misc/test/language_tour/browser_test.dart (cascade-operator)"?>
```dart
querySelector('#confirm') // 객체를 가져옵니다.
  ?..text = 'Confirm' // 그것의 멤버를 사용합니다.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'))
  ..scrollIntoView();
```

:::version-note
`?..` 구문에는 최소 2.12의 [언어 버전][language version]이 필요합니다.
:::

이전 코드는 다음과 같습니다.

<?code-excerpt "misc/test/language_tour/browser_test.dart (cascade-operator-example-expanded)"?>
```dart
var button = querySelector('#confirm');
button?.text = 'Confirm';
button?.classes.add('important');
button?.onClick.listen((e) => window.alert('Confirmed!'));
button?.scrollIntoView();
```

또한 캐스케이드를 중첩할 수도 있습니다. 예를 들어:

<?code-excerpt "misc/lib/language_tour/operators.dart (nested-cascades)"?>
```dart
final addressBook = (AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
```

실제 객체를 반환하는 함수에서 캐스케이드를 구성하는 데 주의하세요. 
예를 들어, 다음 코드는 실패합니다.

<?code-excerpt "misc/lib/language_tour/operators.dart (cannot-cascade-on-void)" plaster="none"?>
```dart
var sb = StringBuffer();
sb.write('foo')
  ..write('bar'); // 오류: 'void'에 대해 'write' 메서드가 정의되지 않았습니다.
```

`sb.write()` 호출은 void를 반환하고, `void`에 대한 캐스케이드를 구성할 수 없습니다.

:::note
엄밀히 말해서, 캐스케이드에 대한 "더블 도트" 표기법은 연산자가 아닙니다.
그것은 단지 Dart 구문의 일부일 뿐입니다.
:::

## 스프레드 연산자 {:#spread-operators}

스프레드 연산자는 컬렉션을 생성하는 표현식을 평가하고, 결과 값을 언팩하고, 다른 컬렉션에 삽입합니다.

**스프레드 연산자는 실제로 연산자 표현식이 아닙니다.** 
`...`/`...?` 구문은 컬렉션 리터럴 자체의 일부입니다. 
따라서, [컬렉션](/language/collections#spread-operators) 페이지에서, 
스프레드 연산자에 대해 자세히 알아볼 수 있습니다.

연산자가 아니기 때문에 구문에는 "[연산자 우선 순위](#operators)"가 없습니다. 
사실상 가장 낮은 "우선 순위"를 가지고 있습니다. 
다음과 같은 모든 종류의 표현식이 스프레드 대상으로 유효합니다.

```dart
[...a + b]
```

## 기타 연산자 {:#other-operators}

나머지 연산자의 대부분은 다른 예에서 이미 보았습니다.

| 연산자 | 이름                         | 의미                                                                                                                                                                                                                                                 |
|:----------:|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `()`     | 함수 적용         | 함수 호출을 나타냅니다 |
| `[]`     | Subscript 액세스             | 재정의 가능한 `[]` 연산자에 대한 호출을 나타냅니다. 예: `fooList[1]`은 인덱스 `1`에 있는 요소에 액세스하기 위해 int `1`을 `fooList`에 전달합니다. |
| `?[]`    | 조건부 subscript 액세스 | `[]`와 유사하지만, 가장 왼쪽 피연산자는 null일 수 있음. 예: `fooList?[1]`은 `fooList`가 null인 경우(식은 null로 평가됨)를 제외하고, 인덱스 `1`에 있는 요소에 액세스하기 위해 int `1`을 `fooList`에 전달함. |
| `.`      | Member 액세스  | 표현식의 속성을 참조합니다. 예: `foo.bar`는 표현식 `foo`에서 속성 `bar`를 선택합니다. |
| `?.`     | 조건부 member 액세스    | `.`와 유사하지만, 가장 왼쪽 피연산자는 null이 될 수 있습니다. 예: `foo?.bar`는 `foo`가 null이 아닌 한 표현식 `foo`에서 속성 `bar`를 선택합니다. (이 경우 `foo?.bar`의 값은 null입니다) |
| `!`      | null이 아닌 어설션 연산자  | 표현식을 기본 non-nullable 타입으로 캐스팅하고, 캐스팅이 실패하면 런타임 예외를 발생시킵니다. 예: `foo!.bar`는 `foo`가 null이 아니라고 주장하고, 속성 `bar`를 선택합니다. 단, `foo`가 null인 경우 런타임 예외가 발생합니다. |

{:.table .table-striped}

`.`, `?.` 및 `..` 연산자에 대한 자세한 내용은 [클래스][Classes]를 참조하세요.


[operators as class members]: /language/methods#operators
[Dart language specification]: /guides/language/spec
[identical()]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/identical.html
[Operators]: /language/methods#operators
[library prefixes]: /language/libraries#specifying-a-library-prefix
[if-else]: /language/branches#if
[language version]: /guides/language/evolution#language-versioning
[Classes]: /language/classes
