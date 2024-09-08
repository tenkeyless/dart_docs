---
title: dart:async
# description: Learn about the major features in Dart's dart:async library.
description: Dart의 dart:async 라이브러리의 주요 기능에 대해 알아보세요.
prevpage:
  url: /libraries/dart-core
  title: dart:core
nextpage:
  url: /libraries/dart-math
  title: dart:math
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

비동기 프로그래밍은 종종 콜백 함수를 사용하지만, 
Dart는 [Future][] 및 [Stream][] 객체라는 대안을 제공합니다. 
Future는 언젠가 미래에 제공될 결과에 대한 약속과 같습니다. 
Stream은 이벤트와 같은 값의 시퀀스를 가져오는 방법입니다. 
Future, Stream 등은 dart:async 라이브러리에 있습니다. ([API 참조][dart:async])

:::note
항상 Future 또는 Stream API를 직접 사용할 필요는 없습니다. 
Dart 언어는 `async` 및 `await`와 같은 키워드를 사용하여 비동기 코딩을 지원합니다. 
자세한 내용은 [비동기 프로그래밍 튜토리얼](/libraries/async/async-await)을 확인하세요.
:::

dart:async 라이브러리는 웹 앱과 명령줄 앱 모두에서 작동합니다. 
사용하려면, dart:async를 import 합니다.

<?code-excerpt "misc/lib/library_tour/async/future.dart (import)"?>
```dart
import 'dart:async';
```

:::tip
Future 및 Stream API를 사용하기 위해 dart:async를 import 할 필요는 없습니다. 
dart:core가 해당 클래스를 export 하기 때문입니다.
:::

## Future {:#future}

Future 객체는 Dart 라이브러리 전반에 나타나며, 
종종 비동기 메서드에서 반환되는 객체로 나타납니다. 
Future가 *완료*되면, 해당 값을 사용할 준비가 됩니다.

### await 사용 {:#using-await}

Future API를 직접 사용하기 전에, 대신 `await`를 사용하는 것을 고려하세요. 
`await` 표현식을 사용하는 코드는, Future API를 사용하는 코드보다 이해하기 쉬울 수 있습니다.

다음 함수를 고려하세요. 
Future의 `then()` 메서드를 사용하여 세 개의 비동기 함수를 연속으로 실행하고, 
각 함수가 완료될 때까지 기다린 후 다음 함수를 실행합니다.

<?code-excerpt "misc/lib/library_tour/async/future.dart (run-using-future)"?>
```dart
void runUsingFuture() {
  // ...
  findEntryPoint().then((entryPoint) {
    return runExecutable(entryPoint, args);
  }).then(flushThenExit);
}
```

await 표현식이 포함된 동일한 코드는 동기 코드와 더 유사해 보입니다.

<?code-excerpt "misc/lib/library_tour/async/future.dart (run-using-async-await)"?>
```dart
Future<void> runUsingAsyncAwait() async {
  // ...
  var entryPoint = await findEntryPoint();
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
}
```

`async` 함수는 Futures에서 예외를 catch 할 수 있습니다. 예를 들어:

<?code-excerpt "misc/lib/library_tour/async/future.dart (catch)"?>
```dart
var entryPoint = await findEntryPoint();
try {
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
} catch (e) {
  // 오류를 처리합니다...
}
```

:::important
비동기 함수는 Futures를 반환합니다. 
함수가 future를 반환하지 않도록 하려면, 다른 솔루션을 사용하세요. 
예를 들어, 함수에서 `async` 함수를 호출할 수 있습니다.
:::

`await` 및 관련 Dart 언어 기능 사용에 대한 자세한 내용은, 
[비동기 프로그래밍 튜토리얼](/libraries/async/async-await)을 참조하세요.

### 기본 사용법 {:#basic-usage}

`then()`을 사용하면 future가 완료될 때 실행되는 코드를 예약할 수 있습니다. 
예를 들어, [`Client.read()`][]는, HTTP 요청이 시간이 걸릴 수 있으므로, Future를 반환합니다. 
`then()`을 사용하면 Future가 완료되고 약속된 문자열 값을 사용할 수 있을 때, 
일부 코드를 실행할 수 있습니다.

<?code-excerpt "misc/lib/library_tour/async/basic.dart (then)"?>
```dart
httpClient.read(url).then((String result) {
  print(result);
});
```

Future 객체에서 발생할 수 있는 오류나 예외를 처리하려면, 
`catchError()`를 사용합니다.

