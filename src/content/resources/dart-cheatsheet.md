---
# title: Dart cheatsheet
title: Dart 치트시트
# description: Interactively learn (or relearn) some of Dart's unique features.
description: Dart의 고유한 기능 중 일부를 대화형으로 학습(또는 다시 학습)해 보세요.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>

Dart 언어는 다른 언어에서 온 코더가 배우기 쉽도록 설계되었지만, 몇 가지 고유한 기능이 있습니다. 
이 튜토리얼은 이러한 언어 기능 중 가장 중요한 기능을 안내합니다.

이 튜토리얼의 내장된 편집기는 부분적으로 완성된 코드 스니펫을 가지고 있습니다. 
이러한 편집기를 사용하여 코드를 완성하고, **Run** 버튼을 클릭하여 지식을 테스트할 수 있습니다. 
편집기에는 철저한 테스트 코드도 포함되어 있습니다. **테스트 코드를 편집하지 마세요**. 
하지만, 테스트에 대해 배우기 위해 자유롭게 공부하세요.

도움이 필요하면, 각 DartPad 아래의 **...에 대한 솔루션** 드롭다운을 확장하여, 설명과 답을 확인하세요.

:::note
이 페이지에서는 실행 가능한 예제를 표시하기 위해 내장된 DartPad를 사용합니다.
{% render 'dartpads-embedded-troubleshooting.md' %}
:::

## 문자열 보간 {:#string-interpolation}

표현식의 값을 문자열 안에 넣으려면, `${expression}`을 사용합니다. 
표현식이 식별자인 경우 `{}`를 생략할 수 있습니다.

다음은 문자열 보간을 사용하는 몇 가지 예입니다.

| 문자열                      | 결과                             |
|-----------------------------|------------------------------------|
| `'${3 + 2}'`                | `'5'`                              |
| `'${"word".toUpperCase()}'` | `'WORD'`                           |
| `'$myObject'`               | The value of `myObject.toString()` |

### Code example {:.no_toc}

다음 함수는 두 개의 정수를 매개변수로 사용합니다. 
두 정수를 공백으로 구분하여 포함하는 문자열을 반환하도록 합니다. 
예를 들어, `stringify(2, 3)`는 `'2 3'`을 반환해야 합니다.

```dartpad
String stringify(int x, int y) {
  TODO('Return a formatted string here');
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!): 
void main() {
  assert(stringify(2, 3) == '2 3',
      "Your stringify method returned '${stringify(2, 3)}' instead of '2 3'");
  print('Success!');
}
```

<details>
  <summary>문자열 보간 예제에 대한 솔루션</summary>

  `x`와 `y`는 모두 간단한 값이고, 
  Dart의 문자열 보간은 이를 문자열 표현으로 변환합니다. 
  `$` 연산자를 사용하여 작은 따옴표 안에 공백을 두고 참조하기만 하면 됩니다.

  ```dart
  String stringify(int x, int y) {
    return '$x $y';
  }
  ```

</details>


## Nullable 변수 {:#nullable-variables}

Dart는 sound null 안전성을 적용합니다. 
즉, 값은 null이 될 수 있다고 말하지 않는 한, null이 될 수 없습니다. 
즉, 타입은 기본적으로 null이 허용되지 않습니다.

예를 들어 다음 코드를 고려해 보세요. 
null 안전성이 있는 경우, 이 코드는 오류를 반환합니다. 
`int` 타입의 변수는 `null` 값을 가질 수 없습니다.

<?code-excerpt "misc/bin/cheatsheet/nullable.dart (invalid-null)" replace="/null;/[!null!];/g"?>
```dart
int a = [!null!]; // 유효하지 않음.
```

변수를 생성할 때, 변수가 `null`이 될 수 있음을 나타내기 위해, 타입에 `?`를 추가합니다.

<?code-excerpt "misc/bin/cheatsheet/nullable.dart (valid-null)" replace="/int\?/[!int?!]/g"?>
```dart
[!int?!] a = null; // 유효함.
```

모든 버전의 Dart에서, 
초기화되지 않은 변수의 기본값은 `null`이므로, 
해당 코드를 약간 단순화할 수 있습니다.

<?code-excerpt "misc/bin/cheatsheet/nullable.dart (simple-null)"?>
```dart
int? a; // a의 초기값은 null입니다.
```

Dart의 null 안전성에 대해 자세히 알아보려면, [sound null 안전성 가이드](/null-safety)를 읽어보세요.

### Code example {:.no_toc}

이 DartPad에서 두 개의 변수를 선언합니다.

* `'Jane'` 값을 가진 `name`이라는 nullable `String`.
* `null` 값을 가진 `address`라는 nullable `String`.

DartPad의 모든 초기 오류를 무시합니다.

```dartpad
// TODO: Declare the two variables here


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!): 
void main() {
  try {
    if (name == 'Jane' && address == null) {
      // verify that "name" is nullable
      name = null;
      print('Success!');
    } else {
      print('Not quite right, try again!');
    }
  } catch (e) {
    print('Exception: ${e.runtimeType}');
  }
}
```

<details>
  <summary>nullable 변수에 대한 솔루션 예</summary>

  두 변수를 `String` 뒤에 `?`로 선언합니다. 
  그런 다음 `'Jane'`을 `name`에 할당하고, 
  `address`는 초기화하지 않은 상태로 둡니다.

  ```dart
  String? name = 'Jane';
  String? address;
  ```

</details>

## Null 인식 연산자 {:#null-aware-operators}

Dart는 null일 수 있는 값을 처리하기 위한 몇 가지 편리한 연산자를 제공합니다. 
하나는 `??=` 할당 연산자로, 해당 변수가 현재 null인 경우에만 변수에 값을 할당합니다.

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (null-aware-operators)"?>
```dart
int? a; // = null
a ??= 3;
print(a); // <-- 3 출력.

a ??= 5;
print(a); // <-- 여전히 3 출력.
```

또 다른 null 인식 연산자는 `??`로, 
표현식의 값이 null인 경우 왼쪽의 표현식을 반환하고, 
null인 경우, 오른쪽의 표현식을 평가하여 반환합니다.

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (null-aware-operators-2)"?>
```dart
print(1 ?? 3); // <-- 1 출력.
print(null ?? 12); // <-- 12 출력.
```

### Code example {:.no_toc}

다음 스니펫에 설명된 동작을 구현하려면, `??=` 및 `??` 연산자를 대체해 보세요.

DartPad의 모든 초기 오류를 무시합니다.

```dartpad
String? foo = 'a string';
String? bar; // = null

// baz에 'a string'을 할당하도록 연산자를 대체하세요.
String? baz = foo /* TODO */ bar;

void updateSomeVars() {
  // 'a string'을 bar에 할당하도록 연산자를 대체하세요.
  bar /* TODO */ 'a string';
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  try {
    updateSomeVars();
    
    if (foo != 'a string') {
      print('Looks like foo somehow ended up with the wrong value.');
    } else if (bar != 'a string') {
      print('Looks like bar ended up with the wrong value.');
    } else if (baz != 'a string') {
      print('Looks like baz ended up with the wrong value.');
    } else {
      print('Success!');
    }
  } catch (e) {
    print('Exception: ${e.runtimeType}.');
  }
  
}
```

<details>
  <summary>null 인식 연산자에 대한 솔루션 예</summary>

  이 연습에서 해야 할 일은 `TODO` 주석을 `??` 또는 `??=`로 바꾸는 것뿐입니다. 
  위의 텍스트를 읽고 둘 다 이해했는지 확인한 다음 시도해 보세요.

  ```dart
  // baz에 'a string'을 할당하도록 연산자를 대체하세요.
  String? baz = foo ?? bar;
  
  void updateSomeVars() {
    // 'a string'을 bar에 할당하도록 연산자를 대체하세요.
    bar ??= 'a string';
  }
  ```

</details>


## 조건부 속성 액세스 {:#conditional-property-access}

null일 수 있는 개체의 속성이나 메서드에 대한 액세스를 보호하려면, 
마침표(`.`) 앞에 물음표(`?`)를 넣습니다.

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (conditional-property-access)" replace="/result = //g; /;//g;"?>
```dart
myObject?.someProperty
```

