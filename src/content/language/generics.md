---
# title: Generics
title: 제네릭
# description: Learn about generic types in Dart.
description: Dart의 제네릭 타입에 대해 알아보세요.
prevpage:
  url: /language/collections
  # title: Collections
  title: 컬렉션
nextpage:
  url: /language/typedefs
  title: Typedefs
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

기본 배열 타입인 [`List`][]에 대한 API 문서를 보면, 실제로 타입이 `List<E>`라는 것을 알 수 있습니다. 
\<...\> 표기법은 List를 *제네릭(generic)* (또는 *매개변수화된(parameterized)*) 타입으로 표시합니다. 
즉, 형식적 타입 매개변수가 있는 타입입니다. 
[관례에 따라][By convention], 대부분의 타입 변수는 E, T, S, K, V와 같이 단일 문자 이름을 갖습니다.

## 제네릭을 사용하는 이유는 무엇입니까? {:#why-use-generics}

제네릭은 종종 타입 안전성을 위해 필요하지만, 코드를 실행하는 것 이상의 이점이 있습니다.

* 제네릭 타입을 올바르게 지정하면, 더 나은 코드가 생성됩니다.
* 제네릭을 사용하면, 코드 중복을 줄일 수 있습니다.

문자열만 포함하는 리스트를 원하는 경우 `List<String>`("문자열 리스트"라고 읽음)로 선언할 수 있습니다. 
이렇게 하면, 여러분, 동료 프로그래머, 도구가 리스트에 문자열이 아닌 항목을 할당하는 것이 실수일 가능성이 있음을 감지할 수 있습니다. 
다음은 그 예입니다.

```dart tag=fails-sa
var names = <String>[];
names.addAll(['Seth', 'Kathy', 'Lars']);
names.add(42); // 에러
```

제네릭을 사용하는 또 다른 이유는 코드 중복을 줄이는 것입니다. 
제네릭을 사용하면 정적 분석의 이점을 활용하면서도, 여러 타입 간에 단일 인터페이스와 구현을 공유할 수 있습니다. 
예를 들어, 객체를 캐싱하기 위한 인터페이스를 만든다고 가정해 보겠습니다.

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (object-cache)"?>
```dart
abstract class ObjectCache {
  Object getByKey(String key);
  void setByKey(String key, Object value);
}
```

이 인터페이스의 문자열별 버전이 필요하다는 것을 알게 되어, 다른 인터페이스를 만듭니다.

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (string-cache)"?>
```dart
abstract class StringCache {
  String getByKey(String key);
  void setByKey(String key, String value);
}
```

나중에, 이 인터페이스의 숫자별 버전을 원한다고 결정하죠... 아이디어를 얻으셨죠.

제네릭 타입은 이 모든 인터페이스를 만드는 번거로움을 덜어줍니다. 
대신, 타입 매개변수를 취하는 단일 인터페이스를 만들 수 있습니다.

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (cache)"?>
```dart
abstract class Cache<T> {
  T getByKey(String key);
  void setByKey(String key, T value);
}
```

이 코드에서 T는 대체 타입입니다. 개발자가 나중에 정의할 타입이라고 생각할 수 있는 플레이스홀더입니다.

## 컬렉션 리터럴 사용 {:#using-collection-literals}

리스트, 세트 및 맵 리터럴은 매개변수화될 수 있습니다. 
매개변수화된 리터럴은 이미 본 리터럴과 똑같지만, 
여는 괄호 앞에 <code>&lt;<em>type</em>></code>(리스트 및 세트의 경우) 또는, 
<code>&lt;<em>keyType</em>, <em>valueType</em>></code>(맵의 경우)를 추가합니다. 
다음은 타입화된 리터럴을 사용하는 예입니다.

<?code-excerpt "misc/lib/language_tour/generics/misc.dart (collection-literals)"?>
```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var uniqueNames = <String>{'Seth', 'Kathy', 'Lars'};
var pages = <String, String>{
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};
```

## 생성자로 매개변수화된 타입 사용 {:#using-parameterized-types-with-constructors}

생성자를 사용할 때 하나 이상의 타입을 지정하려면, 
클래스 이름 바로 뒤에 유형을 각괄호(`<...>`)로 묶습니다. 예를 들어:

<?code-excerpt "misc/test/language_tour/generics_test.dart (constructor-1)"?>
```dart
var nameSet = Set<String>.from(names);
```

다음 코드는 View 타입의 정수 키와 값을 갖는 맵을 생성합니다.