<?code-excerpt "misc/lib/library_tour/async/basic.dart (catch-error)"?>
```dart
httpClient.read(url).then((String result) {
  print(result);
}).catchError((e) {
  // 오류를 처리하거나 무시합니다.
});
```

`then().catchError()` 패턴은 `try`-`catch`의 비동기 버전입니다.

:::important
`then()`의 결과에 대해 `catchError()`를 호출해야 합니다. 
원래 `Future`의 결과에 대해서는 호출하지 마세요. 
그렇지 않으면, `catchError()`는 원래 Future의 계산에서만 오류를 처리할 수 있고, 
`then()`에서 등록한 핸들러에서는 오류를 처리할 수 없습니다.
:::

[`Client.read()`]: {{site.pub-api}}/http/latest/http/Client/read.html

### 여러 비동기 메서드 체이닝 {:#chaining-multiple-asynchronous-methods}

`then()` 메서드는 Future를 반환하여, 
특정 순서로 여러 비동기 함수를 실행하는 유용한 방법을 제공합니다. 
`then()`으로 등록된 콜백이 Future를 반환하면, 
`then()`은 콜백에서 반환된 Future와 동일한 결과로 완료되는 Future를 반환합니다. 
콜백이 다른 타입의 값을 반환하면, `then()`은 값으로 완료되는 새 Future를 만듭니다.

<?code-excerpt "misc/lib/library_tour/async/future.dart (then-chain)"?>
```dart
Future result = costlyQuery(url);
result
    .then((value) => expensiveWork(value))
    .then((_) => lengthyComputation())
    .then((_) => print('Done!'))
    .catchError((exception) {
  /* 예외 처리... */
});
```

앞의 예에서, 메서드는 다음 순서로 실행됩니다.

1.  `costlyQuery()`
2.  `expensiveWork()`
3.  `lengthyComputation()`

await를 사용하여 작성된 동일한 코드는 다음과 같습니다.

<?code-excerpt "misc/lib/library_tour/async/future.dart (then-chain-as-await)"?>
```dart
try {
  final value = await costlyQuery(url);
  await expensiveWork(value);
  await lengthyComputation();
  print('Done!');
} catch (e) {
  /* 예외 처리... */
}
```

### 여러 개의 Future 기다리기 {:#waiting-for-multiple-futures}

때때로 알고리즘은 많은 비동기 함수를 호출하고, 
계속하기 전에 모든 함수가 완료될 때까지 기다려야 합니다. 
[Future.wait()][] 정적 메서드를 사용하여, 
여러 Future를 관리하고 완료될 때까지 기다리세요.

<?code-excerpt "misc/lib/library_tour/async/future.dart (wait)" replace="/elideBody;/\/* ... *\//g"?>
```dart
Future<void> deleteLotsOfFiles() async =>  ...
Future<void> copyLotsOfFiles() async =>  ...
Future<void> checksumLotsOfOtherFiles() async =>  ...

await Future.wait([
  deleteLotsOfFiles(),
  copyLotsOfFiles(),
  checksumLotsOfOtherFiles(),
]);
print('Done with all the long steps!');
```

`Future.wait()`는 제공된 모든 Future가 완료되면 완료되는 Future를 반환합니다. 
제공된 Future 중 하나가 실패하면, 결과와 함께 완료되거나 오류와 함께 완료됩니다.

### 여러 개의 Future 오류 처리 {:#handling-errors-for-multiple-futures}

또한 future의 [iterable]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/FutureIterable/wait.html) 또는 [record]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/FutureRecord2/wait.html)에서 병렬 연산을 기다릴 수 있습니다.

이러한 확장은 제공된 모든 future의 결과 값이 있는 future를 반환합니다. 
`Future.wait`와 달리, 오류를 처리할 수도 있습니다.

컬렉션의 future가 오류로 완료되면, 
`wait`는 [`ParallelWaitError`][]로 완료됩니다. 
이를 통해 호출자는 개별 오류를 처리하고, 
필요한 경우 성공적인 결과를 폐기할 수 있습니다.

각 개별 future의 결과 값이 _필요하지 않은_ 경우, future의 _iterable_ 에서 `wait`를 사용합니다.

