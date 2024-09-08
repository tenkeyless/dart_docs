---
# title: Futures and error handling
title: Future와 오류 처리
# description: >-
#   Everything you wanted to know about handling errors and exceptions
#   when writing asynchronous code. And then some.
description: >-
  비동기 코드를 작성할 때 오류와 예외를 처리하는 방법에 대해 알고 싶은 모든 것. 그리고 그 이상.
---

Dart 언어는 네이티브 [비동기 지원](/language/async)을 갖추고 있어, 
비동기 Dart 코드를 읽고 쓰기가 훨씬 쉽습니다. 
그러나 일부 코드(특히 오래된 코드)는 여전히,
(`then()`, `catchError()`, `whenComplete()`와 같은) 
[Future 메서드][Future class]를 사용할 수 있습니다.

이 페이지는 이러한 Future 메서드를 사용할 때, 
흔히 저지르는 실수를 피하는 데 도움이 될 수 있습니다.

:::warning
코드가 언어의 비동기 지원인 `async`, `await` 및 try-catch를 사용한 오류 처리를 사용하는 경우, 
이 페이지는 필요하지 않습니다. 
자세한 내용은 [비동기 프로그래밍 튜토리얼](/libraries/async/async-await)을 참조하세요.
:::


## Future API 및 콜백 {:#the-future-api-and-callbacks}

Future API를 사용하는 함수는, 
Future를 완료하는 값(또는 오류)을 처리하는 콜백을 등록합니다. 
예를 들어:

<?code-excerpt "futures/lib/simple.dart (then-catch)"?>
```dart
myFunc().then(processValue).catchError(handleError);
```

등록된 콜백은 다음 규칙에 따라 실행됩니다. 
- `then()`의 콜백은 값으로 완료되는 Future에서 호출되면 실행되고, 
- `catchError()`의 콜백은 오류로 완료되는 Future에서 호출되면 실행됩니다.

위의 예에서 `myFunc()`의 Future가 값으로 완료되는 경우, `then()`의 콜백이 실행됩니다. 
- `then()` 내에서 새 오류가 생성되지 않으면, `catchError()`의 콜백이 실행되지 않습니다. 
- 반면 `myFunc()`가 오류로 완료되는 경우, `then()`의 콜백이 실행되지 않고, 
  `catchError()`의 콜백이 실행됩니다.

## catchError()와 함께 then()을 사용하는 예 {:#examples-of-using-then-with-catcherror}

체인된 `then()` 및 `catchError()` 호출은 Futures를 다룰 때 일반적인 패턴이며, 
try-catch 블록의 대략적인 동등물로 간주될 수 있습니다.

다음 몇 섹션에서는 이 패턴의 예를 보여줍니다.

### 포괄적인 오류 처리기로서의 catchError() {:#catcherror-as-a-comprehensive-error-handler}

다음 예제는 `then()` 콜백 내부에서 예외를 throw하는 것과, 
오류 처리기로서 `catchError()`의 다재다능함을 보여줍니다.

<?code-excerpt "futures/lib/simple.dart (comprehensive-errors)" replace="/ellipsis\(\);/.../g;"?>
```dart
myFunc().then((value) {
  doSomethingWith(value);
  ...
  throw Exception('Some arbitrary error');
}).catchError(handleError);
```

`myFunc()`의 Future가 값으로 완료되면, `then()`의 콜백이 실행됩니다. 
`then()`의 콜백 내의 코드가 throw(위의 예에서처럼)하면, `then()`의 Future가 오류로 완료됩니다. 
해당 오류는 `catchError()`에서 처리합니다.

`myFunc()`의 Future가 오류로 완료되면, `then()`의 Future가 해당 오류로 완료됩니다. 
해당 오류는 `catchError()`에서도 처리합니다.

오류가 `myFunc()` 내부에서 발생했는지 `then()` 내부에서 발생했는지에 관계없이, 
`catchError()`에서 성공적으로 처리합니다.

### then() 내부의 오류 처리 {:#error-handling-within-then}

더 세부적인 오류 처리를 위해, 
`then()` 내에 두 번째 (`onError`) 콜백을 등록하여 오류가 있는 Futures를 처리할 수 있습니다. 
`then()`의 시그니처는 다음과 같습니다.

<?code-excerpt "futures/lib/simple.dart (future-then)"?>
```dart
Future<R> then<R>(FutureOr<R> Function(T value) onValue, {Function? onError});
```

`then()`으로 _전달된_ 오류와 `then()` _내부_ 에서 생성된 오류를 구별하려는 경우에만, 
선택적 onError 콜백을 등록합니다.