<?code-excerpt "misc/test/language_tour/generics_test.dart (constructor-2)"?>
```dart
var views = Map<int, View>();
```


## 제네릭 컬렉션과 그 안에 포함된 타입 {:#generic-collections-and-the-types-they-contain}

Dart 제네릭 타입은 *reified(구체화)* 되어 있습니다. 즉, 런타임에 타입 정보를 전달합니다. 
예를 들어, 컬렉션의 타입을 테스트할 수 있습니다.

<?code-excerpt "misc/test/language_tour/generics_test.dart (generic-collections)"?>
```dart
var names = <String>[];
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```

:::note
반면, Java의 제네릭은 *erasure(지우기)* 를 사용하는데, 
이는 제네릭 타입 매개변수가 런타임에 제거된다는 것을 의미합니다. 
Java에서 객체가 List인지 테스트할 수 있지만, 
`List<String>`인지는 테스트할 수 없습니다.
:::


## 매개변수화된 타입 제한 {:#restricting-the-parameterized-type}

제네릭 타입을 구현할 때, 인수로 제공할 수 있는 타입을 제한하여, 
인수가 특정 타입의 하위 타입이어야 하도록 할 수 있습니다. 
`extends`를 사용하여 이를 수행할 수 있습니다.

일반적인 사용 사례는 타입을 `Object`의 하위 타입으로 만들어서(기본값인 [`Object?`][top-and-bottom] 대신),
타입이 null이 되지 않도록 하는 것입니다.

<?code-excerpt "misc/lib/language_tour/generics/misc.dart (non-nullable)"?>
```dart
class Foo<T extends Object> {
  // Foo에 T에 대해 제공된 모든 타입은 null을 허용하지 않아야 합니다.
}
```

`extends`는 `Object` 외의 다른 타입과 함께 사용할 수 있습니다. 
다음은 `SomeBaseClass`를 확장하는 예입니다. 
그러면 `SomeBaseClass`의 멤버를 `T` 타입의 객체에서 호출할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/generics/base_class.dart (generic)" replace="/extends SomeBaseClass(?=. \{)/[!$&!]/g"?>
```dart
class Foo<T [!extends SomeBaseClass!]> {
  // 구현은 여기에 옵니다...
  String toString() => "Instance of 'Foo<$T>'";
}

class Extender extends SomeBaseClass {...}
```

`SomeBaseClass` 또는 그 하위 타입을 제네릭 인수로 사용해도 됩니다.

<?code-excerpt "misc/test/language_tour/generics_test.dart (SomeBaseClass-ok)" replace="/Foo.\w+./[!$&!]/g"?>
```dart
var someBaseClassFoo = [!Foo<SomeBaseClass>!]();
var extenderFoo = [!Foo<Extender>!]();
```

제네릭 인수를 지정하지 않아도 됩니다.

<?code-excerpt "misc/test/language_tour/generics_test.dart (no-generic-arg-ok)" replace="/expect\((.*?).toString\(\), .(.*?).\);/print($1); \/\/ $2/g"?>
```dart
var foo = Foo();
print(foo); // Instance of 'Foo<SomeBaseClass>'
```

`SomeBaseClass`가 아닌 타입을 지정하면 오류가 발생합니다.

```dart tag=fails-sa
var foo = [!Foo<Object>!]();
```


## 제네릭 메서드 사용 {:#using-generic-methods}

메서드와 함수는 또한 타입 인수를 허용합니다.

<!-- {{site.dartpad}}/a02c53b001977efa4d803109900f21bb -->
<!-- https://gist.github.com/a02c53b001977efa4d803109900f21bb -->
<?code-excerpt "misc/test/language_tour/generics_test.dart (method)" replace="/<T.(?=\()|T/[!$&!]/g"?>
```dart
[!T!] first[!<T>!](List<[!T!]> ts) {
  // 초기 작업이나 오류 검사를 수행한 다음...
  [!T!] tmp = ts[0];
  // 추가적인 검사나 처리를 실시합니다...
  return tmp;
}
```

여기서 `first`의 제네릭 타입 매개변수(`<T>`)를 사용하면, 여러 곳에서 타입 인수 `T`를 사용할 수 있습니다.

* 함수의 반환 타입(`T`).
* 인수 타입(`List<T>`).
* 로컬 변수 타입(`T tmp`).

[`List`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/List-class.html
[By convention]: /effective-dart/design#do-follow-existing-mnemonic-conventions-when-naming-type-parameters
[top-and-bottom]: /null-safety/understanding-null-safety#top-and-bottom