앞의 코드는 다음 코드와 동일합니다.

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (conditional-property-access-equivalent)" replace="/result = //g; /;//g;"?>
```dart
(myObject != null) ? myObject.someProperty : null
```

단일 표현식에서 `?.`를 여러번 체인으로 연결할 수 있습니다.

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (conditional-property-access-multiple)" replace="/result = //g; /;//g;"?>
```dart
myObject?.someProperty?.someMethod()
```

앞의 코드는 `myObject` 또는 `myObject.someProperty`가 null인 경우 null을 반환하고, 
`someMethod()`를 호출하지 않습니다.


### Code example {:.no_toc}

다음 함수는 nullable 문자열을 매개변수로 사용합니다. 
조건부 속성 액세스를 사용하여, `str`의 대문자 버전을 반환하거나, `str`이 `null`이면 `null`을 반환해 보세요.

```dartpad
String? upperCaseIt(String? str) {
  // TODO: 여기서 `toUpperCase` 메서드에 조건부로 접근해 보세요.
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  try {
    String? one = upperCaseIt(null);
    if (one != null) {
      print('Looks like you\'re not returning null for null inputs.');
    } else {
      print('Success when str is null!');
    }
  } catch (e) {
    print('Tried calling upperCaseIt(null) and got an exception: \n ${e.runtimeType}.');
  }
  
  try {
    String? two = upperCaseIt('a string');
    if (two == null) {
      print('Looks like you\'re returning null even when str has a value.');
    } else if (two != 'A STRING') {
      print('Tried upperCaseIt(\'a string\'), but didn\'t get \'A STRING\' in response.');
    } else {
      print('Success when str is not null!');
    }
  } catch (e) {
    print('Tried calling upperCaseIt(\'a string\') and got an exception: \n ${e.runtimeType}.');
  }
}
```

<details>
  <summary>조건부 속성 액세스 예제에 대한 솔루션</summary>

  이 연습에서 문자열을 조건부로 소문자로 바꾸려면 다음과 같이 할 수 있습니다. 
  `str?.toLowerCase()`. 
  문자열을 대문자로 바꾸려면 동일한 메서드를 사용하세요!

  ```dart
  String? upperCaseIt(String? str) {
    return str?.toUpperCase();
  }
  ```

</details>

## 컬렉션 리터럴 {:#collection-literals}

Dart에는 리스트, 맵 및 세트에 대한 빌트인 지원이 있습니다. 리터럴을 사용하여 만들 수 있습니다.

<?code-excerpt "misc/test/cheatsheet/collections_test.dart (collection-literals-inferred)"?>
```dart
final aListOfStrings = ['one', 'two', 'three'];
final aSetOfStrings = {'one', 'two', 'three'};
final aMapOfStringsToInts = {
  'one': 1,
  'two': 2,
  'three': 3,
};
```

Dart의 타입 추론은 이러한 변수에 타입을 할당할 수 있습니다. 
이 경우 추론된 타입은 `List<String>`, `Set<String>`, `Map<String, int>`입니다.

또는 직접 타입을 지정할 수 있습니다.

<?code-excerpt "misc/test/cheatsheet/collections_test.dart (collection-literals-specified)"?>
```dart
final aListOfInts = <int>[];
final aSetOfInts = <int>{};
final aMapOfIntToDouble = <int, double>{};
```

하위 타입의 내용으로 리스트를 초기화하지만, 
여전히 리스트를 `List<BaseType>`로 유지하고 싶은 경우, 
타입을 지정하는 것이 편리합니다.

<?code-excerpt "misc/test/cheatsheet/collections_test.dart (collection-literals-subtypes)"?>
```dart
final aListOfBaseType = <BaseType>[SubType(), SubType()];
```

### Code example {:.no_toc}

다음 변수를 표시된 값으로 설정해 보세요. 기존 null 값을 대체합니다.

```dartpad
// 여기에 'a', 'b', 'c'를 순서대로 포함하는 리스트를 할당하세요.
final aListOfStrings = null;

// 여기에 3, 4, 5를 포함하는 세트를 할당하세요.
final aSetOfInts = null;

// String to int의 맵을 지정하여, aMapOfStringsToInts['myKey']가 12를 반환하도록 합니다.
final aMapOfStringsToInts = null;

// 여기에 비어있는 List<double>을 할당하세요.
final anEmptyListOfDouble = null;

// 여기에 비어있는 Set<String>을 할당하세요.
final anEmptySetOfString = null;

// 여기에 비어있는 double to int의 Map을 할당합니다.
final anEmptyMapOfDoublesToInts = null;


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final errs = <String>[];
  
  if (aListOfStrings is! List<String>) {
    errs.add('aListOfStrings should have the type List<String>.');
  } else if (aListOfStrings.length != 3) {
    errs.add('aListOfStrings has ${aListOfStrings.length} items in it, \n rather than the expected 3.');
  } else if (aListOfStrings[0] != 'a' || aListOfStrings[1] != 'b' || aListOfStrings[2] != 'c') {
    errs.add('aListOfStrings doesn\'t contain the correct values (\'a\', \'b\', \'c\').');
  }

  if (aSetOfInts is! Set<int>) {
    errs.add('aSetOfInts should have the type Set<int>.');
  } else if (aSetOfInts.length != 3) {
    errs.add('aSetOfInts has ${aSetOfInts.length} items in it, \n rather than the expected 3.');
  } else if (!aSetOfInts.contains(3) || !aSetOfInts.contains(4) || !aSetOfInts.contains(5)) {
    errs.add('aSetOfInts doesn\'t contain the correct values (3, 4, 5).');
  }

  if (aMapOfStringsToInts is! Map<String, int>) {
    errs.add('aMapOfStringsToInts should have the type Map<String, int>.');
  } else if (aMapOfStringsToInts['myKey'] != 12) {
    errs.add('aMapOfStringsToInts doesn\'t contain the correct values (\'myKey\': 12).');
  }

  if (anEmptyListOfDouble is! List<double>) {
    errs.add('anEmptyListOfDouble should have the type List<double>.');
  } else if (anEmptyListOfDouble.isNotEmpty) {
    errs.add('anEmptyListOfDouble should be empty.');
  }

  if (anEmptySetOfString is! Set<String>) {
    errs.add('anEmptySetOfString should have the type Set<String>.');
  } else if (anEmptySetOfString.isNotEmpty) {
    errs.add('anEmptySetOfString should be empty.');
  }

  if (anEmptyMapOfDoublesToInts is! Map<double, int>) {
    errs.add('anEmptyMapOfDoublesToInts should have the type Map<double, int>.');
  } else if (anEmptyMapOfDoublesToInts.isNotEmpty) {
    errs.add('anEmptyMapOfDoublesToInts should be empty.');
  }

  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }

  // ignore_for_file: unnecessary_type_check
}
```

<details>
  <summary>컬렉션 리터럴 예제에 대한 솔루션</summary>

  각 등호 뒤에 리스트, 세트 또는 맵 리터럴을 추가합니다. 
  비어있는 선언은 추론할 수 없으므로, 타입을 지정하는 것을 잊지 마세요.
  Add a list, set, or map literal after each equals sign. Remember to specify the types for the empty declarations, since they can't be inferred.

  ```dart
  // 여기에 'a', 'b', 'c'를 순서대로 포함하는 리스트를 할당하세요.
  final aListOfStrings = ['a', 'b', 'c'];

  // 여기에 3, 4, 5를 포함하는 세트를 할당하세요.
  final aSetOfInts = {3, 4, 5};

  // String to int의 맵을 지정하여, aMapOfStringsToInts['myKey']가 12를 반환하도록 합니다.
  final aMapOfStringsToInts = {'myKey': 12};

  // 여기에 비어있는 List<double>을 할당하세요.
  final anEmptyListOfDouble = <double>[];

  // 여기에 비어있는 Set<String>을 할당하세요.
  final anEmptySetOfString = <String>{};

  // 여기에 비어있는 double to int의 Map을 할당합니다.
  final anEmptyMapOfDoublesToInts = <double, int>{};
  ```

</details>

## 화살표 구문 {:#arrow-syntax}

