---
# title: Iterable collections
title: Iterable 컬렉션
# description: >-
#   An interactive guide to using Iterable objects such as lists and sets.
description: >-
  (리스트와 세트 등의) Iterable 객체를 사용하는 방법에 대한 상호작용 가이드입니다.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

이 튜토리얼에서는 [Iterable][iterable class] 클래스를 구현하는 컬렉션을 사용하는 방법을 알려줍니다. 
예를 들어 [List][list class] 및 [Set][set class] Iterable은, 
모든 종류의 Dart 애플리케이션에 대한 기본 구성 요소이며, 
눈치채지 못하더라도 이미 사용하고 있을 것입니다. 
이 튜토리얼은 Iterable을 최대한 활용하는 데 도움이 됩니다.

내장된 DartPad 편집기를 사용하여, 예제 코드를 실행하고 연습 문제를 완료하여 지식을 테스트할 수 있습니다.

이 튜토리얼을 최대한 활용하려면, [Dart 구문](/language)에 대한 기본 지식이 있어야 합니다.

이 튜토리얼은 다음 자료를 다룹니다.

* Iterable의 요소를 읽는 방법.
* Iterable의 요소가 조건을 충족하는지 확인하는 방법.
* Iterable의 내용을 필터링하는 방법.
* Iterable의 내용을 다른 값으로 매핑하는 방법.

이 튜토리얼을 완료하는 데 걸리는 예상 시간: 60분.

:::note
이 페이지에서는 내장된 DartPad를 사용하여 예제와 연습문제를 표시합니다.
{% render 'dartpads-embedded-troubleshooting.md' %}
:::

이 튜토리얼의 연습은 부분적으로 완성된 코드 조각을 포함합니다. 
DartPad를 사용하여 코드를 완성하고, **Run** 버튼을 클릭하여 지식을 테스트할 수 있습니다. 
**`main` 함수나 그 아래에 있는 테스트 코드를 편집하지 마세요**.

도움이 필요하면 각 연습 후에 **Hint** 또는 **Solution** 드롭다운을 확장하세요.

## 컬렉션이란 무엇인가요? {:#what-are-collections}

컬렉션은 _요소(elements)_ 라고 불리는 객체 그룹을 나타내는 객체입니다. 
Iterables 객체는 일종의 컬렉션입니다.

컬렉션은 비어 있을 수도 있고, 여러 요소를 포함할 수도 있습니다. 
목적에 따라, 컬렉션은 다른 구조와 구현을 가질 수 있습니다. 
다음은 가장 일반적인 컬렉션 타입 중 일부입니다.

* [List:][list class] 인덱스로 요소를 읽는 데 사용됩니다.
* [Set:][set class] 한 번만 발생할 수 있는 요소를 포함하는 데 사용됩니다.
* [Map:][map class] 키를 사용하여 요소를 읽는 데 사용됩니다.

## Iterable이란 무엇인가요? {:#what-is-an-iterable}

`Iterable`은 순차적으로 액세스할 수 있는 요소의 컬렉션입니다.

Dart에서, `Iterable`은 추상 클래스이므로, 직접 인스턴스화할 수 없습니다. 
그러나, 새 `List` 또는 `Set`을 만들어, 새 `Iterable`을 만들 수 있습니다.

`List`와 `Set`은 모두 `Iterable`이므로, `Iterable` 클래스와 동일한 메서드와 속성을 갖습니다.

`Map`은 구현에 따라, 내부적으로 다른 데이터 구조를 사용합니다. 
예를 들어, [HashMap][hashmap class]는 키를 사용하여 요소(_값(values)_ 이라고도 함)를 가져오는, 
해시 테이블을 사용합니다. 
`Map`의 요소는 맵의 `entries` 또는 `values` 속성을 사용하여, `Iterable` 객체로 읽을 수도 있습니다.

이 예에서는 `int`의 `List`를 보여줍니다. 
이는 `int`의 `Iterable`이기도 ​​합니다.

<?code-excerpt "iterables/test/iterables_test.dart (iterable)"?>
```dart
Iterable<int> iterable = [1, 2, 3];
```

`List`와의 차이점은 `Iterable`의 경우, 
인덱스로 요소를 읽는 것이 효율적일 것이라고 보장할 수 없다는 것입니다. 
`Iterable`은 `List`와 달리 `[]` 연산자가 없습니다.

예를 들어, **잘못된** 다음 코드를 고려해 보세요.

<?code-excerpt "iterables/test/iterables_test.dart (iterable-elementat)" replace="/\.elementAt\(1\)/[![1]!]/g"?>
```dart tag=bad
Iterable<int> iterable = [1, 2, 3];
int value = iterable[![1]!];
```

`[]`로 요소를 읽으면, 컴파일러는 `'[]'` 연산자가 `Iterable` 클래스에 대해 정의되지 않았다고 알려줍니다. 
즉, 이 경우 `[index]`를 사용할 수 없습니다.

대신 `elementAt()`로 요소를 읽을 수 있습니다. 
이 함수는 해당 위치에 도달할 때까지, 반복 가능한(iterable) 요소의 요소를 단계별로 살펴봅니다.

