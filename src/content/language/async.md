---
# title: Asynchrony support
title: 비동기 지원
# description: Information on writing asynchronous code in Dart.
description: Dart로 비동기 코드를 작성하는 방법에 대한 정보.
# short-title: Async
short-title: 비동기
prevpage:
  url: /language/concurrency
  # title: Concurrency
  title: 동시성
nextpage:
  url: /language/isolates
  title: Isolates
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

Dart 라이브러리는 [`Future`][] 또는 [`Stream`][] 객체를 반환하는 함수로 가득합니다. 
이러한 함수는 _비동기_ 입니다. 
즉, 작업이 완료될 때까지 기다리지 않고, 시간이 많이 걸릴 수 있는 작업(예: I/O)을 설정한 후, 반환합니다.

`async` 및 `await` 키워드는 비동기 프로그래밍을 지원하여, 
동기 코드와 유사한 비동기 코드를 작성할 수 있습니다.

## Futures 다루기 {:#handling-futures}

완료된 Future의 결과가 필요할 때, 두 가지 옵션이 있습니다.

* `async`와 `await`를 사용합니다.
  * 여기와 [비동기 프로그래밍 튜토리얼](/libraries/async/async-await)에 설명이 있습니다.
* Future API를 사용합니다.
  * [`dart:async` 문서](/libraries/dart-async#future)에 설명이 있습니다.

`async`와 `await`를 사용하는 코드는 비동기적이지만, 동기적 코드와 매우 유사합니다. 
예를 들어, 비동기 함수의 결과를 기다리기 위해, `await`를 사용하는 코드는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/async.dart (await-look-up-version)"?>
```dart
await lookUpVersion();
```

`await`를 사용하려면, 코드가 `async` 함수에 있어야 합니다. 
즉, `async`로 표시된 함수입니다.

<?code-excerpt "misc/lib/language_tour/async.dart (checkVersion)" replace="/async|await/[!$&!]/g"?>
```dart
Future<void> checkVersion() [!async!] {
  var version = [!await!] lookUpVersion();
  // 버전으로 작업하기
}
```

:::note
`async` 함수는 시간이 많이 걸리는 연산을 수행할 수 있지만, 해당 연산을 기다리지 않습니다. 
대신 `async` 함수는 첫 번째 `await` 표현식을 만날 때까지만 실행합니다. 
그런 다음, `Future` 객체를 반환하고, `await` 표현식이 완료된 후에만, 실행을 재개합니다.
:::

`await`를 사용하는 코드에서 오류를 처리하고 정리하려면, 
`try`, `catch`, `finally`를 사용합니다.

<?code-excerpt "misc/lib/language_tour/async.dart (try-catch)"?>
```dart
try {
  version = await lookUpVersion();
} catch (e) {
  // 버전을 찾을 수 없는 문제에 대응
}
```

`async` 함수에서 `await`를 여러 번 사용할 수 있습니다. 
예를 들어, 다음 코드는 함수의 결과를 세 번 기다립니다.

<?code-excerpt "misc/lib/language_tour/async.dart (repeated-await)"?>
```dart
var entrypoint = await findEntryPoint();
var exitCode = await runExecutable(entrypoint, args);
await flushThenExit(exitCode);
```

<code>await <em>expression</em></code>에서, 
<code><em>expression</em></code>의 값은 일반적으로 Future입니다. 
그렇지 않은 경우, 값은 자동으로 Future로 래핑됩니다. 
이 Future 객체는 객체를 반환한다는 약속(promise)을 나타냅니다. 
<code>await <em>expression</em></code>의 값은 반환된 객체입니다. 
await 표현식은 해당 객체를 사용할 수 있을 때까지 실행을 일시 중지합니다.

**`await`를 사용할 때 컴파일 타임 오류가 발생하면, `await`가 `async` 함수에 있는지 확인하세요.** 
예를 들어, 앱의 `main()` 함수에서 `await`를 사용하려면, `main()` 본문을 `async`로 표시해야 합니다.

<?code-excerpt "misc/lib/language_tour/async.dart (main)" replace="/async|await/[!$&!]/g"?>
```dart
void main() [!async!] {
  checkVersion();
  print('In main: version is ${[!await!] lookUpVersion()}');
}
```

:::note
앞의 예는 결과를 기다리지 않고, `async` 함수(`checkVersion()`)를 사용합니다. 
이 관행은 코드가 함수 실행을 마쳤다고 가정하는 경우, 문제를 일으킬 수 있습니다. 
이 문제를 피하려면, [unawaited_futures linter 규칙][unawaited_futures linter rule]을 사용하세요.
:::

future, `async`, `await` 사용에 대한 상호작용 소개는, 
[비동기 프로그래밍 튜토리얼](/libraries/async/async-await)을 참조하세요.

## async 함수 선언 {:#declaring-async-functions}

`async` 함수는 본문에 `async` 수정자가 표시된 함수입니다.

함수에 `async` 키워드를 추가하면 Future를 반환합니다. 
예를 들어, String을 반환하는 이 동기 함수를 생각해 보세요.

<?code-excerpt "misc/lib/language_tour/async.dart (sync-look-up-version)"?>
```dart
String lookUpVersion() => '1.0.0';
```

`async` 함수로 변경하는 경우(예: 미래 구현에 시간이 많이 소요되는 경우), 반환되는 값은 Future입니다.

<?code-excerpt "misc/lib/language_tour/async.dart (async-look-up-version)"?>
```dart
Future<String> lookUpVersion() async => '1.0.0';
```

함수의 본문은 Future API를 사용할 필요가 없습니다. 
Dart는 필요한 경우 Future 객체를 만듭니다. 
함수가 유용한 값을 반환하지 않으면, 반환 유형을 `Future<void>`로 만듭니다.

Future, `async` 및 `await` 사용에 대한 상호작용 소개는, 
[비동기 프로그래밍 튜토리얼](/libraries/async/async-await)을 참조하세요.

{% comment %}
TODO #1117: Where else should we cover generalized void?
{% endcomment %}

## Streams 다루기 {:#handling-streams}

Stream에서 값을 가져와야 할 때, 두 가지 옵션이 있습니다.

* `async`와 _비동기 for 루프_ (`await for`)를 사용합니다.
* [`dart:async` 문서](/libraries/dart-async#stream)에 설명된 대로, Stream API를 사용합니다.

:::note
`await for`를 사용하기 전에, 코드가 더 명확해지는지, 
그리고 스트림의 모든 결과를 정말로 기다리고 싶은지 확인하세요. 
예를 들어, UI 이벤트 리스너에 대해 `await for`를 **사용해서는 안 됩니다**. 
UI 프레임워크는 끝없는 이벤트 스트림을 보내기 때문입니다.
:::

비동기 for 루프의 형식은 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/async.dart (await-for)"?>
```dart
await for (varOrType identifier in expression) {
  // 스트림이 값을 방출할 때마다 실행됩니다.
}
```

<code><em>expression</em></code>의 값은 Stream 타입이어야 합니다. 
실행은 다음과 같이 진행됩니다.

1. 스트림이 값을 방출할 때까지 기다립니다.
2. 변수를 방출된 값으로 설정하여, for 루프의 본문을 실행합니다.
3. 스트림이 닫힐 때까지 1과 2를 반복합니다.

스트림 수신을 중지하려면, 
`break` 또는 `return` 문을 사용하여 for 루프를 중단하고, 
스트림 구독을 취소할 수 있습니다.

**비동기 for 루프를 구현할 때 컴파일 타임 오류가 발생하면, `await for`가 `async` 함수에 있는지 확인하세요.** 
예를 들어, 앱의 `main()` 함수에서 비동기 for 루프를 사용하려면, 
`main()` 본문을 `async`로 표시해야 합니다.

<?code-excerpt "misc/lib/language_tour/async.dart (number-thinker)" replace="/async|await for/[!$&!]/g"?>
```dart
void main() [!async!] {
  // ...
  [!await for!] (final request in requestServer) {
    handleRequest(request);
  }
  // ...
}
```

Dart의 비동기 프로그래밍 지원에 대한 자세한 내용은, 
[`dart:async`](/libraries/dart-async) 라이브러리 문서를 확인하세요.

[`Future`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future-class.html
[`Stream`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html
[unawaited_futures linter rule]: /tools/linter-rules/unawaited_futures
