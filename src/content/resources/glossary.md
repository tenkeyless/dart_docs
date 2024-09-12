---
# title: Glossary
title: 용어집
# description: A glossary reference for terminology used across dart.dev.
description: dart.dev에서 사용되는 용어에 대한 용어집입니다.
---

다음은 Dart 문서 전체에서 사용되는 용어의 정의입니다.

## 상수 컨텍스트 {:#constant-context}

_상수 컨텍스트_ 는 `const` 키워드를 포함할 필요가 없는 코드 영역입니다. 
해당 영역의 모든 것이 상수여야 한다는 사실에 의해 암시되기 때문입니다. 
다음 위치는 상수 컨텍스트입니다.

* `const` 키워드로 접두사가 붙은 리스트, 맵 또는 세트 리터럴 내부의 모든 것. 예:

  ```dart
  var l = const [/*상수 컨텍스트*/];
  ```

* 상수 생성자 호출 내부의 인수. 예:

  ```dart
  var p = const Point(/*상수 컨텍스트*/);
  ```

* `const` 키워드로 접두사가 붙은 변수의 초기화자입니다. 예:

  ```dart
  const v = /*상수 컨텍스트*/;
  ```

* 어노테이션

* `case` 절의 표현식. 예:

  ```dart
  void f(int e) {
    switch (e) {
      case /*상수 컨텍스트*/:
        break;
    }
  }
  ```

## 명확한 할당 {:#definite-assignment}

명확한 할당 분석은, 코드의 각 지점에서 각 로컬 변수에 대해, 다음 중 어느 것이 true인지 판별하는 프로세스입니다.

- 변수에 확실히 값이 할당되었습니다. (_확실히 할당됨(definitely assigned)_)
- 변수에 확실히 값이 할당되지 않았습니다. (_확실히 할당되지 않음(definitely unassigned)_)
- 해당 지점에 도달하기 위해 수행한 실행 경로에 따라, 
  변수에 값이 할당되었을 수도 있고 할당되지 않았을 수도 있습니다.

명확한 할당 분석은 값이 할당되지 않았을 수 있는 변수가 참조되는 위치나, 
한 번만 값을 할당할 수 있는 변수에, 이미 값이 할당된 후에, 값이 다시 할당되는 위치와 같은,
코드의 문제를 찾는 데 도움이 됩니다.

예를 들어, 다음 코드에서 변수 `s`는 `print`에 인수로 전달될 때 확실히 할당되지 않습니다.

```dart
void f() {
  String s;
  print(s);
}
```

하지만 다음 코드에서는, 변수 `s`가 명확하게 할당되었습니다.

```dart
void f(String name) {
  String s = 'Hello $name!';
  print(s);
}
```

명확한 할당 분석은 여러 실행 경로가 있을 때 변수가 확정적으로 할당되었는지(또는 할당되지 않았는지)를 알려줄 수도 있습니다. 
다음 코드에서 `print` 함수는 실행이 `if` 문의 true 또는 false 분기를 거치면 호출되지만, 
`s`는 어떤 분기를 취하든 할당되기 때문에, `print`에 전달되기 전에 확정적으로 할당됩니다.

```dart
void f(String name, bool casual) {
  String s;
  if (casual) {
    s = 'Hi $name!';
  } else {
    s = 'Hello $name!';
  }
  print(s);
}
```

흐름 분석에서, `if` 문의 끝은 _join_ 이라고 합니다. (두 개 이상의 실행 경로가 다시 합쳐지는 곳입니다.) 
조인이 있는 경우, 분석은 병합되는 모든 경로에 따라 확실히 할당된 경우 **변수가 확실히 할당되었다**고 하고, 
모든 경로에 따라 확실히 할당 해제된 경우 **확실히 할당 해제되었다**고 말합니다.

때로는 변수에 한 경로에서는 값이 할당되지만 다른 경로에서는 할당되지 않는 경우가 있는데, 
이 경우 변수에 값이 할당되었을 수도 있고 할당되지 않았을 수도 있습니다. 
다음 예에서 `if` 문의 진정한 분기는 실행되었을 수도 있고 실행되지 않았을 수도 있으므로, 
변수에 값이 할당되었을 수도 있고 할당되지 않았을 수도 있습니다.

```dart
void f(String name, bool casual) {
  String s;
  if (casual) {
    s = 'Hi $name!';
  }
  print(s);
}
```

`s`에 값을 할당하지 않는 false 분기가 있는 경우에도 마찬가지입니다.

루프 분석은 조금 더 복잡하지만 동일한 기본 추론을 따릅니다. 
예를 들어, `while` 루프의 조건은 항상 실행되지만, 본문은 실행될 수도 있고 그렇지 않을 수도 있습니다. 
따라서 `if` 문과 마찬가지로, 
`while` 문의 끝에 조건이 `true`인 경로와 조건이 `false`인 경로 사이에 조인이 있습니다.

