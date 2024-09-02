---
# title: Branches 
title: 분기
# description: Learn how to use branches to control the flow of your Dart code.
description: Dart 코드의 흐름을 제어하기 위해 분기를 사용하는 방법을 알아보세요.
prevpage:
  url: /language/loops
  # title: Loops
  title: 루프
nextpage:
  url: /language/error-handling
  # title: Error handling
  title: 오류 처리
---

이 페이지에서는 분기를 사용하여, Dart 코드의 흐름을 제어하는 ​​방법을 보여줍니다.

- `if` 문과 요소
- `if-case` 문과 요소
- `switch` 문과 표현식

다음을 사용하여 Dart에서 제어 흐름을 조작할 수도 있습니다.

- `for` 및 `while`과 같은 [루프][Loops]
- `try`, `catch` 및 `throw`와 같은 [예외][Exceptions]

## If {:#if}

Dart는 선택적 `else` 절이 있는 `if` 문을 지원합니다. 
`if` 뒤의 괄호 안의 조건은 [boolean][]으로 평가되는 표현식이어야 합니다.

<?code-excerpt "language/lib/control_flow/branches.dart (if-else)"?>
```dart
if (isRaining()) {
  you.bringRainCoat();
} else if (isSnowing()) {
  you.wearJacket();
} else {
  car.putTopDown();
}
```

`if`를 표현식 컨텍스트에서 사용하는 방법을 알아보려면, [조건 표현식][Conditional expressions]을 확인하세요.

### If-case {:#if-case}

Dart `if` 문은 [패턴][pattern] 뒤에 오는 `case` 절을 지원합니다.

<?code-excerpt "language/lib/control_flow/branches.dart (if-case)"?>
```dart
if (pair case [int x, int y]) return Point(x, y);
```

패턴이 값과 일치하면, 분기는 패턴이 범위 내에서 정의한 모든 변수로 실행됩니다.

이전 예에서, 리스트 패턴 `[int x, int y]`는 값 `pair`와 일치하므로, 
분기 `return Point(x, y)`는 패턴이 정의한 변수인 `x`와 `y`로 실행됩니다.

그렇지 않으면, 제어 흐름은 `else` 분기로 진행되어 실행됩니다. (있는 경우):

<?code-excerpt "language/lib/control_flow/branches.dart (if-case-else)"?>
```dart 
if (pair case [int x, int y]) {
  print('Was coordinate array $x,$y');
} else {
  throw FormatException('Invalid coordinates.');
}
```

