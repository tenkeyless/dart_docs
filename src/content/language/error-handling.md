---
# title: Error handling
title: 오류 처리
description: Dart에서 오류와 예외를 처리하는 방법을 알아보세요.
prevpage:
  url: /language/branches
  # title: Branches
  title: 분기
nextpage:
  url: /language/classes
  # title: Classes
  title: 클래스
---

## 예외 (Exceptions) {:#exceptions}

Dart 코드는 예외를 throw하고 catch할 수 있습니다. 
예외는 예상치 못한 일이 발생했음을 나타내는 오류입니다. 
예외가 catch되지 않으면, 예외를 발생시킨 [isolate][]가 일시 중단되고, 
일반적으로 isolate과 해당 프로그램이 종료됩니다.

Java와 달리, Dart의 모든 예외는 검사되지 않은 예외입니다. 
메서드는 어떤 예외를 throw할지 선언하지 않으며, 예외를 catch할 필요가 없습니다.

Dart는 [`Exception`][] 및 [`Error`][] 타입과 수많은 미리 정의된 하위 타입을 제공합니다. 
물론, 사용자는 고유한 예외를 정의할 수 있습니다. 
그러나, Dart 프로그램은 Exception 및 Error 객체뿐만 아니라, 
null이 아닌 모든 객체를 예외로 throw할 수 있습니다.

### Throw {:#throw}

예외를 던지거나, *발생시키는* 예는 다음과 같습니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (throw-FormatException)"?>
```dart
throw FormatException('Expected at least 1 section');
```

임의의 객체를 던질 수도 있습니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (out-of-llamas)"?>
```dart
throw 'Out of llamas!';
```

:::note
프로덕션 품질의 코드는 일반적으로 [`Error`][] 또는 [`Exception`][]을 구현하는 타입을 throw합니다.
:::

예외를 발생시키는 것은 표현식이므로, 
=\> 문에서 예외를 발생시킬 수 있으며, 
표현식을 허용하는 다른 모든 곳에서도 예외를 발생시킬 수 있습니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (throw-is-an-expression)"?>
```dart
void distanceTo(Point other) => throw UnimplementedError();
```

### Catch {:#catch}

예외를 Catch하거나, 캡처하면, 예외가 전파되는 것을 막을 수 있습니다. (예외를 다시 throw하지 않는 한) 
예외를 Catch하면, 예외를 처리할 기회가 생깁니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try)"?>
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

여러 타입의 예외를 throw할 수 있는 코드를 처리하려면, 여러 catch 절을 지정할 수 있습니다. 
throw된 객체의 타입과 일치하는 첫 번째 catch 절이 예외를 처리합니다. 
catch 절이 타입을 지정하지 않으면, 해당 절은 throw된 객체의 모든 타입을 처리할 수 있습니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch)"?>
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 특정 예외
  buyMoreLlamas();
} on Exception catch (e) {
  // 그 밖에 예외인 사항
  print('Unknown exception: $e');
} catch (e) {
  // 지정된 유형 없음, 모든 것을 처리합니다
  print('Something really unknown: $e');
}
```

앞의 코드에서 보듯이, `on` 또는 `catch` 또는 둘 다 사용할 수 있습니다. 
예외 타입을 지정해야 할 때 `on`을 사용합니다. 
예외 처리기에 예외 객체가 필요할 때 `catch`를 사용합니다.

`catch()`에 하나 또는 두 개의 매개변수를 지정할 수 있습니다. 
첫 번째는 발생한 예외이고, 두 번째는 스택 추적([`StackTrace`][] 객체)입니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch-2)" replace="/\(e.*?\)/[!$&!]/g"?>
```dart
try {
  // ···
} on Exception catch [!(e)!] {
  print('Exception details:\n $e');
} catch [!(e, s)!] {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
```

예외를 부분적으로 처리하면서, 전파를 허용하려면 `rethrow` 키워드를 사용합니다.

<?code-excerpt "misc/test/language_tour/exceptions_test.dart (rethrow)" replace="/rethrow;/[!$&!]/g"?>
```dart
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // 런타임 오류
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    [!rethrow;!] // 호출자에게 예외를 볼 수 있도록 허용합니다.
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
```


### Finally {:#finally}

예외가 발생하든 발생하지 않든 일부 코드가 실행되도록 하려면, `finally` 절을 사용합니다. 
예외와 일치하는 `catch` 절이 없으면, `finally` 절이 실행된 후에 예외가 전파됩니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (finally)"?>
```dart
try {
  breedMoreLlamas();
} finally {
  // 예외가 발생하더라도, 항상 정리하세요.
  cleanLlamaStalls();
}
```

`finally` 절은 일치하는 `catch` 절 뒤에 실행됩니다.

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch-finally)"?>
```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // 예외를 먼저 처리합니다.
} finally {
  cleanLlamaStalls(); // 그리고 청소를 합니다.
}
```

자세한 내용은 [코어 라이브러리 예외 문서](/libraries/dart-core#exceptions)를 확인하세요.

## Assert {:#assert}

개발하는 동안, boolean 조건이 거짓인 경우 정상적인 실행을 중단시키기 위해, 
assert 문(`assert(<condition>, <optionalMessage>);`)을 사용합니다.

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert)"?>
```dart
// 변수에 null이 아닌 값이 있는지 확인하세요.
assert(text != null);

// 값이 100보다 작은지 확인하세요.
assert(number < 100);

// 이것이 https URL인지 확인하세요.
assert(urlString.startsWith('https'));
```

어설션에 메시지를 첨부하려면, 
`assert`의 두 번째 인수로 문자열을 추가합니다. 
(선택적으로 [끝에 쉼표 (trailing comma)][trailing comma] 사용 가능):

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert-with-message)"?>
```dart
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

`assert`의 첫 번째 인수는 boolean 값으로 해결되는 모든 표현식일 수 있습니다. 
표현식의 값이 참이면, 어설션이 성공하고 실행이 계속됩니다. 
거짓이면, 어설션이 실패하고 예외([`AssertionError`][])가 발생합니다.

정확히 언제 어설션이 작동할까요? 사용하는 도구와 프레임워크에 따라 다릅니다.

* Flutter는 [디버그 모드][Flutter debug mode]에서 어설션을 활성화합니다.
* [`webdev serve`][]와 같은 개발 전용 도구는 일반적으로 기본적으로 어설션을 활성화합니다.
* [`dart run`][] 및 [`dart compile js`][]와 같은 일부 도구는, 
  명령줄 플래그인 `--enable-asserts`를 통해 어설션을 지원합니다.

프로덕션 코드에서는, 어설션이 무시되고, `assert`에 대한 인수는 평가되지 않습니다.

[trailing comma]: /language/collections#trailing-comma
[`AssertionError`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/AssertionError-class.html
[Flutter debug mode]: {{site.flutter-docs}}/testing/debugging#debug-mode-assertions
[`webdev serve`]: /tools/webdev#serve
[`dart run`]: /tools/dart-run
[`dart compile js`]: /tools/dart-compile#js

[isolate]: /language/concurrency#isolates
[`Error`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Error-class.html
[`Exception`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Exception-class.html
[`StackTrace`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/StackTrace-class.html
