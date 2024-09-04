---
# title: Built-in types
title: 빌트인 타입
# description: Information on the types Dart supports.
description: Dart가 지원하는 타입에 대한 정보.
prevpage:
  url: /language/keywords
  # title: Keywords
  title: 키워드
nextpage:
  url: /language/records
  # title: Records
  title: 레코드
---

Dart 언어는 다음에 대한 특별 지원을 제공합니다.

- [숫자](#numbers) (`int`, `double`)
- [문자열](#strings) (`String`)
- [Booleans](#booleans) (`bool`)
- [레코드][Records] (`(value1, value2)`)
- [리스트][Lists] (`List`, *배열(arrays)*이라고도 함)
- [집합][Sets] (`Set`)
- [맵][Maps] (`Map`)
- [룬](#runes-and-grapheme-clusters) (`Runes`; 종종 `characters` API로 대체됨)
- [심볼](#symbols) (`Symbol`)
- 값 `null` (`Null`)

이 지원에는 리터럴을 사용하여 객체를 만드는 기능이 포함됩니다. 
예를 들어, `'this is a string'`은 문자열 리터럴이고, `true`는 boolean 리터럴입니다.

Dart의 모든 변수는 객체(*클래스*의 인스턴스)를 참조하므로, 
일반적으로 *생성자*를 사용하여 변수를 초기화할 수 있습니다. 
일부 기본 제공 타입에는 자체 생성자가 있습니다. 
예를 들어, `Map()` 생성자를 사용하여 맵을 만들 수 있습니다.

일부 다른 타입도 Dart 언어에서 특별한 역할을 합니다.

* `Object`: `Null`을 제외한 모든 Dart 클래스의 슈퍼클래스입니다.
* `Enum`: 모든 열거형의 슈퍼클래스입니다.
* `Future`와 `Stream`: [비동기 지원][asynchrony support]에서 사용됩니다.
* `Iterable`: [for-in 루프][iteration] 및 동기 [제너레이터 함수][generator functions]에서 사용됩니다.
* `Never`: 표현식이 평가를 완료할 수 없음을 나타냅니다. (never successfully finish evaluating) 항상 예외를 throw하는 함수에 가장 많이 사용됩니다.
* `dynamic`: 정적 검사를 비활성화하려는 것을 나타냅니다. 일반적으로 대신 `Object` 또는 `Object?`를 사용해야 합니다.
* `void`: 값이 사용되지 않음을 나타냅니다. 종종 반환 타입으로 사용됩니다.

`Object`, `Object?`, `Null` 및 `Never` 클래스는 클래스 계층에서 특별한 역할을 합니다. 
[null 세이프티 이해][Understanding null safety]에서 이러한 역할에 대해 알아보세요.

{% comment %}
If we decide to cover `dynamic` more,
here's a nice example that illustrates what dynamic does:
  dynamic a = 2;
  String b = a; // No problem! Until runtime, when you get an uncaught error.

  Object c = 2;
  String d = c;  // Problem!
{% endcomment %}

## Numbers {:#numbers}

Dart 숫자에는 두 가지 플레이버가 있습니다.

[`int`][]

:   64비트보다 크지 않은 정수 값, [플랫폼에 따라 다릅니다][dart-numbers]. 
    네이티브 플랫폼에서, 값은 -2<sup>63</sup>에서 2<sup>63</sup> - 1까지 가능합니다. 
    웹에서, 정수 값은 JavaScript 숫자(소수점 부분이 없는 64비트 부동 소수점 값)로 표현되며, 
    -2<sup>53</sup>에서 2<sup>53</sup> - 1까지 가능합니다.

[`double`][]

:   IEEE 754 표준에서 지정한 64비트(double-precision) 부동 소수점 숫자입니다.

`int`와 `double`은 모두 [`num`][]의 하위 타입입니다. 
num 타입에는 +, -, /, \*와 같은 기본 연산자가 포함되며, 
`abs()`, `ceil()`, `floor()` 등의 메서드도 있습니다. 
(비트 연산자, 예를 들어 \>\>는 `int` 클래스에 정의되어 있습니다.) 
num과 하위 타입에서 원하는 것을 찾을 수 없다면, [dart:math][] 라이브러리가 있을 수 있습니다.

Integers는 소수점이 없는 숫자입니다. 정수 리터럴을 정의하는 몇 가지 예는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (integer-literals)"?>
```dart
var x = 1;
var hex = 0xDEADBEEF;
```

숫자에 소수점이 포함되어 있으면 double입니다. double 리터럴을 정의하는 몇 가지 예는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (double-literals)"?>
```dart
var y = 1.1;
var exponents = 1.42e5;
```

변수를 num으로 선언할 수도 있습니다. 
이렇게 하면, 변수는 정수와 double 값을 모두 가질 수 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (declare-num)"?>
```dart
num x = 1; // x는 int와 double 값을 모두 가질 수 있습니다.
x += 2.5;
```

필요한 경우 정수 리터럴은 자동으로 double로 변환됩니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (int-to-double)"?>
```dart
double z = 1; // double z = 1.0과 같습니다.
```

문자열을 숫자로 바꾸거나, 반대로 문자열을 숫자로 바꾸는 방법은 다음과 같습니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (number-conversion)"?>
```dart
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');
```

`int` 타입은 전통적인 비트 단위 시프트(`<<`, `>>`, `>>>`), 보수(`~`), AND(`&`), OR(`|`), XOR(`^`) 연산자를 지정하는데, 이는 비트 필드에서 플래그를 조작하고 마스크하는 데 유용합니다. 예를 들어:

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (bit-shifting)"?>
```dart
assert((3 << 1) == 6); // 0011 << 1 == 0110
assert((3 | 4) == 7); // 0011 | 0100 == 0111
assert((3 & 4) == 0); // 0011 & 0100 == 0000
```

더 많은 예를 보려면, [비트별 및 시프트 연산자][bitwise and shift operator] 섹션을 참조하세요.

리터럴 숫자는 컴파일 타임 상수입니다. 
많은 산술 표현식도, 피연산자가 숫자로 평가되는 컴파일 타임 상수인 한, 컴파일 타임 상수입니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-num)"?>
```dart
const msPerSecond = 1000;
const secondsUntilRetry = 5;
const msUntilRetry = secondsUntilRetry * msPerSecond;
```

자세한 내용은 [Dart의 숫자][dart-numbers]를 참조하세요.

## Strings {:#strings}

Dart 문자열(`String` 객체)은 UTF-16 코드 단위의 시퀀스를 보유합니다. 
작은따옴표나 큰따옴표를 사용하여 문자열을 만들 수 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (quoting)"?>
```dart
var s1 = 'Single quotes work well for string literals.';
var s2 = "Double quotes work just as well.";
var s3 = 'It\'s easy to escape the string delimiter.';
var s4 = "It's even easier to use the other delimiter.";
```

<a id="string-interpolation"></a>

`${`*`expression`*`}`를 사용하여, 표현식의 값을 문자열 안에 넣을 수 있습니다. 
표현식이 식별자인 경우, `{}`를 건너뛸 수 있습니다. 
객체에 해당하는 문자열을 가져오기 위해, Dart는 객체의 `toString()` 메서드를 호출합니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (string-interpolation)"?>
```dart
var s = 'string interpolation';

assert('Dart has $s, which is very handy.' ==
    'Dart has string interpolation, '
        'which is very handy.');
assert('That deserves all caps. '
        '${s.toUpperCase()} is very handy!' ==
    'That deserves all caps. '
        'STRING INTERPOLATION is very handy!');
```

:::note
`==` 연산자는 두 객체가 동일한지 테스트합니다. 
두 문자열은 동일한 코드 단위 시퀀스를 포함하는 경우, 동일합니다.
:::

인접 문자열 리터럴이나, `+` 연산자를 사용하여, 문자열을 연결할 수 있습니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (adjacent-string-literals)"?>
```dart
var s1 = 'String '
    'concatenation'
    " works even over line breaks.";
assert(s1 ==
    'String concatenation works even over '
        'line breaks.');

var s2 = 'The + operator ' + 'works, as well.';
assert(s2 == 'The + operator works, as well.');
```

여러 줄로 된 문자열을 만들려면, 작은 따옴표나 큰 따옴표와 함께 삼중 따옴표를 사용합니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (triple-quotes)"?>
```dart
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";
```

`r` 접두사를 붙여 "raw" 문자열을 생성할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (raw-strings)"?>
```dart
var s = r'In a raw string, not even \n gets special treatment.';
```

문자열에서 유니코드 문자를 표현하는 방법에 대한 자세한 내용은, 
[룬(Runes) 및 문자소 클러스터(grapheme clusters)](#runes-and-grapheme-clusters)를 참조하세요.

리터럴 문자열은, 보간된 표현식이 null 또는 숫자, 문자열 또는 boolean 값으로 평가되는 컴파일 타임 상수인 한, 
컴파일 타임 상수입니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (string-literals)"?>
```dart
// 이것들은 const 문자열에서 작동합니다.
const aConstNum = 0;
const aConstBool = true;
const aConstString = 'a constant string';

// 이것은 const 문자열에서는 작동하지 않습니다.
var aNum = 0;
var aBool = true;
var aString = 'a string';
const aConstList = [1, 2, 3];

const validConstString = '$aConstNum $aConstBool $aConstString';
// const invalidConstString = '$aNum $aBool $aString $aConstList';
```

문자열 사용에 대한 자세한 내용은, [문자열 및 정규 표현식](/libraries/dart-core#strings-and-regular-expressions)을 확인하세요.

## Booleans {:#booleans}

boolean 값을 나타내기 위해, Dart에는 `bool`이라는 타입이 있습니다. 
두 개의 객체만 bool 타입을 갖습니다. 
boolean 리터럴 `true`와 `false`로, 둘 다 컴파일 타임 상수입니다.

Dart의 타입 안전성은 <code>if (<em>nonbooleanValue</em>)</code> 또는 <code>assert (<em>nonbooleanValue</em>)</code>와 같은 코드를 사용할 수 없다는 것을 의미합니다. 
대신, 다음과 같이 값을 명시적으로 확인합니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (no-truthy)"?>
```dart
// 빈 문자열을 확인합니다.
var fullName = '';
assert(fullName.isEmpty);

// 0인지 확인합니다.
var hitPoints = 0;
assert(hitPoints == 0);

// null인지 확인합니다.
var unicorn = null;
assert(unicorn == null);

// NaN을 확인합니다.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```

## 룬(Runes)과 문자소 클러스터(grapheme clusters) {:#runes-and-grapheme-clusters}

Dart에서, [룬(Runes)][runes]는 문자열의 유니코드 코드 포인트를 노출합니다. 
[characters 패키지][characters package]를 사용하면 사용자가 인식하는 문자, 
즉 [유니코드(확장) 문자소 클러스터][grapheme clusters]를 보거나 조작할 수 있습니다.

유니코드는 전 세계 모든 문자 체계에서 사용되는 각 문자, 숫자 및 기호에 대해 고유한 숫자 값을 정의합니다. 
Dart 문자열은 UTF-16 코드 단위의 시퀀스이므로, 
문자열 내에서 유니코드 코드 포인트를 표현하려면 특수 구문이 필요합니다. 
유니코드 코드 포인트를 표현하는 일반적인 방법은 `\uXXXX`이며, 여기서 XXXX는 4자리 16진수 값입니다. 
예를 들어, 하트 문자(♥)는 `\u2665`입니다. 
4자리보다 많거나 적은 16진수 숫자를 지정하려면, 값을 중괄호로 묶습니다. 
예를 들어, 웃는 이모티콘(😆)은 `\u{1f606}`입니다.

개별 유니코드 문자를 읽거나 써야 하는 경우, 
characters 패키지에서 String에 정의된 `characters` getter를 사용합니다. 
반환된 [`Characters`][] 객체는 문자소 클러스터의 시퀀스인 문자열입니다. 
다음은 characters API를 사용하는 예입니다.

<?code-excerpt "misc/lib/language_tour/characters.dart"?>
```dart
import 'package:characters/characters.dart';

void main() {
  var hi = 'Hi 🇩🇰';
  print(hi);
  print('The end of the string: ${hi.substring(hi.length - 1)}');
  print('The last character: ${hi.characters.last}');
}
```

출력은 환경에 따라 다음과 같습니다.

```console
$ dart run bin/main.dart
Hi 🇩🇰
The end of the string: ???
The last character: 🇩🇰
```

문자열을 조작하기 위해 문자 패키지를 사용하는 방법에 대한 자세한 내용은, 
문자 패키지의 [예제][characters example] 및 [API 참조][characters API]를 참조하세요.

## Symbols {:#symbols}

[`Symbol`][] 객체는 Dart 프로그램에서 선언된 연산자 또는 식별자를 나타냅니다. 
심볼을 사용할 필요가 없을 수도 있지만, 이름으로 식별자를 참조하는 API의 경우 매우 중요합니다. 
최소화는 식별자 이름은 변경하지만, 식별자 심볼은 변경하지 않기 때문입니다.

식별자의 심볼을 가져오려면, 심볼 리터럴을 사용합니다. 심볼 리터럴은 `#` 뒤에 식별자가 오는 것입니다.

```plaintext
#radix
#bar
```

{% comment %}
The code from the following excerpt isn't actually what is being shown in the page

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (symbols)"?>
```dart
void main() {
  print(Function.apply(int.parse, ['11']));
  print(Function.apply(int.parse, ['11'], {#radix: 16}));
}
```
{% endcomment %}

심볼 리터럴은 컴파일 타임 상수입니다.

[Records]: /language/records
[Lists]: /language/collections#lists
[Sets]: /language/collections#sets
[Maps]: /language/collections#maps
[asynchrony support]: /language/async
[iteration]: /libraries/dart-core#iteration
[generator functions]: /language/functions#generators
[Understanding null safety]: /null-safety/understanding-null-safety#top-and-bottom
[`int`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/int-class.html
[`double`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/double-class.html
[`num`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/num-class.html
[dart:math]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-math
[bitwise and shift operator]: /language/operators#bitwise-and-shift-operators
[dart-numbers]: /guides/language/numbers
[runes]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Runes-class.html
[characters package]: {{site.pub-pkg}}/characters
[grapheme clusters]: https://unicode.org/reports/tr29/#Grapheme_Cluster_Boundaries
[`Characters`]: {{site.pub-api}}/characters/latest/characters/Characters-class.html
[characters API]: {{site.pub-api}}/characters
[characters example]: {{site.pub-pkg}}/characters/example
[`Symbol`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Symbol-class.html