<?code-excerpt "iterables/test/iterables_test.dart (iterable-elementat)"?>
```dart
Iterable<int> iterable = [1, 2, 3];
int value = iterable.elementAt(1);
```

다음 섹션으로 넘어가서, `Iterable`의 요소에 접근하는 방법에 대해 자세히 알아보세요.

## 요소 읽기 {:#reading-elements}

`for-in` 루프를 사용하면, iterable의 요소를 순차적으로 읽을 수 있습니다.

### 예: for-in 루프 사용 {:#example-using-a-for-in-loop}

다음 예제에서는 `for-in` 루프를 사용하여 요소를 읽는 방법을 보여줍니다.

<?code-excerpt "iterables/test/iterables_test.dart (for-in)"?>
```dartpad
void main() {
  const iterable = ['Salad', 'Popcorn', 'Toast'];
  for (final element in iterable) {
    print(element);
  }
}
```

:::note 세부 정보
무대 뒤에서, `for-in` 루프는 _iterator_ 를 사용합니다. 
그러나 `for-in`은 읽고 이해하기 쉽고 오류가 발생할 가능성이 적기 때문에, 
[Iterator API][iterator class]가 직접 사용되는 경우는 거의 없습니다.
:::

:::secondary 주요 용어
* **Iterable**: Dart [Iterable][iterable class] 클래스.
* **Iterator**: `for-in`에서 `Iterable` 객체에서 요소를 읽는 데 사용되는 객체.
* **`for-in` 루프**: `Iterable`에서 요소를 순차적으로 읽는 쉬운 방법.
:::

### 예: first와 last 사용 {:#example-using-first-and-last}

어떤 경우에는, `Iterable`의 첫 번째 또는 마지막 요소에만 액세스하고 싶을 수 있습니다.

`Iterable` 클래스를 사용하면, 요소에 직접 액세스할 수 없으므로, 
`iterable[0]`을 호출하여 첫 ​​번째 요소에 액세스할 수 없습니다. 
대신, `first`를 사용하면, 첫 번째 요소를 가져옵니다.

또한, `Iterable` 클래스를 사용하면, 
연산자 `[]`를 사용하여 마지막 요소에 액세스할 수 없지만, 
`last` 속성을 사용할 수 있습니다.

:::warning
`Iterable`의 마지막 요소에 액세스하려면, 
다른 모든 요소를 ​​단계별로 실행해야 하므로, 
**`last`는 느릴 수 있습니다.** 
**빈 `Iterable`**에 `first` 또는 `last`를 사용하면, 
[StateError][StateError class]가 발생합니다.
:::

<?code-excerpt "iterables/test/iterables_test.dart (first-last)"?>
```dartpad
void main() {
  Iterable<String> iterable = const ['Salad', 'Popcorn', 'Toast'];
  print('The first element is ${iterable.first}');
  print('The last element is ${iterable.last}');
}
```

이 예제에서는, `first`와 `last`를 사용하여, 
`Iterable`의 첫 번째와 마지막 요소를 가져오는 방법을 보았습니다. 
조건을 만족하는 첫 번째 요소를 찾는 것도 가능합니다. 
다음 섹션에서는, `firstWhere()`라는 메서드를 사용하여, 이를 수행하는 방법을 보여줍니다.

### 예: firstWhere() 사용 {:#example-using-firstwhere}

이미 `Iterable`의 요소에 순차적으로 접근할 수 있고, 
첫 번째 또는 마지막 요소를 쉽게 얻을 수 있다는 것을 보았습니다.

이제, `firstWhere()`를 사용하여 특정 조건을 충족하는 첫 번째 요소를 찾는 방법을 알아봅니다. 
이 메서드는 입력이 특정 조건을 충족하는 경우, true를 반환하는 함수인 _predicate (술어)_ 를 전달해야 합니다.

<?code-excerpt "iterables/test/iterables_test.dart (firstwhere)"?>
```dart
String element = iterable.firstWhere((element) => element.length > 5);
```

예를 들어, 5개 이상의 문자로 구성된 첫 번째 `String`을 찾으려면, 
요소 크기가 5보다 클 때 true를 반환하는 술어(predicate)를 전달해야 합니다.

다음 예제를 실행하여, `firstWhere()`가 어떻게 작동하는지 확인하세요. 
모든 함수가 같은 결과를 낼 것이라고 생각하시나요?

<?code-excerpt "iterables/test/iterables_test.dart (first-where-long)"?>
```dartpad
bool predicate(String item) {
  return item.length > 5;
}

void main() {
  const items = ['Salad', 'Popcorn', 'Toast', 'Lasagne'];

  // 간단한 표현식으로 찾을 수 있습니다:
  var foundItem1 = items.firstWhere((item) => item.length > 5);
  print(foundItem1);

  // 또는 함수 블록을 사용해 보세요.
  var foundItem2 = items.firstWhere((item) {
    return item.length > 5;
  });
  print(foundItem2);

  // 또는 함수 참조를 전달합니다.
  var foundItem3 = items.firstWhere(predicate);
  print(foundItem3);

  // 값을 찾을 수 없는 경우에는, `orElse` 함수를 사용할 수도 있습니다!
  var foundItem4 = items.firstWhere(
    (item) => item.length > 10,
    orElse: () => 'None!',
  );
  print(foundItem4);
}
```

