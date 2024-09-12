---
# title: Fixing common type problems
title: 일반적인 타입 문제 해결
description: 일반적인 타입 문제와 해결 방법입니다.
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; /. • (lib|test)\/\w+\.dart:\d+:\d+//g"?>
<?code-excerpt plaster="none"?>
<?code-excerpt path-base="type_system"?>

타입 검사에 문제가 있는 경우, 이 페이지가 도움이 될 수 있습니다. 
자세히 알아보려면, [Dart의 타입 시스템](/language/type-system)에 대해 읽고, 
[이러한 다른 리소스](/language/type-system#other-resources)를 참조하세요.

:::note 이 페이지를 개선하는 데 도움을 주세요!
여기에 나열되지 않은 경고나 오류가 발생하는 경우, 
오른쪽 상단의 **버그 아이콘**을 클릭하여, 문제를 제출하세요. 
**경고나 오류 메시지**를 포함하고, 
가능하다면 재현 가능한 작은 사례와 그에 해당하는 올바른 사례의 코드도 포함하세요.
:::

## 문제 해결 {:#troubleshooting}

Dart는 사운드 타입 시스템을 적용합니다. 
즉, 변수의 값이 정적 타입과 다른 코드를 작성할 수 없습니다. 
`int` 타입의 변수는 소수 자릿수가 있는 숫자를 저장할 수 없습니다. 
Dart는 [컴파일 타임](#static-errors-and-warnings) 및 
[런타임](#runtime-errors)에서 변수 값을 타입과 비교합니다.

변수에 저장된 값이 변수의 정적 타입과 다른 상황에 처할 수 없습니다. 
대부분의 최신 정적 타입 언어와 마찬가지로, 
Dart는 [정적(컴파일 타임)](#static-errors-and-warnings) 및 
[동적(런타임)](#runtime-errors) 검사를 결합하여 이를 달성합니다.

예를 들어, 다음 타입 오류는 컴파일 타임에 감지됩니다.

```dart tag=fails-sa
List<int> numbers = [1, 2, 3];
List<String> [!string = numbers!];
```

`List<int>`도 `List<String>`도 다른 것의 하위 타입이 아니므로, 
Dart는 이를 정적으로 배제합니다.

다음 섹션에서 정적 분석 오류의 다른 예와 다른 오류 타입을 볼 수 있습니다.

## 타입 오류 없음 {:#no-type-errors}

예상한 오류나 경고가 표시되지 않는 경우, 
최신 버전의 Dart를 사용하고 [IDE 또는 편집기](/tools#editors)를 올바르게 구성했는지 확인하세요.

[`dart analyst`](/tools/dart-analyze) 명령으로, 
명령줄을 사용하여 프로그램에서 분석을 실행할 수도 있습니다.

분석이 예상대로 작동하는지 확인하려면, 다음 코드를 Dart 파일에 추가해 보세요.

<?code-excerpt "lib/strong_analysis.dart (static-analysis-enabled)"?>
```dart tag=fails-sa
bool b = [0][0];
```

올바르게 구성된 경우, 분석기에서 다음 오류가 발생합니다.

<?code-excerpt "analyzer-results-stable.txt" retain="/'int' can't be .* 'bool'/" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - A value of type 'int' can't be assigned to a variable of type 'bool'. Try changing the type of the variable, or casting the right-hand type to 'bool'. - invalid_assignment
```

<a name="common-errors"></a>
## 정적 오류 및 경고 {:#static-errors-and-warnings}

이 섹션에서는 분석기나 IDE에서 볼 수 있는 오류와 경고 중 일부를 수정하는 방법을 보여줍니다.

정적 분석은 모든 오류를 포착할 수 없습니다. 
런타임에만 나타나는 오류를 수정하는 데 도움이 필요하면, 
[런타임 오류](#common-errors-and-warnings)를 참조하세요.

### 정의되지 않은 멤버 {:#undefined-member}

<?code-excerpt "analyzer-results-stable.txt" retain="/getter.*isn't defined for the type/" replace="/. Try.*.'context2D'. / /g; /getter/<member\x3E/g; /'\w+'/'...'/g; /-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - The <member> '...' isn't defined for the type '...' - undefined_<member>
```

이러한 오류는 다음 조건에서 나타날 수 있습니다.

- 변수가 정적으로 어떤 슈퍼타입으로 알려져 있지만, 코드는 서브타입을 가정합니다.
- 제네릭 클래스에 제한된 타입 매개변수가 있지만, 클래스의 인스턴스 생성 표현식에서 타입 인수가 생략됩니다.

#### 예제 1: 변수는 정적으로 어떤 슈퍼타입으로 알려져 있지만, 코드는 서브타입을 가정합니다.{:#example-1-a-variable-is-statically-known-to-be-some-supertype-but-the-code-assumes-a-subtype}

다음 코드에서, 분석기는 `context2D`가 정의되지 않았다고 불평합니다.

<?code-excerpt "lib/common_fixes_analysis.dart (canvas-undefined)" replace="/context2D/[!$&!]/g"?>
```dart tag=fails-sa
var canvas = querySelector('canvas')!;
canvas.[!context2D!].lineTo(x, y);
```

<?code-excerpt "analyzer-results-stable.txt" retain="/context2D.*isn't defined for the type/" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - The getter 'context2D' isn't defined for the type 'Element'. Try importing the library that defines 'context2D', correcting the name to the name of an existing getter, or defining a getter or field named 'context2D'. - undefined_getter
```

#### 수정: 멤버 정의를 명시적 타입 선언이나 다운캐스트로 교체합니다. {:#fix-replace-the-definition-of-the-member-with-an-explicit-type-declaration-or-a-downcast}

`querySelector()`의 반환 타입은 `Element?`(`!`가 `Element`로 변환)이지만, 
코드는 그것이 하위 타입인 `CanvasElement`(`context2D`를 정의함)라고 가정합니다. 
`canvas` 필드는 `var`로 선언되어, Dart가 `canvas`를 `Element`로 추론할 수 있습니다.

이 오류는 명시적 다운캐스트로 수정할 수 있습니다.

<?code-excerpt "lib/common_fixes_analysis.dart (canvas-as)" replace="/as \w+/[!$&!]/g"?>
```dart tag=passes-sa
var canvas = querySelector('canvas') [!as CanvasElement!];
canvas.context2D.lineTo(x, y);
```

그렇지 않은 경우, 단일 타입을 사용할 수 없는 상황에서는 `dynamic`을 사용하세요.

<?code-excerpt "lib/common_fixes_analysis.dart (canvas-dynamic)" replace="/dynamic/[!$&!]/g"?>
```dart tag=passes-sa
[!dynamic!] canvasOrImg = querySelector('canvas, img');
var width = canvasOrImg.width;
```

#### 예제 2: 생략된 타입 매개변수가 해당 타입 바운드로 디폴트 설정됩니다. {:#example-2-omitted-type-parameters-default-to-their-type-bounds}

`Iterable`을 확장하는, **바운드된 타입 매개변수**를 갖는 다음의 **제네릭 클래스**를 고려해 보세요.

<?code-excerpt "lib/bounded/my_collection.dart"?>
```dart
class C<T extends Iterable> {
  final T collection;
  C(this.collection);
}
```

다음 코드는 이 클래스의 새 인스턴스를 생성하고(형식 인수 생략) `collection` 멤버에 액세스합니다.

<?code-excerpt "lib/bounded/instantiate_to_bound.dart (undefined-method)" replace="/c\.add\(2\)/[!$&!]/g"?>
```dart tag=fails-sa
var c = C(Iterable.empty()).collection;
[!c.add(2)!];
```

<?code-excerpt "analyzer-results-stable.txt" retain="/add.*isn't defined for the type/" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - The method 'add' isn't defined for the type 'Iterable'. Try correcting the name to the name of an existing method, or defining a method named 'add'. - undefined_method
```

[List][] 타입에는 `add()` 메서드가 있지만, [Iterable][]에는 없습니다.

#### 수정: 타입 인수 지정 또는 다운스트림 오류 수정 {:#fix-specify-type-arguments-or-fix-downstream-errors}

명시적 타입 인수 없이, 제네릭 클래스가 인스턴스화되면, 
각 타입 매개변수는 명시적으로 제공된 경우, 
해당 타입 바운드(이 예에서는 `Iterable`)로 기본 설정되고, 
그렇지 않은 경우, `dynamic`으로 기본 설정됩니다.

이러한 오류는 사례별로 수정해야 합니다. 원래 설계 의도를 잘 이해하는 것이 도움이 됩니다.

명시적으로 타입 인수를 전달하는 것은 타입 오류를 식별하는 데 효과적인 방법입니다. 
예를 들어, `List`를 타입 인수로 지정하도록 코드를 변경하면, 
분석기가 생성자 인수에서 타입 불일치를 감지할 수 있습니다. 
리스트 리터럴과 같은 적절한 타입의 생성자 인수를 제공하여 오류를 수정합니다.

<?code-excerpt "test/strong_test.dart (add-type-arg)" replace="/.List.|\[\]/[!$&!]/g"?>
```dart tag=passes-sa
var c = C[!<List>!]([![]!]).collection;
c.add(2);
```

<hr>

### 잘못된 메서드 재정의 {:#invalid-method-override}

<?code-excerpt "analyzer-results-stable.txt" retain="/isn't a valid override of.*add/" replace="/'[\w\.]+'/'...'/g; /\('.*?'\)//g; /-(.*?):(.*?):(.*?)-/-/g; /' . -/' -/g"?>
```plaintext
error - '...'  isn't a valid override of '...' - invalid_override
```

이러한 오류는 일반적으로 하위 클래스가 원본 클래스의 하위 클래스를 지정하여, 
메서드의 매개변수 타입을 강화할 때 발생합니다.

:::note
이 문제는 제네릭 서브클래스가 타입을 지정하지 않을 때도 발생할 수 있습니다. 
자세한 내용은 [누락된 타입 인수](#missing-type-arguments)를 참조하세요.
:::

#### Example

다음 예제에서, `add()` 메서드의 매개변수는 `int` 타입이며, 
이는 부모 클래스에서 사용되는 매개변수 타입인 `num`의 하위 타입입니다.

<?code-excerpt "lib/common_fixes_analysis.dart (invalid-method-override)" replace="/int(?= \w\b.*=)/[!$&!]/g"?>
```dart tag=fails-sa
abstract class NumberAdder {
  num add(num a, num b);
}

class MyAdder extends NumberAdder {
  @override
  num add([!int!] a, [!int!] b) => a + b;
}
```

<?code-excerpt "analyzer-results-stable.txt" retain="/isn't a valid override of.*add/" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - 'MyAdder.add' ('num Function(int, int)') isn't a valid override of 'NumberAdder.add' ('num Function(num, num)'). - invalid_override
```

부동 소수점 값이 `MyAdder`에 전달되는 다음 시나리오를 고려하세요.

<?code-excerpt "lib/common_fixes_analysis.dart (runtime-failure-if-int)" replace="/1.2/[!1.2!]/g/3.4/[!3.4!]/g"?>
```dart tag=runtime-fail
NumberAdder adder = MyAdder();
adder.add([!1.2!], [!3.4!]);
```

오버라이드가 허용되면, 코드는 런타임에 오류를 발생시킵니다.

#### 수정: 메서드의 매개변수 타입을 확대합니다. {:#fix-widen-the-methods-parameter-types}

하위 클래스의 메서드는 상위 클래스의 메서드가 취하는 모든 객체를 허용해야 합니다.

하위 클래스의 타입을 넓혀서 예를 수정합니다.

<?code-excerpt "lib/common_fixes_analysis.dart (invalid-method-override)" replace="/int(?= \w\b.*=)/[!num!]/g"?>
```dart tag=passes-sa
abstract class NumberAdder {
  num add(num a, num b);
}

class MyAdder extends NumberAdder {
  @override
  num add([!num!] a, [!num!] b) => a + b;
}
```

자세한 내용은, [메서드를 재정의할 때, 적절한 입력 매개변수 타입 사용](/language/type-system#use-proper-param-types)을 참조하세요.

:::note
하위 타입을 사용해야 하는 타당한 이유가 있는 경우, 
[covariant 키워드](#the-covariant-keyword)를 사용할 수 있습니다.
:::

<hr>

### 누락된 타입 인수 {:#missing-type-arguments}

<?code-excerpt "analyzer-results-stable.txt" retain="/isn't a valid override of.*method/" replace="/'\S+'/'...'/g; /\('.*?'\)//g; /-(.*?):(.*?):(.*?)-/-/g; /' . -/' -/g"?>
```plaintext
error - '...'  isn't a valid override of '...' - invalid_override
```

#### Example

다음 예에서, `Subclass`는 `Superclass<T>`를 확장하지만, 타입 인수를 지정하지 않습니다. 
분석기는 `Subclass<dynamic>`을 추론하는데, 
이는 `method(int)`에서 잘못된 오버라이드 오류를 발생시킵니다.

<?code-excerpt "lib/common_fixes_analysis.dart (type-arguments)" replace="/int/[!$&!]/g"?>
```dart tag=fails-sa
class Superclass<T> {
  void method(T param) { ... }
}

class Subclass extends Superclass {
  @override
  void method([!int!] param) { ... }
}
```

<?code-excerpt "analyzer-results-stable.txt" retain="/isn't a valid override of.*method/" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - 'Subclass.method' ('void Function(int)') isn't a valid override of 'Superclass.method' ('void Function(dynamic)'). - invalid_override
```

#### 수정: 제네릭 하위 클래스에 대한 타입 인수 지정 {:#fix-specify-type-arguments-for-the-generic-subclass}

제네릭 서브클래스가 타입 인수를 지정하지 않으면, 분석기는 `dynamic` 타입을 추론합니다. 
이로 인해 오류가 발생할 가능성이 높습니다.

서브클래스에서 타입을 지정하여, 예를 수정할 수 있습니다.

<?code-excerpt "lib/common_fixes_analysis.dart (type-arguments)" replace="/Superclass /Superclass[!<int\x3E!] /g"?>
```dart tag=passes-sa
class Superclass<T> {
  void method(T param) { ... }
}

class Subclass extends Superclass[!<int>!] {
  @override
  void method(int param) { ... }
}
```

분석기를 _strict raw types_ 모드에서 사용하는 것을 고려하세요. 
이렇게 하면, 코드에서 제네릭 타입 인수를 지정할 수 있습니다. 
다음은 프로젝트의 `analysis_options.yaml` 파일에서, 
strict raw types를 활성화하는 예입니다.

```yaml
analyzer:
  language:
    strict-raw-types: true
```

분석기 동작을 커스터마이즈하는 방법에 대해 자세히 알아보려면, 
[정적 분석 커스터마이즈](/tools/analysis)을 참조하세요.

<hr>

<a id ="assigning-mismatched-types"></a>
### 예상치 못한 컬렉션 요소 타입 {:#unexpected-collection-element-type}

<?code-excerpt "analyzer-results-stable.txt" retain="/common_fixes_analysis.*'double' can't be assigned to a variable of type 'int'./" replace="/. Try.*'int'. / /g; /'\S+'/'...'/g; /-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - A value of type '...' can't be assigned to a variable of type '...' - invalid_assignment
```

이는 간단한 dynamic 컬렉션을 만들 때 가끔 발생하는데, 
분석기가 예상치 못한 방식으로 타입을 추론합니다. 
나중에 다른 타입의 값을 추가하면, 분석기가 문제를 보고합니다.

#### Example

다음 코드는 여러 개의 (`String`, `int`) 쌍으로 맵을 초기화합니다. 
분석기는 해당 맵이 `<String, int>` 타입이라고 추론하지만, 
코드는 `<String, dynamic>` 또는 `<String, num>` 중 하나를 가정하는 듯합니다.
코드가 (`String`, `double`) 쌍을 추가하면, 분석기는 다음과 같이 불평합니다.

<?code-excerpt "lib/common_fixes_analysis.dart (inferred-collection-types)" replace="/1.5/[!1.5!]/g"?>
```dart tag=fails-sa
// Map<String, int>로 추론됨
var map = {'a': 1, 'b': 2, 'c': 3};
map['d'] = [!1.5!];
```

<?code-excerpt "analyzer-results-stable.txt" retain="/common_fixes_analysis.*'double' can't be assigned to a variable of type 'int'/" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - A value of type 'double' can't be assigned to a variable of type 'int'. Try changing the type of the variable, or casting the right-hand type to 'int'. - invalid_assignment
```

#### 수정: 타입을 명시적으로 지정하세요 {:#fix-specify-the-type-explicitly}

이 예제는 맵의 타입을 `<String, num>`으로 명시적으로 정의하여 수정할 수 있습니다.

<?code-excerpt "lib/common_fixes_analysis.dart (inferred-collection-types-ok)" replace="/<.*?\x3E/[!$&!]/g"?>
```dart tag=passes-sa
var map = [!<String, num>!]{'a': 1, 'b': 2, 'c': 3};
map['d'] = 1.5;
```

또는, 이 맵이 어떤 값이든 허용하도록 하려면, 타입을 `<String, dynamic>`으로 지정하세요.

<hr>

<a id="constructor-initialization-list"></a>
### 생성자 초기화 리스트 super() 호출 {:#constructor-initialization-list-super-call}

<?code-excerpt "analyzer-results-stable.txt" retain="/The superconstructor call must be last in an initializer list.*/" replace="/Animal/.../g; /-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - The superconstructor call must be last in an initializer list: '...'. - super_invocation_not_last
```

이 오류는 `super()` 호출이 생성자의 초기화 리스트의 마지막이 아닐 때 발생합니다.

#### Example

<?code-excerpt "lib/common_fixes_analysis.dart (super-goes-last)" replace="/super/[!$&!]/g; /_HoneyBadger/HoneyBadger/g"?>
```dart tag=fails-sa
HoneyBadger(Eats food, String name)
    : [!super!](food),
      _name = name { ... }
```

<?code-excerpt "analyzer-results-stable.txt" retain="/The superconstructor call must be last in an initializer list.*/" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - The superconstructor call must be last in an initializer list: 'Animal'. - super_invocation_not_last
```

#### Fix: Put the `super()` call last {:#fix-put-the-super-call-last}

컴파일러는 마지막에 나타나는 `super()` 호출에 의존하면, 더 간단한 코드를 생성할 수 있습니다.

`super()` 호출을 이동하여, 이 오류를 수정하세요.

<?code-excerpt "lib/common_fixes_analysis.dart (super-goes-last-ok)" replace="/super/[!$&!]/g"?>
```dart tag=passes-sa
HoneyBadger(Eats food, String name)
    : _name = name,
      [!super!](food) { ... }
```

<hr>

<a name="uses-dynamic-as-bottom"></a>
### 인수 타입 ...은 매개변수 타입 ...에 할당될 수 없습니다. {:#the-argument-type-cant-be-assigned-to-the-parameter-type}

<?code-excerpt "analyzer-results-stable.txt" retain="/The argument type.*bool Function/" replace="/'bool.*?\)'/'...'/g; /-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - The argument type '...' can't be assigned to the parameter type '...'. - argument_type_not_assignable
```

Dart 1.x에서, `dynamic`은 컨텍스트에 따라 [상위 타입][top type](모든 유형의 슈퍼 타입)과 
[하위 타입][bottom type](모든 타입의 하위 타입)이었습니다. 
즉, 예를 들어 `String` 타입의 매개변수가 있는 함수를, 
`dynamic` 매개변수가 있는 함수 타입을 기대하는 위치에 할당하는 것이 유효했습니다.

그러나 Dart 2에서 `dynamic`(또는 `Object?`와 같은 다른 _상위_ 타입)이 아닌, 
매개변수 타입을 사용하면 컴파일 타임 오류가 발생합니다.

#### Example

<?code-excerpt "lib/common_fixes_analysis.dart (func-fail)" replace="/String/[!$&!]/g"?>
```dart tag=fails-sa
void filterValues(bool Function(dynamic) filter) {}
filterValues(([!String!] x) => x.contains('Hello'));
```

<?code-excerpt "analyzer-results-stable.txt" retain="/The argument type.*bool Function/" replace="/-(.*?)-/-/g"?>
```plaintext
error - The argument type 'bool Function(String)' can't be assigned to the parameter type 'bool Function(dynamic)'. - argument_type_not_assignable
```

#### 수정: 타입 매개변수를 추가하거나, *또는* dynamic에서 명시적으로 캐스팅 {:#fix-add-type-parameters-or-cast-from-dynamic-explicitly}

가능하면, 타입 매개변수를 추가하여 이 오류를 방지하세요.

<?code-excerpt "lib/common_fixes_analysis.dart (func-T)" replace="/<\w+\x3E/[!$&!]/g"?>
```dart tag=passes-sa
void filterValues[!<T>!](bool Function(T) filter) {}
filterValues[!<String>!]((x) => x.contains('Hello'));
```

그렇지 않으면 캐스팅을 사용하세요:

<?code-excerpt "lib/common_fixes_analysis.dart (func-cast)" replace="/([Ff]ilter)1/$1/g; /as \w+/[!$&!]/g"?>
```dart tag=passes-sa
void filterValues(bool Function(dynamic) filter) {}
filterValues((x) => (x [!as String!]).contains('Hello'));
```

<hr>

### 잘못된 타입 추론 {:#incorrect-type-inference}

드물게 Dart의 타입 추론은 제네릭 생성자 호출에서, 
함수 리터럴 인수에 대해 잘못된 타입을 추론할 수 있습니다. 
이는 주로 `Iterable.fold`에 영향을 미칩니다.

#### Example

다음 코드에서, 타입 추론은 `a`의 타입이 `Null`이라고 추론합니다.

<?code-excerpt "lib/common_fixes_analysis.dart (type-inf-null)"?>
```dart tag=fails-sa
var ints = [1, 2, 3];
var maximumOrNull = ints.fold(null, (a, b) => a == null || a < b ? b : a);
```

#### 수정: 명시적 타입 인수로 적절한 타입을 제공하세요 {:#fix-supply-appropriate-type-as-explicit-type-argument}

<?code-excerpt "lib/common_fixes_analysis.dart (type-inf-fix)"?>
```dart tag=passes-sa
var ints = [1, 2, 3];
var maximumOrNull =
    ints.fold<int?>(null, (a, b) => a == null || a < b ? b : a);
```

<hr>

### 충돌하는 슈퍼인터페이스 {:#conflicting-superinterfaces}

두 개 이상의 슈퍼인터페이스를 `implements`하는 클래스는, 
모든 슈퍼인터페이스의 모든 멤버에 대해 유효한 오버라이드를 구현할 수 있어야 합니다.
주어진 이름을 가진 각 멤버는 슈퍼인터페이스에서 호환되는 서명이 필요합니다.

슈퍼인터페이스는 충돌하는 제네릭을 포함해서는 안 됩니다. 
클래스는 간접 슈퍼인터페이스를 포함하여, 
`C<A>`와 `C<B>`를 모두 구현할 수 없습니다.

#### Example

다음 코드에서, 클래스 `C`는 충돌하는 제네릭 인터페이스를 가지고 있습니다. 
일부 멤버에 대한 유효한 오버라이드의 정의는 불가능할 것입니다.

<?code-excerpt "lib/common_fixes_analysis.dart (conflicting-generics)"?>
```dart tag=fails-sa
abstract class C implements List<int>, Iterable<num> {}
```

#### 수정: 일관된 제네릭을 사용하거나, 반복되는 전이적 인터페이스를 피하십시오. {:#fix-use-consistent-generics-or-avoid-repeating-transitive-interfaces}

<?code-excerpt "lib/common_fixes_analysis.dart (compatible-generics)"?>
```dart tag=passes-sa
abstract class C implements List<int> {}
```

<a id="common-errors-and-warnings"></a>

## 런타임 오류 {:#runtime-errors}

이 섹션에서 설명하는 오류는 [런타임](/language/type-system#runtime-checks)에서 보고됩니다.

### 잘못된 캐스트 {:#invalid-casts}

타입 안전성을 보장하기 위해, Dart는 어떤 경우에는 _런타임_ 검사를 삽입해야 합니다. 
다음의 `assumeStrings` 메서드를 고려하세요.

<?code-excerpt "test/strong_test.dart (downcast-check)" replace="/string = objects/[!$&!]/g"?>
```dart tag=passes-sa
void assumeStrings(dynamic objects) {
  List<String> strings = objects; // 런타임 다운캐스트 확인
  String string = strings[0]; // 문자열 값을 기대합니다.
}
```

`strings`에 대한 할당은 `dynamic`을 `List<String>`로 암묵적으로 _다운캐스팅_ 합니다.
(마치 `as List<String>`라고 쓴 것처럼)
따라서, 런타임에 `objects`에 전달하는 값이 `List<String>`이면, 캐스트가 성공합니다.

그렇지 않으면, 캐스트가 런타임에 실패합니다.

<?code-excerpt "test/strong_test.dart (fail-downcast-check)" replace="/\[.*\]/[!$&!]/g"?>
```dart tag=runtime-fail
assumeStrings(<int>[![1, 2, 3]!]);
```

<?code-excerpt "test/strong_test.dart (downcast-check-msg)" replace="/const msg = ./Exception: /g; /.;//g"?>
```plaintext
Exception: type 'List<int>' is not a subtype of type 'List<String>'
```

#### 수정: 타입을 조이거나(tighten) 수정하세요 {:#fix-tighten-or-correct-types}

때로는, 타입이 없는 경우(lack of a type), 
특히 빈 컬렉션의 경우, 의도한 대로 타입이 지정된 컬렉션 대신, 
`<dynamic>` 컬렉션이 생성된다는 것을 의미합니다. 
명시적 타입 인수를 추가하면 도움이 될 수 있습니다.

<?code-excerpt "test/strong_test.dart (typed-list-lit)" replace="/<String\x3E/[!$&!]/g"?>
```dart tag=runtime-success
var list = [!<String>!][];
list.add('a string');
list.add('another');
assumeStrings(list);
```

로컬 변수를 보다 정확하게 입력하고, 추론을 활용할 수도 있습니다.

<?code-excerpt "test/strong_test.dart (typed-list)" replace="/<String\x3E/[!$&!]/g"?>
```dart tag=runtime-success
List[!<String>!] list = [];
list.add('a string');
list.add('another');
assumeStrings(list);
```

(JSON이나 외부 데이터 소스와 같이) 직접 만들지 않은 컬렉션으로 작업하는 경우,
 `Iterable` 구현(예: `List`)에서 제공하는 [cast()][] 메서드를 사용할 수 있습니다.

다음은 선호되는 솔루션의 예입니다.
객체 타입을 좁히는 것입니다.

<?code-excerpt "test/strong_test.dart (cast)" replace="/cast/[!$&!]/g"?>
```dart tag=runtime-success
Map<String, dynamic> json = fetchFromExternalSource();
var names = json['names'] as List;
assumeStrings(names.[!cast!]<String>());
```

## 부록 {:#appendix}

### covariant 키워드 {:#the-covariant-keyword}

일부(드물게 사용되는) 코딩 패턴은 매개변수의 타입을 무효한 하위 타입으로 재정의하여, 
타입을 강화하는 데 의존합니다. 
이 경우 `covariant` 키워드를 사용하여 분석기에 의도적으로 이를 수행하고 있음을 알릴 수 있습니다. 
이렇게 하면 정적 오류가 제거되고 대신 런타임에 무효한 인수 타입이 있는지 확인합니다.

다음은 `covariant`를 사용하는 방법을 보여줍니다.

<?code-excerpt "lib/covariant.dart" replace="/covariant/[!$&!]/g"?>
```dart tag=passes-sa
class Animal {
  void chase(Animal x) { ... }
}

class Mouse extends Animal { ... }

class Cat extends Animal {
  @override
  void chase([!covariant!] Mouse x) { ... }
}
```

이 예에서는 하위 타입에서 `covariant`를 사용하는 방법을 보여주지만, 
`covariant` 키워드는 슈퍼클래스나 서브클래스 메서드에 둘 수 있습니다. 
일반적으로 슈퍼클래스 메서드가 가장 적합한 위치입니다. 
`covariant` 키워드는 단일 매개변수에 적용되며 setters와 필드에서도 지원됩니다.

[bottom type]: https://en.wikipedia.org/wiki/Bottom_type
[cast()]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable/cast.html
[Iterable]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable-class.html
[List]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/List-class.html
[top type]: https://en.wikipedia.org/wiki/Top_type