Dart 코드에서 `=>` 기호를 본 적이 있을 것입니다. 
이 화살표 구문은 오른쪽에 있는 표현식을 실행하고, 값을 반환하는 함수를 정의하는 방법입니다.

예를 들어, `List` 클래스의 `any()` 메서드에 대한 이 호출을 고려해 보세요.

<?code-excerpt "misc/test/cheatsheet/arrow_functions_test.dart (has-empty-long)"?>
```dart
bool hasEmpty = aListOfStrings.any((s) {
  return s.isEmpty;
});
```

해당 코드를 작성하는 더 간단한 방법은 다음과 같습니다.

<?code-excerpt "misc/test/cheatsheet/arrow_functions_test.dart (has-empty-short)"?>
```dart
bool hasEmpty = aListOfStrings.any((s) => s.isEmpty);
```

### Code example {:.no_toc}

화살표 구문을 사용하는 다음 문장을 완성해 보세요.

```dartpad
class MyClass {
  int value1 = 2;
  int value2 = 3;
  int value3 = 5;
  
  // 위 값들의 곱을 반환합니다.
  int get product => TODO();
  
  // value1에 1을 더합니다:
  void incrementValue1() => TODO();
  
  // 리스트의 각 항목을 쉼표로 구분하여, 포함하는 문자열을 반환합니다. (예: 'a,b,c'):
  String joinWithCommas(List<String> strings) => TODO();
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final obj = MyClass();
  final errs = <String>[];
  
  try {
    final product = obj.product;
    
    if (product != 30) {
      errs.add('The product property returned $product \n instead of the expected value (30).'); 
    } 
  } catch (e) {
    print('Tried to use MyClass.product, but encountered an exception: \n ${e.runtimeType}.');
    return;
  }

  try {
    obj.incrementValue1();
    
    if (obj.value1 != 3) {
      errs.add('After calling incrementValue, value1 was ${obj.value1} \n instead of the expected value (3).'); 
    } 
  } catch (e) {
    print('Tried to use MyClass.incrementValue1, but encountered an exception: \n ${e.runtimeType}.');
    return;
  }

  try {
    final joined = obj.joinWithCommas(['one', 'two', 'three']);
    
    if (joined != 'one,two,three') {
      errs.add('Tried calling joinWithCommas([\'one\', \'two\', \'three\']) \n and received $joined instead of the expected value (\'one,two,three\').'); 
    } 
  } catch (e) {
    print('Tried to use MyClass.joinWithCommas, but encountered an exception: \n ${e.runtimeType}.');
    return;
  }

  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>화살표 구문 예제에 대한 솔루션</summary>

  곱의 경우, `*`를 사용하여 세 값을 함께 곱할 수 있습니다.
  `incrementValue1`의 경우, 증가 연산자(`++`)를 사용할 수 있습니다.
  `joinWithCommas`의 경우, `List` 클래스에서 찾을 수 있는 `join` 메서드를 사용합니다.

  ```dart
  class MyClass {
    int value1 = 2;
    int value2 = 3;
    int value3 = 5;

    // 위 값들의 곱을 반환합니다.
    int get product => value1 * value2 * value3;
    
    // value1에 1을 더합니다:
    void incrementValue1() => value1++; 
    
    // 리스트의 각 항목을 쉼표로 구분하여, 포함하는 문자열을 반환합니다. (예: 'a,b,c'):
    String joinWithCommas(List<String> strings) => strings.join(',');
  }
  ```
</details>


## 캐스케이드 (cascades) {:#cascades}

동일한 객체에 대한 일련의 연산을 수행하려면, 캐스케이드(`..`)를 사용합니다.
우리는 모두 다음과 같은 표현식을 보았습니다.

<?code-excerpt "misc/bin/cheatsheet/cascades.dart (no-cascade)" replace="/;//g"?>
```dart
myObject.someMethod()
```

`myObject`에서 `someMethod()`를 호출하고, 표현식의 결과는 `someMethod()`의 반환 값입니다.

다음은 캐스케이드가 있는 동일한 표현식입니다.

<?code-excerpt "misc/bin/cheatsheet/cascades.dart (uses-cascade)" replace="/;//g"?>
```dart
myObject..someMethod()
```

`myObject`에서 `someMethod()`를 여전히 호출하지만, 
표현식의 결과는 반환 값이 **아니라** `myObject`에 대한 참조입니다!

캐스케이드를 사용하면, 별도의 명령문이 필요한 작업을 함께 연결할 수 있습니다. 
예를 들어, `button`이 `null`이 아닌 경우, 
`?.` 조건부 멤버 액세스 연산자를 사용하여 `button`의 속성을 읽는 다음 코드를 고려하세요.

<?code-excerpt "misc/bin/cheatsheet/cascades.dart (query-without-cascades)"?>
```dart
var button = querySelector('#confirm');
button?.text = 'Confirm';
button?.classes.add('important');
button?.onClick.listen((e) => window.alert('Confirmed!'));
button?.scrollIntoView();
```

대신 캐스케이드를 사용하려면, _null-shorting_ 캐스케이드(`?..`)로 시작할 수 있습니다. 
이는 `null` 객체에 캐스케이드 연산이 시도되지 않도록 보장합니다. 
캐스케이드를 사용하면 코드가 단축되고, `button` 변수가 필요 없게 됩니다.

<?code-excerpt "misc/bin/cheatsheet/cascades.dart (query-with-cascades)"?>
```dart
querySelector('#confirm')
  ?..text = 'Confirm'
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'))
  ..scrollIntoView();
```

### Code example {:.no_toc}

캐스케이드를 사용하여 `BigObject`의 `anInt`, `aString`, `aList` 속성을, 
각각 `1`, `'String!'`, `[3.0]`으로 설정한 다음, 
`allDone()`을 호출하는 단일 명령문을 만듭니다.

```dartpad
class BigObject {
  int anInt = 0;
  String aString = '';
  List<double> aList = [];
  bool _done = false;
  
  void allDone() {
    _done = true;
  }
}

