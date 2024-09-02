---
# title: Functions
title: 함수
# description: Everything about functions in Dart.
description: Dart의 함수에 대한 모든 것.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
prevpage:
  url: /language/pattern-types
  # title: Pattern types
  title: 패턴 타입
nextpage:
  url: /language/loops
  # title: Loops
  title: 루프
---

Dart는 진정한 객체 지향 언어이므로, 함수도 객체이며 타입 [Function][Function API reference]이 있습니다. 
즉, 함수는 변수에 할당되거나, 다른 함수에 인수로 전달될 수 있습니다. 
Dart 클래스의 인스턴스를 함수인 것처럼 호출할 수도 있습니다. 
자세한 내용은 [호출 가능한 객체][Callable objects]를 참조하세요.

함수를 구현하는 예는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (function)"?>
```dart
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

효과적인 Dart에서는 [공개 API에 대한 타입 어노테이션][type annotations for public APIs]을 권장하지만, 
타입을 생략해도 함수는 여전히 작동합니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (function-omitting-types)"?>
```dart
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

표현식이 하나만 포함된 함수의 경우, 다음과 같은 단축 구문을 사용할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (function-shorthand)"?>
```dart
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

<code>=> <em>expr</em></code> 구문은 <code>{ return <em>expr</em>; }</code>의 약어입니다.
`=>` 표기법은 때때로 _화살표(arrow)_ 구문이라고도 합니다.

:::note
화살표(`=>`)와 세미콜론(`;`) 사이에는 _표현식_ 만 나타날 수 있습니다. 
표현식은 값으로 평가됩니다. 
즉, Dart가 값을 기대하는 명령문을 작성할 수 없습니다. 
예를 들어, [조건식][conditional expression]은 사용할 수 있지만, [if 명령문][if statement]은 사용할 수 없습니다. 
이전 예에서, `_nobleGases[atomicNumber] != null;`은 boolean 값을 반환합니다. 
그런 다음, 함수는 `atomicNumber`가 noble gas 범위에 속하는지 여부를 나타내는 boolean 값을 반환합니다.
:::

## 파라미터 {:#parameters}

함수에는 *필수 위치(required positional)* 매개변수가 개수에 제한 없이 포함될 수 있습니다. 
이 뒤에는 *이름이 지정된(named)* 매개변수 또는 *선택적 위치(optional positional)* 매개변수가 올 수 있습니다.(둘 다는 아님)

:::note
일부 API(특히 [Flutter][] 위젯 생성자)는 필수 매개변수에 대해서도, 명명된 매개변수만 사용합니다. 
자세한 내용은 다음 섹션을 참조하세요.
:::

함수에 인수를 전달하거나 함수 매개변수를 정의할 때, [끝 쉼표(trailing commas)][]를 사용할 수 있습니다.

### 명명된 매개변수 {:#named-parameters}

명명된 매개변수는 명시적으로 `required`로 표시되지 않는 한, 선택 사항입니다.

함수를 정의할 때, <code>{<em>param1</em>, <em>param2</em>, …}</code>를 사용하여 명명된 매개변수를 지정합니다. 
기본값을 제공하지 않거나 명명된 매개변수를 `required`로 표시하지 않으면, 
기본값이 `null`이 되므로 해당 타입은 null 가능해야 합니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (specify-named-parameters)"?>
```dart
/// [bold] 및 [hidden] 플래그를 설정합니다...
void enableFlags({bool? bold, bool? hidden}) {...}
```

함수를 호출할 때, <code><em>paramName</em>: <em>value</em></code>를 사용하여, 
명명된 인수를 지정할 수 있습니다. 예를 들어:

<?code-excerpt "misc/lib/language_tour/functions.dart (use-named-parameters)"?>
```dart
enableFlags(bold: true, hidden: false);
```

<a id="default-parameters"></a>
`null` 외에 명명된 매개변수에 대한 기본값을 정의하려면, `=`를 사용하여 기본값을 지정합니다. 
지정된 값은 컴파일 타임 상수여야 합니다. 예를 들어:

<?code-excerpt "misc/lib/language_tour/functions.dart (named-parameter-default-values)"?>
```dart
/// [bold] 및 [hidden] 플래그를 설정합니다...
void enableFlags({bool bold = false, bool hidden = false}) {...}

