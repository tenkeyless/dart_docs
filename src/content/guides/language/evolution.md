---
# title: Dart language evolution
title: Dart 언어 진화
# short-title: Language evolution
short-title: 언어 진화
# description: Notable changes and additions to the Dart programming language.
description: Dart 프로그래밍 언어의 주요 변경 사항 및 추가 사항.
lastVerified: 2024-08-04
---

이 페이지는 Dart 프로그래밍 언어의 주요 변경 사항과 추가 사항을 나열합니다.

* 가장 최근에 지원되는 언어 버전에 대한 구체적인 세부 정보를 알아보려면, [언어 문서][language documentation] 또는 [언어 사양][language specification]을 확인하세요.
* Dart SDK의 변경 사항에 대한 전체 내역은 [SDK 변경 로그][SDK changelog]를 참조하세요.
* [언어 버전][language versioned] 변경 사항을 포함한 중요한 변경 사항(breaking changes)에 대한 전체 내역은 [중요 변경 사항][Breaking changes] 페이지를 확인하세요.

2.0 이후에 도입된 언어 기능을 사용하려면, 
Dart가 해당 기능을 처음 지원했을 때의 릴리스보다 낮은 [SDK 제약 조건][SDK constraint]을 설정하세요.

**예:** [2.12][]에서 도입된 null 안전성을 사용하려면, 
`pubspec.yaml` 파일에서 `2.12.0`을 하위 제약 조건으로 설정하세요.

```yaml
environment:
  sdk: '>=2.12.0 <3.0.0'
```

[2.12]: #dart-2-12
[SDK constraint]: /tools/pub/pubspec#sdk-constraints
[language versioned]: #language-versioning
[Breaking changes]: /resources/breaking-changes

:::tip
Dart 언어에 대해 논의, 조사, 추가된 기능을 검토하려면, 
Dart 언어 GitHub 저장소의 [언어 퍼널(language funnel)][language funnel] 추적기를 확인하세요.
:::


## 각 릴리스의 변경 사항 {:#changes-in-each-release}

### Dart 3.5