BigObject fillBigObject(BigObject obj) {
  // obj를 업데이트하고 반환하는 단일 명령문을 만듭니다.
  return TODO('obj..');
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  BigObject obj;

  try {
    obj = fillBigObject(BigObject());
  } catch (e) {
    print('Caught an exception of type ${e.runtimeType} \n while running fillBigObject');
    return;
  }

  final errs = <String>[];

  if (obj.anInt != 1) {
    errs.add(
        'The value of anInt was ${obj.anInt} \n rather than the expected (1).');
  }

  if (obj.aString != 'String!') {
    errs.add(
        'The value of aString was \'${obj.aString}\' \n rather than the expected (\'String!\').');
  }

  if (obj.aList.length != 1) {
    errs.add(
        'The length of aList was ${obj.aList.length} \n rather than the expected value (1).');
  } else {
    if (obj.aList[0] != 3.0) {
      errs.add(
          'The value found in aList was ${obj.aList[0]} \n rather than the expected (3.0).');
    }
  }
  
  if (!obj._done) {
    errs.add('It looks like allDone() wasn\'t called.');
  }

  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>캐스케이드 예제에 대한 솔루션</summary>

  이 연습에 대한 최상의 솔루션은 `obj..`로 시작하고, 
  4개의 할당 연산이 함께 연결됩니다. 
  `return obj..anInt = 1`로 시작한 다음, 
  또다른 캐스케이드(`..`)를 추가하고 다음 할당을 시작합니다.

  ```dart
  BigObject fillBigObject(BigObject obj) {
    return obj
      ..anInt = 1
      ..aString = 'String!'
      ..aList.add(3)
      ..allDone();
  }
  ```
</details>


## Getters 및 setters {:#getters-and-setters}

간단한 필드에서 허용하는 것보다 속성을 더 많이 제어해야 할 때마다 getter와 setter를 정의할 수 있습니다.

예를 들어, 속성 값이 유효한지 확인할 수 있습니다.

<?code-excerpt "misc/lib/cheatsheet/getters_setters.dart"?>
```dart
class MyClass {
  int _aProperty = 0;

  int get aProperty => _aProperty;

  set aProperty(int value) {
    if (value >= 0) {
      _aProperty = value;
    }
  }
}
```

getter를 사용하여 계산된 속성을 정의할 수도 있습니다.

<?code-excerpt "misc/lib/cheatsheet/getter_compute.dart"?>
```dart
class MyClass {
  final List<int> _values = [];

  void addValue(int value) {
    _values.add(value);
  }

  // 계산된 속성.
  int get count {
    return _values.length;
  }
}
```

### Code example {:.no_toc}

private 가격 `List<double>`을 보관하는 쇼핑 카트 클래스가 있다고 가정해 보겠습니다. 다음을 추가합니다.

* 가격 합계를 반환하는 `total`이라는 getter
* 새 리스트에 음수 가격이 포함되지 않는 한, 리스트를 새 리스트로 대체하는 setter
  (이 경우 setter는 `InvalidPriceException`을 throw해야 함)

DartPad의 모든 초기 오류를 무시합니다.

```dartpad
class InvalidPriceException {}

class ShoppingCart {
  List<double> _prices = [];
  
  // TODO: 여기에 "total" getter를 추가합니다.

  // TODO: 여기에 "prices" setter를 추가합니다.
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  var foundException = false;
  
  try {
    final cart = ShoppingCart();
    cart.prices = [12.0, 12.0, -23.0];
  } on InvalidPriceException {
    foundException = true;
  } catch (e) {
    print('Tried setting a negative price and received a ${e.runtimeType} \n instead of an InvalidPriceException.');
    return;
  }
  
  if (!foundException) {
    print('Tried setting a negative price \n and didn\'t get an InvalidPriceException.');
    return;
  }
  
  final secondCart = ShoppingCart();
  
  try {
    secondCart.prices = [1.0, 2.0, 3.0];
  } catch(e) {
    print('Tried setting prices with a valid list, \n but received an exception: ${e.runtimeType}.');
    return;
  }
  
  if (secondCart._prices.length != 3) {
    print('Tried setting prices with a list of three values, \n but _prices ended up having length ${secondCart._prices.length}.');
    return;
  }

  if (secondCart._prices[0] != 1.0 || secondCart._prices[1] != 2.0 || secondCart._prices[2] != 3.0) {
    final vals = secondCart._prices.map((p) => p.toString()).join(', ');
    print('Tried setting prices with a list of three values (1, 2, 3), \n but incorrect ones ended up in the price list ($vals) .');
    return;
  }
  
  var sum = 0.0;
  
  try {
    sum = secondCart.total;
  } catch (e) {
    print('Tried to get total, but received an exception: ${e.runtimeType}.');
    return;
  }
  
  if (sum != 6.0) {
    print('After setting prices to (1, 2, 3), total returned $sum instead of 6.');
    return;
  }
  
  print('Success!');
}
```

<details>
  <summary>getters와 setters 예제에 대한 솔루션</summary>

  이 연습에는 두 가지 함수가 편리합니다. 
  하나는 `fold`로, 리스트를 단일 값으로 축소할 수 있습니다. (전체를 계산하는 데 사용)
  다른 하나는 `any`로, 제공된 함수로 리스트의 각 아이템을 확인할 수 있습니다.
  (가격 setter에 음수 가격이 있는지 확인하는 데 사용)

  ```dart
  // 여기에 "total" getter를 추가합니다.
  double get total => _prices.fold(0, (e, t) => e + t);

  // 여기에 "prices" setter를 추가합니다.
  set prices(List<double> value) {
    if (value.any((p) => p < 0)) {
      throw InvalidPriceException();
    }
    
    _prices = value;
  }
  ```

</details>


## 선택적인 위치 매개변수 {:#optional-positional-parameters}

Dart에는 위치 매개변수와 명명된 매개변수의 두 가지 함수 매개변수가 있습니다.
위치 매개변수는 여러분이 익숙할 만한 종류입니다.

<?code-excerpt "misc/lib/cheatsheet/optional_positional_args.dart (optional-positional-args)"?>
```dart
int sumUp(int a, int b, int c) {
  return a + b + c;
}
  // ···
  int total = sumUp(1, 2, 3);
```

Dart를 사용하면, 이러한 위치 매개변수를 대괄호로 묶어 선택 사항으로 만들 수 있습니다.

<?code-excerpt "misc/lib/cheatsheet/optional_positional_args.dart (optional-positional-args-2)" replace="/total2/total/g"?>
```dart
int sumUpToFive(int a, [int? b, int? c, int? d, int? e]) {
  int sum = a;
  if (b != null) sum += b;
  if (c != null) sum += c;
  if (d != null) sum += d;
  if (e != null) sum += e;
  return sum;
}
  // ···
  int total = sumUpToFive(1, 2);
  int otherTotal = sumUpToFive(1, 2, 3, 4, 5);
```

선택적 위치 매개변수는 항상 함수 매개변수 리스트의 마지막에 있습니다. 
다른 기본값을 제공하지 않는 한 기본값은 null입니다.

<?code-excerpt "misc/lib/cheatsheet/optional_positional_args2.dart (sum-no-impl)"?>
```dart
int sumUpToFive(int a, [int b = 2, int c = 3, int d = 4, int e = 5]) {
  // ···
}

void main() {
  int newTotal = sumUpToFive(1);
  print(newTotal); // <-- 15 출력
}
```

### Code example {:.no_toc}

1~5개의 정수를 받아들이고, 쉼표로 구분된 숫자의 문자열을 반환하는 `joinWithCommas()`라는 함수를 구현합니다. 
다음은 함수 호출과 반환 값의 몇 가지 예입니다.

| 함수 호출                   |  반환 값 |
|---------------------------------|----------------|
| `joinWithCommas(1)`             | `'1'`          |
| `joinWithCommas(1, 2, 3)`       | `'1,2,3'`      |
| `joinWithCommas(1, 1, 1, 1, 1)` | `'1,1,1,1,1'`  |

<br>

```dartpad
String joinWithCommas(int a, [int? b, int? c, int? d, int? e]) {
  return TODO();
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final errs = <String>[];
  
  try {
    final value = joinWithCommas(1);
    
    if (value != '1') {
      errs.add('Tried calling joinWithCommas(1) \n and got $value instead of the expected (\'1\').'); 
    } 
  } on UnimplementedError {
    print('Tried to call joinWithCommas but failed. \n Did you implement the method?');
    return;
  } catch (e) {
    print('Tried calling joinWithCommas(1), \n but encountered an exception: ${e.runtimeType}.');
    return;
  }

  try {
    final value = joinWithCommas(1, 2, 3);
    
    if (value != '1,2,3') {
      errs.add('Tried calling joinWithCommas(1, 2, 3) \n and got $value instead of the expected (\'1,2,3\').'); 
    } 
  } on UnimplementedError {
    print('Tried to call joinWithCommas but failed. \n Did you implement the method?');
    return;
  } catch (e) {
    print('Tried calling joinWithCommas(1, 2 ,3), \n but encountered an exception: ${e.runtimeType}.');
    return;
  }

  try {
    final value = joinWithCommas(1, 2, 3, 4, 5);
    
    if (value != '1,2,3,4,5') {
      errs.add('Tried calling joinWithCommas(1, 2, 3, 4, 5) \n and got $value instead of the expected (\'1,2,3,4,5\').'); 
    } 
  } on UnimplementedError {
    print('Tried to call joinWithCommas but failed. \n Did you implement the method?');
    return;
  } catch (e) {
    print('Tried calling stringify(1, 2, 3, 4 ,5), \n but encountered an exception: ${e.runtimeType}.');
    return;
  }

  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>위치 매개변수 예제에 대한 솔루션</summary>

  `b`, `c`, `d`, `e` 매개변수는 호출자가 제공하지 않으면 null입니다. 
  따라서 중요한 것은 최종 문자열에 추가하기 전에, 
  해당 인수가 `null`인지 확인하는 것입니다.

  ```dart
  String joinWithCommas(int a, [int? b, int? c, int? d, int? e]) {
    var total = '$a';
    if (b != null) total = '$total,$b';
    if (c != null) total = '$total,$c';
    if (d != null) total = '$total,$d';
    if (e != null) total = '$total,$e';
    return total;
  }
  ```

</details>

<a id="optional-named-parameters"></a>
## 명명된 매개변수 {:#named-parameters}

매개변수 리스트 끝에 중괄호 구문을 사용하면, 이름이 있는 매개변수를 정의할 수 있습니다.

명명된 매개변수는 `required`로 명시적으로 표시되지 않는 한, 선택 사항입니다.

<?code-excerpt "misc/lib/cheatsheet/named_parameters.dart"?>
```dart
void printName(String firstName, String lastName, {String? middleName}) {
  print('$firstName ${middleName ?? ''} $lastName');
}

void main() {
  printName('Dash', 'Dartisan');
  printName('John', 'Smith', middleName: 'Who');
  // 명명된 인수는 인수 리스트의 어느 곳에나 배치될 수 있습니다.
  printName('John', middleName: 'Who', 'Smith');
}
```

예상할 수 있듯이, nullable 명명된 매개변수의 기본값은 `null`이지만, 커스텀 기본값을 제공할 수 있습니다.

매개변수의 타입이 non-nullable인 경우, 
(다음 코드에서와 같이) 기본값을 제공하거나,  
([constructor 섹션](#using-this-in-a-constructor)에서와 같이) 매개변수를 `required`로 표시해야 합니다.

<?code-excerpt "misc/test/cheatsheet/arguments_test.dart (defaulted-middle)" replace="/ = ''/[! = ''!]/g;"?>
```dart
void printName(String firstName, String lastName, {String middleName[! = ''!]}) {
  print('$firstName $middleName $lastName');
}
```

함수는 선택적 위치 매개변수와 명명된 매개변수를 동시에 가질 수 없습니다.


### Code example {:.no_toc}

`MyDataObject` 클래스에 `copyWith()` 인스턴스 메서드를 추가합니다. 
세 개의 명명된 nullable 매개변수를 사용해야 합니다.

* `int? newInt`
* `String? newString`
* `double? newDouble`

`copyWith()` 메서드는 현재 인스턴스를 기반으로 새 `MyDataObject`를 반환해야 하며, 
이전 매개변수의 데이터(있는 경우)를 객체의 속성에 복사해야 합니다. 
예를 들어 `newInt`가 null이 아니면, 해당 값을 `anInt`에 복사합니다.

DartPad의 모든 초기 오류를 무시합니다.

```dartpad
class MyDataObject {
  final int anInt;
  final String aString;
  final double aDouble;

  MyDataObject({
     this.anInt = 1,
     this.aString = 'Old!',
     this.aDouble = 2.0,
  });

  // TODO: 여기에 copyWith 메서드를 추가하세요:
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final source = MyDataObject();
  final errs = <String>[];
  
  try {
    final copy = source.copyWith(newInt: 12, newString: 'New!', newDouble: 3.0);
    
    if (copy.anInt != 12) {
      errs.add('Called copyWith(newInt: 12, newString: \'New!\', newDouble: 3.0), \n and the new object\'s anInt was ${copy.anInt} rather than the expected value (12).');
    }
    
    if (copy.aString != 'New!') {
      errs.add('Called copyWith(newInt: 12, newString: \'New!\', newDouble: 3.0), \n and the new object\'s aString was ${copy.aString} rather than the expected value (\'New!\').');
    }
    
    if (copy.aDouble != 3) {
      errs.add('Called copyWith(newInt: 12, newString: \'New!\', newDouble: 3.0), \n and the new object\'s aDouble was ${copy.aDouble} rather than the expected value (3).');
    }
  } catch (e) {
    print('Called copyWith(newInt: 12, newString: \'New!\', newDouble: 3.0) \n and got an exception: ${e.runtimeType}');
  }
  
  try {
    final copy = source.copyWith();
    
    if (copy.anInt != 1) {
      errs.add('Called copyWith(), and the new object\'s anInt was ${copy.anInt} \n rather than the expected value (1).');
    }
    
    if (copy.aString != 'Old!') {
      errs.add('Called copyWith(), and the new object\'s aString was ${copy.aString} \n rather than the expected value (\'Old!\').');
    }
    
    if (copy.aDouble != 2) {
      errs.add('Called copyWith(), and the new object\'s aDouble was ${copy.aDouble} \n rather than the expected value (2).');
    }
  } catch (e) {
    print('Called copyWith() and got an exception: ${e.runtimeType}');
  }
  
  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>명명된 매개변수 예제에 대한 솔루션</summary>

  `copyWith` 메서드는 많은 클래스와 라이브러리에 나타납니다. 
  여러분의 메서드는 몇 가지 작업을 수행해야 합니다. 
  선택적 명명된 매개변수를 사용하고, 
  `MyDataObject`의 새 인스턴스를 만들고, 
  매개변수의 데이터를 사용하여 채웁니다.
  (또는 매개변수가 null인 경우 현재 인스턴스의 데이터)
  이것은 `??` 연산자를 더 연습할 수 있는 기회입니다!

  ```dart
    MyDataObject copyWith({int? newInt, String? newString, double? newDouble}) {
      return MyDataObject(
        anInt: newInt ?? this.anInt,
        aString: newString ?? this.aString,
        aDouble: newDouble ?? this.aDouble,
      );
    }
  ```
</details>


## 예외 {:#exceptions}

Dart 코드는 예외를 throw하고 catch할 수 있습니다. 
Java와 달리 Dart의 모든 예외는 unchecked입니다. 
메서드는 throw할 수 있는 예외를 선언하지 않으며, 예외를 catch할 필요가 없습니다.

Dart는 `Exception` 및 `Error` 타입을 제공하지만, 
null이 아닌 모든 객체를 throw할 수 있습니다.

<?code-excerpt "misc/test/cheatsheet/exceptions_test.dart (simple-throws)"?>
```dart
throw Exception('Something bad happened.');
throw 'Waaaaaaah!';
```

예외를 처리할 때는 `try`, `on`, `catch` 키워드를 사용하세요.

<?code-excerpt "misc/test/cheatsheet/exceptions_test.dart (try-on-catch)"?>
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 특정 예외
  buyMoreLlamas();
} on Exception catch (e) {
  // 그 밖에 예외인 사항
  print('Unknown exception: $e');
} catch (e) {
  // 지정된 타입 없음, 모든 것을 처리합니다
  print('Something really unknown: $e');
}
```

`try` 키워드는 다른 대부분의 언어에서와 마찬가지로 작동합니다. 
`on` 키워드를 사용하여 유형별로 특정 예외를 필터링하고, 
`catch` 키워드를 사용하여 예외 객체에 대한 참조를 가져옵니다.

예외를 완전히 처리할 수 없는 경우, 
`rethrow` 키워드를 사용하여 예외를 전파합니다.

<?code-excerpt "misc/test/cheatsheet/exceptions_test.dart (try-catch)"?>
```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('I was just trying to breed llamas!');
  rethrow;
}
```

예외가 발생하든 발생하지 않든 코드를 실행하려면, `finally`를 사용합니다.

<?code-excerpt "misc/test/cheatsheet/exceptions_test.dart (try-catch-finally)"?>
```dart
try {
  breedMoreLlamas();
} catch (e) {
  // ... 예외 처리 ...
} finally {
  // 예외가 발생하더라도, 항상 정리하세요.
  cleanLlamaStalls();
}
```

### Code example {:.no_toc}

Implement `tryFunction()` below. It should execute an untrustworthy method and then do the following:

* If `untrustworthy()` throws an `ExceptionWithMessage`, call `logger.logException` with the exception type and message (try using `on` and `catch`).
* If `untrustworthy()` throws an `Exception`, call `logger.logException` with the exception type (try using `on` for this one).
* If `untrustworthy()` throws any other object, don't catch the exception.
* After everything's caught and handled, call `logger.doneLogging` (try using `finally`).

아래에 `tryFunction()`을 구현합니다. 
신뢰할 수 없는 메서드를 실행한 다음, 다음을 수행해야 합니다.

* `untrustworthy()`가 `ExceptionWithMessage`를 throw하는 경우, 
  예외 타입과 메시지로 `logger.logException`을 호출합니다. (`on`과 `catch`를 사용해 보세요)
* `untrustworthy()`가 `Exception`을 throw하는 경우, 
  예외 타입으로 `logger.logException`을 호출합니다. (여기서는 `on`을 사용해 보세요)
* `untrustworthy()`가 다른 객체를 throw하는 경우, 예외를 catch하지 않습니다.
* 모든 것이 catch되고 처리된 후, `logger.doneLogging`을 호출합니다. (`finally`를 사용해 보세요)

```dartpad
typedef VoidFunction = void Function();