if-case 문은 _단일_ 패턴에 대해 일치하고 [구조 분해][destructure]하는 방법을 제공합니다. 
_여러_ 패턴에 대해 값을 테스트하려면, [switch](#switch)를 사용합니다.

:::version-note
if 문의 Case 절에는 최소 3.0의 [언어 버전][language version]이 필요합니다.
:::

<a id="switch"></a>
## Switch 문 {:#switch-statements}

`switch` 문은 일련의 case에 대해 값 표현식을 평가합니다. 
각 `case` 절은 일치할 값에 대한 [패턴][pattern]입니다. 
case에 [어떤 종류의 패턴][any kind of pattern]이든 사용할 수 있습니다.

값이 case의 패턴과 일치하면, case 본문이 실행됩니다. 
비어 있지 않은 `case` 절은 완료 후 switch의 끝으로 점프합니다. 
`break` 문이 필요하지 않습니다. 
비어 있지 않은 `case` 절을 끝내는 다른 유효한 방법은, 
[`continue`][break], [`throw`][] 또는 [`return`][] 문입니다.

`case` 절이 일치하지 않을 때 코드를 실행하려면, 
`default` 또는 [와일드카드 `_`][wildcard `_`] 절을 사용합니다.

<?code-excerpt "language/lib/control_flow/branches.dart (switch)"?>
```dart
var command = 'OPEN';
switch (command) {
  case 'CLOSED':
    executeClosed();
  case 'PENDING':
    executePending();
  case 'APPROVED':
    executeApproved();
  case 'DENIED':
    executeDenied();
  case 'OPEN':
    executeOpen();
  default:
    executeUnknown();
}
```

<a id="switch-share"></a>

빈 케이스는 다음 케이스로 넘어가며, 케이스가 본문을 공유할 수 있습니다. 
넘어가지 않는 빈 케이스의 경우, 본문에 [`break`][break]를 사용합니다. 
비순차적 폴스루(non-sequential fall-through)의 경우, 
[`continue` 문][break]과 레이블을 사용할 수 있습니다.

<?code-excerpt "language/lib/control_flow/branches.dart (switch-empty)"?>
```dart
switch (command) {
  case 'OPEN':
    executeOpen();
    continue newCase; // newCase 라벨에서 계속 실행됩니다.

  case 'DENIED': // 빈 케이스가 떨어집니다.
  case 'CLOSED':
    executeClosed(); // DENIED 및 CLOSED 모두에 대해 실행됩니다.

  newCase:
  case 'PENDING':
    executeNowClosed(); // OPEN과 PENDING 모두에 적용됩니다.
}
```

[Logical-or 패턴][logical-or patterns]를 사용하면, 케이스가 본문이나 가드를 공유할 수 있습니다. 
패턴과 케이스 절에 대해 자세히 알아보려면, [Switch 문과 표현식][Switch statements and expressions]에 있는 패턴 설명서를 확인하세요.

[Switch statements and expressions]: /language/patterns#switch-statements-and-expressions

### 스위치 표현식 {:#switch-expressions}

`switch` _expression_ 은 일치하는 case의 표현식 본문에 따라 값을 생성합니다. 
Dart에서 표현식을 허용하는 곳이면 어디에서나 switch 표현식을 사용할 수 있지만, 
표현식 명령문의 시작 부분은 *예외* 입니다. 예를 들어:

```dart
var x = switch (y) { ... };

print(switch (x) { ... });

return switch (x) { ... };
```

표현식 문장의 시작 부분에 스위치를 사용하려면, [switch 문장](#switch-statements)을 사용하세요.

switch 표현식을 사용하면, switch _문장_ 을 다음과 같이 다시 쓸 수 있습니다.

<?code-excerpt "language/lib/control_flow/branches.dart (switch-stmt)"?>
```dart
// 슬래시, 별표, 쉼표, 세미콜론 등은 상수 변수입니다.
switch (charCode) {
  case slash || star || plus || minus: // Logical-or 패턴
    token = operator(charCode);
  case comma || semicolon: // Logical-or 패턴
    token = punctuation(charCode);
  case >= digit0 && <= digit9: // Relational 및 logical-and 패턴
    token = number();
  default:
    throw FormatException('Invalid');
}
```

다음과 같은 _표현식_으로 표현하면:

<?code-excerpt "language/lib/control_flow/branches.dart (switch-exp)"?>
```dart
token = switch (charCode) {
  slash || star || plus || minus => operator(charCode),
  comma || semicolon => punctuation(charCode),
  >= digit0 && <= digit9 => number(),
  _ => throw FormatException('Invalid')
};
```

`switch` 표현식의 구문은 `switch` 문 구문과 다릅니다.

- 케이스는 `case` 키워드로 시작하지 _않습니다_.
- 케이스 본문은 일련의 문장 대신 단일 표현식입니다.
- 각 케이스에는 본문이 있어야 합니다. 빈 케이스에 대한 암묵적 폴스루는 없습니다.
- 케이스 패턴은 `:` 대신 `=>`를 사용하여 본문과 구분됩니다.
- 케이스는 `,`로 구분합니다(선택적으로 끝에 `,`를 사용할 수 있음).
- 기본 케이스는 `default`와 `_`를 모두 허용하는 대신, `_`*만 사용할 수* 있습니다.

:::version-note
스위치 표현식에는 최소 3.0의 [언어 버전][language version]이 필요합니다.
:::

### 완전성 검사 {:#exhaustiveness-checking}

완전성 검사는 값이 스위치에 입력되었지만, 
어떤 케이스에도 맞지 않을 경우 컴파일 타임 오류를 보고하는 기능입니다.

<?code-excerpt "language/lib/control_flow/branches.dart (exh-bool)"?>
```dart
// bool?에 대한 비완전한(Non-exhaustive) 스위치, null 가능성과 일치하는 케이스가 누락됨:
switch (nullableBool) {
  case true:
    print('yes');
  case false:
    print('no');
}
```

디폴트 케이스(`default` 또는 `_`)는 스위치를 통과할 수 있는 모든 가능한 값을 포함합니다. 
이렇게 하면 어떤 타입에 대해서도 완전하게 스위치를 만들 수 있습니다.

[Enums][enum] 및 [sealed 타입][sealed]은 디폴트 케이스가 없어도 가능한 값이 알려져 있고, 
완전히 열거 가능하기 때문에 스위치에 특히 유용합니다. 
클래스에서 [`sealed` 수정자][sealed]를 사용하여,
해당 클래스의 하위 타입을 전환할 때 완전성 검사를 활성화합니다.

<?code-excerpt "language/lib/patterns/algebraic_datatypes.dart (algebraic-datatypes)"?>
```dart
sealed class Shape {}

class Square implements Shape {
  final double length;
  Square(this.length);
}

class Circle implements Shape {
  final double radius;
  Circle(this.radius);
}

double calculateArea(Shape shape) => switch (shape) {
      Square(length: var l) => l * l,
      Circle(radius: var r) => math.pi * r * r
    };
```

누군가가 `Shape`의 새로운 하위 클래스를 추가한다면, 
이 `switch` 표현식은 불완전할 것입니다. 
완전성 검사는 누락된 하위 타입을 알려줍니다. 
이를 통해 Dart를 다소 [함수적 대수적 데이터 타입 스타일](https://en.wikipedia.org/wiki/Algebraic_data_type)로 사용할 수 있습니다.

<a id="when"></a>
## Guard 절 {:#guard-clause}

`case` 절 뒤에 선택적 guard 절을 설정하려면, 키워드 `when`을 사용합니다. 
guard 절은 `if case`와 `switch` 문과 표현식 뒤에 올 수 있습니다.

```dart
// Switch 문:
switch (something) {
  case somePattern when some || boolean || expression:
    //             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Guard 절.
    body;
}

// Switch 표현식:
var value = switch (something) {
  somePattern when some || boolean || expression => body,
  //               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Guard 절.
}

// If-case 문:
if (something case somePattern when some || boolean || expression) {
  //                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Guard 절.
  body;
}
```

guard는 매칭 _후_ 임의의 boolean 표현식을 평가합니다. 
이를 통해 케이스 본문을 실행해야 하는지 여부에 대한 추가 제약 조건을 추가할 수 있습니다. 
guard 절이 거짓으로 평가되면, 전체 스위치를 종료하는 대신 다음 케이스로 실행이 진행됩니다.

[language version]: /guides/language/evolution#language-versioning
[loops]: /language/loops
[exceptions]: /language/error-handling
[conditional expressions]: /language/operators#conditional-expressions
[boolean]: /language/built-in-types#booleans
[pattern]: /language/patterns
[enum]: /language/enums
[`throw`]: /language/error-handling#throw
[`return`]: /language/functions#return-values
[wildcard `_`]: /language/pattern-types#wildcard
[break]: /language/loops#break-and-continue
[sealed]: /language/class-modifiers#sealed
[any kind of pattern]: /language/pattern-types
[destructure]: /language/patterns#destructuring
[section on switch]: /language/patterns#switch-statements-and-expressions
[logical-or patterns]: /language/patterns#or-pattern-switch