_2024년 8월 6일 출시_
| [Dart 3.5 발표](https://medium.com/dartlang/dart-3-5-6ca36259fa2f)

Dart 3.5는 새로운 언어 기능을 추가하지 않았지만, 
타입 추론 중에 고려되는 컨텍스트에 사소한 변경을 가했습니다. 
여기에는 다음과 같은 언어가 아닌 버전 변경 사항(non-language versioned changes)이 포함됩니다.

* `await` 표현식의 컨텍스트가 `dynamic`인 경우, 표현식의 피연산자에 대한 컨텍스트는 이제 `FutureOr<_>`입니다.
* 전체 if-null 표현식(`e1 ?? e2`)에 대한 컨텍스트가 `dynamic`인 경우, 
  `e2`에 대한 컨텍스트는 이제 `e1`의 정적 타입입니다.

### Dart 3.4
_Released 14 May 2024_
| [Dart 3.4 announcement](https://medium.com/dartlang/dart-3-4-bd8d23b4462a)

Dart 3.4 made several improvements related to type analysis. These include:

* Improvements to the type analysis of conditional expressions,
  if-null expressions and assignments, and switch expressions.
* Aligning the pattern context type schema for cast patterns with the spec.
* Making the type schema for the null-aware spread operator (`...?`)
  nullable for maps and set literals, to match the behavior of list literals.

### Dart 3.3
_Released 15 February 2024_
| [Dart 3.3 announcement](https://medium.com/dartlang/dart-3-3-325bf2bf6c13)

Dart 3.3 added some enhancements to the language:

* [Extension types][] are a new feature in Dart that allow zero-cost wrapping
  of an existing type. They are similar to wrapper classes and extension methods,
  but with implementation differences and different tradeoffs.

  ```dart
  extension type Meters(int value) {
    String get label => '${value}m';
    Meters operator +(Meters other) => Meters(value + other.value);
  }

  void main() {
    var m = Meters(42); // Has type `Meters`.
    var m2 = m + m; // OK, type `Meters`.
    // int i = m; // Compile-time error, wrong type.
    // m.isEven; // Compile-time error, no such member.
    assert(identical(m, m.value)); // Succeeds.
  }
  ```

* Abstract getters are now promotable under the rules of
  [private final field promotion][], if there are no conflicting declarations.

### Dart 3.2
_Released 15 November 2023_
| [Dart 3.2 announcement](https://medium.com/dartlang/dart-3-2-c8de8fe1b91f)

Dart 3.2 added enhancements to flow analysis, including:

* Expanded [type promotion](/null-safety/understanding-null-safety#type-promotion-on-null-checks)
  to work on private final fields. Previously only available for
  local variables and parameters, now private final fields can promote to
  non-nullable types through null checks and `is` tests. For example,
  the following code is now sound:

  ```dart
  class Example {
    final int? _privateField;
  
    Example(this._privateField);
  
    void f() {
      if (_privateField != null) {
        // _privateField has now been promoted; you can use it without
        // null checking it.
        int i = _privateField; // OK
      }
    }
  }
  
  // Private field promotions also work from outside of the class:
  void f(Example x) {
    if (x._privateField != null) {
      int i = x._privateField; // OK
    }
  }
  ```
  
  For more information on when private final fields can and can't promote, check
  out [Fixing type promotion failures](/tools/non-promotion-reasons).

* Corrected inconsistencies in type promotion behavior of
  [if-case](/language/branches#if-case) statements
  where the value being matched against throws an exception.


### Dart 3.1
_Released 16 August 2023_
| [Dart 3.1 announcement](https://medium.com/dartlang/dart-3-1-a-retrospective-on-functional-style-programming-in-dart-3-a1f4b3a7cdda)

Dart 3.1 added no new features and made no changes to the language.

### Dart 3.0
_Released 10 May 2023_
| [Dart 3.0 announcement](https://medium.com/dartlang/announcing-dart-3-53f065a10635)

Dart 3.0 introduced several new major language features:

* [Patterns][], a new category of grammar that lets you
  match and destructure values.
* [Records][], a new type that lets you aggregate
  multiple values of different types in a single function return.
* [Class modifiers][], a new set of keywords that let you
  control how a class or mixin can be used.
* [Switch expressions][], a new form of multi-way branching
  allowed where expressions are expected.
* [If-case clauses][], a new conditional construct that matches a value
  against a pattern and executes the then or else branch, depending
  on whether the pattern matches.

Dart 3.0 also introduced a few breaking language changes:

* Class declarations without the [`mixin`][] class modifier
  can no longer be applied as mixins.
* It is now a compile time error if a colon (`:`) is used as the separator
  before the default value of an optional named parameter.
  Use an equal sign (`=`) instead.
* It is now a compile-time error if a `continue` statement targets a
  label that is not attached to a
  loop statement (`for`, `do`, and `while`) or a `switch` member.

:::note
The 3.0 release of the Dart SDK dropped support for
[language versions][] before 2.12.
:::

[Patterns]: /language/patterns
[Records]: /language/records
[Class modifiers]: /language/class-modifiers
[Switch expressions]: /language/branches#switch-expressions
[If-case clauses]: /language/branches#if-case
[`mixin`]: /language/mixins#class-mixin-or-mixin-class
[language versions]: #language-versioning

### Dart 2.19
_Released 25 January 2023_

Dart 2.19 introduced some precautions surrounding type inference.
These include:

* More flow analysis flags for unreachable code cases.
* No longer delegate inaccessible private names to `noSuchMethod`.
* Top-level type inference throws on cyclic dependencies.

Dart 2.19 also introduced support for unnamed libraries.
Library directives, used for appending library-level doc comments and
annotations, can and [should][] now be written without a name:

```dart
/// A really great test library.
@TestOn('browser')
library;
```

[should]: /effective-dart/style#dont-explicitly-name-libraries

### Dart 2.18
_Released 30 August 2022_
| [Dart 2.18 announcement](https://medium.com/dartlang/dart-2-18-f4b3101f146c)

Dart 2.18 enhanced type inference.
This change allows information flow between arguments in generic function calls.
Before 2.18, if you didn't specify an argument's type in some methods,
Dart reported errors.
These type errors cited potential null occurrences.
With 2.18, the compiler infers the argument type
from other values in an invocation.
You don't need to specify the argument type inline.

Dart 2.18 also discontinued support for mixin classes that don't extend
`Object`.

To learn more about these features, check out:

* [Type argument inference][]
* [Adding features to a class: mixins][]

[Type argument inference]: /language/type-system#type-argument-inference
[Adding features to a class: mixins]: /language/mixins

### Dart 2.17
_Released 11 May 2022_
| [Dart 2.17 announcement](https://medium.com/dartlang/dart-2-17-b216bfc80c5d)

Dart 2.17 expanded enum functionality with enhanced enums.
Enhanced enums allow enum declarations to define members
including fields, constructors, methods, getters, etc.

Dart 2.17 added support for super-initializer parameters in
constructors.
Super parameters allow you to avoid having to manually pass each
parameter into the super invocation of a non-redirecting constructor.
You can instead use super parameters to forward parameters to a
superclass constructor.

Dart 2.17 removed some restrictions on named arguments.
Named arguments can now be freely interleaved with positional arguments.
As of Dart 2.17, you can write the following code:

```dart
void main() {
  test(skip: true, 'A test description', () {
    // Very long function body here...
  });
}
```

To learn more about these features, check out:

* [Enhanced enums][]
* [Super parameters][]
* [Named parameters][]

[Enhanced enums]: /language/enums#declaring-enhanced-enums
[Super parameters]: /language/constructors#super-parameters
[Named parameters]: /language/functions#named-parameters

### Dart 2.16
_Released 3 February 2022_
| [Dart 2.16 announcement](https://medium.com/dartlang/dart-2-15-7e7a598e508a)

Dart 2.16 added no new features to the Dart language.
It did expand the Dart tools.

### Dart 2.15
_Released 8 December 2021_
| [Dart 2.15 announcement](https://medium.com/dartlang/dart-2-15-7e7a598e508a)

Dart 2.15 improved support for function pointers, known as _tear-offs._
In particular, constructor tear-offs are now supported.

### Dart 2.14
_Released 8 September 2021_
| [Dart 2.14 announcement](https://medium.com/dartlang/announcing-dart-2-14-b48b9bb2fb67)

Dart 2.14 added the unsigned shift (or _triple-shift_) operator (`>>>`).
This new operator works like `>>`,
except that it always fills the most significant bits with zeros.

To learn more about these operators, check out [bitwise and shift operators][].

[bitwise and shift operators]: /language/operators#bitwise-and-shift-operators

Dart 2.14 removed some restrictions on type arguments.
You can pass type arguments to annotations and use a generic function
type as a type argument.
As of Dart 2.14, you can write the following code:

```dart
@TypeHelper<int>(42, "The meaning")
late List<T Function<T>(T)> idFunctions;
var callback = [<T>(T value) => value];
late S Function<S extends T Function<T>(T)>(S) f;
```

### Dart 2.13
_Released 19 May 2021_
| [Dart 2.13 announcement](https://medium.com/dartlang/announcing-dart-2-13-c6d547b57067)

Dart 2.13 expanded support for **[type aliases][]** (`typedef`).
Type aliases used to work only for function types
but now work for any type.
You can use the new name created with a type alias
anywhere the original type could be used.

Dart 2.13 improved the struct support in **[Dart FFI][]**,
adding support for inline arrays and packed structs.

### Dart 2.12
_Released 3 March 2021_
| [Dart 2.12 announcement](https://medium.com/dartlang/announcing-dart-2-12-499a6e689c87)

Dart 2.12 added support for **[sound null safety][]**.
When you opt into null safety, types in your code are non-nullable by default,
meaning that variables can't contain null unless you say they can.
With null safety, your runtime null-dereference errors
turn into edit-time analysis errors.

In Dart 2.12, **[Dart FFI][]** graduated from beta to the stable channel.

### Dart 2.10
_Released 1 October 2020_
| [Dart 2.10 announcement](https://medium.com/dartlang/announcing-dart-2-10-350823952bd5)

Dart 2.10 added no new features to the Dart language.

### Dart 2.9
_Released 5 August 2020_

Dart 2.9 added no new features to the Dart language.

### Dart 2.8
_Released 6 May 2020_
| [Dart 2.8 announcement](https://medium.com/dartlang/announcing-dart-2-8-7750918db0a)

Dart 2.8 didn't add any features to the Dart language. It did
contain a number of preparatory [breaking changes][2.8 breaking changes]
to improve nullability-related usability and performance for [null safety][].

### Dart 2.7
_Released 11 December 2019_
| [Dart 2.7 announcement](https://medium.com/dartlang/dart-2-7-a3710ec54e97)

Dart 2.7 added support for **[extension methods][]**,
enabling you to add functionality to any type
—-even types you don't control—-
with the brevity and auto-complete experience of regular method calls.

The following example extends the `String` class from
`dart:core` with a new `parseInt()` method:

```dart
extension ParseNumbers on String {
  int parseInt() {
    return int.parse(this);
  }
}

void main() {
  int i = '42'.parseInt();
  print(i);
}
```

### Dart 2.6
_Released 5 November 2019_
| [Dart 2.6 announcement](https://medium.com/dartlang/dart2native-a76c815e6baf)

Dart 2.6 introduced a
[breaking change (dart-lang/sdk#37985)]({{site.repo.dart.sdk}}/issues/37985).
Constraints where `Null` serves as a subtype of `FutureOr<T>`
now yield `Null` as the solution for `T`.

For example: The following code now prints `Null`.
Before Dart 2.6, it printed `dynamic`.
The anonymous closure `() {}` returns the `Null` type.

```dart
import 'dart:async';

void foo<T>(FutureOr<T> Function() f) { print(T); }

main() { foo(() {}); }
```

### Dart 2.5
_Released 10 September 2019_
| [Dart 2.5 announcement](https://medium.com/dartlang/announcing-dart-2-5-super-charged-development-328822024970)

Dart 2.5 didn't add any features to the Dart language, but it did add
support for [calling native C code][] from Dart code
using a new **core library, `dart:ffi`.**

### Dart 2.4
_Released 27 June 2019_


Dart 2.4 introduces a breaking change
[dart-lang/sdk#35097]({{site.repo.dart.sdk}}/issues/35097).

Dart now enforces covariance of type variables used in super-interfaces.
For example: Prior to this release Dart accepted, but now rejects,
the following code:

```dart
class A<X> {};
class B<X> extends A<void Function(X)> {};
```

You can now use `async` as an identifier in
asynchronous and generator functions.

### Dart 2.3
_Released 8 May 2019_
| [Dart 2.3 announcement](https://medium.com/dartlang/announcing-dart-2-3-optimized-for-building-user-interfaces-e84919ca1dff)

Dart 2.3 added three operators designed to improve code that performs
list manipulation, such as declarative UI code.

The **[spread operator][]**
enables unpacking the elements from one list into another.
In the following example, the list returned by `buildMainElements()`
is unpacked into the list being passed to the `children` argument:

```dart
Widget build(BuildContext context) {
  return Column(children: [
    Header(),
    ...buildMainElements(),
    Footer(),
  ]);
}
```

The **[collection if][]** operator enables adding elements conditionally.
The following example adds a `FlatButton` element unless
the app displays the last page:

```dart
Widget build(BuildContext context) {
  return Column(children: [
    Text(mainText),
    if (page != pages.last)
      FlatButton(child: Text('Next')),
  ]);
}
```

The **[collection for][]** operator enables building repeated elements.
The following example adds one `HeadingAction` element for
each section in `sections`:

```dart
Widget build(BuildContext context) {
  return Column(children: [
    Text(mainText),
    for (var section in sections)
      HeadingAction(section.heading),
  ]);
}
```


### Dart 2.2
_Released 26 February 2019_
| [Dart 2.2 announcement](https://medium.com/dartlang/announcing-dart-2-2-faster-native-code-support-for-set-literals-7e2ab19cc86d)

Dart 2.2 added support for **[set literals][]**:

```dart
const Set<String> currencies = {'EUR', 'USD', 'JPY'};
```

### Dart 2.1
_Released 15 November 2018_
| [Dart 2.1 announcement](https://medium.com/dartlang/announcing-dart-2-1-improved-performance-usability-9f55fca6f31a)

Dart 2.1 added support for **int-to-double conversion**,
allowing developers to set `double` values using integer literals.
This feature removed the annoyance of being forced to use a
`double` literal (for example, `4.0`)
when the value was an integer in concept.

In the following Flutter code, `horizontal` and `vertical` have type `double`:

```dart
padding: const EdgeInsets.symmetric(
  horizontal: 4,
  vertical: 8,
)
```

### Dart 2.0
_Released 22 February 2018_
| [Dart 2.0 announcement](https://medium.com/dartlang/announcing-dart-2-80ba01f43b6)

Dart 2.0 implemented a new **[sound type system][]**.
Before Dart 2.0, types weren't fully sound, and
Dart relied heavily on runtime type checking.
Dart 1.x code had to be migrated to Dart 2.

## 언어 버전 관리 {:#language-versioning}

단일 Dart SDK는 동시에 여러 버전의 Dart 언어를 지원할 수 있습니다. 
컴파일러는 코드가 타겟팅하는 버전을 결정하고, 해당 버전에 따라 코드를 해석합니다.

언어 버전 관리가 Dart가 [null safety][]와 같은 호환되지 않는 기능을 도입하는 드문 경우에 중요해집니다. 
Dart가 중대한 변경 사항을 도입하면, 컴파일된 코드가 더 이상 컴파일되지 않을 수 있습니다. 
언어 버전 관리를 사용하면, 각 라이브러리의 언어 버전을 설정하여, 호환성을 유지할 수 있습니다.

null safety의 경우, Dart SDK 2.12~2.19에서는 null safety를 사용하도록 코드를 업데이트하도록 _선택_ 할 수 있습니다. 
Dart는 언어 버전 관리를 사용하여, null 안전하지 않은 코드가 null 안전 코드와 함께 실행되도록 허용합니다. 
이 결정을 통해, null 안전하지 않은 코드에서 null 안전 코드로 마이그레이션할 수 있습니다. 
앱이나 패키지가 호환되지 않는 기능이 있는 새 언어 버전으로 마이그레이션하는 방법의 예를 검토하려면, 
[null safety로 마이그레이션](/null-safety/migration-guide)을 확인하세요.

각 패키지에는 `pubspec.yaml` 파일의 SDK 제약 조건의 **하한(lower bound)**과 동일한 기본 언어 버전이 있습니다.

**예:** `pubspec.yaml` 파일의 다음 항목은 이 패키지가 기본적으로 Dart 2.18 언어 버전을 사용한다는 것을 나타냅니다.

```yaml
environment:
  sdk: '>=2.18.0 <3.0.0'
```

### 언어 버전 번호 {:#language-version-numbers}

Dart는 언어 버전을 마침표로 구분된 두 개의 숫자로 포맷합니다. 
이는 major 버전 번호와 minor 버전 번호로 읽힙니다. 
minor 버전 번호는 중요한 변경 사항(breaking changes)을 도입할 수도 있습니다.

Dart 릴리스는 언어 버전에 패치 번호를 추가할 수 있습니다. 
패치는 버그 수정을 제외하고는 언어를 변경해서는 안 됩니다. 
예를 들어 Dart 2.18.3은 Dart 2.18 SDK 언어 버전의 최신 릴리스 역할을 합니다.

각 Dart SDK는 주요 버전 번호 내의 모든 언어 버전을 지원합니다. 
즉, Dart SDK 2.18.3은 언어 버전 2.0에서 2.18까지 지원하지만, Dart 1.x는 지원하지 않습니다.

SDK 버전에서 언어 버전을 파생하는 것은 다음을 의미합니다.

* SDK의 마이너 버전이 출시될 때마다 새로운 언어 버전이 나타납니다. 
  실제로 이러한 언어 버전 중 다수는 이전 버전과 매우 유사한 방식으로 작동하며, 서로 완벽하게 호환됩니다. 
  예를 들어 Dart 2.9 언어는 Dart 2.8 언어와 매우 유사하게 작동합니다.

* SDK의 패치 릴리스가 출시될 때 새로운 언어 기능을 도입할 수 없습니다. 
  예를 들어 2.18.3 릴리스는 언어 버전 2.18로 *유지*됩니다. 
  2.18.2, 2.18.1 및 2.18.0과 호환되어야 합니다.

### 라이브러리별 언어 버전 선택 {:#per-library-language-version-selection}

기본적으로 패키지의 모든 Dart 파일은 동일한 언어 버전을 사용합니다. 
Dart는 기본 언어 버전을 `pubspec.yaml` 파일에 지정된 SDK 제약 조건의 하한으로 식별합니다. 
때때로 Dart 파일은 이전 언어 버전을 사용해야 할 수 있습니다. 
예를 들어, 패키지의 모든 파일을 동시에 null 안전성으로 마이그레이션할 수 없을 수 있습니다.

Dart는 라이브러리별 언어 버전 선택을 지원합니다. 
패키지의 나머지 부분과 다른 언어 버전을 선택하려면, [Dart 라이브러리][Dart library]에 다음 형식의 주석을 포함해야 합니다.

```dart
// @dart = <major>.<minor>
```

예를 들어:

```dart
// 이 파일의 내용에 대한 설명입니다.
// @dart = 2.17
import 'dart:math';
...
```

`@dart` 문자열은 `//` 주석(`///` 또는 `/*`가 아님)에 있어야 하며, 
파일의 모든 Dart 코드 앞에 나타나야 합니다. 
`@dart` 및 버전 문자열 내부를 제외하고는 공백(탭과 공백)은 중요하지 않습니다. 
이전 예에서 보듯이 다른 주석은 `@dart` 주석 앞에 나타날 수 있습니다.

Dart 팀이 이 버전 관리 방법을 개발한 이유와 방법을 알아보려면, 
[언어 버전 관리 사양][language versioning specification]을 확인하세요.

[2.8 breaking changes]: {{site.repo.dart.sdk}}/issues/40686
[calling native C code]: /interop/c-interop
[collection for]: /language/collections#control-flow-operators
[collection if]: /language/collections#control-flow-operators
[Dart library]: /guides/libraries/create-packages#organizing-a-package
[Dart FFI]: /interop/c-interop
[extension methods]: /language/extension-methods
[Extension types]: /language/extension-types
[language funnel]: {{site.repo.dart.lang}}/projects/1
[language specification]: /guides/language/spec
[language documentation]: /language
[language versioning specification]: {{site.repo.dart.lang}}/blob/main/accepted/2.8/language-versioning/feature-specification.md#dart-language-versioning
[null safety]: /null-safety
[private final field promotion]: /tools/non-promotion-reasons
[SDK changelog]: {{site.repo.dart.sdk}}/blob/main/CHANGELOG.md
[set literals]: /language/collections#sets
[sound null safety]: /null-safety
[sound type system]: /language/type-system
[spread operator]: /language/collections#spread-operators
[type aliases]: /language/typedefs