// bold는 true가 되고, hidden는 false가 됩니다.
enableFlags(bold: true);
```

대신 명명된 매개변수를 필수로 지정하여, 호출자가 매개변수에 대한 값을 제공하도록 요구하는 경우, 
`required` 어노테이션을 추가합니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (required-named-parameters)" replace="/required/[!$&!]/g"?>
```dart
const Scrollbar({super.key, [!required!] Widget child});
```

누군가 `child` 인수를 지정하지 않고 `Scrollbar`를 생성하려고 하면, 분석기가 문제를 보고합니다.

:::note
`required`로 표시된 매개변수는 여전히 null이 될 수 있습니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (required-named-parameters-nullable)" replace="/Widget\?/[!$&!]/g; /ScrollbarTwo/Scrollbar/g;"?>
```dart
const Scrollbar({super.key, required [!Widget?!] child});
```
:::

위치 인수를 먼저 배치하고 싶을 수도 있지만, Dart에서는 필요하지 않습니다. 
Dart에서는 API에 맞는 경우 명명된 인수를 인수 리스트의 아무 곳에나 배치할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/functions.dart (named-arguments-anywhere)"?>
```dart
repeat(times: 2, () {
  ...
});
```

### 선택적인 위치 매개변수 {:#optional-positional-parameters}

함수 매개변수 집합을 `[]`로 래핑하면, 선택적 위치 매개변수로 표시됩니다. 
기본값을 제공하지 않으면, 기본값이 `null`이 되므로 해당 타입은 null 가능해야 합니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (optional-positional-parameters)"?>
```dart
String say(String from, String msg, [String? device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}
```

선택적 매개변수 없이, 이 함수를 호출하는 예는 다음과 같습니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (call-without-optional-param)"?>
```dart
assert(say('Bob', 'Howdy') == 'Bob says Howdy');
```

다음은 세 번째 매개변수와 함께 이 함수를 호출하는 예입니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (call-with-optional-param)"?>
```dart
assert(say('Bob', 'Howdy', 'smoke signal') ==
    'Bob says Howdy with a smoke signal');
```

`null` 외에 선택적 위치 매개변수에 대한 기본값을 정의하려면, `=`를 사용하여 기본값을 지정합니다. 
지정된 값은 컴파일 타임 상수여야 합니다. 예를 들어:

<?code-excerpt "misc/test/language_tour/functions_test.dart (optional-positional-param-default)"?>
```dart
String say(String from, String msg, [String device = 'carrier pigeon']) {
  var result = '$from says $msg with a $device';
  return result;
}

assert(say('Bob', 'Howdy') == 'Bob says Howdy with a carrier pigeon');
```

<a id="the-main-function" aria-hidden="true"></a>

## main() 함수 {:#main}

모든 앱에는 앱의 진입점 역할을 하는, 최상위 `main()` 함수가 있어야 합니다. 
`main()` 함수는 `void`를 반환하고, 인수에 대한 선택적 `List<String>` 매개변수를 갖습니다.

간단한 `main()` 함수는 다음과 같습니다.

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dart
void main() {
  print('Hello, World!');
}
```

인수를 받는 명령줄 앱의 `main()` 함수 예는 다음과 같습니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (main-args)"?>
```dart title="args.dart"
// 다음과 같이 앱을 실행합니다: dart run args.dart 1 test
void main(List<String> arguments) {
  print(arguments);

  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
```

[args 라이브러리]({{site.pub-pkg}}/args)를 사용하여, 명령줄 인수를 정의하고 구문 분석할 수 있습니다.

## 일급 객체로서의 함수 {:#functions-as-first-class-objects}

함수를 다른 함수에 매개변수로 전달할 수 있습니다. 예를 들어:

<?code-excerpt "misc/lib/language_tour/functions.dart (function-as-param)"?>
```dart
void printElement(int element) {
  print(element);
}

var list = [1, 2, 3];

// printElement를 매개변수로 전달합니다.
list.forEach(printElement);
```

다음과 같이 변수에 함수를 할당할 수도 있습니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (function-as-var)"?>
```dart
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
assert(loudify('hello') == '!!! HELLO !!!');
```

이 예에서는 익명 함수를 사용합니다. 다음 섹션에서 이에 대해 자세히 설명합니다.

## 익명 함수 {:#anonymous-functions}

`main()` 또는 `printElement()`와 같이 대부분 함수에 이름을 지정하지만, 
이름 없이 함수를 만들 수도 있습니다. 
이러한 함수를 _익명 함수(anonymous functions)_, _람다(lambdas)_ 또는 _클로저(closures)_ 라고 합니다.

익명 함수는 다음과 같은 점에서 명명된 함수와 유사합니다.

* 0개 이상의 매개변수, 쉼표로 구분.
* 괄호 사이에 선택적 타입 어노테이션.

다음 코드 블록에는 함수의 본문이 포함되어 있습니다.

```dart
([[Type] param1[, ...]]) {
  codeBlock;
}
```

다음 예에서는 타입이 지정되지 않은 매개변수인 `item`을 갖는 익명 함수를 정의합니다. 
익명 함수는 이를 `map` 함수에 전달합니다. 
리스트의 각 항목에 대해 호출된 `map` 함수는, 각 문자열을 대문자로 변환합니다. 
그런 다음, `forEach`에 전달된 익명 함수는, 변환된 각 문자열을 길이와 함께 출력합니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (anonymous-function)"?>
```dart
const list = ['apples', 'bananas', 'oranges'];

var uppercaseList = list.map((item) {
  return item.toUpperCase();
}).toList();
// 매핑 후 리스트로 변환

for (var item in uppercaseList) {
  print('$item: ${item.length}');
}
```

**Run**을 클릭하여 코드를 실행합니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (anonymous-function-main)"?>
```dartpad
void main() {
  const list = ['apples', 'bananas', 'oranges'];

  var uppercaseList = list.map((item) {
    return item.toUpperCase();
  }).toList();
  // 매핑 후 리스트로 변환

  for (var item in uppercaseList) {
    print('$item: ${item.length}');
  }
}
```

함수에 단일 표현식 또는 반환 문만 포함된 경우, 화살표 표기법을 사용하여 줄일 수 있습니다. 
다음 줄을 DartPad에 붙여넣고 **Run**을 클릭하여, 기능적으로 동일한지 확인합니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (anon-func)"?>
```dart
var uppercaseList = list.map((item) => item.toUpperCase()).toList();
uppercaseList.forEach((item) => print('$item: ${item.length}'));
```

## 어휘 범위 (Lexical scope) {:#lexical-scope}

Dart는 코드 레이아웃에 따라 변수의 범위를 결정합니다. 
이 기능이 있는 프로그래밍 언어는 어휘 범위 언어(lexically scoped language)라고 합니다. 
"중괄호를 바깥쪽으로 따라가면" 변수가 범위에 있는지 확인할 수 있습니다.

**예제:** 각 범위 레벨에 변수가 있는 일련의 중첩 함수:

<?code-excerpt "misc/test/language_tour/functions_test.dart (nested-functions)"?>
```dart
bool topLevel = true;

void main() {
  var insideMain = true;

  void myFunction() {
    var insideFunction = true;

    void nestedFunction() {
      var insideNestedFunction = true;

      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNestedFunction);
    }
  }
}
```

`nestedFunction()` 메서드는 모든 레벨의 변수를 사용할 수 있으며, 최상위 레벨까지 올라갈 수 있습니다.

## 어휘 클로저 (Lexical closures) {:#lexical-closures}

함수가 해당 범위 밖에 있을 때 어휘 범위의 변수에 액세스할 수 있는 함수 객체를 _클로저(closure)_ 라고 합니다.

함수는 주변 범위에서 정의된 변수를 닫을 수 있습니다. 
다음 예에서 `makeAdder()`는 변수 `addBy`를 캡처합니다. 
반환된 함수가 어디로 가든 `addBy`를 기억합니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (function-closure)"?>
```dart
/// [addBy]를 함수 인수에 더하는 함수를 반환합니다.
Function makeAdder(int addBy) {
  return (int i) => addBy + i;
}

void main() {
  // 2를 더하는 함수를 만듭니다.
  var add2 = makeAdder(2);

  // 4를 더하는 함수를 만듭니다.
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```

## 떼어내기 (Tear-offs) {:#tear-offs}

괄호 없이 함수, 메서드 또는 명명된 생성자를 참조할 때 Dart는 _tear-off (떼어내기)_ 를 생성합니다. 
이것은 함수와 동일한 매개변수를 사용하고 호출할 때 기본 함수를 호출하는 클로저입니다. 
코드에 클로저가 허용하는 것과 동일한 매개변수를 사용하여 명명된 함수를 호출하는 클로저가 필요한 경우, 
람다로 호출을 래핑하지 마세요. 
tear-off를 사용하세요.

<?code-excerpt "misc/lib/language_tour/tear_offs.dart (variables)" ?>
```dart
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();
```

<?code-excerpt "misc/lib/language_tour/tear_offs.dart (good-example)" ?>
```dart tag=good
// 함수 tear-off
charCodes.forEach(print);

// 메서드 tear-off
charCodes.forEach(buffer.write);
```

<?code-excerpt "misc/lib/language_tour/tear_offs.dart (bad-example)" ?>
```dart tag=bad
// 함수 람다
charCodes.forEach((code) {
  print(code);
});

// 메서드 람다
charCodes.forEach((code) {
  buffer.write(code);
});
```

## 동등성을 위한 함수 테스트 {:#testing-functions-for-equality}

다음은 최상위 함수, 정적 메서드, 인스턴스 메서드의 동등성을 테스트하는 예입니다.

<?code-excerpt "misc/lib/language_tour/function_equality.dart"?>
```dart
void foo() {} // 최상위 함수

class A {
  static void bar() {} // 정적 메서드
  void baz() {} // 인스턴스 메서드
}

void main() {
  Function x;

  // 최상위 함수 비교.
  x = foo;
  assert(foo == x);

  // 정적 메서드 비교.
  x = A.bar;
  assert(A.bar == x);

  // 인스턴스 메서드 비교.
  var v = A(); // A의 인스턴스 #1
  var w = A(); // A의 인스턴스 #2
  var y = w;
  x = w.baz;

  // 이러한 클로저는 동일한 인스턴스(#2)를 참조하므로, 동일합니다.
  assert(y.baz == x);

  // 이러한 클로저는 서로 다른 인스턴스를 참조하므로, 동일하지 않습니다.
  assert(v.baz != w.baz);
}
```


## 값 리턴 {:#return-values}

모든 함수는 값을 반환합니다. 
반환 값이 지정되지 않으면, `return null;` 문이 함수 본문에 암묵적으로 추가됩니다.

<?code-excerpt "misc/test/language_tour/functions_test.dart (implicit-return-null)"?>
```dart
foo() {}

assert(foo() == null);
```

함수에서 여러 값을 반환하려면, [레코드][record]에 있는 값을 집계합니다.

```dart
(String, int) foo() {
  return ('something', 42);
}
```

[record]: /language/records#multiple-returns

## Generators {:#generators}

값 시퀀스를 지연(lazily) 생성해야 하는 경우, _생성기 함수(generator function)_ 를 사용하는 것을 고려하세요. 
Dart에는 두 가지 종류의 생성기 함수에 대한 빌트인 지원이 있습니다.

* **동기** 생성기: [`Iterable`] 객체를 반환합니다.
* **비동기** 생성기: [`Stream`] 객체를 반환합니다.

**동기** 생성기 함수를 구현하려면, 함수 본문을 `sync*`로 표시하고, 
`yield` 문을 사용하여 값을 전달합니다.

<?code-excerpt "misc/test/language_tour/async_test.dart (sync-generator)"?>
```dart
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}
```

**비동기** 생성기 함수를 구현하려면, 함수 본문을 `async*`로 표시하고, 
`yield` 문을 사용하여 값을 전달합니다.

<?code-excerpt "misc/test/language_tour/async_test.dart (async-generator)"?>
```dart
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}
```

생성기가 재귀적이라면, `yield*`를 사용하여 성능을 개선할 수 있습니다.

<?code-excerpt "misc/test/language_tour/async_test.dart (recursive-generator)"?>
```dart
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```


[`Iterable`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable-class.html
[`Stream`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html

## 외부 함수 {:#external}

외부 함수는 선언과 별도로 구현된 본문을 가진 함수입니다. 
다음과 같이 함수 선언 앞에 `external` 키워드를 포함합니다.

```dart
external void someFunc(int i);
```

외부 함수의 구현은 다른 Dart 라이브러리에서 제공되거나, 
보다 일반적으로 다른 언어에서 제공될 수 있습니다. 
상호 운용 컨텍스트(interop contexts)에서, `external`은 외부 함수 또는 값에 대한 타입 정보를 도입하여, 
Dart에서 사용할 수 있도록 합니다. 
구현 및 사용은 플랫폼에 따라 크게 다르므로,
자세한 내용은 예를 들어 [C][] 또는 [JavaScript][]에 대한 상호 운용 문서를 확인하세요.

외부 함수는 최상위 함수, [인스턴스 메서드][instance methods], [getters 또는 setters][getters or setters] 또는 [비리디렉션 생성자(non-redirecting constructors)][non-redirecting constructors]일 수 있습니다. 
[인스턴스 변수][instance variable]도 `external`일 수 있으며, 
이는 외부 getter 및 (변수가 `final`이 아닌 경우) 외부 setter와 동일합니다.

[instance methods]: /language/methods#instance-methods
[getters or setters]: /language/methods#getters-and-setters
[non-redirecting constructors]: /language/constructors#redirecting-constructors
[instance variable]: /language/classes#instance-variables
[C]: /interop/c-interop
[JavaScript]: /interop/js-interop

[Function API reference]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Function-class.html
[Callable objects]: /language/callable-objects
[type annotations for public APIs]: /effective-dart/design#do-type-annotate-fields-and-top-level-variables-if-the-type-isnt-obvious
[if statement]: /language/branches#if
[conditional expression]: /language/operators#conditional-expressions
[Flutter]: {{site.flutter}}
[trailing commas]: /language/collections#lists
