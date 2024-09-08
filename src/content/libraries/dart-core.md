---
title: dart:core
# description: Learn about the major features in Dart's dart:core library.
description: Dart의 dart:core 라이브러리의 주요 기능에 대해 알아보세요.
prevpage:
  url: /libraries
  # title: Core libraries
  title: Core 라이브러리
nextpage:
  url: /libraries/dart-async
  title: dart:async
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

dart:core 라이브러리([API 참조][dart:core])는 작지만, 
중요한 빌트인 기능 세트를 제공합니다. 
이 라이브러리는 모든 Dart 프로그램에 자동으로 import 됩니다.

## 콘솔에 출력하기 {:#printing-to-the-console}

최상위 레벨의 `print()` 메서드는 단일 인수(모든 객체)를 취하고, 
해당 객체의 문자열 값(`toString()`에서 반환된 값)을 콘솔에 표시합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (print)"?>
```dart
print(anObject);
print('I drink $tea.');
```

기본 문자열과 `toString()`에 대한 자세한 내용은, 
언어 투어의 [문자열](/language/built-in-types#strings)을 참조하세요.

## 숫자 {:#numbers}

dart:core 라이브러리는 숫자 작업을 위한, 
몇 가지 기본 유틸리티를 갖춘 num, int 및 double 클래스를 정의합니다.

int 및 double의 `parse()` 메서드를 사용하여, 
문자열을 정수 또는 double로 변환할 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (int-double-parse)"?>
```dart
assert(int.parse('42') == 42);
assert(int.parse('0x42') == 66);
assert(double.parse('0.50') == 0.5);
```

또는 num의 parse() 메서드를 사용하면, 
가능하면 정수를 생성하고 그렇지 않으면 double을 생성합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (num-parse)"?>
```dart
assert(num.parse('42') is int);
assert(num.parse('0x42') is int);
assert(num.parse('0.50') is double);
```

정수의 기수를 지정하려면, `radix` 매개변수를 추가합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (radix)"?>
```dart
assert(int.parse('42', radix: 16) == 66);
```

`toString()` 메서드를 사용하여, int 또는 double을 문자열로 변환합니다. 
소수점 오른쪽의 자릿수를 지정하려면, [toStringAsFixed()][toStringAsFixed()]를 사용합니다. 
문자열의 유효 자릿수를 지정하려면, [toStringAsPrecision():][toStringAsPrecision()]을 사용합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (to-string)"?>
```dart
// int를 문자열로 변환합니다.
assert(42.toString() == '42');

// double을 문자열로 변환합니다.
assert(123.456.toString() == '123.456');

// 소수점 뒤의 숫자 자릿수를 지정하세요.
assert(123.456.toStringAsFixed(2) == '123.46');

// 유효 숫자의 개수를 지정하세요.
assert(123.456.toStringAsPrecision(2) == '1.2e+2');
assert(double.parse('1.2e+2') == 120.0);
```

자세한 내용은 [int][int], [double][double] 및 [num][num]에 대한 API 문서를 참조하세요. 
또한, [dart:math 섹션](/libraries/dart-math)도 참조하세요.

## 문자열과 정규 표현식 {:#strings-and-regular-expressions}

Dart의 문자열은 UTF-16 코드 유닛의 불변(immutable) 시퀀스입니다. 
언어 투어에는 [문자열](/language/built-in-types#strings)에 대한 자세한 정보가 있습니다. 
정규 표현식(RegExp 객체)을 사용하여 문자열 내에서 검색하고, 문자열의 일부를 바꿀 수 있습니다.

String 클래스는 `split()`, `contains()`, `startsWith()`, `endsWith()` 등의 메서드를 정의합니다.

### 문자열 내부에서 검색 {:#searching-inside-a-string}

문자열 내의 특정 위치를 찾을 수 있고, 
문자열이 특정 패턴으로 시작하거나 끝나는지 확인할 수도 있습니다. 
예를 들어:

<?code-excerpt "misc/test/library_tour/core_test.dart (contains-etc)"?>
```dart
// 문자열이 다른 문자열을 포함하고 있는지 확인합니다.
assert('Never odd or even'.contains('odd'));

// 문자열은 다른 문자열로 시작합니까?
assert('Never odd or even'.startsWith('Never'));

// 문자열은 다른 문자열로 끝나나요?
assert('Never odd or even'.endsWith('even'));

// 문자열 내에서 문자열의 위치를 ​​찾아 보세요.
assert('Never odd or even'.indexOf('odd') == 6);
```

### 문자열에서 데이터 추출 {:#extracting-data-from-a-string}

문자열에서 개별 문자를 각각 문자열 또는 int로 가져올 수 있습니다. 
정확히 말해서, 실제로 개별 UTF-16 코드 유닛을 가져옵니다. 
음자리표 기호('\\u{1D11E}')와 같은 높은 번호의 문자는 각각 두 개의 코드 유닛입니다.

또한 하위 문자열을 추출하거나, 문자열을 하위 문자열 리스트로 분할할 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (substring-etc)"?>
```dart
// 하위 문자열을 가져옵니다.
assert('Never odd or even'.substring(6, 9) == 'odd');

// 문자열 패턴을 사용해 문자열을 분할합니다.
var parts = 'progressive web apps'.split(' ');
assert(parts.length == 3);
assert(parts[0] == 'progressive');

// 인덱스로 UTF-16 코드 유닛(문자열로서)를 가져옵니다.
assert('Never odd or even'[0] == 'N');

// split() 함수를 빈 문자열 매개변수와 함께 사용하면, 
// 모든 문자의 리스트(문자열)를 얻을 수 있습니다. 
// 반복 작업에 유용합니다.
for (final char in 'hello'.split('')) {
  print(char);
}

// 문자열의 모든 UTF-16 코드 유닛을 가져옵니다.
var codeUnitList = 'Never odd or even'.codeUnits.toList();
assert(codeUnitList[0] == 78);
```

:::note
많은 경우, 순수한 코드 유닛이 아닌, 유니코드 문자소 클러스터로 작업하고 싶을 것입니다. 
이는 사용자가 인식하는 문자입니다. 
(예: "🇬🇧"는 사용자가 인식하는 문자 중 하나이지만, 여러 개의 UTF-16 코드 유닛)
이를 위해, Dart 팀은 [`characters` 패키지]({{site.pub-pkg}}/characters)를 제공합니다.
:::

### 대문자 또는 소문자로 변환 {:#converting-to-uppercase-or-lowercase}

문자열을 대문자와 소문자 변형으로 쉽게 변환할 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (case-conversions)"?>
```dart
// 대문자로 변환합니다.
assert('web apps'.toUpperCase() == 'WEB APPS');

// 소문자로 변환합니다.
assert('WEB APPS'.toLowerCase() == 'web apps');
```

:::note
이러한 방법은 모든 언어에 적용되는 것은 아닙니다. 
예를 들어, 터키어 알파벳의 점이 없는 *I*는 잘못 변환됩니다.
:::


### Trimming 및 빈 문자열 {:#trimming-and-empty-strings}

`trim()`로 모든 선행 및 후행 공백을 제거합니다. 
문자열이 비어 있는지(길이가 0인지) 확인하려면, `isEmpty`를 사용합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (trim-etc)"?>
```dart
// 문자열을 trim 합니다.
assert('  hello  '.trim() == 'hello');

// 문자열이 비어 있는지 확인합니다.
assert(''.isEmpty);

// 공백만 있는 문자열은 비어 있지 않습니다.
assert('  '.isNotEmpty);
```

### 문자열의 일부를 교체 {:#replacing-part-of-a-string}

문자열은 불변(immutable) 객체이므로, 생성할 수는 있지만 변경할 수는 없습니다. 
[String API 참조][String]을 자세히 살펴보면, 
어떤 메서드도 실제로 문자열의 상태를 변경하지 않는다는 것을 알 수 있습니다. 
예를 들어, `replaceAll()` 메서드는 원래 문자열을 변경하지 않고, 새 문자열을 반환합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (replace)"?>
```dart
var greetingTemplate = 'Hello, NAME!';
var greeting = greetingTemplate.replaceAll(RegExp('NAME'), 'Bob');

// greetingTemplate은 변경되지 않았습니다.
assert(greeting != greetingTemplate);
```

### 문자열 빌드 {:#building-a-string}

문자열을 프로그래밍 방식으로 생성하려면, StringBuffer를 사용할 수 있습니다. 
StringBuffer는 `toString()`이 호출될 때까지 새 String 객체를 생성하지 않습니다. 
`writeAll()` 메서드에는 구분 기호(이 경우 공백)를 지정할 수 있는, 
선택적 두 번째 매개변수가 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (string-buffer)"?>
```dart
var sb = StringBuffer();
sb
  ..write('Use a StringBuffer for ')
  ..writeAll(['efficient', 'string', 'creation'], ' ')
  ..write('.');

var fullString = sb.toString();

assert(fullString == 'Use a StringBuffer for efficient string creation.');
```

### 정규 표현식 {:#regular-expressions}

RegExp 클래스는 JavaScript 정규 표현식과 동일한 기능을 제공합니다. 
문자열의 효율적인 검색 및 패턴 매칭을 위해 정규 표현식을 사용합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (regexp)"?>
```dart
// 다음은 하나 이상의 숫자에 대한 정규 표현식입니다.
var numbers = RegExp(r'\d+');

var allCharacters = 'llamas live fifteen to twenty years';
var someDigits = 'llamas live 15 to 20 years';

// contains()는 정규 표현식을 사용할 수 있습니다.
assert(!allCharacters.contains(numbers));
assert(someDigits.contains(numbers));

// 모든 일치 항목을 다른 문자열로 바꿉니다.
var exedOut = someDigits.replaceAll(numbers, 'XX');
assert(exedOut == 'llamas live XX to XX years');
```

RegExp 클래스에서도 직접 작업할 수 있습니다. 
Match 클래스는 정규 표현식 일치에 대한 액세스를 제공합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (match)"?>
```dart
var numbers = RegExp(r'\d+');
var someDigits = 'llamas live 15 to 20 years';

// 정규 표현식과 일치하는 문자열이 있는지 확인합니다.
assert(numbers.hasMatch(someDigits));

// 모든 일치 항목을 반복합니다.
for (final match in numbers.allMatches(someDigits)) {
  print(match.group(0)); // 15, 그다음 20
}
```

### 더 많은 정보 {:#more-information}

전체 메서드 리스트는 [String API 참조][String]을 참조하세요. 
또한 [StringBuffer][], [Pattern][], [RegExp][] 및 [Match][]에 대한 API 참조도 참조하세요.

## 컬렉션 {:#collections}

Dart는 리스트, 세트, ​​맵에 대한 클래스를 포함하는 코어 컬렉션 API와 함께 제공됩니다.

:::tip
리스트와 세트 모두에서 사용할 수 있는 API를 사용하는 연습을 하려면, 
[반복 가능한 컬렉션 튜토리얼](/libraries/collections/iterables)을 따르세요.
:::

### Lists {:#lists}

언어 투어에서 보여 주듯이, 
리터럴을 사용하여 [리스트](/language/collections#lists)를 만들고 초기화할 수 있습니다. 
또는 List 생성자 중 하나를 사용합니다. 
List 클래스는 리스트에 아이템을 추가하고, 
리스트에서 아이템을 제거하기 위한 여러 메서드도 정의합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (list)"?>
```dart
// 빈 문자열 리스트를 만듭니다.
var grains = <String>[];
assert(grains.isEmpty);

// 리스트 리터럴을 사용하여 리스트를 만듭니다.
var fruits = ['apples', 'oranges'];

// 리스트에 추가합니다.
fruits.add('kiwis');

// 여러 아이템들을 리스트에 추가합니다.
fruits.addAll(['grapes', 'bananas']);

// 리스트의 길이를 얻습니다.
assert(fruits.length == 5);

// 단일 아이템을 제거합니다.
var appleIndex = fruits.indexOf('apples');
fruits.removeAt(appleIndex);
assert(fruits.length == 4);

// 리스트의 모든 아이템을 제거합니다.
fruits.clear();
assert(fruits.isEmpty);

// 생성자 중 하나를 사용하여 리스트를 만들 수도 있습니다.
var vegetables = List.filled(99, 'broccoli');
assert(vegetables.every((v) => v == 'broccoli'));
```

`indexOf()`를 사용하여 리스트에서 객체의 인덱스를 찾으세요.

<?code-excerpt "misc/test/library_tour/core_test.dart (index-of)"?>
```dart
var fruits = ['apples', 'oranges'];

// 인덱스로 리스트 아이템에 접근합니다.
assert(fruits[0] == 'apples');

// 리스트에서 아이템을 찾습니다.
assert(fruits.indexOf('apples') == 0);
```

`sort()` 메서드를 사용하여 리스트를 정렬합니다. 
두 객체를 비교하는 정렬 함수를 제공할 수 있습니다. 
이 정렬 함수는 *더 작은* 경우 \< 0, *같은* 경우 0, *더 큰* 경우 \> 0을 반환해야 합니다. 
다음 예에서는 [Comparable][]에 의해 정의되고, 
String에 의해 구현되는 `compareTo()`를 사용합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (compare-to)"?>
```dart
var fruits = ['bananas', 'apples', 'oranges'];

// 리스트를 정렬합니다.
fruits.sort((a, b) => a.compareTo(b));
assert(fruits[0] == 'apples');
```

리스트는 매개변수화된 타입([제네릭](/language/generics))이므로, 
리스트에 포함되어야 하는 타입을 지정할 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (list-of-string)"?>
```dart
// 이 리스트에는 문자열만 포함되어야 합니다.
var fruits = <String>[];

fruits.add('apples');
var fruit = fruits[0];
assert(fruit is String);
```

<?code-excerpt "misc/lib/library_tour/core/collections.dart (list-of-string)"?>
```dart tag=fails-sa
fruits.add(5); // 오류: 'int'를 'String'에 할당할 수 없습니다.
```

:::note
많은 경우, Dart가 대신 [추론](/language/type-system#type-inference)해주기 때문에, 
제네릭 타입을 명시적으로 지정할 필요가 없습니다. 
`['Dash', 'Dart']`와 같은 리스트는 `List<String>`(문자열 리스트)로 이해됩니다.

하지만 제네릭 타입을 지정해야 할 때가 있습니다. 
예를 들어, Dart가 추론할 것이 없는 경우, 
`[]`는 여러 가지의 조합 리스트가 될 수 있습니다. 
원하는 것이 아닌 경우가 많으므로, 
`<String>[]` 또는 `<Person>[]` 또는 이와 유사한 것을 작성합니다.
:::

전체 메서드 리스트는 [List API 참조][List]를 참조하세요.

### Sets {:#sets}

Dart의 세트는 고유한 아이템의 순서 없는 컬렉션입니다. 
세트는 순서가 없기 때문에, 인덱스(위치)로 세트의 아이템을 가져올 수 없습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (set)"?>
```dart
// 빈 문자열 세트를 생성합니다.
var ingredients = <String>{};

// 새로운 아이템들을 추가합니다.
ingredients.addAll(['gold', 'titanium', 'xenon']);
assert(ingredients.length == 3);

// 중복된 아이템을 추가해도, 효과가 없습니다.
ingredients.add('gold');
assert(ingredients.length == 3);

// 세트에서 아이템을 제거합니다.
ingredients.remove('gold');
assert(ingredients.length == 2);

// 생성자 중 하나를 사용하여, 세트를 생성할 수도 있습니다.
var atomicNumbers = Set.from([79, 22, 54]);
```

`contains()` 및 `containsAll()`를 사용하여, 
하나 이상의 객체가 세트에 있는지 확인합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (contains)"?>
```dart
var ingredients = Set<String>();
ingredients.addAll(['gold', 'titanium', 'xenon']);

// 세트에 아이템이 있는지 확인합니다.
assert(ingredients.contains('titanium'));

// 세트에 모든 아이템이 있는지 확인하세요.
assert(ingredients.containsAll(['titanium', 'xenon']));
```

교집합(intersection)은 그 아이템이 다른 두 세트에 속하는 세트입니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (intersection)"?>
```dart
var ingredients = Set<String>();
ingredients.addAll(['gold', 'titanium', 'xenon']);

// 두 세트의 교집합을 만듭니다.
var nobleGases = Set.from(['xenon', 'argon']);
var intersection = ingredients.intersection(nobleGases);
assert(intersection.length == 1);
assert(intersection.contains('xenon'));
```

전체 메서드 리스트는 [Set API 참조][Set]를 참조하세요.

### Maps {:#maps}

*딕셔너리* 또는 *해시*로 일반적으로 알려진, 
맵은 키-값 쌍의 순서 없는 컬렉션입니다. 
맵은 키를 일부 값에 연결하여 쉽게 검색할 수 있도록 합니다. 
JavaScript와 달리, Dart 객체는 맵이 아닙니다.

간결한 리터럴 구문을 사용하여 맵을 선언하거나, 기존 생성자를 사용할 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (map)"?>
```dart
// 맵은 문자열을 키로 사용하는 경우가 많습니다.
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

// 맵은 생성자에서 빌드할 수 있습니다.
var searchTerms = Map();

// 맵은 매개변수화된 타입이므로, 
// 키와 값의 타입을 지정할 수 있습니다.
var nobleGases = Map<int, String>();
```

대괄호 구문을 사용하여 맵 아이템을 추가, get 및 set합니다. 
`remove()`를 사용하여, 맵에서 키와 해당 값을 제거합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (remove)"?>
```dart
var nobleGases = {54: 'xenon'};

// 키로 값을 검색합니다.
assert(nobleGases[54] == 'xenon');

// 맵에 키가 포함되어 있는지 확인합니다.
assert(nobleGases.containsKey(54));

// 키와 값을 제거합니다.
nobleGases.remove(54);
assert(!nobleGases.containsKey(54));
```

맵에서 모든 값이나 모든 키를 검색할 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (keys)"?>
```dart
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

// 모든 키를 순서 없는 컬렉션(Iterable)으로 가져옵니다.
var keys = hawaiianBeaches.keys;

assert(keys.length == 3);
assert(Set.from(keys).contains('Oahu'));

// 모든 값을 순서 없는 컬렉션(List의 Iterable)으로 가져옵니다.
var values = hawaiianBeaches.values;
assert(values.length == 3);
assert(values.any((v) => v.contains('Waikiki')));
```

맵에 키가 있는지 확인하려면, `containsKey()`를 사용합니다. 
맵 값은 null일 수 있으므로, 키의 값을 가져오고 null인지 확인하는 것만으로는, 
키의 존재를 확인할 수 없습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (contains-key)"?>
```dart
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

assert(hawaiianBeaches.containsKey('Oahu'));
assert(!hawaiianBeaches.containsKey('Florida'));
```

키가 맵에 이미 존재하지 않는 경우에만 키에 값을 할당하려면, 
`putIfAbsent()` 메서드를 사용합니다. 
값을 반환하는 함수를 제공해야 합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (put-if-absent)"?>
```dart
var teamAssignments = <String, String>{};
teamAssignments.putIfAbsent('Catcher', () => pickToughestKid());
assert(teamAssignments['Catcher'] != null);
```

전체 메서드 리스트는 [Map API 참조][Map]를 참조하세요.

### 공통 컬렉션 메서드 {:#common-collection-methods}

List, Set, Map은 많은 컬렉션에서 발견되는 공통 기능을 공유합니다. 
이러한 공통 기능 중 일부는 List와 Set이 구현하는, Iterable 클래스에 의해 정의됩니다.

:::note
  Map은 Iterable을 구현하지 않지만, 
  Map의 `keys` 및 `values` 속성을 사용하여, 
  Iterable을 가져올 수 있습니다.
:::

`isEmpty` 또는 `isNotEmpty`를 사용하여 리스트, 세트 또는 맵에 아이템이 있는지 확인하세요.

<?code-excerpt "misc/test/library_tour/core_test.dart (is-empty)"?>
```dart
var coffees = <String>[];
var teas = ['green', 'black', 'chamomile', 'earl grey'];
assert(coffees.isEmpty);
assert(teas.isNotEmpty);
```

리스트, 세트 또는 맵의 각 아이템에 함수를 적용하려면, `forEach()`를 사용하면 됩니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (list-for-each)"?>
```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

teas.forEach((tea) => print('I drink $tea'));
```

맵에서 `forEach()`를 호출할 때, 
함수는 두 개의 인수(키와 값)를 받아야 합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (map-for-each)"?>
```dart
hawaiianBeaches.forEach((k, v) {
  print('I want to visit $k and swim at $v');
  // I want to visit Oahu and swim at [Waikiki, Kailua, Waimanalo], etc.
});
```

Iterables은 `map()` 메서드를 제공하는데, 
이 메서드는 모든 결과를 단일 객체로 제공합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (list-map)"?>
```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

var loudTeas = teas.map((tea) => tea.toUpperCase());
loudTeas.forEach(print);
```

:::note
`map()`이 반환하는 객체는 *지연 평가(lazily evaluated)*되는 Iterable입니다. 
반환된 객체에서 아이템을 요청할 때까지 함수는 호출되지 않습니다.
:::

각 항목에서 함수가 즉시 호출되도록 하려면, 
`map().toList()` 또는 `map().toSet()`를 사용합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (to-list)"?>
```dart
var loudTeas = teas.map((tea) => tea.toUpperCase()).toList();
```

Iterable의 `where()` 메서드를 사용하여, 
조건에 맞는 모든 아이템들을 가져옵니다. 
Iterable의 `any()` 및 `every()` 메서드를 사용하여, 
일부 또는 모든 아이템들이 조건에 맞는지 확인합니다.
{% comment %}
PENDING: Change example as suggested by floitsch to have (maybe)
cities instead of isDecaffeinated.
{% endcomment %}


<?code-excerpt "misc/test/library_tour/core_test.dart (where-etc)"?>
```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

// Chamomile에는 카페인이 없습니다.
bool isDecaffeinated(String teaName) => teaName == 'chamomile';

// where()를 사용하면, 제공된 함수에서 true를 반환하는 항목만 찾을 수 있습니다.
var decaffeinatedTeas = teas.where((tea) => isDecaffeinated(tea));
// 또는 teas.where(isDecaffeinated)

// any()를 사용하여, 컬렉션에 있는 아이템 중 적어도 하나 이상이 조건을 만족하는지 확인합니다.
assert(teas.any(isDecaffeinated));

// every()를 사용하여, 컬렉션의 모든 아이템이 조건을 만족하는지 확인합니다.
assert(!teas.every(isDecaffeinated));
```

메서드의 전체 리스트는 [Iterable API 참조][Iterable]와 
[List][], [Set][Set] 및 [Map][Map]에 대한 참조를 참조하세요.

## URI {:#uris}

[Uri 클래스][Uri]는 URI(*URL*이라고도 함)에서,
사용할 문자열을 인코딩하고 디코딩하는 함수를 제공합니다. 
이러한 함수는 (`&` 및 `=`와 같이) URI에 특수한 문자를 처리합니다. 
Uri 클래스는 또한 URI의 구성 요소(호스트, 포트, 스킴 등)를 구문 분석하고 노출합니다.

{% comment %}
{PENDING: show
constructors: Uri.http, Uri.https, Uri.file, per floitsch's suggestion}
{% endcomment %}

### 완전히 적격한 URI 인코딩 및 디코딩 {:#encoding-and-decoding-fully-qualified-uris}

URI에서 특별한 의미가 있는 문자(예: `/`, `:`, `&`, `#`)를 *제외한* 문자를 인코딩하고 디코딩하려면,
`encodeFull()` 및 `decodeFull()` 메서드를 사용합니다. 
이러한 메서드는 완전히 적격한 URI를 인코딩하거나 디코딩하는 데 적합하며, 
특수 URI 문자는 그대로 둡니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (encode-full)"?>
```dart
var uri = 'https://example.org/api?foo=some message';

var encoded = Uri.encodeFull(uri);
assert(encoded == 'https://example.org/api?foo=some%20message');

var decoded = Uri.decodeFull(encoded);
assert(uri == decoded);
```

`some`과 `message` 사이의 공백만 인코딩된 것을 주목하세요.

### URI 구성 요소 인코딩 및 디코딩 {:#encoding-and-decoding-uri-components}

URI에서 특별한 의미를 갖는 모든 문자열 문자(`/`, `&`, `:` 포함, 이에 제한되지 않음)를 인코딩하고 디코딩하려면, 
`encodeComponent()` 및 `decodeComponent()` 메서드를 사용합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (encode-component)"?>
```dart
var uri = 'https://example.org/api?foo=some message';

var encoded = Uri.encodeComponent(uri);
assert(
    encoded == 'https%3A%2F%2Fexample.org%2Fapi%3Ffoo%3Dsome%20message');

var decoded = Uri.decodeComponent(encoded);
assert(uri == decoded);
```

모든 특수 문자가 어떻게 인코딩되는지 주목하세요. 예를 들어, `/`는 `%2F`로 인코딩됩니다.

### URI 구문 분석 {:#parsing-uris}

Uri 객체나 URI 문자열이 있는 경우, 
(`path`와 같은) Uri 필드를 사용하여 해당 부분을 가져올 수 있습니다. 
문자열에서 Uri를 만들려면, `parse()` 정적 메서드를 사용합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (uri-parse)"?>
```dart
var uri = Uri.parse('https://example.org:8080/foo/bar#frag');

assert(uri.scheme == 'https');
assert(uri.host == 'example.org');
assert(uri.path == '/foo/bar');
assert(uri.fragment == 'frag');
assert(uri.origin == 'https://example.org:8080');
```

더 많은 URI 구성 요소를 얻으려면 [Uri API 참조][Uri]를 확인하세요.

### URI 빌드 {:#building-uris}

`Uri()` 생성자를 사용하여, 개별 부분에서 URI를 구축할 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (uri)"?>
```dart
var uri = Uri(
    scheme: 'https',
    host: 'example.org',
    path: '/foo/bar',
    fragment: 'frag',
    queryParameters: {'lang': 'dart'});
assert(uri.toString() == 'https://example.org/foo/bar?lang=dart#frag');
```

fragment를 지정할 필요가 없고, http 또는 https 체계로 URI를 생성하는 경우, 
대신 [`Uri.http`][] 또는 [`Uri.https`][] 팩토리 생성자를 사용할 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (uri-http)"?>
```dart
var httpUri = Uri.http('example.org', '/foo/bar', {'lang': 'dart'});
var httpsUri = Uri.https('example.org', '/foo/bar', {'lang': 'dart'});

assert(httpUri.toString() == 'http://example.org/foo/bar?lang=dart');
assert(httpsUri.toString() == 'https://example.org/foo/bar?lang=dart');
```

[`Uri.http`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Uri/Uri.http.html
[`Uri.https`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Uri/Uri.https.html

## 날짜 및 시간 {:#dates-and-times}

DateTime 객체는 특정 시점입니다. 시간대는 UTC 또는 로컬 시간대입니다.

여러 생성자와 메서드를 사용하여, DateTime 객체를 만들 수 있습니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (date-time)"?>
```dart
// 현재 날짜와 시간을 가져옵니다.
var now = DateTime.now();

// 로컬 시간대로 새로운 DateTime을 생성합니다.
var y2k = DateTime(2000); // 2000년 1월 1일

// 월과 일을 지정하세요.
y2k = DateTime(2000, 1, 2); // 2000년 1월 2일

// 날짜를 UTC 시간으로 지정하세요.
y2k = DateTime.utc(2000); // 2000년 1월 1일, UTC

// Unix 시대부터 경과한 날짜와 시간을 ms 단위로 지정하세요.
y2k = DateTime.fromMillisecondsSinceEpoch(946684800000, isUtc: true);

// ISO 8601 날짜를 UTC 시간대로 구문 분석합니다.
y2k = DateTime.parse('2000-01-01T00:00:00Z');

// 기존 DateTime에서, 일부 속성만 조정하여, 새 DateTime을 만듭니다.
var sameTimeLastYear = now.copyWith(year: now.year - 1);
```

:::warning
`DateTime` 연산은 일광 절약 시간제 및 기타 비표준 시간 조정과 관련하여, 
예상치 못한 결과를 초래할 수 있습니다.
:::

날짜의 `millisecondsSinceEpoch` 속성은, 
"Unix epoch"(1970년 1월 1일 UTC) 이후의 밀리초 수를 반환합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (milliseconds-since-epoch)"?>
```dart
// 2000년 1월 1일, UTC
var y2k = DateTime.utc(2000);
assert(y2k.millisecondsSinceEpoch == 946684800000);

// 1970년 1월 1일, UTC
var unixEpoch = DateTime.utc(1970);
assert(unixEpoch.millisecondsSinceEpoch == 0);
```

Duration 클래스를 사용하여, 두 날짜 간의 차이를 계산하고, 날짜를 앞이나 뒤로 이동합니다.

<?code-excerpt "misc/test/library_tour/core_test.dart (duration)"?>
```dart
var y2k = DateTime.utc(2000);

// 1년을 더하세요.
var y2001 = y2k.add(const Duration(days: 366));
assert(y2001.year == 2001);

// 30일을 빼세요.
var december2000 = y2001.subtract(const Duration(days: 30));
assert(december2000.year == 2000);
assert(december2000.month == 12);

// 두 날짜의 차이를 계산합니다.
// Duration 객체를 반환합니다.
var duration = y2001.difference(y2k);
assert(duration.inDays == 366); // Y2K는 윤년이었습니다.
```

:::warning
`Duration`을 사용하여 `DateTime`을 일 단위로 이동하면, 
시계 이동(예: 일광 절약 시간제)으로 인해 문제가 될 수 있습니다. 
일자를 이동해야 하는 경우, UTC 날짜를 사용하세요.
:::

전체 메서드 리스트는, [DateTime][] 및 [Duration][]에 대한 API 참조를 참조하세요.


## 유틸리티 클래스 {:#utility-classes}

코어 라이브러리에는 정렬, 값 매핑, 반복에 유용한 다양한 유틸리티 클래스가 포함되어 있습니다.

### 비교 객체 {:#comparing-objects}

[Comparable][] 인터페이스를 구현하여 객체를 다른 객체와 비교할 수 있음을 나타냅니다. 
일반적으로 정렬을 위해 사용합니다. 
`compareTo()` 메서드는 *더 작은* 경우 \< 0, *같은* 경우 0, *더 큰* 경우 \> 0을 반환합니다.

<?code-excerpt "misc/lib/library_tour/core/comparable.dart"?>
```dart
class Line implements Comparable<Line> {
  final int length;
  const Line(this.length);

  @override
  int compareTo(Line other) => length - other.length;
}

void main() {
  var short = const Line(1);
  var long = const Line(100);
  assert(short.compareTo(long) < 0);
}
```

### 맵 키 구현 {:#implementing-map-keys}

Dart의 각 객체는 자동으로 정수 해시 코드를 제공하므로, 맵에서 키로 사용할 수 있습니다. 
그러나, `hashCode` getter를 재정의하여, 커스텀 해시 코드를 생성할 수 있습니다. 
그렇게 하는 경우, `==` 연산자도 재정의할 수 있습니다. 
동일한 객체(`==`를 통해)는 동일한 해시 코드를 가져야 합니다. 
해시 코드는 고유할 필요는 없지만, 잘 분산되어야 합니다.

:::tip
`hashCode` getter를 일관되고 쉽게 구현하려면, 
`Object` 클래스에서 제공하는 정적 hashing 메서드를 사용하는 것을 고려하세요.

객체의 여러 속성에 대한 단일 해시 코드를 생성하려면, [`Object.hash()`][]를 사용할 수 있습니다. 
컬렉션에 대한 해시 코드를 생성하려면, 
[`Object.hashAll()`][](요소 순서가 중요한 경우) 또는 
[`Object.hashAllUnordered()`][]를 사용할 수 있습니다.
:::

[`Object.hash()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Object/hash.html
[`Object.hashAll()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Object/hashAll.html
[`Object.hashAllUnordered()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Object/hashAllUnordered.html

{% comment %}
Note: There's disagreement over whether to include identical() in the ==
implementation. It might improve speed, at least when you need to
compare many fields. They don't do identical() automatically because, by
convention, NaN != NaN.
{% endcomment %}

<?code-excerpt "misc/lib/library_tour/core/hash_code.dart"?>
```dart
class Person {
  final String firstName, lastName;

  Person(this.firstName, this.lastName);

  // `Object` 클래스가 제공하는 정적 hashing 메서드를 사용하여 hashCode를 재정의합니다.
  @override
  int get hashCode => Object.hash(firstName, lastName);

  // 일반적으로, `hashCode`를 재정의하는 경우, `==` 연산자를 구현해야 합니다.
  @override
  bool operator ==(Object other) {
    return other is Person &&
        other.firstName == firstName &&
        other.lastName == lastName;
  }
}

void main() {
  var p1 = Person('Bob', 'Smith');
  var p2 = Person('Bob', 'Smith');
  var p3 = 'not a person';
  assert(p1.hashCode == p2.hashCode);
  assert(p1 == p2);
  assert(p1 != p3);
}
```

### Iteration {:#iteration}

[Iterable][] 및 [Iterator][] 클래스는 값 컬렉션에 대한 순차적 액세스를 지원합니다. 
이러한 컬렉션을 사용하는 연습을 하려면, 
[Iterable 컬렉션 튜토리얼](/libraries/collections/iterables)을 따르세요.

for-in 루프에서 사용할 수 있는 Iterator를 제공할 수 있는 클래스를 만드는 경우, 
Iterable을 확장하거나(가능하다면) 구현하세요. 
Iterator를 구현하여 실제 반복 기능을 정의하세요.

<?code-excerpt "misc/lib/library_tour/core/iterator.dart (structure)"?>
```dart
class Process {
  // ...의 과정을 나타냅니다.
}

class ProcessIterator implements Iterator<Process> {
  @override
  Process get current => ...
  @override
  bool moveNext() => ...
}

// 모든 프로세스를 반복할 수 있는 신화적인 클래스입니다. 
// [Iterable]의 하위 클래스를 확장합니다.
class Processes extends IterableBase<Process> {
  @override
  final Iterator<Process> iterator = ProcessIterator();
}

void main() {
  // Iterable 객체는 for-in과 함께 사용할 수 있습니다.
  for (final process in Processes()) {
    // "process"로 무언가를 하세요.
  }
}
```

## 예외 {:#exceptions}

Dart 코어 라이브러리는 많은 일반적인 예외와 오류를 정의합니다. 
예외(Exceptions)는 미리 계획하고 catch 할 수 있는 조건으로 간주됩니다. 
오류(Errors)는 예상하지 않거나 계획하지 않는 조건입니다.

가장 일반적인 오류 몇 가지는 다음과 같습니다.

[NoSuchMethodError][]
: 수신 객체(`null`일 수 있음)가 메서드를 구현하지 않을 때 발생합니다.

[ArgumentError][]
: 예상치 못한 인수를 만나는 메서드에서 발생할 수 있습니다.

애플리케이션별 예외를 발생시키는 것은, 오류가 발생했음을 나타내는 일반적인 방법입니다. 
Exception 인터페이스를 구현하여, 커스텀 예외를 정의할 수 있습니다.

<?code-excerpt "misc/lib/library_tour/core/exception.dart"?>
```dart
class FooException implements Exception {
  final String? msg;

  const FooException([this.msg]);

  @override
  String toString() => msg ?? 'FooException';
}
```

자세한 내용은, [예외](/language/error-handling#exceptions)(언어 투어) 및 [예외 API 참조][Exception]를 참조하세요.

## 약한 참조 및 종료자(finalizers) {:#weak-references-and-finalizers}

Dart는 [가비지 수집][garbage-collected] 언어입니다. 
즉, 참조되지 않은 모든 Dart 객체는 가비지 수집기에 의해 폐기될 수 있습니다. 
이 기본 동작은 네이티브 리소스와 관련된 일부 시나리오에서 바람직하지 않을 수 있거나, 
대상 객체를 수정할 수 없는 경우입니다.

[WeakReference][]는, 가비지 수집기에 의해 수집되는 방식에 영향을 미치지 않는, 대상 객체에 대한 참조를 저장합니다. 
또 다른 옵션은 [Expando][]를 사용하여, 객체에 속성을 추가하는 것입니다.

[Finalizer][]는, 객체가 더 이상 참조되지 않은 후에, 콜백 함수를 실행하는 데 사용할 수 있습니다. 
그러나, 이 콜백을 실행한다는 보장은 없습니다.

[NativeFinalizer][]는 [dart:ffi][]를 사용하여, 네이티브 코드와 상호 작용할 때 더 강력한 보장을 제공합니다. 
해당 콜백은 객체가 더 이상 참조되지 않은 후에 최소한 한 번 호출됩니다. 
또한, 데이터베이스 연결이나 파일 열기와 같은 네이티브 리소스를 닫는 데 사용할 수 있습니다.

객체가 너무 일찍 가비지 수집되고 종료되지 않도록 하기 위해, 
클래스는 [Finalizable][] 인터페이스를 구현할 수 있습니다. 
로컬 변수가 Finalizable이면, 선언된 코드 블록이 종료될 때까지 가비지 수집되지 않습니다.

:::version-note
Dart 2.17에서는 약한 참조(weak references)와 종료자(finalizers)에 대한 지원이 추가되었습니다.
:::


[ArgumentError]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/ArgumentError-class.html
[Comparable]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Comparable-class.html
[DateTime]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/DateTime-class.html
[Duration]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Duration-class.html
[Exception]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Exception-class.html
[Expando]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Expando-class.html
[Finalizable]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-ffi/Finalizable-class.html
[Finalizer]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Finalizer-class.html
[Iterable]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable-class.html
[Iterator]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterator-class.html
[List]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/List-class.html
[Map]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Map-class.html
[Match]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Match-class.html
[NativeFinalizer]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-ffi/NativeFinalizer-class.html
[NoSuchMethodError]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/NoSuchMethodError-class.html
[Pattern]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Pattern-class.html
[RegExp]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/RegExp-class.html
[Set]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Set-class.html
[StringBuffer]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/StringBuffer-class.html
[String]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/String-class.html
[Uri]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Uri-class.html
[WeakReference]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/WeakReference-class.html
[dart:core]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/dart-core-library.html
[dart:ffi]: /guides/libraries/c-interop
[double]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/double-class.html
[garbage-collected]: https://medium.com/flutter/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30
[int]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/int-class.html
[num]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/num-class.html
[toStringAsFixed()]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/num/toStringAsFixed.html
[toStringAsPrecision()]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/num/toStringAsPrecision.html