자세한 내용은 [명확한 할당의 사양][definiteAssignmentSpec]을 참조하세요.

[definiteAssignmentSpec]: {{site.repo.dart.lang}}/blob/main/resources/type-system/flow-analysis.md

## 함수 {:#function}

달리 언급되지 않는 한, *함수*라는 용어는 최상위 함수, 로컬 함수, 정적 메서드, 인스턴스 메서드를 말합니다.

자세한 내용은 [함수][_functions_]에 대한 문서를 참조하세요.

[_functions_]: /language/functions

## Irrefutable 패턴 {:#irrefutable-pattern}

_Irrefutable(반박할 수 없는) 패턴_ 은 항상 일치하는 패턴입니다. 
Irrefutable 패턴은 _Irrefutable 컨텍스트_ 에 나타날 수 있는 유일한 패턴입니다. 
[_declaration_][] 및 [_assignment_][] 패턴 컨텍스트입니다.

[_declaration_]: /language/patterns#variable-declaration 
[_assignment_]: /language/patterns#variable-assignment

## Mixin 애플리케이션 {:#mixin-application}

_믹스인 애플리케이션_ 은 믹스인이 클래스에 적용될 때 생성되는 클래스입니다. 
예를 들어, 다음 선언을 고려하세요.

```dart
class A {}

mixin M {}

class B extends A with M {}
```

클래스 `B`는 `M`에서 `A`로의 믹스인 애플리케이션의 하위 클래스이며, 때때로 `A+M`으로 명명됩니다. 
클래스 `A+M`은 `A`의 하위 클래스이며, `M`에서 복사된 멤버를 갖습니다.

다음과 같이 정의하여, 믹스인 애플리케이션에 실제 이름을 지정할 수 있습니다.

```dart
class A {}

mixin M {}

class A_M = A with M;
```

`A_M`의 선언이 주어졌을 때, 다음의 `B` 선언은 원래 예에서의 `B` 선언과 동등합니다.

```dart
class B extends A_M {}
```

## 오버라이드 추론 {:#override-inference}

오버라이드 추론은 메서드 선언에서 누락된 모든 타입이, 
오버라이드하는 메서드 또는 메서드의 해당 타입을 기반으로 추론되는 프로세스입니다.

후보 메서드(타입 정보가 누락된 메서드)가 단일 상속된 메서드를 오버라이드하는 경우, 
오버라이드된 메서드의 해당 타입이 추론됩니다. 
예를 들어 다음 코드를 고려해 보세요.

```dart
class A {
  int m(String s) => 0;
}

class B extends A {
  @override
  m(s) => 1;
}
```

`B`에서 `m`의 선언은 반환 타입과 매개변수 타입이 모두 누락되어 후보입니다. 
단일 메서드(`A`의 메서드 `m`)를 오버라이드하기 때문에, 
오버라이드된 메서드의 타입이 누락된 타입을 추론하는 데 사용되고, 
`B`의 메서드가 `int m(String s) => 1;`로 선언된 것과 같습니다.

후보 메서드가 여러 메서드를 오버라이드하고, 
오버라이드된 메서드 중 하나인 함수 타입 M<sub>s</sub>가, 
다른 모든 오버라이드된 메서드의 함수 타입의 슈퍼타입인 경우, 
M<sub>s</sub>가 누락된 타입을 추론하는 데 사용됩니다. 
예를 들어, 다음 코드를 고려해 보겠습니다.

```dart
class A {
  int m(num n) => 0;
}

class B {
  num m(int i) => 0;
}

class C implements A, B {
  @override
  m(n) => 1;
}
```

`C`에서 `m`의 선언은 반환 타입과 매개변수 타입이 모두 없기 때문에, 
오버라이드 추론의 후보입니다. 
`A`의 `m`과 `B`의 `m`을 모두 오버라이드하므로, 
누락된 타입을 추론할 수 있는 타입을 선택해야 합니다. 
하지만 `A`의 `m` 함수 타입(`int Function(num)`)이 
`B`의 `m` 함수 타입(`num Function(int)`)의 슈퍼 타입이기 때문에, 
`A`의 함수가 누락된 타입을 추론하는 데 사용됩니다. 
결과는 `C`에서 메서드를 `int m(num n) => 1;`로 선언하는 것과 같습니다.

오버라이드된 메서드 중 어느 것도, 
다른 모든 오버라이드된 메서드의 슈퍼 타입인 함수 타입을 갖지 않으면 오류입니다.

## Part 파일 {:#part-file}

part 파일은 `part of` 지시어를 포함하는 Dart 소스 파일입니다. 
사용 지침은 [효과적인 Dart][part] 항목을 방문하세요.

