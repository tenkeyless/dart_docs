---
# title: Introduction to Dart
title: Dart 소개
# description: A brief introduction to Dart programs and important concepts.
description: Dart 프로그램과 중요한 개념에 대한 간략한 소개입니다.
# short-title: Dart basics
short-title: 다트 기초
nextpage:
  url: /language/variables
  # title: Variables
  title: 변수
---

이 페이지에서는 주요 기능 샘플을 통해 Dart 언어에 대한 간략한 소개를 제공합니다.

Dart 언어에 대해 자세히 알아보려면, 왼쪽 메뉴의 **언어** 아래에 나열된 심층적인 개별 주제 페이지를 방문하세요.

Dart의 핵심 라이브러리에 대한 내용은 [핵심 라이브러리 설명서](/libraries)를 확인하세요. 
또한 [Dart 치트시트](/resources/dart-cheatsheet)를 확인하여, 보다 상호 작용적인 소개를 확인할 수 있습니다.

## Hello World {:#hello-world}

모든 앱에는 실행이 시작되는 최상위 `main()` 함수가 필요합니다. 
값을 명시적으로 반환하지 않는 함수는 `void` 반환 타입을 갖습니다. 
콘솔에 텍스트를 표시하려면 최상위 `print()` 함수를 사용할 수 있습니다.

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dart
void main() {
  print('Hello, World!');
}
```

Dart의 [`main()` 함수][the `main()` function]에 대해 자세히 알아보세요. 
여기에는 명령줄 인수에 대한 선택적 매개변수도 포함됩니다.

[the `main()` function]: /language/functions#the-main-function

## 변수 {:#variables}

[type-safe](/language/type-system) Dart 코드에서도, 
`var`를 사용하여 명시적으로 타입을 지정하지 않고도 대부분의 변수를 선언할 수 있습니다. 
타입 추론 덕분에 이러한 변수의 타입은 초기 값에 의해 결정됩니다.

<?code-excerpt "misc/test/samples_test.dart (var)"?>
```dart
var name = 'Voyager I';
var year = 1977;
var antennaDiameter = 3.7;
var flybyObjects = ['Jupiter', 'Saturn', 'Uranus', 'Neptune'];
var image = {
  'tags': ['saturn'],
  'url': '//path/to/saturn.jpg'
};
```

Dart의 변수, 기본값, `final` 및 `const` 키워드, 
정적 타입에 대해 [자세히 알아보세요](/language/variables).

## 흐름 제어 문 {:#control-flow-statements}

Dart는 일반적인 흐름 제어 문을 지원합니다.

<?code-excerpt "misc/test/samples_test.dart (control-flow)"?>
```dart
if (year >= 2001) {
  print('21st century');
} else if (year >= 1901) {
  print('20th century');
}

for (final object in flybyObjects) {
  print(object);
}

for (int month = 1; month <= 12; month++) {
  print(month);
}

