---
# title: Collections
title: 컬렉션
# description: Summary of the different types of collections in Dart.
description: Dart의 다양한 컬렉션 타입에 대한 요약입니다.
prevpage:
  url: /language/records
  # title: Records
  title: 레코드
nextpage:
  url: /language/generics
  # title: Generics
  title: 제네릭
---

Dart에는 리스트, 집합 및 맵 [컬렉션][collections]에 대한 빌트인 지원이 있습니다. 
컬렉션에 포함된 타입 구성에 대해 자세히 알아보려면, [제네릭][Generics]을 확인하세요.

## Lists {:#lists}

아마도 거의 모든 프로그래밍 언어에서 가장 흔한 컬렉션은 *배열* 또는 정렬된 객체 그룹일 것입니다. 
Dart에서 배열은 [`List`][] 객체이므로, 대부분 사람들은 그냥 *리스트*라고 부릅니다.

Dart 리스트 리터럴은 표현식이나 값의 쉼표로 구분된 목록으로 표시되며, 대괄호(`[]`)로 묶입니다. 
다음은 간단한 Dart 리스트입니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (list-literal)"?>
```dart
var list = [1, 2, 3];
```

:::note
Dart는 `list`가 `List<int>` 타입을 가지고 있다고 추론합니다. 
이 목록에 정수가 아닌 객체를 추가하려고 하면, 분석기나 런타임에서 오류가 발생합니다. 
자세한 내용은 [타입 추론][type inference]을 읽어보세요.
:::

<a id="trailing-comma"></a>
Dart 컬렉션 리터럴의 마지막 항목 뒤에 쉼표를 추가할 수 있습니다. 
이 _마지막 쉼표(trailing comma)_ 는 컬렉션에 영향을 미치지 않지만, 
복사-붙여넣기 오류를 방지하는 데 도움이 될 수 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (trailing-commas)"?>
```dart
var list = [
  'Car',
  'Boat',
  'Plane',
];
```

리스트는 0부터 시작하는 인덱싱을 사용하는데, 
여기서 0은 첫 번째 값의 인덱스이고, `list.length - 1`은 마지막 값의 인덱스입니다. 
`.length` 속성을 사용하여 리스트의 길이를 얻고, 
서브스크립트 연산자(`[]`)를 사용하여, 리스트의 값에 접근할 수 있습니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-indexing)"?>
```dart
var list = [1, 2, 3];
assert(list.length == 3);
assert(list[1] == 2);

list[1] = 1;
assert(list[1] == 1);
```

컴파일 타임 상수인 리스트를 생성하려면, 리스트 리터럴 앞에 `const`를 추가합니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-list)"?>
```dart
var constantList = const [1, 2, 3];
// constantList[1] = 1; // 이 줄은 오류를 발생시킵니다.
```

리스트에 대한 자세한 내용은 [`dart:core` 문서](/libraries/dart-core#lists)의 리스트 섹션을 참조하세요.

## Sets {:#sets}

Dart의 세트는 고유한 항목의 순서 없는 컬렉션입니다. 
Dart는 세트 리터럴과 [`Set`][] 타입을 통해 세트를 지원합니다.

세트 리터럴을 사용하여 만든, 간단한 Dart 세트는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-literal)"?>
```dart
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
```

:::note
Dart는 `halogens`가 `Set<String>` 타입을 가지고 있다고 추론합니다. 
잘못된 타입의 값을 세트에 추가하려고 하면, 분석기나 런타임에서 오류가 발생합니다. 
자세한 내용은 [타입 추론](/language/type-system#type-inference)을 참조하세요.
:::

빈 세트를 생성하려면, `{}`를 타입 인수 뒤에 사용하거나, `Set` 타입의 변수에 `{}`를 할당합니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-vs-map)"?>
```dart
var names = <String>{};
// Set<String> names = {}; // 이것도 동작합니다.
// var names = {}; // set가 아닌 map을 만듭니다.
```

:::note Set 또는 map?
맵 리터럴의 구문은 세트 리터럴의 구문과 비슷합니다. 
맵 리터럴이 먼저 나왔기 때문에 `{}`는 기본적으로 `Map` 타입으로 설정됩니다. 
`{}`에 대한 타입 주석이나 할당된 변수를 잊으면, 
Dart는 `Map<dynamic, dynamic>` 타입의 객체를 만듭니다.
:::

`add()` 또는 `addAll()` 메서드를 사용하여 기존 세트에 아이템을 추가합니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-add-items)"?>
```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
```

`.length`를 사용하여 세트에 있는 아이템의 개수를 가져옵니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (set-length)"?>
```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
assert(elements.length == 5);
```

컴파일 타임 상수인 세트를 생성하려면, 세트 리터럴 앞에 `const`를 추가합니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-set)"?>
```dart
final constantSet = const {
  'fluorine',
  'chlorine',
  'bromine',
  'iodine',
  'astatine',
};
// constantSet.add('helium'); // 이 줄은 오류를 발생시킵니다.
```

