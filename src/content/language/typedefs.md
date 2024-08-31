---
title: Typedefs
# description: Learn about type aliases in Dart.
description: Dart의 타입 별칭에 대해 알아보세요.
toc: false
prevpage:
  url: /language/generics
  # title: Generics
  title: 제네릭
nextpage:
  url: /language/type-system
  # title: Type system
  title: 타입 시스템
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

타입 별칭(type alias)은 종종 `typedef` 키워드로 선언되기 때문에 _typedef_ 라고 불리며, 
타입을 참조하는 간결한 방법입니다. 
다음은 `IntList`라는 타입 별칭을 선언하고 사용하는 예입니다.

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (int-list)"?>
```dart
typedef IntList = List<int>;
IntList il = [1, 2, 3];
```

타입 별칭에는 타입 매개변수가 있을 수 있습니다.

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (list-mapper)"?>
```dart
typedef ListMapper<X> = Map<X, List<X>>;
Map<String, List<String>> m1 = {}; // 장황함.
ListMapper<String> m2 = {}; // 동일한 내용이지만 더 짧고 명확합니다.
```

:::version-note
2.13 이전에는, typedef가 함수 타입으로 제한되었습니다.
새로운 typedef를 사용하려면, 최소 2.13의 [언어 버전][language version]이 필요합니다.
:::

대부분의 상황에서 함수에 typedef 대신 [인라인 함수 타입][inline function types]을 사용하는 것이 좋습니다.
그러나 함수 typedef는 여전히 유용할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (compare)"?>
```dart
typedef Compare<T> = int Function(T a, T b);

int sort(int a, int b) => a - b;

void main() {
  assert(sort is Compare<int>); // True!
}
```

[language version]: /guides/language/evolution#language-versioning
[inline function types]: /effective-dart/design#prefer-inline-function-types-over-typedefs
