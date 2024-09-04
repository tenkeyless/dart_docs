---
# title: Records
title: 레코드
# description: Summary of the record data structure in Dart.
description: Dart의 레코드 데이터 구조 요약.
prevpage:
  url: /language/built-in-types
  # title: Built-in types
  title: 빌트인 타입
nextpage:
  url: /language/collections
  # title: Collections
  title: 컬렉션
---

:::version-note
기록에는 최소 3.0의 [언어 버전][language version]이 필요합니다.
:::

레코드는 익명, 불변(immutable), 집계(aggregate) 타입입니다. 
다른 [컬렉션 타입][collection types]과 마찬가지로, 여러 객체를 단일 객체로 묶을 수 있습니다. 
다른 컬렉션 타입과 달리, 레코드는 고정 크기, 이기종(heterogeneous)이며, 타입이 지정됩니다.

레코드는 실제 값(real values)입니다. 
변수에 저장하고, 중첩하고, 함수에 전달하고, 리스트, 맵, 세트와 같은 데이터 구조에 저장할 수 있습니다.

## Record 구문 {:#record-syntax}

_레코드 표현식_ 은 괄호로 묶인 명명된 필드나 위치 필드의 쉼표로 구분된 리스트입니다.

<?code-excerpt "language/test/records_test.dart (record-syntax)"?>
```dart
var record = ('first', a: 2, b: true, 'last');
```

_레코드 타입 어노테이션(Record type annotations)_ 은 괄호로 묶인 타입의 쉼표로 구분된 리스트입니다. 
레코드 타입 어노테이션을 사용하여 반환 타입과 매개변수 타입을 정의할 수 있습니다. 
예를 들어, 다음 `(int, int)` 문은 레코드 타입 어노테이션입니다.

<?code-excerpt "language/test/records_test.dart (record-type-annotation)"?>
```dart
(int, int) swap((int, int) record) {
  var (a, b) = record;
  return (b, a);
}
```

레코드 표현식과 타입 어노테이션의 필드는 
[매개변수와 인수][parameters and arguments]가 함수에서 작동하는 방식을 반영합니다. 
위치 필드는 괄호 안에 직접 들어갑니다.

<?code-excerpt "language/test/records_test.dart (record-type-declaration)"?>
```dart
// 변수 선언의 레코드 타입 어노테이션:
(String, int) record;

// 레코드 표현식으로 초기화합니다.
record = ('A string', 123);
```

레코드 타입 어노테이션에서, 명명된 필드는 모든 위치 필드 뒤에 있는 중괄호로 구분된 타입-이름 쌍 섹션 안에 들어갑니다. 레코드 표현식에서, 이름은 각 필드 값 앞에 오고 그 뒤에 콜론이 옵니다.

<?code-excerpt "language/test/records_test.dart (record-type-named-declaration)"?>
```dart
// 변수 선언의 레코드 타입 어노테이션:
({int a, bool b}) record;

// 레코드 표현식으로 초기화합니다.
record = (a: 123, b: true);
```