이 예에서, 술어를 작성하는 세 가지 다른 방법을 볼 수 있습니다.

* **표현식으로:**
  테스트 코드에는 화살표 구문(`=>`)을 사용하는 한 줄이 있습니다.
* **블록으로:**
  테스트 코드에는 괄호와 반환 문 사이에 여러 줄이 있습니다.
* **함수로:**
  테스트 코드는 매개변수로 `firstWhere()` 메서드에 전달되는 외부 함수에 있습니다.

옳고 그른 방법은 없습니다. 
자신에게 가장 잘 맞는 방법을 사용하면, 코드를 읽고 이해하기가 더 쉬워집니다.

마지막 예에서는 `firstWhere()`를 선택적 명명된 매개변수 `orElse`로 호출합니다. 
이는 요소가 발견되지 않을 때 대안을 제공합니다. 
이 경우, 제공된 조건을 충족하는 요소가 없기 때문에, 텍스트 `'None!'`이 반환됩니다.


:::note
If no element satisfies the test predicate and
the `orElse` parameter isn't provided,
then `firstWhere()` throws a [StateError.][StateError class]
:::

:::secondary 간단한 검토
* `Iterable`의 요소는 순차적으로 액세스해야 합니다.
* 모든 요소를 ​​반복하는 가장 쉬운 방법은 `for-in` 루프를 사용하는 것입니다.
* `first` 및 `last` getters를 사용하여, 첫 번째 및 마지막 요소를 가져올 수 있습니다.
* `firstWhere()`로 조건을 충족하는 첫 번째 요소를 찾을 수도 있습니다.
* 테스트 술어를 표현식, 블록 또는 함수로 작성할 수 있습니다.

  **핵심 용어:**
* **술어 (Predicate):**
  특정 조건이 충족되면, `true`를 반환하는 함수입니다.
:::

### 연습: 테스트 술어(predicate) 작성 연습 {:#exercise-practice-writing-a-test-predicate}

다음 연습은 부분적으로 완성된 코드 스니펫을 포함하는 실패한 유닛 테스트입니다. 
여러분의 과제는 테스트를 통과시키는 코드를 작성하여 연습을 완료하는 것입니다. 
`main()`을 구현할 필요는 없습니다.

이 연습은 `singleWhere()`를 소개합니다. 
이 메서드는 `firstWhere()`와 비슷하게 작동하지만, 
이 경우 `Iterable`의 한 요소만 술어를 충족할 것으로 예상합니다. 
`Iterable`의 두 개 이상의 요소 또는 술어 조건을 충족하는 요소가 없는 경우, 
메서드는 [StateError][StateError class] 예외를 throw합니다.

:::warning
`singleWhere()`는 마지막 요소까지 전체 `Iterable`을 단계별로 실행합니다. 
`Iterable`이 무한하거나 많은 수의 요소를 포함하고 있는 경우, 문제가 발생할 수 있습니다.
:::

목표는 다음 조건을 만족하는 `singleWhere()`에 대한 술어를 구현하는 것입니다.

* 요소에 문자 `'a'`가 포함됩니다.
* 요소는 문자 `'M'`으로 시작합니다.

테스트 데이터의 모든 요소는 [문자열][String class]입니다. 
도움을 위해 클래스 문서를 확인할 수 있습니다.

```dartpad theme="dark"
// 다음 조건으로 singleWhere의 술어를 구현하세요.
// * 요소에 문자 `'a'`가 포함되어 있습니다
// * 요소가 문자 `'M'`으로 시작합니다
String singleWhere(Iterable<String> items) {
  return items.singleWhere(TODO('Implement the outlined predicate.'));
}

// 다음 코드는 당신의 솔루션에 대한 피드백을 제공하는 데 사용됩니다.
// 읽거나 수정할 필요가 없습니다.
void main() {
  const items = [
    'Salad',
    'Popcorn',
    'Milk',
    'Toast',
    'Sugar',
    'Mozzarella',
    'Tomato',
    'Egg',
    'Water',
  ];

  try {
    final str = singleWhere(items);
    if (str == 'Mozzarella') {
      print('Success. All tests passed!');
    } else {
      print(
        'Tried calling singleWhere, but received $str instead of '
        'the expected value \'Mozzarella\'',
      );
    }
  } on StateError catch (stateError) {
    print(
      'Tried calling singleWhere, but received a StateError: ${stateError.message}. '
      'singleWhere will fail if 0 or many elements match the predicate.',
    );
  } on UnimplementedError {
    print(
      'Tried running `singleWhere`, but received an error. '
      'Did you implement the function?',
    );
  } catch (e) {
    print('Tried calling singleWhere, but received an exception: $e');
  }
}
```

<details>
  <summary title="Expand for a hint on the predicate exercise.">힌트</summary>

  당신의 솔루션에서는 `String` 클래스의 `contains` 및 `startsWith` 메서드를 활용할 수 있습니다.

</details>

<details>
  <summary title="Expand for the solution of the predicate exercise.">솔루션</summary>

  ```dart
  String singleWhere(Iterable<String> items) {
    return items.singleWhere(
            (element) => element.startsWith('M') && element.contains('a'));
  }
  ```