class ExceptionWithMessage {
  final String message;
  const ExceptionWithMessage(this.message);
}

// 예외를 기록하려면 logException을 호출하고, 완료되면 doneLogging을 호출합니다.
abstract class Logger {
  void logException(Type t, [String? msg]);
  void doneLogging();
}

void tryFunction(VoidFunction untrustworthy, Logger logger) {
  // TODO: 여기에 tryFunction의 내용을 작성하세요:
}

// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
class MyLogger extends Logger {
  Type? lastType;
  String lastMessage = '';
  bool done = false;
  
  void logException(Type t, [String? message]) {
    lastType = t;
    lastMessage = message ?? lastMessage;
  }
  
  void doneLogging() => done = true;  
}

void main() {
  final errs = <String>[];
  var logger = MyLogger();
  
  try {
    tryFunction(() => throw Exception(), logger);
  
    if ('${logger.lastType}' != 'Exception' && '${logger.lastType}' != '_Exception') {
      errs.add('Untrustworthy threw an Exception, but a different type was logged: \n ${logger.lastType}.');
    }
    
    if (logger.lastMessage != '') {
      errs.add('Untrustworthy threw an Exception with no message, but a message \n was logged anyway: \'${logger.lastMessage}\'.');
    }
    
    if (!logger.done) {
      errs.add('Untrustworthy threw an Exception, \n and doneLogging() wasn\'t called afterward.');
    }
  } catch (e) {
    print('Untrustworthy threw an exception, and an exception of type \n ${e.runtimeType} was unhandled by tryFunction.');
  }
  
  logger = MyLogger();
  
  try {
    tryFunction(() => throw ExceptionWithMessage('Hey!'), logger);
  
    if (logger.lastType != ExceptionWithMessage) {
      errs.add('Untrustworthy threw an ExceptionWithMessage(\'Hey!\'), but a \n different type was logged: ${logger.lastType}.');
    }
    
    if (logger.lastMessage != 'Hey!') {
      errs.add('Untrustworthy threw an ExceptionWithMessage(\'Hey!\'), but a \n different message was logged: \'${logger.lastMessage}\'.');
    }
    
    if (!logger.done) {
      errs.add('Untrustworthy threw an ExceptionWithMessage(\'Hey!\'), \n and doneLogging() wasn\'t called afterward.');
    }
  } catch (e) {
    print('Untrustworthy threw an ExceptionWithMessage(\'Hey!\'), \n and an exception of type ${e.runtimeType} was unhandled by tryFunction.');
  }
  
  logger = MyLogger();
  bool caughtStringException = false;

  try {
    tryFunction(() => throw 'A String', logger);
  } on String {
    caughtStringException = true;
  }

  if (!caughtStringException) {
    errs.add('Untrustworthy threw a string, and it was incorrectly handled inside tryFunction().');
  }
  
  logger = MyLogger();
  
  try {
    tryFunction(() {}, logger);
  
    if (logger.lastType != null) {
      errs.add('Untrustworthy didn\'t throw an Exception, \n but one was logged anyway: ${logger.lastType}.');
    }
    
    if (logger.lastMessage != '') {
      errs.add('Untrustworthy didn\'t throw an Exception with no message, \n but a message was logged anyway: \'${logger.lastMessage}\'.');
    }
    
    if (!logger.done) {
      errs.add('Untrustworthy didn\'t throw an Exception, \n but doneLogging() wasn\'t called afterward.');
    }
  } catch (e) {
    print('Untrustworthy didn\'t throw an exception, \n but an exception of type ${e.runtimeType} was unhandled by tryFunction anyway.');
  }
  
  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>예외에 대한 솔루션 예</summary>

  이 연습은 까다로워 보이지만, 실제로는 하나의 큰 `try` 문입니다. 
  `try` 내부에서 `untrustworthy`를 호출한 다음, 
  `on`, `catch`, `finally`를 사용하여 예외를 잡고, 
  logger에서 메서드를 호출합니다.

  ```dart
  void tryFunction(VoidFunction untrustworthy, Logger logger) {
    try {
      untrustworthy();
    } on ExceptionWithMessage catch (e) {
      logger.logException(e.runtimeType, e.message);
    } on Exception {
      logger.logException(Exception);
    } finally {
      logger.doneLogging();
    }
  }
  ```

</details>


## 생성자에서 `this` 사용 {:#using-this-in-a-constructor}

Dart는 생성자에서 속성에 값을 할당하기 위한, 편리한 shortcut을 제공합니다. 
생성자를 선언할 때, `this.propertyName`을 사용하세요.

<?code-excerpt "misc/lib/cheatsheet/this_constructor.dart (required-positional)"?>
```dart
class MyColor {
  int red;
  int green;
  int blue;

  MyColor(this.red, this.green, this.blue);
}

final color = MyColor(80, 80, 128);
```

이 기술은 명명된 매개변수에도 적용됩니다. 
속성 이름은 매개변수의 이름이 됩니다.

<?code-excerpt "misc/lib/cheatsheet/this_constructor.dart (required-named)" replace="/int.*;/.../g; /olorRN/olor/g;"?>
```dart
class MyColor {
  ...

  MyColor({required this.red, required this.green, required this.blue});
}

final color = MyColor(red: 80, green: 80, blue: 80);
```

앞의 코드에서, `red`, `green`, `blue`는 `required`로 표시되어 있습니다. 
이러한 `int` 값은 null이 될 수 없기 때문입니다. 
기본값을 추가하면, `required`를 생략할 수 있습니다.

<?code-excerpt "misc/lib/cheatsheet/this_constructor.dart (defaulted)" replace="/olorO/olor/g; /.positional//g; /.named//g;"?>
```dart
MyColor([this.red = 0, this.green = 0, this.blue = 0]);
// 또는
MyColor({this.red = 0, this.green = 0, this.blue = 0});
```

### Code example {:.no_toc}

`MyClass`에 `this.` 구문을 사용하여, 
클래스의 세 가지 속성에 대한 값을 수신하고 할당하는 한 줄짜리 생성자를 추가합니다.

DartPad의 모든 초기 오류를 무시합니다.

```dartpad
class MyClass {
  final int anInt;
  final String aString;
  final double aDouble;
  
  // TODO: 여기에 생성자를 만듭니다.
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final errs = <String>[];
  
  try {
    final obj = MyClass(1, 'two', 3);
    
    if (obj.anInt != 1) {
      errs.add('Called MyClass(1, \'two\', 3) and got an object with anInt of ${obj.anInt} \n instead of the expected value (1).');
    }

    if (obj.anInt != 1) {
      errs.add('Called MyClass(1, \'two\', 3) and got an object with aString of \'${obj.aString}\' \n instead of the expected value (\'two\').');
    }

    if (obj.anInt != 1) {
      errs.add('Called MyClass(1, \'two\', 3) and got an object with aDouble of ${obj.aDouble} \n instead of the expected value (3).');
    }
  } catch (e) {
    print('Called MyClass(1, \'two\', 3) and got an exception \n of type ${e.runtimeType}.');
  }
  
  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>`this` 예제에 대한 솔루션</summary>

  이 연습은 한 줄짜리 솔루션입니다. 
  `this.anInt`, `this.aString`, `this.aDouble`을 매개변수로 하여 생성자를 선언합니다.

  ```dart    
  MyClass(this.anInt, this.aString, this.aDouble);
  ```

</details>

{% comment %}
This one seems super easy compared to previous ones.
We've already seen it in the Exceptions example,
and I'd already used it in a previous example.
Move it up higher? Or make it more challenging, somehow?
Maybe require both positional and optional named parameters (with defaults)?
{% endcomment %}

## 이니셜라이저 리스트 {:#initializer-lists}

생성자를 구현할 때, 생성자 본문이 실행되기 전에 몇 가지 설정을 해야 할 때가 있습니다. 
예를 들어, final 필드는 생성자 본문이 실행되기 전에 값이 있어야 합니다. 
생성자의 서명과 본문 사이에 있는 이니셜라이저 리스트에서 이 작업을 수행하세요.

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (initializer-list-no-comment)"?>
```dart
Point.fromJson(Map<String, double> json)
    : x = json['x']!,
      y = json['y']! {
  print('In Point.fromJson(): ($x, $y)');
}
```

이니셜라이저 리스트는 개발 중에만 실행되는 어설션을 넣기에 편리한 곳이기도 합니다.

<?code-excerpt "misc/lib/cheatsheet/initializer_lists.dart (assert)"?>
```dart
NonNegativePoint(this.x, this.y)
    : assert(x >= 0),
      assert(y >= 0) {
  print('I just made a NonNegativePoint: ($x, $y)');
}
```

### Code example {:.no_toc}

아래의 `FirstTwoLetters` 생성자를 완성하세요. 
이니셜라이저 리스트를 사용하여 `word`의 처음 두 문자를 `letterOne` 및 `LetterTwo` 속성에 할당하세요. 
추가 점수를 위해, `assert`를 추가하여 두 문자 미만의 단어를 잡으세요.

DartPad의 모든 초기 오류를 무시하세요.

{% comment %}
Is the assert even executed? I can't see any effect on the test,
which makes me think asserts are ignored.
Also, the test just checks for the presence of any exception, not for
an AssertionError.

Also, my print() wasn't visible in the Output until I fixed my code and/or
the test. That was unexpected.
It'd be cool if Output appeared only if you want it, like Solution does.

FINALLY: Suggest using https://pub.dev/packages/characters
if this is a user-entered string.
{% endcomment %}

```dartpad
class FirstTwoLetters {
  final String letterOne;
  final String letterTwo;

  // TODO: 여기에 이니셜라이저 리스트가 있는 생성자를 만듭니다.
  FirstTwoLetters(String word)

}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final errs = <String>[];

  try {
    final result = FirstTwoLetters('My String');
    
    if (result.letterOne != 'M') {
      errs.add('Called FirstTwoLetters(\'My String\') and got an object with \n letterOne equal to \'${result.letterOne}\' instead of the expected value (\'M\').');
    }

    if (result.letterTwo != 'y') {
      errs.add('Called FirstTwoLetters(\'My String\') and got an object with \n letterTwo equal to \'${result.letterTwo}\' instead of the expected value (\'y\').');
    }
  } catch (e) {
    errs.add('Called FirstTwoLetters(\'My String\') and got an exception \n of type ${e.runtimeType}.');
  }

  bool caughtException = false;
  
  try {
    FirstTwoLetters('');
  } catch (e) {
    caughtException = true;
  }
  
  if (!caughtException) {
    errs.add('Called FirstTwoLetters(\'\') and didn\'t get an exception \n from the failed assertion.');
  }
  
  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>이니셜라이저 리스트 예제에 대한 솔루션</summary>

  두 가지 할당이 이루어져야 합니다. 
  `letterOne`에 `word[0]`을 할당하고, 
  `letterTwo`에 `word[1]`을 할당해야 합니다.

  ```dart    
    FirstTwoLetters(String word)
        : assert(word.length >= 2),
          letterOne = word[0],
          letterTwo = word[1];
  ```
</details>

## 명명된 생성자 {:#named-constructors}

{% comment %}
Much like JavaScript, Dart doesn't support method overloads
(two methods with the same name but different signatures).
[ISSUE: methods & constructors aren't the same thing,
so I deleted that. We can add it back if we can word it better.]
{% endcomment %}
클래스가 여러 생성자를 가질 수 있도록, Dart는 명명된 생성자를 지원합니다.

<?code-excerpt "misc/lib/cheatsheet/named_constructor.dart (point-class)"?>
```dart
class Point {
  double x, y;

  Point(this.x, this.y);

  Point.origin()
      : x = 0,
        y = 0;
}
```

명명된 생성자를 사용하려면, 전체 이름을 사용하여 호출합니다.

<?code-excerpt "misc/test/cheatsheet/constructor_test.dart (origin-point)"?>
```dart
final myPoint = Point.origin();
```

### Code example {:.no_toc}

`Color` 클래스에 `Color.black`이라는 생성자를 제공하여, 세 가지 속성을 모두 0으로 설정합니다.

DartPad의 모든 초기 오류를 무시합니다.

```dartpad
class Color {
  int red;
  int green;
  int blue;
  
  Color(this.red, this.green, this.blue);

  // TODO: 여기에 "Color.black"이라는 이름의 생성자를 만듭니다.

}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final errs = <String>[];

  try {
    final result = Color.black();
    
    if (result.red != 0) {
      errs.add('Called Color.black() and got a Color with red equal to \n ${result.red} instead of the expected value (0).');
    }

    if (result.green != 0) {
      errs.add('Called Color.black() and got a Color with green equal to \n ${result.green} instead of the expected value (0).');
    }

    if (result.blue != 0) {
  errs.add('Called Color.black() and got a Color with blue equal to \n ${result.blue} instead of the expected value (0).');
    }
  } catch (e) {
    print('Called Color.black() and got an exception of type \n ${e.runtimeType}.');
    return;
  }

  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>명명된 생성자 예제에 대한 솔루션</summary>

  생성자에 대한 선언은 `Color.black():`로 시작해야 합니다. 
  이니셜라이저 리스트(콜론 뒤)에서 `red`, `green`, `blue`를 `0`으로 설정합니다.

  ```dart    
    Color.black()
        : red = 0,
          green = 0,
          blue = 0;
  ```

</details>

## Factory 생성자 {:#factory-constructors}

Dart는 팩토리 생성자를 지원하는데, 이는 하위 타입 또는 null을 반환할 수 있습니다. 
팩토리 생성자를 만들려면, `factory` 키워드를 사용합니다.

<?code-excerpt "misc/lib/cheatsheet/factory_constructors.dart"?>
```dart
class Square extends Shape {}

class Circle extends Shape {}

class Shape {
  Shape();

  factory Shape.fromTypeName(String typeName) {
    if (typeName == 'square') return Square();
    if (typeName == 'circle') return Circle();

    throw ArgumentError('Unrecognized $typeName');
  }
}
```

### Code example {:.no_toc}

`IntegerHolder.fromList`라는 팩토리 생성자에서, `TODO();` 줄을 바꿔 다음을 반환합니다.

* 목록에 **하나**의 값이 있는 경우, 해당 값을 사용하여 `IntegerSingle` 인스턴스를 만듭니다.
* 목록에 **두**개의 값이 있는 경우, 순서대로 값을 사용하여 `IntegerDouble` 인스턴스를 만듭니다.
* 목록에 **세**개의 값이 있는 경우, 순서대로 값을 사용하여 `IntegerTriple` 인스턴스를 만듭니다.
* 그렇지 않으면, `Error`를 throw합니다.

성공하면 콘솔에 `Success!`가 표시됩니다.

```dartpad
class IntegerHolder {
  IntegerHolder();
  
  // 이 팩토리 생성자를 구현합니다.
  factory IntegerHolder.fromList(List<int> list) {
    TODO();
  }
}

class IntegerSingle extends IntegerHolder {
  final int a;

  IntegerSingle(this.a);
}

class IntegerDouble extends IntegerHolder {
  final int a;
  final int b;

  IntegerDouble(this.a, this.b);
}

class IntegerTriple extends IntegerHolder {
  final int a;
  final int b;
  final int c;

  IntegerTriple(this.a, this.b, this.c);
}

// Tests your solution (Don't edit from this point to end of file):
void main() {
  final errs = <String>[];

  // Run 5 tests to see which values have valid integer holders
  for (var tests = 0; tests < 5; tests++) {
    if (!testNumberOfArgs(errs, tests)) return;
  }

  // The goal is no errors with values 1 to 3,
  // but have errors with values 0 and 4.
  // The testNumberOfArgs method adds to the errs array if
  // the values 1 to 3 have an error and
  // the values 0 and 4 don't have an error
  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}

bool testNumberOfArgs(List<String> errs, int count) {
  bool _threw = false;
  final ex = List.generate(count, (index) => index + 1);
  final callTxt = "IntegerHolder.fromList(${ex})";
  try {
    final obj = IntegerHolder.fromList(ex);
    final String vals = count == 1 ? "value" : "values";
    // Uncomment the next line if you want to see the results realtime
    // print("Testing with ${count} ${vals} using ${obj.runtimeType}.");
    testValues(errs, ex, obj, callTxt);
  } on Error {
    _threw = true;
  } catch (e) {
    switch (count) {
      case (< 1 && > 3):
        if (!_threw) {
          errs.add('Called ${callTxt} and it didn\'t throw an Error.');
        }
      default:
        errs.add('Called $callTxt and received an Error.');
    }
  }
  return true;
}

void testValues(List<String> errs, List<int> expectedValues, IntegerHolder obj,
    String callText) {
  for (var i = 0; i < expectedValues.length; i++) {
    int found;
    if (obj is IntegerSingle) {
      found = obj.a;
    } else if (obj is IntegerDouble) {
      found = i == 0 ? obj.a : obj.b;
    } else if (obj is IntegerTriple) {
      found = i == 0
          ? obj.a
          : i == 1
              ? obj.b
              : obj.c;
    } else {
      throw ArgumentError(
          "This IntegerHolder type (${obj.runtimeType}) is unsupported.");
    }

    if (found != expectedValues[i]) {
      errs.add(
          "Called $callText and got a ${obj.runtimeType} " + 
          "with a property at index $i value of $found " +
          "instead of the expected (${expectedValues[i]}).");
    }
  }
}

```

<details>
  <summary>팩토리 생성자 예제에 대한 솔루션</summary>

  팩토리 생성자 내부에서 리스트의 길이를 확인한 다음, 
  필요에 따라 `IntegerSingle`, `IntegerDouble` 또는 `IntegerTriple`을 생성하여 반환합니다.

  `TODO();`를 다음 코드 블록으로 바꿉니다.

  ```dart
    switch (list.length) {
      case 1:
        return IntegerSingle(list[0]);
      case 2:
        return IntegerDouble(list[0], list[1]);
      case 3:
        return IntegerTriple(list[0], list[1], list[2]);
      default:
        throw ArgumentError("List must between 1 and 3 items. This list was ${list.length} items.");
    }
  ```

</details>

## 리다이렉트 생성자 {:#redirecting-constructors}

때때로 생성자의 유일한 목적은 같은 클래스의 다른 생성자로 리다이렉트하는 것입니다. 
리다이렉팅 생성자의 본문은 비어 있으며, 생성자 호출은 콜론(`:`) 뒤에 나타납니다.

<?code-excerpt "misc/lib/cheatsheet/redirecting_constructors.dart (redirecting-constructors)"?>
```dart
class Automobile {
  String make;
  String model;
  int mpg;

  // 이 클래스의 메인 생성자입니다.
  Automobile(this.make, this.model, this.mpg);

  // 메인 생성자에게 위임(Delegates)합니다.
  Automobile.hybrid(String make, String model) : this(make, model, 60);

  // 명명된 생성자에게 위임(Delegates)합니다.
  Automobile.fancyHybrid() : this.hybrid('Futurecar', 'Mark 2');
}
```

### Code example {:.no_toc}

위의 `Color` 클래스를 기억하시나요? 
`black`이라는 명명된 생성자를 만드세요. 
하지만 속성을 수동으로 할당하는 대신, 인수로 0을 사용하여 기본 생성자로 리디렉션하세요.

DartPad의 모든 초기 오류는 무시하세요.

```dartpad
class Color {
  int red;
  int green;
  int blue;
  
  Color(this.red, this.green, this.blue);

  // TODO: 여기에 "black"이라는 이름의 생성자를 만들고, 
  // 기존 생성자를 호출하도록 리디렉션합니다.
}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final errs = <String>[];

  try {
    final result = Color.black();
    
    if (result.red != 0) {
      errs.add('Called Color.black() and got a Color with red equal to \n ${result.red} instead of the expected value (0).');
    }

    if (result.green != 0) {
      errs.add('Called Color.black() and got a Color with green equal to \n ${result.green} instead of the expected value (0).');
    }

    if (result.blue != 0) {
  errs.add('Called Color.black() and got a Color with blue equal to \n ${result.blue} instead of the expected value (0).');
    }
  } catch (e) {
    print('Called Color.black() and got an exception of type ${e.runtimeType}.');
    return;
  }

  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>리다이렉트 생성자를 위한 솔루션 예제</summary>

  생성자는 `this(0, 0, 0)`로 리다이렉트되어야 합니다.

  ```dart
    Color.black() : this(0, 0, 0);
  ```

</details>

## Const 생성자 {:#const-constructors}

클래스가 절대 변하지 않는 객체를 생성하는 경우, 
이러한 객체를 컴파일 타임 상수로 만들 수 있습니다. 
이를 위해 `const` 생성자를 정의하고, 
모든 인스턴스 변수가 final인지 확인하세요.

<?code-excerpt "misc/lib/cheatsheet/redirecting_constructors.dart (const-constructors)"?>
```dart
class ImmutablePoint {
  static const ImmutablePoint origin = ImmutablePoint(0, 0);

  final int x;
  final int y;

  const ImmutablePoint(this.x, this.y);
}
```

### Code example {:.no_toc}

`Recipe` 클래스를 수정하여 인스턴스가 상수가 될 수 있도록 하고, 다음을 수행하는 상수 생성자를 만듭니다.

* 세 개의 매개변수가 있습니다: `ingredients`, `calceries`, `milligramsOfSodium`. (이 순서대로)
* `this.` 구문을 사용하여, 매개변수 값을 같은 이름의 객체 속성에 자동으로 할당합니다.
* 생성자 선언에서, `Recipe` 바로 앞에 `const` 키워드가 있는 상수입니다.

DartPad의 모든 초기 오류를 무시합니다.

```dartpad
// TODO: 이 메서드 내의 변수도 수정해야 합니다.
class Recipe {
  List<String> ingredients;
  int calories;
  double milligramsOfSodium;

  // TODO: 여기에 const 생성자를 만듭니다.

}


// 당신의 솔루션을 테스트합니다. (편집하지 마세요!):
void main() {
  final errs = <String>[];

  try {
    const obj = Recipe(['1 egg', 'Pat of butter', 'Pinch salt'], 120, 200);
    
    if (obj.ingredients.length != 3) {
      errs.add('Called Recipe([\'1 egg\', \'Pat of butter\', \'Pinch salt\'], 120, 200) \n and got an object with ingredient list of length ${obj.ingredients.length} rather than the expected length (3).');
    }
    
    if (obj.calories != 120) {
      errs.add('Called Recipe([\'1 egg\', \'Pat of butter\', \'Pinch salt\'], 120, 200) \n and got an object with a calorie value of ${obj.calories} rather than the expected value (120).');
    }
    
    if (obj.milligramsOfSodium != 200) {
      errs.add('Called Recipe([\'1 egg\', \'Pat of butter\', \'Pinch salt\'], 120, 200) \n and got an object with a milligramsOfSodium value of ${obj.milligramsOfSodium} rather than the expected value (200).');
    }
  } catch (e) {
    print('Tried calling Recipe([\'1 egg\', \'Pat of butter\', \'Pinch salt\'], 120, 200) \n and received a null.');
  }

  if (errs.isEmpty) {
    print('Success!');
  } else {
    errs.forEach(print);
  }
}
```

<details>
  <summary>const 생성자 예제에 대한 솔루션</summary>

  생성자를 const로 만들려면 모든 속성을 final로 만들어야 합니다.

  ```dart
  class Recipe {
    final List<String> ingredients;
    final int calories;
    final double milligramsOfSodium;

    const Recipe(this.ingredients, this.calories, this.milligramsOfSodium);
  }
  ```

</details>

## 다음은 무엇인가요? {:#whats-next}

이 튜토리얼을 사용하여, Dart 언어의 가장 흥미로운 기능에 대한 지식을 배우거나 테스트하는 데 도움이 되었기를 바랍니다.

다음에 시도할 수 있는 것은 다음과 같습니다.

* [다른 Dart 튜토리얼](/tutorials)을 시도해 보세요.
* [Dart 언어 투어](/language)를 읽어 보세요.
* [DartPad]({{site.dartpad}})로 플레이해 보세요.
* [Dart SDK 받기](/get-dart).