[part]: /effective-dart/usage#do-use-strings-in-part-of-directives

## 잠재적으로 non-nullable {:#potentially-non-nullable}

타입은 명시적으로 non-nullable이거나 타입 매개변수인 경우, _잠재적으로 non-nullable_ 입니다.

타입은 물음표가 뒤에 오지 않는 타입 이름인 경우, 명시적으로 non-nullable 입니다. 
`Null` 및 `dynamic`과 같이 항상 null을 허용하는 몇 가지 타입이 있으며, 
`FutureOr`는 물음표가 뒤에 오지 않고 타입 인수가 non-nullable인 경우(예: `FutureOr<String>`)에만, 
non-nullable 입니다.

타입 매개변수는 실제 런타임 타입(타입 인수로 지정된 타입)이 non-nullable 일 수 있으므로, 
잠재적으로 non-nullable 입니다. 
예를 들어, `class C<T> {}`의 선언이 주어지면, 
타입 `C`는 `C<int>`와 같이 non-nullable 타입 인수와 함께 사용될 수 있습니다.

## Public 라이브러리 {:#public-library}

공개 라이브러리는 패키지의 `lib` 디렉토리에 위치하지만, 
`lib/src` 디렉토리에는 없는 라이브러리입니다.

## Refutable 패턴 {:#refutable-pattern}

_Refutable(반박 가능한) 패턴_ 은 패턴이 값과 일치하는지 확인하기 위해 값에 대해 테스트할 수 있는 패턴입니다. 
일치하지 않으면 패턴은 일치를 _반박(refutes)_ 하거나 거부(denies)합니다. 
Refutable 패턴은 [_일치하는 컨텍스트_][_matching contexts_]에 나타납니다.

[_matching contexts_]: /language/patterns#matching

## Subclass {:#subclass}

_하위 클래스_ 는 [`extends`](/language/extend) 키워드나, 
[mixin 애플리케이션](#mixin-application)을 사용하여, 
다른 클래스의 구현을 상속하는 클래스입니다.

```dart
class A extends B {} // A는 B의 하위 클래스(subclass)이고 B는 A의 상위 클래스(superclass)입니다.

class B1 extends A with M {} // B1에는 슈퍼클래스 `A with M`이 있고, 이 슈퍼클래스에는 슈퍼클래스 A가 있습니다.
```

하위 클래스 관계는 연관된 [subtype](#subtype) 관계도 의미합니다. 
예를 들어, `class A`는 클래스 `A`의 인스턴스가 거주하는(inhabit) 연관된 타입 `A`를 암묵적으로 정의합니다. 
따라서, `class A extends B`는 클래스 `A`가 `B`의 하위 클래스라는 것을 선언할 뿐만 아니라, 
*타입* `A`가 타입 `B`의 *하위 타입*임을 확립합니다.

하위 클래스 관계는 하위 타입 관계의 하위 집합입니다. 
문서에 "`S`는 `T`의 하위 타입이어야 합니다"라고 나와 있을 때, 
`S`가 `T`의 하위 클래스가 되어도 괜찮습니다. 
그러나 그 반대는 사실이 아닙니다. 
모든 하위 타입이 하위 클래스는 아닙니다. 
자세한 내용은 [subtype](#subtype) 항목을 참조하세요.

## Subtype {:#subtype}

_하위 타입_ 관계는 특정 타입의 값이 다른 타입인 상위 타입의 값이 예상되는 곳에서 대체 가능한 관계입니다.
예를 들어, `S`가 `T`의 하위 타입인 경우, `T` 타입의 값이 예상되는 곳에서 `S` 타입의 값을 대체할 수 있습니다.

하위 타입은 상위 타입의 모든 연산(그리고 아마도 일부 추가 연산)을 지원합니다. 
실제로 이는 하위 타입의 값을 상위 타입을 예상하는 모든 위치에 할당할 수 있으며, 
상위 타입의 모든 메서드가 하위 타입에서 사용 가능하다는 것을 의미합니다.

이는 적어도 정적으로는 사실입니다. 특정 API는 연산에 따라 런타임에 대체를 허용하지 않을 수 있습니다.

일부 하위 타입 관계는, 
nullable 타입(예: `int`는 `int?`의 하위 타입) 및 
함수 타입(예: `String Function()`는 `void Function()`의 하위 타입)과 같이, 
타입의 구조를 기반으로 합니다.

하위 타입은 [구현](/language/classes#implicit-interfaces) 또는 
[상속](/language/extend)(직접 또는 간접)을 통해 클래스에 대해 도입될 수도 있습니다.

```dart
class A implements B {} // A는 B의 하위 타입이지만, B의 하위 클래스는 아닙니다.

class C extends D {} // C는 D의 하위 타입이자, 하위 클래스입니다.
```