</details>

## 조건 확인 {:#checking-conditions}

`Iterable`로 작업할 때, 때때로 컬렉션의 모든 요소가 어떤 조건을 만족하는지 확인해야 합니다.

다음과 같은 `for-in` 루프를 사용하여 솔루션을 작성하고 싶을 수 있습니다.

<?code-excerpt "iterables/test/iterables_test.dart (every-bad)"?>
```dart tag=bad
for (final item in items) {
  if (item.length < 5) {
    return false;
  }
}
return true;
```

하지만, `every()` 메서드를 사용하면 동일한 작업을 수행할 수 있습니다.

<?code-excerpt "iterables/test/iterables_test.dart (every-good)"?>
```dart
return items.every((item) => item.length >= 5);
```

`every()` 메서드를 사용하면 더 읽기 쉽고 간결하며 오류가 덜 발생하는 코드를 얻을 수 있습니다.

### 예: any() 및 every() 사용 {:#example-using-any-and-every}

`Iterable` 클래스는 조건을 검증하는 데 사용할 수 있는 두 가지 메서드를 제공합니다.

* `any()`: 적어도 하나의 요소가 조건을 충족하면, true를 반환합니다.
* `every()`: 모든 요소가 조건을 충족하면, true를 반환합니다.

이 연습을 실행하여 실제로 작동하는지 확인하세요.

<?code-excerpt "iterables/test/iterables_test.dart (any-every)"?>
```dartpad
void main() {
  const items = ['Salad', 'Popcorn', 'Toast'];

  if (items.any((item) => item.contains('a'))) {
    print('At least one item contains "a"');
  }

  if (items.every((item) => item.length >= 5)) {
    print('All items have length >= 5');
  }
}
```

이 예에서, `any()`는 적어도 하나의 요소에 문자 `a`가 포함되어 있는지 확인하고, 
`every()`는 모든 요소의 길이가 5 이상인지 확인합니다.

코드를 실행한 후 `any()`의 술어를 변경하여 false를 반환해 보세요.

<?code-excerpt "iterables/test/iterables_test.dart (any-false)"?>
```dart
if (items.any((item) => item.contains('Z'))) {
  print('At least one item contains "Z"');
} else {
  print('No item contains "Z"');
}
```

`any()`를 사용하여 `Iterable`의 어떤 요소도 특정 조건을 만족하지 않는지 확인할 수도 있습니다.

### 연습: Iterable이 조건을 만족하는지 확인 {:#exercise-verify-that-an-iterable-satisfies-a-condition}

다음 연습은 이전 예제에서 설명한, `any()` 및 `every()` 메서드를 사용하는 연습을 제공합니다. 
이 경우, `age` 멤버 필드가 있는 `User` 객체로 표현된 사용자 그룹으로 작업합니다.

`any()` 및 `every()`를 사용하여 두 함수를 구현합니다.

* 1부: `anyUserUnder18()` 구현.
  * 적어도 한 명의 사용자가 17세 이하이면, `true`를 반환합니다.
* 2부: `everyUserOver13()` 구현.
  * 모든 사용자가 14세 이상인 경우, `true`를 반환합니다.