<?code-excerpt "futures/lib/simple.dart (throws-then-catch)"?>
```dart
asyncErrorFunction().then(successCallback, onError: (e) {
  handleError(e); // 원래 에러.
  anotherAsyncErrorFunction(); // 이런, 새로운 오류가 발생했습니다.
}).catchError(handleError); // then() 내부에서 발생한 오류가 처리되었습니다.
```

위의 예에서, `asyncErrorFunction()`의 Future의 오류는 `onError` 콜백으로 처리됩니다. 
`anotherAsyncErrorFunction()`은 `then()`의 Future가 오류와 함께 완료되도록 합니다. 
이 오류는 `catchError()`로 처리됩니다.

일반적으로, 두 가지 다른 오류 처리 전략을 구현하는 것은 권장되지 않습니다. 
`then()` 내에서 오류를 포착해야 하는 강력한 이유가 있는 경우에만, 두 번째 콜백을 등록합니다.

### 긴 체인 중간의 오류 {:#errors-in-the-middle-of-a-long-chain}

`then()` 호출을 연속으로 실행하고, 
`catchError()`를 사용하여 체인의 어느 부분에서든 생성된 오류를 잡는 것이 일반적입니다.

<?code-excerpt "futures/lib/long_chain.dart"?>
```dart
Future<String> one() => Future.value('from one');
Future<String> two() => Future.error('error from two');
Future<String> three() => Future.value('from three');
Future<String> four() => Future.value('from four');

void main() {
  one() // Future는 "from one"으로 완료됩니다.
      .then((_) => two()) // Future는 two()의 오류로 완료됩니다.
      .then((_) => three()) // Future는 two()의 오류로 완료됩니다.
      .then((_) => four()) // Future는 two()의 오류로 완료됩니다.
      .then((value) => value.length) // Future는 two()의 오류로 완료됩니다.
      .catchError((e) {
    print('Got error: $e'); // 마지막으로 콜백이 실행됩니다.
    return 42; // Future는 42로 완료됩니다.
  }).then((value) {
    print('The value is $value');
  });
}

// 이 프로그램의 출력:
//   Got error: error from two
//   The value is 42
```

위의 코드에서, `one()`의 Future는 값으로 완료되지만, `two()`의 Future는 오류로 완료됩니다. 
오류로 완료되는 Future에서 `then()`이 호출되면, `then()`의 콜백은 실행되지 않습니다. 
대신, `then()`의 Future는 수신자의 오류로 완료됩니다. 
이 예에서, 이는 `two()`가 호출된 후, 
모든 후속 `then()`에서 반환된 Future가 `two()`의 오류로 완료됨을 의미합니다. 
이 오류는 결국 `catchError()` 내에서 처리됩니다.

### 특정 오류 처리 {:#handling-specific-errors}

특정 오류를 catch 하고 싶다면? 아니면 두 개 이상의 오류를 catch 하고 싶다면?

`catchError()`는 `test`라는 선택적 명명된 인수를 사용하는데, 
이를 통해 발생한 오류의 종류를 쿼리할 수 있습니다.

<?code-excerpt "futures/lib/simple.dart (future-catch-error)"?>
```dart
Future<T> catchError(Function onError, {bool Function(Object error)? test});
```

제공된 매개변수에 따라 사용자를 인증하고, 사용자를 적절한 URL로 리디렉션하는 함수인 `handleAuthResponse(params)`를 고려해 보세요. 
복잡한 워크플로를 감안할 때, `handleAuthResponse()`는 다양한 오류와 예외를 생성할 수 있으며, 
이를 다르게 처리해야 합니다. 
`test`를 사용하여 이를 수행하는 방법은 다음과 같습니다.

<?code-excerpt "futures/lib/simple.dart (auth-response)" replace="/ellipsis\(\)/.../g;"?>
```dart
void main() {
  handleAuthResponse(const {'username': 'dash', 'age': 3})
      .then((_) => ...)
      .catchError(handleFormatException, test: (e) => e is FormatException)
      .catchError(handleAuthorizationException,
          test: (e) => e is AuthorizationException);
}
```

## whenComplete()를 사용한 비동기 try-catch-finally {:#async-try-catch-finally-using-whencomplete}

`then().catchError()`가 try-catch를 mirror 하는 경우, 
`whenComplete()`는 'finally'와 동일합니다. 
`whenComplete()` 내에 등록된 콜백은 `whenComplete()`의 수신자가 완료될 때 호출되며, 
값과 함께 완료하든 오류와 함께 완료하든 상관없습니다.