세트에 대한 자세한 내용은, [`dart:core` 문서](/libraries/dart-core#sets)의 Sets 섹션을 참조하세요.

## Maps {:#maps}

일반적으로, 맵은 키와 값을 연관짓는 객체입니다. 
키와 값은 모두 모든 타입의 객체가 될 수 있습니다. 
각 *키*는 한 번만 발생하지만, 동일한 *값*을 여러 번 사용할 수 있습니다. 
맵에 대한 Dart 지원은 맵 리터럴과 [`Map`][] 타입에서 제공됩니다.

맵 리터럴을 사용하여 만든 간단한 Dart 맵 몇 가지가 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-literal)"?>
```dart
var gifts = {
  // 키:    값
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};

var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

:::note
Dart는 `gifts`가 `Map<String, String>` 타입을 가지고, 
`nobleGases`가 `Map<int, String>` 타입을 가지고 있다고 추론합니다. 
두 맵에 잘못된 타입의 값을 추가하려고 하면 분석기나 런타임에서 오류가 발생합니다. 
자세한 내용은 [타입 추론][type inference]을 참조하세요.
:::

Map 생성자를 사용하여 동일한 객체를 생성할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-constructor)"?>
```dart
var gifts = Map<String, String>();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = Map<int, String>();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';
```

:::note
C# 또는 Java와 같은 언어에서 왔다면, `Map()` 대신 `new Map()`을 볼 수 있을 것으로 기대할 수 있습니다. 
Dart에서, `new` 키워드는 선택 사항입니다. 자세한 내용은 [생성자 사용][Using constructors]을 참조하세요.
:::

`[]=` 서브스크립트 할당 연산자를 사용하여, 기존 맵에 새로운 키-값 쌍을 추가합니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-add-item)"?>
```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds'; // 키-값 쌍 추가
```

서브스크립트 연산자(`[]`)를 사용하여 맵에서 값을 검색합니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-retrieve-item)"?>
```dart
var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');
```

맵에 없는 키를 찾으면, `null`이 반환됩니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-missing-key)"?>
```dart
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);
```

`.length`를 사용하여 맵에 있는 키-값 쌍의 수를 가져옵니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-length)"?>
```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);
```

컴파일 타임 상수인 맵을 생성하려면, 맵 리터럴 앞에 `const`를 추가합니다.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-map)"?>
```dart
final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};

// constantMap[2] = 'Helium'; // 이 줄은 오류를 발생시킵니다.
```

맵에 대한 자세한 내용은, [`dart:core` 문서](/libraries/dart-core#maps)의 Maps 섹션을 참조하세요.

## 연산자 {:#operators}

### 스프레드 연산자 {:#spread-operators}

Dart는 리스트, 맵, 세트 리터럴에서 **스프레드 연산자** (`...`)와 **null 인식 스프레드 연산자** (`...?`)를 지원합니다. 
스프레드 연산자는 여러 값을 컬렉션에 삽입하는 간결한 방법을 제공합니다.

예를 들어, 스프레드 연산자(`...`)를 사용하여 리스트의 모든 값을 다른 리스트에 삽입할 수 있습니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-spread)"?>
```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

스프레드 연산자 오른쪽에 있는 표현식이 null일 가능성이 있는 경우, 
null 인식 스프레드 연산자(`...?`)를 사용하여 예외를 방지할 수 있습니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-null-spread)"?>
```dart
var list2 = [0, ...?list];
assert(list2.length == 1);
```

스프레드 연산자 사용에 대한 자세한 내용과 예는, [스프레드 연산자 제안][spread proposal]을 참조하세요.

<a id="collection-operators"></a>
### 흐름 제어 연산자 {:#control-flow-operators}

Dart는 리스트, 맵, 세트 리터럴에서 사용할 수 있는 **컬렉션 if**와 **컬렉션 for**를 제공합니다. 
이러한 연산자를 사용하면, 조건문(`if`)과 반복(`for`)을 사용하여 컬렉션을 빌드할 수 있습니다.

다음은 **컬렉션 if**를 사용하여, 3개 또는 4개의 항목이 있는 리스트를 만드는 예입니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-if)"?>
```dart
var nav = ['Home', 'Furniture', 'Plants', if (promoActive) 'Outlet'];
```

Dart는 컬렉션 리터럴 내부에서도 [if-case][]를 지원합니다.

```dart
var nav = ['Home', 'Furniture', 'Plants', if (login case 'Manager') 'Inventory'];
```

다음은 다른 리스트에 아이템을 추가하기 전에, 
리스트의 아이템을 조작하기 위해 **컬렉션 for**를 사용하는 예입니다.

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-for)"?>
```dart
var listOfInts = [1, 2, 3];
var listOfStrings = ['#0', for (var i in listOfInts) '#$i'];
assert(listOfStrings[1] == '#1');
```

컬렉션 `if` 및 `for` 사용에 대한 자세한 내용과 예는, 
[흐름 제어 컬렉션 제안][collections proposal]을 참조하세요.

[collections]: /libraries/dart-core#collections
[type inference]: /language/type-system#type-inference
[`List`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/List-class.html
[`Map`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Map-class.html
[Using constructors]: /language/classes#using-constructors
[collections proposal]: {{site.repo.dart.lang}}/blob/main/accepted/2.3/control-flow-collections/feature-specification.md
[spread proposal]: {{site.repo.dart.lang}}/blob/main/accepted/2.3/spread-collections/feature-specification.md
[generics]: /language/generics
[`Set`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Set-class.html
[if-case]: /language/branches#if-case