```dart
void main() async {
  Future<void> delete() async =>  ...
  Future<void> copy() async =>  ...
  Future<void> errorResult() async =>  ...
  
  try {
    // 리스트에 있는 각 Future를 기다리고, Future 리스트를 반환합니다. 
    var results = await [delete(), copy(), errorResult()].wait;

    } on ParallelWaitError<List<bool?>, List<AsyncError?>> catch (e) {

    print(e.values[0]);    // successful future를 출력합니다.
    print(e.values[1]);    // successful future를 출력합니다.
    print(e.values[2]);    // 결과가 오류인 경우 null을 출력합니다.

    print(e.errors[0]);    // 결과가 성공적이면 null을 출력합니다.
    print(e.errors[1]);    // 결과가 성공적이면 null을 출력합니다.
    print(e.errors[2]);    // 오류를 출력합니다
  }

}
```

각 퓨처의 개별 결과 값이 _필요할_ 때, 퓨처의 _레코드_ 에 `wait`를 사용합니다. 
이렇게 하면 퓨처가 서로 다른 타입일 수 있다는 추가 이점이 있습니다.

```dart
void main() async {
  Future<int> delete() async =>  ...
  Future<String> copy() async =>  ...
  Future<bool> errorResult() async =>  ...

  try {    
    // 레코드의 각 future를 기다리고, future 레코드를 반환합니다.
    (int, String, bool) result = await (delete(), copy(), errorResult()).wait;
  
  } on ParallelWaitError<(int?, String?, bool?),
      (AsyncError?, AsyncError?, AsyncError?)> catch (e) {
    // ...
    }

  // 결과를 가지고 무엇인가를 합니다.
  var deleteInt  = result.$1;
  var copyString = result.$2;
  var errorBool  = result.$3;
}
```

## Stream {:#stream}

스트림 객체는 Dart API 전반에 나타나며 데이터 시퀀스를 나타냅니다. 
예를 들어, 버튼 클릭과 같은 HTML 이벤트는 스트림을 사용하여 전달됩니다. 
파일을 스트림으로 읽을 수도 있습니다.


### 비동기 for 루프 사용 {:#using-an-asynchronous-for-loop}

Stream API를 사용하는 대신, 비동기 for 루프(`await for`)를 사용할 수 있는 경우가 있습니다.

다음 함수를 고려해 보세요. 
Stream의 `listen()` 메서드를 사용하여 파일 리스트를 구독하고, 
각 파일이나 디렉토리를 검색하는 함수 리터럴을 전달합니다.

<?code-excerpt "misc/lib/library_tour/async/stream.dart (listen)" replace="/listen/[!$&!]/g"?>
```dart
void main(List<String> arguments) {
  // ...
  FileSystemEntity.isDirectory(searchPath).then((isDir) {
    if (isDir) {
      final startingDir = Directory(searchPath);
      startingDir.list().[!listen!]((entity) {
        if (entity is File) {
          searchFile(entity, searchTerms);
        }
      });
    } else {
      searchFile(File(searchPath), searchTerms);
    }
  });
}
```

비동기 for 루프(`await for`)를 포함하여, 
await 표현식이 있는 동일한 코드는, 
동기 코드와 더 유사해 보입니다.

<?code-excerpt "misc/lib/library_tour/async/stream.dart (await-for)" replace="/await for/[!$&!]/g"?>
```dart
void main(List<String> arguments) async {
  // ...
  if (await FileSystemEntity.isDirectory(searchPath)) {
    final startingDir = Directory(searchPath);
    [!await for!] (final entity in startingDir.list()) {
      if (entity is File) {
        searchFile(entity, searchTerms);
      }
    }
  } else {
    searchFile(File(searchPath), searchTerms);
  }
}
```

:::important
`await for`를 사용하기 전에, 코드가 더 명확해지는지, 
그리고 스트림의 모든 결과를 정말로 기다리고 싶은지 확인하세요. 
예를 들어, DOM 이벤트 리스너에 대해 `await for`를 **사용해서는 안 됩니다**. 
DOM은 끝없는 이벤트 스트림을 보내기 때문입니다. 
`await for`를 사용하여 두 개의 DOM 이벤트 리스너를 연속으로 등록하면, 
두 번째 종류의 이벤트는 처리되지 않습니다.
:::

`await` 및 관련 Dart 언어 기능 사용에 대한 자세한 내용은, 
[비동기 프로그래밍 튜토리얼](/libraries/async/async-await)을 참조하세요.

### 스트림 데이터 수신 {:#listening-for-stream-data}

값이 도착하는 대로 각 값을 가져오려면, 
`await for`를 사용하거나 `listen()` 메서드를 사용하여 스트림을 구독합니다.

<?code-excerpt "misc/lib/library_tour/async/stream_web.dart (listen)" replace="/listen/[!$&!]/g"?>
```dart
// 버튼에 이벤트 핸들러를 추가합니다.
submitButton.onClick.[!listen!]((e) {
  // 버튼을 클릭하면, 이 코드가 실행됩니다.
  submitData();
});
```