<?code-excerpt "futures/lib/simple.dart (connect-server)"?>
```dart
final server = connectToServer();
server
    .post(myUrl, fields: const {'name': 'Dash', 'profession': 'mascot'})
    .then(handleResponse)
    .catchError(handleError)
    .whenComplete(server.close);
```

우리는 `server.post()`가 유효한 응답을 생성하든 오류를 생성하든 상관없이 `server.close`를 호출하고 싶습니다. 
우리는 이것을 `whenComplete()` 내부에 배치하여, 이것이 발생하도록 합니다.

### whenComplete()에서 반환된 Future 완료 {:#completing-the-future-returned-by-whencomplete}

`whenComplete()` 내부에서 오류가 발생하지 않으면(no error is emitted), 
해당 Future는 `whenComplete()`가 호출된 Future와 같은 방식으로 완료됩니다. 
이는 예를 통해 이해하는 것이 가장 쉽습니다.

아래 코드에서, `then()`의 Future는 오류로 완료되므로, 
`whenComplete()`의 Future도 해당 오류로 완료됩니다.

<?code-excerpt "futures/lib/when_complete.dart (with-error)" replace="/withErrorMain/main/g; "?>
```dart
void main() {
  asyncErrorFunction()
      // Future는 오류로 완료됩니다:
      .then((_) => print("Won't reach here"))
      // Future는 동일한 오류로 완료됩니다.
      .whenComplete(() => print('Reaches here'))
      // Future는 동일한 오류로 완료됩니다.
      .then((_) => print("Won't reach here"))
      // 오류는 여기에서 처리됩니다:
      .catchError(handleError);
}
```

아래 코드에서, `then()`의 Future는 오류로 완료되고, 
이제 `catchError()`에서 처리합니다. 
`catchError()`의 Future는 `someObject`로 완료되고, 
`whenComplete()`의 Future는 같은 객체로 완료되기 때문입니다.

<?code-excerpt "futures/lib/when_complete.dart (with-object)" replace="/ellipsis\(\)/.../g; /withObjectMain/main/g; "?>
```dart
void main() {
  asyncErrorFunction()
      // Future는 오류로 완료됩니다:
      .then((_) => ...)
      .catchError((e) {
          handleError(e);
          printErrorMessage();
          return someObject; // Future는 someObject로 완료됩니다.
  }).whenComplete(() => print('Done!')); // Future는 someObject로 완료됩니다.
}
```

### whenComplete() 내에서 발생하는 오류 {:#errors-originating-within-whencomplete}

`whenComplete()`의 콜백이 오류를 발생시키면, 
`whenComplete()`의 Future가 해당 오류로 완료됩니다.

<?code-excerpt "futures/lib/when_complete.dart (when-complete-error)" replace="/whenCompleteError/main/g; "?>
```dart
void main() {
  asyncErrorFunction()
      // Future는 값으로 완료됩니다.
      .catchError(handleError)
      // Future는 오류로 완료됩니다:
      .whenComplete(() => throw Exception('New error'))
      // 오류는 다음과 같이 처리됩니다:
      .catchError(handleError);
}
```


## 잠재적인 문제: 오류 핸들러를 조기에 등록하는데 실패함 {:#potential-problem-failing-to-register-error-handlers-early}

Future가 완료되기 전에 오류 핸들러를 설치하는 것이 중요합니다. 
이렇게 하면 Future가 오류와 함께 완료되고, 
오류 핸들러가 아직 연결되지 않았으며, 
오류가 실수로 전파되는 시나리오를 피할 수 있습니다. 
다음 코드를 고려하세요.

<?code-excerpt "futures/lib/early_error_handlers.dart (bad)" replace="/ellipsis\(\)/.../g; /mainBad/main/g;"?>
```dart
void main() {
  Future<Object> future = asyncErrorFunction();

  // BAD: asyncErrorFunction() 예외를 처리하기에는 너무 늦었습니다.
  Future.delayed(const Duration(milliseconds: 500), () {
    future.then(...).catchError(...);
  });
}
```

위의 코드에서, `catchError()`는 `asyncErrorFunction()`가 호출된 후 반 초가 지나야 등록되고, 
오류는 처리되지 않습니다.

`asyncErrorFunction()`가 `Future.delayed()` 콜백 내에서 호출되면, 문제가 사라집니다.

<?code-excerpt "futures/lib/early_error_handlers.dart (good)" replace="/ellipsis\(\)/.../g; /mainGood/main/g;"?>
```dart
void main() {
  Future.delayed(const Duration(milliseconds: 500), () {
    asyncErrorFunction()
        .then(...)
        .catchError(...); // 우리는 여기에 도착합니다.
  });
}
```