```dartpad
bool anyUserUnder18(Iterable<User> users) {
  // TODO: anyUserUnder18 함수를 구현합니다.
}

bool everyUserOver13(Iterable<User> users) {
  // TODO: everyUserOver13 함수를 구현합니다.
}

class User {
  final String name;
  final int age;

  User(
    this.name,
    this.age,
  );
}

// 다음 코드는 당신의 솔루션에 대한 피드백을 제공하는 데 사용됩니다.
// 읽거나 수정할 필요가 없습니다.
void main() {
  final users = [
    User('Alice', 21),
    User('Bob', 17),
    User('Claire', 52),
    User('David', 14),
  ];

  try {
    final out = anyUserUnder18(users);
    if (!out) {
      print('Looks like `anyUserUnder18` is wrong. Keep trying!');
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `anyUserUnder18`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print('Tried running `anyUserUnder18`, but received an exception: $e');
    return;
  }

  try {
    // 18세 이상의 사용자가 한 명만 있는 경우, false여야 합니다.
    final out = anyUserUnder18([User('Alice', 21)]);
    if (out) {
      print(
          'Looks like `anyUserUnder18` is wrong. What if all users are over 18?');
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `anyUserUnder18`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print(
      'Tried running `anyUserUnder18([User("Alice", 21)])`, '
      'but received an exception: $e',
    );
    return;
  }

  try {
    final out = everyUserOver13(users);
    if (!out) {
      print(
        'Looks like `everyUserOver13` is wrong. '
        'There are no users under 13!',
      );
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `everyUserOver13`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print(
      'Tried running `everyUserOver13`, '
      'but received an exception: $e',
    );
    return;
  }

  try {
    final out = everyUserOver13([User('Dan', 12)]);
    if (out) {
      print(
        'Looks like `everyUserOver13` is wrong. '
        'There is at least one user under 13!',
      );
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `everyUserOver13`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print(
      'Tried running `everyUserOver13([User(\'Dan\', 12)])`, '
      'but received an exception: $e',
    );
    return;
  }

  print('Success. All tests passed!');
}
```

<details>
  <summary title="Expand for a hint on the conditional filtering exercise.">힌트</summary>

  `Iterable` 클래스의 `any` 및 `every` 메서드를 사용하는 것을 잊지 마세요. 
  이러한 메서드 사용에 대한 도움말 및 예는, 
  [이전에 논의된 내용](#example-using-any-and-every)을 참조하세요.

</details>

<details>
  <summary title="Expand for the solution of the conditional filtering exercise.">솔루션</summary>

  ```dart
  bool anyUserUnder18(Iterable<User> users) {
    return users.any((user) => user.age < 18);
  }
  
  bool everyUserOver13(Iterable<User> users) {
    return users.every((user) => user.age > 13);
  }
  ```

</details>

:::secondary 간단한 리뷰
* `for-in` 루프를 사용하여 조건을 확인할 수 있지만, 더 나은 방법이 있습니다.
* `any()` 메서드를 사용하면, 조건을 충족하는 요소가 존재하는지를 확인할 수 있습니다.
* `every()` 메서드를 사용하면, 모든 요소가 조건을 충족하는지 확인할 수 있습니다.
:::

## 필터링 {:#filtering}

이전 섹션에서는 특정 조건을 만족하는 요소를 찾는 데 도움이 되는, 
`firstWhere()` 또는 `singleWhere()`와 같은 메서드를 다룹니다.

하지만 특정 조건을 만족하는 모든 요소를 ​​찾으려면 어떻게 해야 할까요? 
`where()` 메서드를 사용하여 이를 달성할 수 있습니다.

<?code-excerpt "iterables/test/iterables_test.dart (where)"?>
```dart
var evenNumbers = numbers.where((number) => number.isEven);
```

이 예에서, `numbers`는 여러 개의 `int` 값을 가진 `Iterable`을 포함하고, 
`where()`는 짝수인 모든 숫자를 찾습니다.

`where()`의 출력은 또 다른 `Iterable`이며, 
이를 사용하여 반복하거나 다른 `Iterable` 메서드를 적용할 수 있습니다. 
다음 예에서, `where()`의 출력은 `for-in` 루프 내부에서 직접 사용됩니다.

<?code-excerpt "iterables/test/iterables_test.dart (where-for)"?>
```dart
var evenNumbers = numbers.where((number) => number.isEven);
for (final number in evenNumbers) {
  print('$number is even');
}
```

### 예: where() 사용 {:#example-using-where}

이 예제를 실행하여, `where()`를 `any()` 등 다른 메서드와 함께 사용하는 방법을 확인하세요.

<?code-excerpt "iterables/test/iterables_test.dart (numbers-where)"?>
```dartpad
void main() {
  var evenNumbers = const [1, -2, 3, 42].where((number) => number.isEven);

  for (final number in evenNumbers) {
    print('$number is even.');
  }

  if (evenNumbers.any((number) => number.isNegative)) {
    print('evenNumbers contains negative numbers.');
  }

  // 술어를 만족하는 요소가 없으면, 출력은 비어 있습니다.
  var largeNumbers = evenNumbers.where((number) => number > 1000);
  if (largeNumbers.isEmpty) {
    print('largeNumbers is empty!');
  }
}
```

이 예에서, `where()`는 짝수인 모든 숫자를 찾는 데 사용되고, 
`any()`는 결과에 음수가 포함되어 있는지 확인하는 데 사용됩니다.

이 예의 후반부에서, `where()`는 1000보다 큰 모든 숫자를 찾는 데 다시 사용됩니다. 
숫자가 없기 때문에 결과는 빈 `Iterable`입니다.

:::note
`where()`의 술어를 만족하는 요소가 없으면, 메서드는 빈 `Iterable`을 반환합니다. 
`singleWhere()` 또는 `firstWhere()`와 달리, 
`where()`는 [StateError][StateError class] 예외를 throw하지 않습니다.
:::

### 예: takeWhile 사용 {:#example-using-takewhile}

`takeWhile()` 및 `skipWhile()` 메서드는, 
또한 `Iterable`에서 요소를 필터링하는 데 도움이 될 수 있습니다.

이 예제를 실행하여, 
`takeWhile()` 및 `skipWhile()`가
숫자를 포함하는 `Iterable`을 어떻게 분할할 수 있는지 확인하세요.

<?code-excerpt "iterables/test/iterables_test.dart (take-while-long)"?>
```dartpad
void main() {
  const numbers = [1, 3, -2, 0, 4, 5];

  var numbersUntilZero = numbers.takeWhile((number) => number != 0);
  print('Numbers until 0: $numbersUntilZero');

  var numbersStartingAtZero = numbers.skipWhile((number) => number != 0);
  print('Numbers starting at 0: $numbersStartingAtZero');
}
```

이 예에서, `takeWhile()`은 술어를 만족하는 요소 이전의 모든 요소를 ​​포함하는 `Iterable`을 반환합니다. 
반면, `skipWhile()`은 술어를 _만족하지 않는_ 첫 번째 요소를 포함하여, 
그 이후의 모든 요소를 ​​포함하는 `Iterable`을 반환합니다.

예제를 실행한 후, `takeWhile()`을 변경하여, 첫 번째 음수에 도달할 때까지 요소를 가져옵니다.

<?code-excerpt "iterables/test/iterables_test.dart (takewhile)"?>
```dart
var numbersUntilNegative =
    numbers.takeWhile((number) => !number.isNegative);