while (year < 2016) {
  year += 1;
}
```

[`break` 및 `continue`](/language/loops), 
[`switch` 및 `case`](/language/branches), 
[`assert`](/language/error-handling#assert)를 포함하여, 
Dart의 흐름 제어 문에 대해 자세히 알아보세요.

## 함수 {:#functions}

우리는 각 함수의 인수와 반환 값의 [타입을 지정하는 것](/effective-dart/design#types)을 권장합니다.

<?code-excerpt "misc/test/samples_test.dart (functions)"?>
```dart
int fibonacci(int n) {
  if (n == 0 || n == 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

var result = fibonacci(20);
```

단축형 `=>` (_arrow_) 구문은 단일 문장을 포함하는 함수에 유용합니다. 
이 구문은 익명 함수를 인수로 전달할 때 특히 유용합니다.

<?code-excerpt "misc/test/samples_test.dart (arrow)"?>
```dart
flybyObjects.where((name) => name.contains('turn')).forEach(print);
```

익명 함수(`where()`에 대한 인수)를 보여주는 것 외에도, 
이 코드는 함수를 인수로 사용할 수 있음을 보여줍니다. 
최상위 `print()` 함수는 `forEach()`에 대한 인수입니다.

Dart의 함수에 대한 [자세한 내용](/language/functions)(선택적 매개변수, 기본 매개변수 값, 어휘적 범위 포함)을 읽어보세요.

## 주석 {:#comments}

Dart 주석은 일반적으로 `//`로 시작합니다.

```dart
// 이것은 일반적인 한 줄짜리 주석입니다.

/// 이것은 라이브러리, 클래스 및 멤버를 문서화하는 데 사용되는 문서 주석입니다. 
/// IDE 및 dartdoc과 같은 도구는 문서 주석을 특별히 취급합니다.

/* 이와 같은 주석도 지원됩니다. */
```

[Dart의 주석](/language/comments)에 대해 자세히 알아보고, 문서화 툴의 작동 방식도 알아보세요.

## Imports {:#imports}

다른 라이브러리에 정의된 API에 액세스하려면, `import`를 사용합니다.

<?code-excerpt "misc/test/samples_test.dart (import)" plaster="none"?>
```dart
// 핵심 라이브러리 import
import 'dart:math';

// 외부 패키지에서 라이브러리 import
import 'package:test/test.dart';

// 파일 import
import 'path/to/my_other_file.dart';
```

Dart의 [라이브러리](/language/libraries)와 가시성(visibility), 
라이브러리 접두사, `show`와 `hide`, 
그리고 `deferred` 키워드를 통한 지연 로딩(lazy loading)에 대해 자세히 알아보세요.

## 클래스 {:#classes}

다음은 세 개의 속성, 두 개의 생성자, 그리고 메서드가 있는 클래스의 예입니다. 
속성 중 하나는 직접 설정할 수 없으므로, getter 메서드(변수 대신)를 사용하여 정의됩니다. 
이 메서드는 문자열 보간을 사용하여, 문자열 리터럴 내부에 변수의 문자열 동등물을 출력합니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (class)"?>
```dart
class Spacecraft {
  String name;
  DateTime? launchDate;

  // 읽기 전용 non-final 속성
  int? get launchYear => launchDate?.year;

  // 멤버에게 할당하기 위한 구문적 편의 기능을 갖춘 생성자입니다.
  Spacecraft(this.name, this.launchDate) {
    // 초기화 코드는 여기에 옵니다.
  }

  // 기본 생성자로 전달되는 명명된 생성자입니다.
  Spacecraft.unlaunched(String name) : this(name, null);

  // 메서드.
  void describe() {
    print('Spacecraft: $name');
    // getter에서는 타입 승격이 작동하지 않습니다.
    var launchDate = this.launchDate;
    if (launchDate != null) {
      int years = DateTime.now().difference(launchDate).inDays ~/ 365;
      print('Launched: $launchYear ($years years ago)');
    } else {
      print('Unlaunched');
    }
  }
}
```

문자열 보간, 리터럴, 표현식 및 `toString()` 메서드를 포함하여, 
[문자열](/language/built-in-types#strings)에 대한 자세한 내용을 읽어보세요.

`Spacecraft` 클래스를 다음과 같이 사용할 수 있습니다.

<?code-excerpt "misc/test/samples_test.dart (use-class)" plaster="none"?>
```dart
var voyager = Spacecraft('Voyager I', DateTime(1977, 9, 5));
voyager.describe();

var voyager3 = Spacecraft.unlaunched('Voyager III');
voyager3.describe();
```

초기화 리스트, 선택적 `new`와 `const`, 리디렉션 생성자, `factory` 생성자, getter, setter 등을 포함하여, 
Dart의 [클래스](/language/classes)에 대해 자세히 알아보세요.

## 열거형 {:#enums}

열거형은 미리 정의된 값이나 인스턴스 집합을 열거하는 방식으로, 
해당 타입의 다른 인스턴스가 존재하지 않도록 보장합니다.

다음은 미리 정의된 행성 타입의 간단한 리스트를 정의하는 간단한 `enum`의 예입니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (simple-enum)"?>
```dart
enum PlanetType { terrestrial, gas, ice }
```

다음은 행성을 설명하는 클래스의 향상된 열거형 선언의 예입니다. 
정의된 상수 인스턴스 집합, 즉 우리 태양계의 행성이 있습니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (enhanced-enum)"?>
```dart
/// 태양계의 여러 행성과 그 속성 중 일부를 나열한 열거형입니다.
enum Planet {
  mercury(planetType: PlanetType.terrestrial, moons: 0, hasRings: false),
  venus(planetType: PlanetType.terrestrial, moons: 0, hasRings: false),
  // ···
  uranus(planetType: PlanetType.ice, moons: 27, hasRings: true),
  neptune(planetType: PlanetType.ice, moons: 14, hasRings: true);

  /// 상수 생성 생성자
  const Planet(
      {required this.planetType, required this.moons, required this.hasRings});

  /// 모든 인스턴스 변수는 final 입니다.
  final PlanetType planetType;
  final int moons;
  final bool hasRings;

  /// 향상된 열거형은 getters 및 기타 메서드를 지원합니다.
  bool get isGiant =>
      planetType == PlanetType.gas || planetType == PlanetType.ice;
}
```

`Planet` 열거형을 다음과 같이 사용할 수 있습니다.

<?code-excerpt "misc/test/samples_test.dart (use-enum)" plaster="none"?>
```dart
final yourPlanet = Planet.earth;

if (!yourPlanet.isGiant) {
  print('Your planet is not a "giant planet".');
}
```

Dart의 [열거형](/language/enums)에 대해 자세히 알아보세요. 
여기에는 향상된 열거형 요구 사항, 자동으로 도입된 속성, 열거형 값 이름 액세스, switch 문 지원 등이 포함됩니다.

## 상속 {:#inheritance}

Dart는 단일 상속을 갖습니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (extends)"?>
```dart
class Orbiter extends Spacecraft {
  double altitude;

  Orbiter(super.name, DateTime super.launchDate, this.altitude);
}
```

클래스 [확장](/language/extend), 선택적 `@override` 어노테이션 등에 대해 자세히 알아보세요.

## Mixins {:#mixins}

믹스인은 여러 클래스 계층에서 코드를 재사용하는 방법입니다. 다음은 믹스인 선언입니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (mixin)"?>
```dart
mixin Piloted {
  int astronauts = 1;

  void describeCrew() {
    print('Number of astronauts: $astronauts');
  }
}
```

클래스에 믹스인의 기능을 추가하려면, 믹스인으로 클래스를 확장하기만 하면 됩니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (mixin-use)" replace="/with/[!$&!]/g"?>
```dart
class PilotedCraft extends Spacecraft [!with!] Piloted {
  // ···
}
```

`PilotedCraft`는 이제 `astronauts` 필드와 `describeCrew()` 메서드를 갖습니다.

[믹스인](/language/mixins)에 대해 자세히 알아보세요.

## 인터페이스와 추상 클래스 {:#interfaces-and-abstract-classes}

모든 클래스는 암묵적으로 인터페이스를 정의합니다.
따라서, 어떤 클래스든 구현할 수 있습니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (implements)"?>
```dart
class MockSpaceship implements Spacecraft {
  // ···
}
```

[암묵적 인터페이스](/language/classes#implicit-interfaces)나 
명시적 [`interface` 키워드](/language/class-modifiers#interface)에 대해 자세히 알아보세요.

구체적(concrete) 클래스에 의해 확장(또는 구현)될 추상 클래스를 만들 수 있습니다. 
추상 클래스는 추상 메서드(빈 본문 포함)를 포함할 수 있습니다.

<?code-excerpt "misc/lib/samples/spacecraft.dart (abstract)" replace="/abstract/[!$&!]/g"?>
```dart
[!abstract!] class Describable {
  void describe();

  void describeWithEmphasis() {
    print('=========');
    describe();
    print('=========');
  }
}
```

`Describable`을 확장하는 모든 클래스에는 `describeWithEmphasis()` 메서드가 있으며, 
이 메서드는 `describe()`의 익스텐더 구현을 호출합니다.

[추상 클래스](/language/class-modifiers#abstract)와 메서드에 대해 자세히 알아보세요.

## Async {:#async}

콜백 지옥을 피하고, `async`와 `await`를 사용하여 코드를 훨씬 더 읽기 쉽게 만들어보세요.

<?code-excerpt "misc/test/samples_test.dart (async)" replace="/async/[!$&!]/g"?>
```dart
const oneSecond = Duration(seconds: 1);
// ···
Future<void> printWithDelay(String message) [!async!] {
  await Future.delayed(oneSecond);
  print(message);
}
```

위의 메서드는 다음과 같습니다.

<?code-excerpt "misc/test/samples_test.dart (future-then)"?>
```dart
Future<void> printWithDelay(String message) {
  return Future.delayed(oneSecond).then((_) {
    print(message);
  });
}
```

다음 예제에서 보듯이, `async`와 `await`는 비동기 코드를 읽기 쉽게 만드는 데 도움이 됩니다.

<?code-excerpt "misc/test/samples_test.dart (await)"?>
```dart
Future<void> createDescriptions(Iterable<String> objects) async {
  for (final object in objects) {
    try {
      var file = File('$object.txt');
      if (await file.exists()) {
        var modified = await file.lastModified();
        print(
            'File for $object already exists. It was modified on $modified.');
        continue;
      }
      await file.create();
      await file.writeAsString('Start describing $object in this file.');
    } on IOException catch (e) {
      print('Cannot create description for $object: $e');
    }
  }
}
```

`async*`를 사용하면, 스트림을 읽기 편한 방식으로 빌드할 수도 있습니다.

<?code-excerpt "misc/test/samples_test.dart (async-star)"?>
```dart
Stream<String> report(Spacecraft craft, Iterable<String> objects) async* {
  for (final object in objects) {
    await Future.delayed(oneSecond);
    yield '${craft.name} flies by $object';
  }
}
```

`async` 함수, `Future`, `Stream` 및 비동기 루프(`await for`)를 포함한, 
[비동기](/language/async) 지원에 대해 자세히 알아보세요.

## 예외 {:#exceptions}

예외를 발생시키려면, `throw`를 사용합니다.

<?code-excerpt "misc/test/samples_test.dart (throw)"?>
```dart
if (astronauts == 0) {
  throw StateError('No astronauts.');
}
```

예외를 캐치하려면, `try` 명령문을 `on` 또는 `catch` (또는 둘 다)와 함께 사용합니다.

<?code-excerpt "misc/test/samples_test.dart (try)" replace="/on.*e\)/[!$&!]/g"?>
```dart
Future<void> describeFlybyObjects(List<String> flybyObjects) async {
  try {
    for (final object in flybyObjects) {
      var description = await File('$object.txt').readAsString();
      print(description);
    }
  } [!on IOException catch (e)!] {
    print('Could not describe object: $e');
  } finally {
    flybyObjects.clear();
  }
}
```

위의 코드는 비동기적입니다. `try`는 동기 코드와 `async` 함수의 코드 모두에 적용됩니다.

스택 추적, `rethrow`, `Error`와 `Exception`의 차이점을 포함한,
[예외](/language/error-handling#exceptions)에 대해 자세히 알아보세요.

## 중요한 개념 {:#important-concepts}

Dart 언어에 대해 계속 배우면서, 다음 사실과 개념을 명심하세요.

- 변수에 넣을 수 있는 모든 것은 *객체*이고, 모든 객체는 *클래스*의 인스턴스입니다. 
  숫자, 함수, `null`도 객체입니다. 
  `null`을 제외하고([사운드 널 세이프티][ns]를 활성화한 경우),
  모든 객체는 [`Object`][] 클래스에서 상속됩니다.


  :::version-note
  [널 세이프티][ns]는 Dart 2.12에서 도입되었습니다.
  널 세이프티를 사용하려면 최소 2.12의 [언어 버전][language version]이 필요합니다.
  :::

- Dart는 강력하게 타입화되어 있지만, Dart는 타입을 추론할 수 있기 때문에, 타입 어노테이션은 선택 사항입니다. 
  `var number = 101`에서 `number`는 `int` 타입으로 추론됩니다.

- [널 세이프티][ns]를 활성화하면, 변수는 `null`을 포함할 수 없습니다. 
  변수의 타입 끝에 물음표(`?`)를 붙여, 변수를 null 허용 변수로 만들 수 있습니다. 
  예를 들어, `int?` 타입의 변수는 정수일 수도 있고, `null`일 수도 있습니다. 
  표현식이 `null`로 평가되지 않는다는 것을 _알고_ 있지만, 
  Dart가 동의하지 않는 경우 `!`를 추가하여, null이 아니라고 단언할 수 있습니다.
  (그리고 null인 경우 예외를 throw할 수 있습니다) 
  예: `int x = nullableButNotNullInt!`

- 모든 타입이 허용된다는 것을 명시적으로 말하고 싶을 때는, `Object?` 타입(널 세이프티를 활성화한 경우), 
  `Object`를 사용하거나, 런타임까지 타입 검사를 연기해야 ​​하는 경우에는,
  [특수 타입 `dynamic`][ObjectVsDynamic]을 사용합니다.

- Dart는 `List<int>` (정수 리스트) 또는 `List<Object>` (모든 타입의 객체 리스트)와 같은 제네릭 타입을 지원합니다.

- Dart는 최상위 함수(예: `main()`)와 클래스나 객체에 연결된 함수(각각 *정적* 및 *인스턴스 메서드*)를 지원합니다. 
  함수 내에서 함수를 만들 수도 있습니다. (*중첩* 또는 *로컬 함수*)

- 마찬가지로, Dart는 최상위 *변수*와 클래스나 객체에 연결된 변수(정적 및 인스턴스 변수)를 지원합니다. 
  인스턴스 변수는 때때로 *필드* 또는 *속성*이라고도 합니다.

- Java와 달리, Dart에는 `public`, `protected`, `private`라는 키워드가 없습니다. 
  식별자가 밑줄(`_`)로 시작하면, 라이브러리에 private 입니다. 
  자세한 내용은 [라이브러리 및 import][Libraries and imports]를 참조하세요.

- *식별자*는 문자나 밑줄(`_`)로 시작할 수 있으며, 그 뒤에 이러한 문자와 숫자를 조합한 문자가 올 수 있습니다.

- Dart에는 *표현식*(런타임 값이 있음)과 *문장*(값이 없음)이 모두 있습니다. 
  예를 들어, [조건식][conditional expression] `condition ? expr1 : expr2`는 `expr1` 또는 `expr2` 값을 갖습니다. 
  이것을 값이 없는 [if-else 문][if-else statement]과 비교해보세요. 
  구문은 종종 하나 이상의 식을 포함하지만, 식은 구문을 직접 포함할 수 없습니다.

- Dart 도구는 두 가지 종류의 문제를 보고할 수 있습니다. _경고(warnings)_ 와 _오류(errors)_ 입니다. 
  - 경고는 코드가 작동하지 않을 수 있다는 표시일 뿐, 프로그램 실행을 방해하지는 않습니다. 
  - 오류는 컴파일 타임 또는 런타임 중 하나일 수 있습니다. 
    - 컴파일 타임 오류는 코드가 전혀 실행되지 못하게 하고, 
    - 런타임 오류는 코드가 실행되는 동안 [예외][exception]가 발생합니다.

## 추가 리소스 {:#additional-resources}

[코어 라이브러리 문서](/libraries/dart-core) 및 [Dart API 참조]({{site.dart-api}})에서, 
더 많은 문서 및 코드 샘플을 찾을 수 있습니다. 
이 사이트의 코드는 [Dart 스타일 가이드](/effective-dart/style)의 규칙을 따릅니다.

[Dart language specification]: /guides/language/spec
[Comments]: /language/comments
[built-in types]: /language/built-in-types
[Strings]: /language/built-in-types#strings
[The main() function]: /language/functions#the-main-function
[ns]: /null-safety
[`Object`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Object-class.html
[language version]: /guides/language/evolution#language-versioning
[ObjectVsDynamic]: /effective-dart/design#avoid-using-dynamic-unless-you-want-to-disable-static-checking
[Libraries and imports]: /language/libraries
[conditional expression]: /language/operators#conditional-expressions
[if-else statement]: /language/branches#if
[exception]: /language/error-handling#exceptions
