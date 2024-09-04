---
# title: Pattern types
title: 패턴 타입
# description: Pattern type reference in Dart.
description: Dart의 패턴 타입 참조.
prevpage:
  url: /language/patterns
  # title: Patterns
  title: 패턴
nextpage:
  url: /language/functions
  # title: Functions
  title: 함수
---

이 페이지는 다양한 종류의 패턴에 대한 참조입니다. 
패턴의 작동 방식, Dart에서 패턴을 사용할 수 있는 곳, 
일반적인 사용 사례에 대한 개요는 메인 [패턴][Patterns] 페이지를 방문하세요.

#### 패턴 우선순위 {:#pattern-precedence}

[연산자 우선순위](/language/operators#operator-precedence-example)와 유사하게, 
패턴 평가는 우선순위 규칙을 따릅니다. 
[괄호로 묶인 패턴](#parenthesized)을 사용하여, 우선순위가 낮은 패턴을 먼저 평가할 수 있습니다.

이 문서에서는 패턴 타입을 우선순위에 따라 오름차순으로 나열합니다.

* [Logical-or](#logical-or) 패턴은 [Logical-and](#logical-and)보다 우선순위가 낮고, 
  Logical-and 패턴은 [Relational](#relational) 패턴보다 우선순위가 낮습니다.

* 후위 단항(Post-fix unary) 패턴([cast](#cast), [null-check](#null-check), [null-assert](#null-assert))은 
  동일한 우선순위 레벨을 공유합니다.

* 나머지 기본 패턴은 가장 높은 우선순위를 공유합니다. 
  컬렉션 타입([record](#record), [list](#list), [map](#map)) 및 [Object](#object) 패턴은, 
  다른 데이터를 포함하므로 먼저 외부 패턴으로 평가됩니다.

## Logical-or {:#logical-or}

`subpattern1 || subpattern2`

Logical-or 패턴은 하위 패턴을 `||`로 구분하고, 브랜치 중 하나라도 일치하면 일치합니다. 
브랜치는 왼쪽에서 오른쪽으로 평가됩니다. 
브랜치가 일치하면, 나머지는 평가되지 않습니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (logical-or)"?>
```dart
var isPrimary = switch (color) {
  Color.red || Color.yellow || Color.blue => true,
  _ => false
};
```

Logical-or 패턴의 하위 패턴은 변수를 바인딩할 수 있지만, 
패턴이 일치할 때 하나의 브랜치만 평가되기 때문에, 
브랜치는 동일한 변수 세트를 정의해야 합니다.

## Logical-and {:#logical-and}

`subpattern1 && subpattern2`

`&&`로 구분된 패턴 쌍은 두 하위 패턴이 모두 일치하는 경우에만 일치합니다. 
왼쪽 분기가 일치하지 않으면, 오른쪽 분기는 평가되지 않습니다.

Logical-and 패턴의 하위 패턴은 변수를 바인딩할 수 있지만, 
각 하위 패턴의 변수는 겹치지 않아야 합니다. 
패턴이 일치하면, 둘 다 바인딩되기 때문입니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (logical-and)"?>
```dart
switch ((1, 2)) {
  // 오류, 두 하위 패턴 모두 'b'를 바인딩하려고 시도했습니다.
  case (var a, var b) && (var b, var c): // ...
}
```

## Relational {:#relational}

`== expression`

`< expression`

Relational 패턴은 일치된 값을 주어진 상수와 비교하는데, 
등호 또는 관계 연산자인 `==`, `!=`, `<`, `>`, `<=`, `>=`를 사용합니다.

패턴은, 상수를 인수로 사용하여 일치된 값에 적절한 연산자를 호출할 때, 일치하여 `true`를 반환합니다.

Relational 패턴은 숫자 범위에서 일치시키는 데 유용하며, 
특히 [logical-and 패턴](#logical-and)과 결합할 때 유용합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (relational)"?>
```dart
String asciiCharType(int char) {
  const space = 32;
  const zero = 48;
  const nine = 57;

  return switch (char) {
    < space => 'control',
    == space => 'space',
    > space && < zero => 'punctuation',
    >= zero && <= nine => 'digit',
    _ => ''
  };
}
```

## Cast {:#cast}

`foo as String`

캐스트 패턴을 사용하면, 값을 다른 하위 패턴에 전달하기 전에, 
구조 분해 중간에 [타입 캐스트][type cast]를 삽입할 수 있습니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (cast)"?>
```dart
(num, Object) record = (1, 's');
var (i as int, s as String) = record;
```

값에 명시된 타입이 없으면 캐스트 패턴은 [throw][]합니다. 
[null-assert 패턴](#null-assert)과 마찬가지로, 
이를 통해 일부 구조 분해되지 않은 값의 예상 타입을 강제로 assert 할 수 있습니다.

## Null-check	{:#null-check}

`subpattern?`

Null-check 패턴은 값이 null이 아니면 먼저 매치하고, 
그런 다음 내부 패턴을 같은 값과 매치합니다. 
이를 통해 매치되는 nullable 값의 null이 불가능한 베이스 타입인 변수를 바인딩할 수 있습니다.

throw하지 않고 `null` 값을 매치 실패로 처리하려면, null-check 패턴을 사용합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (null-check)"?>
```dart
String? maybeString = 'nullable with base type String';
switch (maybeString) {
  case var s?:
  // 여기서 's'는 null이 불가능한 문자열 타입을 갖습니다.
}
```

값이 _null인 경우_ 일치시키려면, [상수 패턴](#constant) `null`을 사용하세요.

## Null-assert {:#null-assert}

`subpattern!`

Null-assert 패턴은 객체가 null이 아니면 먼저 일치하고, 그 다음에는 값에 일치합니다. 
null이 아닌 값이 흐르도록 허용하지만, 일치된 값이 null이면 [throw][]합니다.

`null` 값이 자동으로 일치 실패로 처리되지 않도록 하려면, 
일치할 때 null-assert 패턴을 사용합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (null-assert-match)"?>
```dart
List<String?> row = ['user', null];
switch (row) {
  case ['user', var name!]: // ...
  // 여기서 'name'은 null이 될 수 없는 문자열입니다.
}
```

변수 선언 패턴에서 `null` 값을 제거하려면, null-assert 패턴을 사용하세요.

<?code-excerpt "language/lib/patterns/pattern_types.dart (null-assert-dec)"?>
```dart
(int?, int?) position = (2, 3);

var (x!, y!) = position;
```

값이 _null인 경우_ 일치시키려면, [상수 패턴](#constant) `null`을 사용하세요.

## Constant	{:#constant}

`123, null, 'string', math.pi, SomeClass.constant, const Thing(1, 2), const (1 + 2)`

값이 상수와 같을 때, 상수 패턴이 일치합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (constant)"?>
```dart
switch (number) {
  // 1 == number인 경우, 일치합니다.
  case 1: // ...
}
```

간단한 리터럴과 명명된 상수에 대한 참조를 상수 패턴으로 직접 사용할 수 있습니다.

- 숫자 리터럴(`123`, `45.56`)
- Boolean 리터럴(`true`)
- 문자열 리터럴(`'string'`)
- 명명된 상수(`someConstant`, `math.pi`, `double.infinity`)
- 상수 생성자(`const Point(0, 0)`)
- 상수 컬렉션 리터럴(`const []`, `const {1, 2}`)

더 복잡한 상수 표현식은 괄호로 묶고, `const` 접두사로 붙여야 합니다. (`const (1 + 2)`):

<?code-excerpt "language/lib/patterns/pattern_types.dart (complex-constant)"?>
```dart
// 리스트 또는 맵 패턴:
case [a, b]: // ...

// 리스트 또는 맵 리터럴:
case const [a, b]: // ...
```

## Variable {:#variable}

`var bar, String str, final int _`

변수 패턴은 일치하거나, 구조 분해된 값에 새 변수를 바인딩합니다. 
일반적으로 [구조 분해 패턴][destructure]의 일부로 발생하여, 구조 분해된 값을 캡처합니다.

변수는 패턴이 일치할 때만 도달할 수 있는 코드 영역의 범위에 있습니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (variable)"?>
```dart
switch ((1, 2)) {
  // 'var a'와 'var b'는 각각 1과 2에 바인딩되는 변수 패턴입니다.
  case (var a, var b): // ...
  // 'a'와 'b'는 case body의 범위에 속합니다.
}
```

_typed_ 변수 패턴은 일치된 값이 선언된 타입을 가지고 있는 경우에만 일치하고, 그렇지 않으면 실패합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (variable-typed)"?>
```dart
switch ((1, 2)) {
  // 일치하지 않습니다.
  case (int a, String b): // ...
}
```

[와일드카드 패턴](#wildcard)을 변수 패턴으로 사용할 수 있습니다.

## Identifier	{:#identifier}

`foo, _`

Identifier 패턴은 나타나는 컨텍스트에 따라, [상수 패턴](#constant) 또는 [변수 패턴](#variable)처럼 동작할 수 있습니다.

- [선언][Declaration] 컨텍스트: 식별자(identifier) 이름으로 새 변수를 선언합니다.
  `var (a, b) = (1, 2);`
- [할당][Assignment] 컨텍스트: 식별자 이름으로 기존 변수에 할당합니다.
  `(a, b) = (3, 4);`
- [일치][Matching] 컨텍스트: 명명된 상수 패턴으로 처리합니다. (이름이 `_`인 경우 제외):

  <?code-excerpt "language/lib/patterns/pattern_types.dart (match-context)"?>
  ```dart
  const c = 1;
  switch (2) {
    case c:
      print('match $c');
    default:
      print('no match'); // "no match" 출력.
  }
  ``` 

- 모든 컨텍스트에서 [와일드카드](#wildcard) 식별자: 모든 값과 일치하고 해당 값을 삭제합니다.
  `case [_, var y, _]: print('The middle element is $y');`

## Parenthesized {:#parenthesized}

`(subpattern)`

괄호로 묶인 표현식과 마찬가지로, 패턴의 괄호를 사용하면, 
[패턴 우선순위](#pattern-precedence)를 제어하고, 
더 높은 우선순위가 예상되는 곳에 더 낮은 우선순위의 패턴을 삽입할 수 있습니다.

예를 들어, boolean 상수 `x`, `y`, `z`가 각각 `true`, `true`, `false`와 같다고 가정해 보겠습니다. 
다음 예제는 boolean 표현식 평가와 비슷하지만, 이 예제는 패턴과 일치합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (parens)"?>
```dart
// ...
x || y => 'matches true',
x || y && z => 'matches true',
x || (y && z) => 'matches true',
// `x || y && z`는 `x || (y && z)`와 같습니다.
(x || y) && z => 'matches nothing',
// ...
```

Dart는 왼쪽에서 오른쪽으로 패턴을 일치시키기 시작합니다.

1. 첫 번째 패턴은 `x`가 `true`와 일치하므로, `true`와 일치합니다.
2. 두 번째 패턴은 `x`가 `true`와 일치하므로, `true`와 일치합니다.
3. 세 번째 패턴은 `x`가 `true`와 일치하므로, `true`와 일치합니다.
4. 네 번째 패턴 `(x || y) && z`에는, 일치 항목이 없습니다.

   * `x`는 `true`와 일치하므로, Dart는 `y`와 일치하려고 하지 않습니다.
   * `(x || y)`는 `true`와 일치하지만, `z`는 `true`와 일치하지 않습니다.
   * 따라서, 패턴 `(x || y) && z`는 `true`와 일치하지 않습니다.
   * 하위 패턴 `(x || y)`는 `false`와 일치하지 않으므로, Dart는 `z`와 일치하려고 하지 않습니다.
   * 따라서, 패턴 `(x || y) && z`는 `false`와 일치하지 않습니다.
   * 결론적으로, `(x || y) && z`는 일치 항목이 없습니다.

## List {:#list}

`[subpattern1, subpattern2]`

리스트 패턴은 [`List`][]를 구현하는 값과 일치한 다음, 
해당 하위 패턴을 리스트의 요소와 재귀적으로 일치시켜, 위치별로 구조 분해합니다.

<?code-excerpt "language/lib/patterns/switch.dart (list-pattern)"?>
```dart
const a = 'a';
const b = 'b';
switch (obj) {
  // 리스트 패턴 [a, b]는 obj가 두 개의 필드를 갖는 리스트인 경우 먼저 obj와 일치하고, 
  // 해당 필드가 상수 하위 패턴 'a' 및 'b'와 일치하는 경우입니다.
  case [a, b]:
    print('$a, $b');
}
```  

리스트 패턴은 패턴의 요소 수가 전체 리스트와 일치해야 합니다. 
그러나, [나머지 요소](#rest-element)를 플레이스 홀더로 사용하여, 
리스트의 어떤 개수의 요소에 대해서도 설명할 수 있습니다.

### 나머지 요소 {:#rest-element}

리스트 패턴에는 _하나의_ 나머지 요소(`...`)가 포함될 수 있으며, 
이를 통해 임의 길이의 리스트와 일치할 수 있습니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (rest)"?>
```dart
var [a, b, ..., c, d] = [1, 2, 3, 4, 5, 6, 7];
// "1 2 6 7" 출력. 
print('$a $b $c $d');
```

나머지 요소는 리스트의 다른 하위 패턴과 일치하지 않는 요소를, 
새 리스트으로 수집하는 하위 패턴을 가질 수도 있습니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (rest-sub)"?>
```dart
var [a, b, ...rest, c, d] = [1, 2, 3, 4, 5, 6, 7];
// "1 2 [3, 4, 5] 6 7" 출력.
print('$a $b $rest $c $d');
```

## Map {:#map}

`{"key": subpattern1, someConst: subpattern2}`

맵 패턴은 [`Map`][]을 구현하는 값과 매치한 다음, 맵의 키와 재귀적으로 매치하여 구조 분해합니다.

맵 패턴은 패턴이 전체 맵과 매치될 필요가 없습니다. 
맵 패턴은 패턴과 매치되지 않는 맵에 포함된 모든 키를 무시합니다.

## Record {:#record}

`(subpattern1, subpattern2)`

`(x: subpattern1, y: subpattern2)`

레코드 패턴은 [record][] 객체와 일치하고 해당 필드를 구조 분해합니다. 
값이 패턴과 동일한 [shape][]를 가진 레코드가 아니면, 일치가 실패합니다. 
그렇지 않으면, 필드 하위 패턴이 레코드의 해당 필드와 일치합니다.

레코드 패턴은 패턴이 전체 레코드와 일치해야 합니다. 
패턴을 사용하여 _명명된_ 필드가 있는 레코드를 구조 분해하려면, 패턴에 필드 이름을 포함합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (record)"?>
```dart
var (myString: foo, myNumber: bar) = (myString: 'string', myNumber: 1);
```

getter 이름은 생략될 수 있으며, 
필드 하위 패턴의 [변수 패턴](#variable) 또는 [식별자 패턴](#identifier)에서 추론될 수 있습니다. 
이러한 패턴 쌍은 각각 동등합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (record-getter)"?>
```dart
// 가변적인 하위 패턴을 사용한 Record 패턴:
var (untyped: untyped, typed: int typed) = record;
var (:untyped, :int typed) = record;

switch (record) {
  case (untyped: var untyped, typed: int typed): // ...
  case (:var untyped, :int typed): // ...
}

// null-check 및 null-assert 하위 패턴을 사용한 Record 패턴:
switch (record) {
  case (checked: var checked?, asserted: var asserted!): // ...
  case (:var checked?, :var asserted!): // ...
}

// 캐스트 하위 패턴이 있는 Record 패턴:
var (untyped: untyped as int, typed: typed as String) = record;
var (:untyped as int, :typed as String) = record;
```

## Object {:#object}

`SomeClass(x: subpattern1, y: subpattern2)`

Object 패턴은 객체 속성에 대한 getters를 사용하여 데이터를 구조 분해하기 위해, 
지정된 명명된 타입과 일치하는 값을 확인합니다. 
값에 동일한 타입이 없으면 [반박(refuted)][refuted]됩니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (object)"?>
```dart
switch (shape) {
  // shape이 Rect 타입인 경우 일치하고, Rect의 속성과 대조됩니다.
  case Rect(width: var w, height: var h): // ...
}
```  

getter 이름은 생략될 수 있으며, 
필드 하위 패턴의 [변수 패턴](#variable) 또는 [식별자 패턴](#identifier)에서 유추될 수 있습니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (object-getter)"?>
```dart
// 새로운 변수 x와 y를 Point의 x, y 속성 값에 바인딩합니다.
var Point(:x, :y) = Point(1, 2);
```

객체 패턴은 패턴이 전체 객체와 일치할 것을 요구하지 않습니다. 
객체에 패턴이 구조 분해하지 않는 추가 필드가 있는 경우에도, 여전히 일치할 수 있습니다.

## Wildcard {:#wildcard}

`_`

`_`라는 패턴은 [변수 패턴](#variable) 또는 [식별자 패턴](#identifier) 중 하나인 와일드카드로, 
어떤 변수에도 바인딩하거나 할당하지 않습니다.

나중에 위치 값을 구조화하기 위해, 하위 패턴이 필요한 곳에서, 플레이스홀더로 유용합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (wildcard)"?>
```dart
var list = [1, 2, 3];
var [_, two, _] = list;
```

값의 타입을 테스트하지만 값을 이름에 바인딩하지 않으려는 경우, 
타입 어노테이션이 있는 와일드카드 이름이 유용합니다.

<?code-excerpt "language/lib/patterns/pattern_types.dart (wildcard-typed)"?>
```dart
switch (record) {
  case (int _, String _):
    print('First field is int and second is String.');
}
```

[Patterns]: /language/patterns
[type cast]: /language/operators#type-test-operators
[destructure]: /language/patterns#destructuring
[throw]: /language/error-handling#throw
[Declaration]: /language/patterns#variable-declaration
[Assignment]: /language/patterns#variable-assignment
[Matching]: /language/patterns#matching
[`List`]: /language/collections#lists
[`Map`]: /language/collections#maps
[refuted]: /resources/glossary#refutable-pattern
[record]: /language/records
[shape]: /language/records#record-types
[switch]: /language/branches#switch