레코드 타입의 명명된 필드의 이름은, [레코드의 타입 정의](#record-types) 또는 그 _모양_ 의 일부입니다. 
이름이 다른 명명된 필드가 있는 두 레코드는 타입이 다릅니다.

<?code-excerpt "language/test/records_test.dart (record-type-mismatched-names)"?>
```dart
({int a, int b}) recordAB = (a: 1, b: 2);
({int x, int y}) recordXY = (x: 3, y: 4);

// 컴파일 오류! 이 레코드는 동일한 타입이 없습니다.
// recordAB = recordXY;
```

레코드 타입 어노테이션에서, *위치(positional)* 필드의 이름을 지정할 수도 있지만, 
이러한 이름은 오로지 문서화를 위한 것이며, 레코드의 타입에는 영향을 미치지 않습니다.

<?code-excerpt "language/test/records_test.dart (record-type-matched-names)"?>
```dart
(int a, int b) recordAB = (1, 2);
(int x, int y) recordXY = (3, 4);

recordAB = recordXY; // OK.
```

이는 함수 선언이나 함수 typedef의 위치 매개변수가 이름을 가질 수 있지만, 
해당 이름이 함수의 시그니처에 영향을 미치지 않는 방식과 유사합니다.

자세한 내용과 예는, [레코드 타입](#record-types) 및 [레코드 동등성](#record-equality)을 확인하세요.

## Record 필드 {:#record-fields}

레코드 필드는 빌트인 getters를 통해 액세스할 수 있습니다. 
레코드는 변경할 수 없으므로(immutable), 필드에는 setters가 없습니다.

명명된 필드는 같은 이름의 getters를 노출합니다. 
위치 필드는 `$<position>` 이름의 getters를 노출하고, 명명된 필드를 건너뜁니다.

<?code-excerpt "language/test/records_test.dart (record-getters)"?>
```dart
var record = ('first', a: 2, b: true, 'last');

print(record.$1); // 'first' 출력
print(record.a); // 2 출력
print(record.b); // true 출력
print(record.$2); // 'last' 출력
```

레코드 필드 액세스를 더욱 간소화하려면, [패턴][pattern] 페이지를 확인하세요.

## Record 타입 {:#record-types}

개별 레코드 타입에 대한 타입 선언은 없습니다. 
레코드는 필드 타입에 따라 구조적으로 타입이 지정됩니다. 
레코드의 _모양_(필드 집합, 필드 타입 및 이름(있는 경우))은 레코드의 타입을 고유하게 결정합니다.

레코드의 각 필드에는 고유한 타입이 있습니다. 
필드 타입은 동일한 레코드 내에서 다를 수 있습니다. 
타입 시스템은 레코드에서 액세스하는 모든 필드의 타입을 인식합니다.

<?code-excerpt "language/test/records_test.dart (record-getters-two)"?>
```dart
(num, Object) pair = (42, 'a');

var first = pair.$1; // 정적 타입 `num`, 런타임 타입 `int`.
var second = pair.$2; // 정적 타입 `Object`, 런타임 타입 `String`.
```

동일한 필드 집합을 가진 레코드를 생성하는 두 개의 관련 없는 라이브러리를 고려합니다. 
타입 시스템은 라이브러리가 서로 결합되지 않았더라도, 해당 레코드가 동일한 타입임을 이해합니다.

## Record 동등성 {:#record-equality}

두 레코드는 같은 _모양_(필드 집합)을 가지고 있고, 해당 필드의 값이 같으면 같습니다. 
명명된 필드 _순서_ 는 레코드 모양의 일부가 아니므로, 명명된 필드의 순서는 동등성에 영향을 미치지 않습니다.

예를 들어:

<?code-excerpt "language/test/records_test.dart (record-shape)"?>
```dart
(int x, int y, int z) point = (1, 2, 3);
(int r, int g, int b) color = (1, 2, 3);

print(point == color); // 'true'를 출력합니다.
```

<?code-excerpt "language/test/records_test.dart (record-shape-mismatch)"?>
```dart
({int x, int y, int z}) point = (x: 1, y: 2, z: 3);
({int r, int g, int b}) color = (r: 1, g: 2, b: 3);

print(point == color); // 'false'를 출력합니다. Lint: 관련 없는 타입에 대해 같음.
```

레코드는 필드의 구조에 따라, 자동으로 `hashCode` 및 `==` 메서드를 정의합니다.

## 여러 값 리턴 {:#multiple-returns}

레코드를 사용하면 함수가 여러 값을 함께 묶어 반환할 수 있습니다. 
반환에서 레코드 값을 검색하려면, [패턴 매칭][pattern]을 사용하여 값을 로컬 변수로 [destructure][]합니다.

<?code-excerpt "language/test/records_test.dart (record-multiple-returns)"?>
```dart
// 레코드로 여러 값을 반환합니다.
(String name, int age) userInfo(Map<String, dynamic> json) {
  return (json['name'] as String, json['age'] as int);
}

final json = <String, dynamic>{
  'name': 'Dash',
  'age': 10,
  'color': 'blue',
};

// 위치 필드가 있는 레코드 패턴을 사용하여 구조 분해:
var (name, age) = userInfo(json);

/* 다음과 같습니다:
  var info = userInfo(json);
  var name = info.$1;
  var age  = info.$2;
*/
```

[명명된 필드](#record-fields)를 사용하여, 레코드를 구조 분해할 수도 있습니다. 
이를 위해 콜론 `:` 구문을 사용합니다. 
이에 대한 자세한 내용은 [패턴 타입][Pattern types] 페이지에서 확인할 수 있습니다.

<?code-excerpt "language/test/records_test.dart (record-name-destructure)"?>
```dart
({String name, int age}) userInfo(Map<String, dynamic> json)
// ···
// 명명된 필드가 있는 레코드 패턴을 사용하여 구조 분해:
final (:name, :age) = userInfo(json);
```

레코드 없이 함수에서 여러 값을 반환할 수 있지만, 다른 방법에는 단점이 있습니다. 
예를 들어, 클래스를 만드는 것은 훨씬 더 장황하고, 
`List`나 `Map`과 같은 다른 컬렉션 타입을 사용하면, 타입 세이프티가 손실됩니다.

:::note
레코드의 다중 반환(multiple-return) 및 이기종 타입(heterogeneous-type) 특성은, 
다양한 타입의 futures 병렬화를 가능하게 하며, 
이에 대한 자세한 내용은 [`dart:async` 문서][`dart:async` documentation]에서 확인할 수 있습니다.
:::

[language version]: /guides/language/evolution#language-versioning
[collection types]: /language/collections
[pattern]: /language/patterns#destructuring-multiple-returns
[`dart:async` documentation]: /libraries/dart-async#handling-errors-for-multiple-futures
[parameters and arguments]: /language/functions#parameters
[destructure]: /language/patterns#destructuring
[Pattern types]: /language/pattern-types#record