이 예에서, `onClick` 속성은 submit 버튼에서 제공하는 `Stream` 객체입니다.

하나의 이벤트만 신경 쓰는 경우, 
(`first`, `last` 또는 `single`과 같은) 속성을 사용하여 가져올 수 있습니다. 
이벤트를 처리하기 전에 테스트하려면, 
(`firstWhere()`, `lastWhere()` 또는 `singleWhere()`와 같은) 메서드를 사용합니다.
{% comment %}
{PENDING: example}
{% endcomment %}

이벤트의 하위 집합에 관심이 있는 경우, 
(`skip()`, `skipWhile()`, `take()`, `takeWhile()`, `where()`와 같은) 메서드를 사용할 수 있습니다.
{% comment %}
{PENDING: example}
{% endcomment %}

### 스트림 데이터 변환 {:#transforming-stream-data}

종종, 스트림의 데이터 형식을 변경해야 사용할 수 있습니다. 
`transform()` 메서드를 사용하여 다른 타입의 데이터로 스트림을 생성합니다.

<?code-excerpt "misc/lib/library_tour/async/stream.dart (transform)"?>
```dart
var lines =
    inputStream.transform(utf8.decoder).transform(const LineSplitter());
```

이 예제에서는 두 개의 변환기를 사용합니다. 
먼저 utf8.decoder를 사용하여, 정수 스트림을 문자열 스트림으로 변환합니다. 
그런 다음 LineSplitter를 사용하여, 문자열 스트림을 개별 라인 스트림으로 변환합니다. 
이러한 변환기는 dart:convert 라이브러리에서 가져온 것입니다.
([dart:convert 섹션](/libraries/dart-convert) 참조)
{% comment %}
  PENDING: add onDone and onError. (See "Streaming file contents".)
{% endcomment %}

### 오류 처리 및 완료 {:#handling-errors-and-completion}

오류 및 완료 처리 코드를 지정하는 방법은, 
비동기 for 루프(`await for`)를 사용하는지 아니면, 
Stream API를 사용하는지에 따라 달라집니다.

비동기 for 루프를 사용하는 경우, 
try-catch를 사용하여 오류를 처리합니다. 
스트림이 닫힌 후 실행되는 코드는 비동기 for 루프 뒤에 있습니다.

<?code-excerpt "misc/lib/library_tour/async/stream.dart (read-file-await-for)" replace="/try|catch/[!$&!]/g"?>
```dart
Future<void> readFileAwaitFor() async {
  var config = File('config.txt');
  Stream<List<int>> inputStream = config.openRead();

  var lines =
      inputStream.transform(utf8.decoder).transform(const LineSplitter());
  [!try!] {
    await for (final line in lines) {
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
  } [!catch!] (e) {
    print(e);
  }
}
```

Stream API를 사용하는 경우, 
`onError` 리스너를 등록하여 오류를 처리합니다. 
`onDone` 리스너를 등록하여 스트림이 닫힌 후의 코드를 실행합니다.

<?code-excerpt "misc/lib/library_tour/async/stream.dart (on-done)" replace="/onDone|onError/[!$&!]/g"?>
```dart
var config = File('config.txt');
Stream<List<int>> inputStream = config.openRead();

inputStream.transform(utf8.decoder).transform(const LineSplitter()).listen(
    (String line) {
  print('Got ${line.length} characters from stream');
}, [!onDone!]: () {
  print('file is now closed');
}, [!onError!]: (e) {
  print(e);
});
```


## 더 많은 정보 {:#more-information}

명령줄 앱에서 Future와 Stream을 사용하는 몇 가지 예를 보려면, 
[dart:io 문서][dart:io documentation]를 ​​확인하세요. 
또한 다음 문서와 튜토리얼을 참조하세요.

- [비동기 프로그래밍: futures, async, await](/libraries/async/async-await)
- [Futures와 오류 처리](/libraries/async/futures-error-handling)
- [비동기 프로그래밍: streams](/libraries/async/using-streams)
- [Dart에서 streams 만들기](/libraries/async/creating-streams)
- [Dart 비동기 프로그래밍: Isolates와 이벤트 루프](/language/concurrency)

[Future.wait()]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future/wait.html
[Future]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future-class.html
[`ParallelWaitError`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/ParallelWaitError-class.html
[Stream]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html
[dart:async]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/dart-async-library.html
[dart:io documentation]: /libraries/dart-io