## 잠재적인 문제: 동기 및 비동기 오류를 실수로 혼합 {:#potential-problem-accidentally-mixing-synchronous-and-asynchronous-errors}

Futures를 반환하는 함수는 거의 항상 future에서 오류를 방출해야 합니다. 
이러한 함수의 호출자가 여러 오류 처리 시나리오를 구현해야 하는 것을 원하지 않으므로, 
동기 오류가 누출되는 것을 방지하고자 합니다. 
다음 코드를 고려하세요.

<?code-excerpt "futures/bin/mixing_errors_problematic.dart (parse)"?>
```dart
Future<int> parseAndRead(Map<String, dynamic> data) {
  final filename = obtainFilename(data); // throw 할 수 있습니다.
  final file = File(filename);
  return file.readAsString().then((contents) {
    return parseFileData(contents); // throw 할 수 있습니다.
  });
}
```

해당 코드의 두 함수는 잠재적으로 동기적으로 throw할 수 있습니다. : `obtainFilename()` 및 `parseFileData()`. 

`parseFileData()`는 `then()` 콜백 내부에서 실행되므로, 오류가 함수 밖으로 누출되지 않습니다. 
대신, `then()`의 Future가 `parseFileData()`의 오류로 완료되고, 
오류는 결국 `parseAndRead()`의 Future를 완료하고, 
오류는 `catchError()`에서 성공적으로 처리할 수 있습니다.

하지만, `obtainFilename()`은 `then()` 콜백 내에서 호출되지 않습니다. 
_throw하는 경우_, 동기 오류가 전파됩니다.

<?code-excerpt "futures/bin/mixing_errors_problematic.dart (main)"?>
```dart
void main() {
  parseAndRead(data).catchError((e) {
    print('Inside catchError');
    print(e);
    return -1;
  });
}

// 프로그램 출력:
//   Unhandled exception:
//   <error from obtainFilename>
//   ...
```

`catchError()`를 사용해도 오류를 캡처하지 않으므로, 
`parseAndRead()`의 클라이언트는 이 오류에 대한 별도의 오류 처리 전략을 구현해야 합니다.

### 솔루션: Future.sync()를 사용하여 코드를 래핑합니다. {:#solution-using-future-sync-to-wrap-your-code}

함수에서 실수로 동기 오류가 발생하지 않도록 하기 위한 일반적인 패턴은, 
함수 본문을 새 `Future.sync()` 콜백 내부로 래핑하는 것입니다.

<?code-excerpt "futures/bin/mixing_errors_solution.dart (parse)"?>
```dart
Future<int> parseAndRead(Map<String, dynamic> data) {
  return Future.sync(() {
    final filename = obtainFilename(data); // throw 할 수 있습니다.
    final file = File(filename);
    return file.readAsString().then((contents) {
      return parseFileData(contents); // throw 할 수 있습니다.
    });
  });
}
```

콜백이 Future가 아닌 값을 반환하면, `Future.sync()`의 Future는 해당 값으로 완료됩니다. 
콜백이 (위의 예에서처럼) throw하면, Future는 오류로 완료됩니다. 
콜백 자체가 Future를 반환하면, 
해당 Future의 값 또는 오류가 `Future.sync()`의 Future를 완료합니다.

`Future.sync()`로 래핑된 코드로, `catchError()`는 모든 오류를 처리할 수 있습니다.

<?code-excerpt "futures/bin/mixing_errors_solution.dart (main)"?>
```dart
void main() {
  parseAndRead(data).catchError((e) {
    print('Inside catchError');
    print(e);
    return -1;
  });
}

// 프로그램 출력:
//   Inside catchError
//   <error from obtainFilename>
```

`Future.sync()`는 코드를 catch 되지 않은 예외에 대해 탄력적으로 만듭니다. 
함수에 많은 코드가 들어 있는 경우, 깨닫지 못한 채 위험한 일을 하고 있을 가능성이 있습니다.

<?code-excerpt "futures/bin/mixing_errors_problematic.dart (fragile)" replace="/ellipsis\(\);/.../g;"?>
```dart
Future fragileFunc() {
  return Future.sync(() {
    final x = someFunc(); // 예상치 못하게 몇몇 희귀한 경우에 throws 합니다.
    var y = 10 / x; // x는 0이어서는 안 됩니다.
    ...
  });
}
```

`Future.sync()`를 사용하면 발생할 수 있는 오류를 처리할 수 있을 뿐만 아니라, 
함수에서 *실수로* 오류가 누출되는 것을 방지할 수도 있습니다.

## 더 많은 정보 {:#more-information}

Future에 대한 자세한 내용은 [Future API 참조][Future class]를 참조하세요.

[Future class]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future-class.html
