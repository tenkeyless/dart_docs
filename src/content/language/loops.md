---
# title: Loops 
title: 루프
# description: Learn how to use loops to control the flow of your Dart code.
description: 루프를 사용하여 Dart 코드의 흐름을 제어하는 ​​방법을 알아보세요.
prevpage:
  url: /language/functions
  # title: Functions
  title: 함수
nextpage:
  url: /language/branches
  # title: Branches
  title: 분기
---

이 페이지에서는 루프와 지원 명령문을 사용하여, Dart 코드의 흐름을 제어하는 ​​방법을 보여줍니다.

- `for` 루프
- `while` 및 `do while` 루프
- `break` 및 `continue`

다음을 사용하여 Dart에서 제어 흐름을 조작할 수도 있습니다.

- `if` 및 `switch`와 같은 [분기][Branching]
- `try`, `catch` 및 `throw`와 같은 [예외][Exceptions]

## For 루프 {:#for-loops}

표준 `for` 루프로 반복할 수 있습니다. 예를 들어:

<?code-excerpt "language/test/control_flow/loops_test.dart (for)"?>
```dart
var message = StringBuffer('Dart is fun');
for (var i = 0; i < 5; i++) {
  message.write('!');
}
```

Dart의 `for` 루프 내부의 클로저는 인덱스의 _값_ 을 캡처합니다.
이렇게 하면 JavaScript에서 흔히 발견되는 함정을 피할 수 있습니다. 예를 들어, 다음을 고려하세요.

<?code-excerpt "language/test/control_flow/loops_test.dart (for-and-closures)"?>
```dart
var callbacks = [];
for (var i = 0; i < 2; i++) {
  callbacks.add(() => print(i));
}

for (final c in callbacks) {
  c();
}
```

예상대로 출력은 `0`과 `1`입니다. 반면, 이 예제는 JavaScript에서 `2`와 `2`를 차례로 출력합니다.

때로는 `List` 또는 `Set`와 같은 [`Iterable`][] 타입을 반복할 때, 
현재 반복 카운터를 알 필요가 없을 수도 있습니다. 
그런 경우, 더 깔끔한 코드를 위해 `for-in` 루프를 사용합니다.

<?code-excerpt "language/lib/control_flow/loops.dart (collection)"?>
```dart
for (final candidate in candidates) {
  candidate.interview();
}
```

반복 가능한 항목에서 얻은 값을 처리하려면, 
`for-in` 루프에서 [패턴][pattern]을 사용할 수도 있습니다.

<?code-excerpt "language/lib/control_flow/loops.dart (collection-for-pattern)"?>
```dart
for (final Candidate(:name, :yearsExperience) in candidates) {
  print('$name has $yearsExperience of experience.');
}
```

:::tip
`for-in`을 사용하는 연습을 하려면, 
[반복 가능한 컬렉션 튜토리얼](/libraries/collections/iterables)을 따르세요.
:::

반복 가능한 클래스에는 또 다른 옵션으로 [forEach()][] 메서드도 있습니다.

<?code-excerpt "language/test/control_flow/loops_test.dart (for-each)"?>
```dart
var collection = [1, 2, 3];
collection.forEach(print); // 1 2 3
```


## While 및 do-while {:#while-and-do-while}

`while` 루프는 루프 전의 조건을 평가합니다.

<?code-excerpt "language/lib/control_flow/loops.dart (while)"?>
```dart
while (!isDone()) {
  doSomething();
}
```

`do`-`while` 루프는 루프 *뒤에* 조건을 평가합니다.

<?code-excerpt "language/lib/control_flow/loops.dart (do-while)"?>
```dart
do {
  printLine();
} while (!atEndOfPage());
```


## Break 및 continue {:#break-and-continue}

루프를 멈추려면 `break`를 사용하세요.

<?code-excerpt "language/lib/control_flow/loops.dart (while-break)"?>
```dart
while (true) {
  if (shutDownRequested()) break;
  processIncomingRequests();
}
```

다음 루프 반복으로 건너뛰려면, `continue`를 사용하세요.

<?code-excerpt "language/lib/control_flow/loops.dart (for-continue)"?>
```dart
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}
```

리스트나 세트와 같은 [`Iterable`][]을 사용하는 경우, 이전 예를 작성하는 방법이 다를 수 있습니다.

<?code-excerpt "language/lib/control_flow/loops.dart (where)"?>
```dart
candidates
    .where((c) => c.yearsExperience >= 5)
    .forEach((c) => c.interview());
```

[exceptions]: /language/error-handling
[branching]: /language/branches
[iteration]: /libraries/dart-core#iteration
[forEach()]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable/forEach.html
[`Iterable`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable-class.html
[pattern]: /language/patterns