```

조건 `number.isNegative`가 `!`로 부정된다는 점에 주의하세요.

### 연습: 리스트에서 요소 필터링하기 {:#exercise-filtering-elements-from-a-list}

다음 연습은 이전 연습의 클래스 `User`와 함께, `where()` 메서드를 사용하는 연습을 제공합니다.

`where()`를 사용하여, 두 함수를 구현합니다.

* 1부: `filterOutUnder21()`를 구현합니다.
  * 21세 이상의 모든 사용자를 포함하는 `Iterable`을 반환합니다.
* 2부: `findShortNamed()`를 구현합니다.
  * 이름의 길이가 3 이하인 모든 사용자를 포함하는 `Iterable`을 반환합니다.


```dartpad theme="dark"
Iterable<User> filterOutUnder21(Iterable<User> users) {
  // TODO: filterOutUnder21 함수를 구현합니다.
}

Iterable<User> findShortNamed(Iterable<User> users) {
  // TODO: findShortNamed 함수를 구현합니다.
}

class User {
  final String name;
  final int age;

  User(
    this.name,
    this.age,
  );
}

// 다음 코드는 당신의 솔루션에 대한 피드백을 제공하는 데 사용됩니다.
// 읽거나 수정할 필요가 없습니다.
void main() {
  final users = [
    User('Alice', 21),
    User('Bob', 17),
    User('Claire', 52),
    User('Dan', 12),
  ];

  try {
    final out = filterOutUnder21(users);
    if (out.any((user) => user.age < 21) || out.length != 2) {
      print(
        'Looks like `filterOutUnder21` is wrong, there are '
        'exactly two users with age under 21. Keep trying!',
      );
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `filterOutUnder21`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print(
      'Tried running `filterOutUnder21`, '
      'but received an exception: ${e.runtimeType}',
    );
    return;
  }

  try {
    final out = findShortNamed(users);
    if (out.any((user) => user.name.length > 3) || out.length != 2) {
      print(
        'Looks like `findShortNamed` is wrong, there are '
        'exactly two users with a three letter name. Keep trying!',
      );
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `findShortNamed`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print(
      'Tried running `findShortNamed`, '
      'but received an exception: ${e.runtimeType}',
    );
    return;
  }

  print('Success. All tests passed!');
}
```

<details>
  <summary title="Expand for a hint on the filtering elements exercise.">힌트</summary>

  `Iterable` 클래스의 `where` 메서드를 활용하는 것을 잊지 마세요. 
  `where` 사용에 대한 도움말과 예는, 
  [이전에 논의한 내용](#example-using-where)을 참조하세요.

</details>

<details>
  <summary title="Expand for the solution of the filtering elements exercise.">솔루션</summary>

  ```dart
  Iterable<User> filterOutUnder21(Iterable<User> users) {
    return users.where((user) => user.age >= 21);
  }
  
  Iterable<User> findShortNamed(Iterable<User> users) {
    return users.where((user) => user.name.length <= 3);
  }
  ```

</details>

:::secondary 간단한 검토
* `where()`로 `Iterable`의 요소를 필터링합니다.
* `where()`의 출력은 또 다른 `Iterable`입니다.
* `takeWhile()` 및 `skipWhile()`를 사용하여, 조건이 충족될 때까지 또는 충족된 후에 요소를 가져옵니다.
* 이러한 메서드의 출력은 빈 `Iterable`일 수 있습니다.
:::

## 매핑 {:#mapping}

`Iterables`를 `map()` 메서드로 매핑하면, 
각 요소에 함수를 적용하여, 각 요소를 새 요소로 바꿀 수 있습니다.

<?code-excerpt "iterables/test/iterables_test.dart (map-int)"?>
```dart
Iterable<int> output = numbers.map((number) => number * 10);
```

이 예에서, `Iterable` 숫자의 각 요소는 10으로 곱해집니다.

`map()`을 사용하여 요소를 다른 객체로 변환할 수도 있습니다. 
예를 들어, 다음 예에서 볼 수 있듯이, 모든 `int`를 `String`으로 변환합니다.

<?code-excerpt "iterables/test/iterables_test.dart (map-string)"?>
```dart
Iterable<String> output = numbers.map((number) => number.toString());
```

:::note
`map()`은 _지연(lazy)_ `Iterable`을 반환합니다. 
즉, 제공된 함수는 요소가 iterated 될 때만 호출됩니다.
:::

### 예: map을 사용하여 요소 변경 {:#example-using-map-to-change-elements}

`map()`를 사용하여 `Iterable`의 모든 요소를 ​​2로 곱하는 방법을 보려면 이 예제를 실행하세요. 
출력은 어떻게 될까요?

<?code-excerpt "iterables/test/iterables_test.dart (numbers-by-two)"?>
```dartpad
void main() {
  var numbersByTwo = const [1, -2, 3, 42].map((number) => number * 2);
  print('Numbers: $numbersByTwo');
}
```

### 연습: 다른 타입으로 매핑하기 {:#exercise-mapping-to-a-different-type}

이전 예제에서, `Iterable`의 요소를 2로 곱했습니다. 
해당 연산의 입력과 출력은 모두 `int`의 `Iterable`이었습니다.

이 연습에서, 코드는 `User`의 `Iterable`을 사용하고, 
각 사용자의 이름과 나이가 포함된 문자열이 포함된 `Iterable`을 반환해야 합니다.

`Iterable`의 각 문자열은 다음 형식을 따라야 합니다. 
`'{name} is {age}'` — 예: `'Alice is 21'`.

```dartpad theme="dark"
Iterable<String> getNameAndAges(Iterable<User> users) {
  // TODO: getNameAndAges 함수를 구현합니다.
}

class User {
  final String name;
  final int age;

  User(
    this.name,
    this.age,
  );
}

// 다음 코드는 당신의 솔루션에 대한 피드백을 제공하는 데 사용됩니다.
// 읽거나 수정할 필요가 없습니다.
void main() {
  final users = [
    User('Alice', 21),
    User('Bob', 17),
    User('Claire', 52),
  ];

  try {
    final out = getNameAndAges(users).toList();
    if (!_listEquals(out, ['Alice is 21', 'Bob is 17', 'Claire is 52'])) {
      print(
        'Looks like `getNameAndAges` is wrong. Keep trying! '
        'The output was: $out',
      );
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `getNameAndAges`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print('Tried running the function, but received an exception: $e');
    return;
  }

  print('Success. All tests passed!');
}

bool _listEquals<T>(List<T>? a, List<T>? b) {
  if (a == null) return b == null;
  if (b == null || a.length != b.length) return false;
  for (var index = 0; index < a.length; index += 1) {
    if (a[index] != b[index]) return false;
  }
  return true;
}
```

<details>
  <summary title="Expand for a hint on the mapping elements exercise.">힌트</summary>

  `Iterable` 클래스의 `map` 메서드를 활용하는 것을 잊지 마세요. 
  `map`을 사용하는 데 대한 도움말과 예는, 
  [이전에 논의한 내용](#example-using-map-to-change-elements)을 참조하세요.

  여러 값을 단일 문자열로 연결하려면, 
  [문자열 보간](/language/built-in-types#string-interpolation)을 사용하는 것을 고려하세요.

</details>

<details>
  <summary title="Expand for the solution of the mapping elements exercise.">솔루션</summary>

  ```dart
  Iterable<String> getNameAndAges(Iterable<User> users) {
    return users.map((user) => '${user.name} is ${user.age}');
  }
  ```

</details>

:::secondary 간단한 리뷰
* `map()`은 `Iterable`의 모든 요소에 함수를 적용합니다.
* `map()`의 출력은 또다른 `Iterable`입니다.
* 함수는 `Iterable`이 반복될 때까지 평가되지 않습니다.
:::

## 연습: 모두 합치기 {:#exercise-putting-it-all-together}

이제 마지막 연습으로, 배운 내용을 연습할 시간입니다.

이 연습은 문자열을 받는 생성자가 있는 `EmailAddress` 클래스를 제공합니다. 
제공되는 또 다른 함수는 이메일 주소가 유효한지 테스트하는 `isValidEmailAddress()`입니다.

| 생성자/함수  | 타입 시그니쳐                           | 설명                                             |
|-----------------------|------------------------------------------|---------------------------------------------------------|
| EmailAddress()        | `EmailAddress(String address)`           | 지정된 주소에 대한 `EmailAddress`를 생성합니다.    | 
| isValidEmailAddress() | `bool isValidEmailAddress(EmailAddress)` | 제공된 `EmailAddress`가 유효한 경우, `true`를 반환합니다. |

{:.table .table-striped}

다음 코드를 작성하세요.

1부: `parseEmailAddresses()` 구현.
  - 이메일 주소가 포함된 `Iterable<String>`을 받아서, 
    `Iterable<EmailAddress>`를 반환하는, 
    `parseEmailAddresses()` 함수를 작성하세요.
  - `map()` 메서드를 사용하여, `String`에서 `EmailAddress`로 매핑합니다.
  - 생성자 `EmailAddress(String)`를 사용하여, `EmailAddress` 객체를 만듭니다.

2부: `anyInvalidEmailAddress()` 구현.
  - `Iterable<EmailAddress>`를 받아서, 
    `Iterable`의 `EmailAddress` 중 하나라도 유효하지 않으면 `true`를 반환하는, 
    `anyInvalidEmailAddress()` 함수를 작성하세요.
  - 제공된 함수 `isValidEmailAddress()`와 함께, 
    `any()` 메서드를 사용합니다.

3부: `validEmailAddresses()` 구현.
  - `Iterable<EmailAddress>`를 가져와서 유효한 주소만 포함하는, 
    또다른 `Iterable<EmailAddress>`를 반환하는, 
    `validEmailAddresses()` 함수를 작성합니다.
  - `where()` 메서드를 사용하여, `Iterable<EmailAddress>`를 필터링합니다.
  - 제공된 함수 `isValidEmailAddress()`를 사용하여, `EmailAddress`가 유효한지 평가합니다.

```dartpad theme="dark"
Iterable<EmailAddress> parseEmailAddresses(Iterable<String> strings) {
  // TODO: parseEmailAddresses 함수를 구현합니다.
}

bool anyInvalidEmailAddress(Iterable<EmailAddress> emails) {
  // TODO: anyInvalidEmailAddress 함수를 구현합니다.
}

Iterable<EmailAddress> validEmailAddresses(Iterable<EmailAddress> emails) {
  // TODO: validEmailAddresses 함수를 구현합니다.
}

class EmailAddress {
  final String address;

  EmailAddress(this.address);

  @override
  bool operator ==(Object other) =>
      identical(this, other) ||
      other is EmailAddress && address == other.address;

  @override
  int get hashCode => address.hashCode;

  @override
  String toString() => 'EmailAddress{address: $address}';
}

// 다음 코드는 당신의 솔루션에 대한 피드백을 제공하는 데 사용됩니다.
// 읽거나 수정할 필요가 없습니다.
void main() {
  const input = [
    'ali@gmail.com',
    'bobgmail.com',
    'cal@gmail.com',
  ];

  const correctInput = ['dash@gmail.com', 'sparky@gmail.com'];

  bool _listEquals<T>(List<T>? a, List<T>? b) {
    if (a == null) return b == null;
    if (b == null || a.length != b.length) return false;
    for (var index = 0; index < a.length; index += 1) {
      if (a[index] != b[index]) return false;
    }
    return true;
  }

  final Iterable<EmailAddress> emails;
  final Iterable<EmailAddress> correctEmails;
  try {
    emails = parseEmailAddresses(input);
    correctEmails = parseEmailAddresses(correctInput);
    if (emails.isEmpty) {
      print(
        'Tried running `parseEmailAddresses`, but received an empty list.',
      );
      return;
    }
    if (!_listEquals(emails.toList(), [
      EmailAddress('ali@gmail.com'),
      EmailAddress('bobgmail.com'),
      EmailAddress('cal@gmail.com'),
    ])) {
      print('Looks like `parseEmailAddresses` is wrong. Keep trying!');
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `parseEmailAddresses`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print(
      'Tried running `parseEmailAddresses`, '
      'but received an exception: $e',
    );
    return;
  }

  try {
    final out = anyInvalidEmailAddress(emails);
    if (!out) {
      print(
        'Looks like `anyInvalidEmailAddress` is wrong. Keep trying! '
        'The result should be false with at least one invalid address.',
      );
      return;
    }
    final falseOut = anyInvalidEmailAddress(correctEmails);
    if (falseOut) {
      print(
        'Looks like `anyInvalidEmailAddress` is wrong. Keep trying! '
        'The result should be false with all valid addresses.',
      );
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `anyInvalidEmailAddress`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print(
        'Tried running `anyInvalidEmailAddress`, but received an exception: $e');
    return;
  }

  try {
    final valid = validEmailAddresses(emails);
    if (emails.isEmpty) {
      print('Tried running `validEmailAddresses`, but received an empty list.');
      return;
    }
    if (!_listEquals(valid.toList(), [
      EmailAddress('ali@gmail.com'),
      EmailAddress('cal@gmail.com'),
    ])) {
      print('Looks like `validEmailAddresses` is wrong. Keep trying!');
      return;
    }
  } on UnimplementedError {
    print(
      'Tried running `validEmailAddresses`, but received an error. '
      'Did you implement the function?',
    );
    return;
  } catch (e) {
    print(
      'Tried running the `validEmailAddresses`, '
      'but received an exception: $e',
    );
    return;
  }

  print('Success. All tests passed!');
}

bool isValidEmailAddress(EmailAddress email) {
  return email.address.contains('@');
}
```

<details>
  <summary title="Expand for the solution of the 'Putting it all together' exercise.">솔루션</summary>

  ```dart
  Iterable<EmailAddress> parseEmailAddresses(Iterable<String> strings) {
    return strings.map((s) => EmailAddress(s));
  }
  
  bool anyInvalidEmailAddress(Iterable<EmailAddress> emails) {
    return emails.any((email) => !isValidEmailAddress(email));
  }
  
  Iterable<EmailAddress> validEmailAddresses(Iterable<EmailAddress> emails) {
    return emails.where((email) => isValidEmailAddress(email));
  }
  ```

</details>

## 다음은 무엇입니까? {:#whats-next}

축하합니다. 튜토리얼을 마쳤습니다! 
더 자세히 알고 싶으시다면, 다음에 어디로 가야 할지에 대한 몇 가지 제안이 있습니다.

* [DartPad]({{site.dartpad}})로 놀아보세요.
* 다른 [튜토리얼](/tutorials)을 시도해보세요.
* 이 튜토리얼에서 다루지 않는 메서드에 대해 알아보려면,
  [Iterable API 참조][iterable class]를 읽어보세요.

[hashmap class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-collection/HashMap-class.html
[iterable class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable-class.html
[iterator class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterator-class.html
[list class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/List-class.html
[map class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Map-class.html
[set class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Set-class.html
[StateError class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/StateError-class.html
[String class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/String-class.html
