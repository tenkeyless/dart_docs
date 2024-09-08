---
# title: "Asynchronous programming: Streams"
title: "비동기 프로그래밍: Streams"
# description: Learn how to consume single-subscriber and broadcast streams.
description: 단일 구독자(single-subscriber) 및 브로드캐스트 스트림을 사용하는 방법을 알아보세요.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
---

:::secondary 요점은 무엇인가요?
* Streams은 비동기 데이터 시퀀스를 제공합니다.
* 데이터 시퀀스에는 사용자가 생성한 이벤트와 파일에서 읽은 데이터가 포함됩니다.
* Stream API에서 **await for** 또는 `listen()`을 사용하여, 스트림을 처리할 수 있습니다.
* Streams은 오류에 응답하는 방법을 제공합니다.
* 스트림에는 단일 구독 또는 브로드캐스트의 두 가지 종류가 있습니다.
:::

Dart의 비동기 프로그래밍은 [Future][] 및 [Stream][] 클래스로 특징지어집니다.

Future는 즉시 완료되지 않는 계산을 나타냅니다. 
일반 함수가 결과를 반환하는 반면, 비동기 함수는 Future를 반환하며, 이는 결국 결과를 포함합니다. 
future는 결과가 준비될 때 알려줍니다.

스트림은 비동기 이벤트의 시퀀스입니다. 
비동기 Iterable과 비슷합니다. 
요청 시 다음 이벤트를 가져오는 대신, 스트림은 준비될 때 이벤트가 있다고 알려줍니다.

## 스트림 이벤트 수신 {:#receiving-stream-events}

스트림은 여러 가지 방법으로 만들 수 있는데, 
이는 다른 글의 주제이지만, 모두 같은 방식으로 사용할 수 있습니다. 
_비동기 for 루프_(일반적으로 **await for**라고 함)는 
**for 루프**가 [Iterable][]을 반복하는 것처럼, 스트림의 이벤트를 반복합니다. 
예를 들어:

<?code-excerpt "misc/lib/tutorial/sum_stream.dart (sum-stream)" replace="/async|await for/[!$&!]/g"?>
```dart
Future<int> sumStream(Stream<int> stream) [!async!] {
  var sum = 0;
  [!await for!] (final value in stream) {
    sum += value;
  }
  return sum;
}
```

이 코드는 정수 이벤트 스트림의 각 이벤트를 수신하고, 이를 합산한 다음, 합계의 (future)를 반환합니다. 
루프 본문이 끝나면, 다음 이벤트가 도착하거나 스트림이 완료될 때까지, 함수가 일시 중지됩니다.

이 함수는 `async` 키워드로 표시되어 있으며, **await for** 루프를 사용할 때 필요합니다.

다음 예제는 `async*` 함수를 사용하여 간단한 정수 스트림을 생성하여 이전 코드를 테스트합니다.

:::note
이 페이지에서는 실행 가능한 예제를 표시하기 위해 임베드된 DartPad를 사용합니다.
{% render 'dartpads-embedded-troubleshooting.md' %}
:::

<?code-excerpt "misc/lib/tutorial/sum_stream.dart"?>
```dartpad
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  await for (final value in stream) {
    sum += value;
  }
  return sum;
}

Stream<int> countStream(int to) async* {
  for (int i = 1; i <= to; i++) {
    yield i;
  }
}

void main() async {
  var stream = countStream(10);
  var sum = await sumStream(stream);
  print(sum); // 55
}
```

:::note
**Run**을 클릭하여 **Console**에서 결과를 확인하세요.
:::

## 오류 이벤트 {:#error-events}

스트림은 더 이상 이벤트가 없을 때 완료되고, 
이벤트를 수신하는 코드는 (새 이벤트가 도착했다는 알림을 받는 것과 마찬가지로) 이에 대해 알림을 받습니다. 
**await for** 루프를 사용하여 이벤트를 읽을 때, 루프는 스트림이 완료되면 중지됩니다.

어떤 경우에는, 스트림이 완료되기 전에 오류가 발생합니다. 
원격 서버에서 파일을 가져오는 동안 네트워크가 실패했거나, 이벤트를 만드는 코드에 버그가 있지만, 
누군가가 이에 대해 알아야 할 수도 있습니다.

스트림은 데이터 이벤트를 전달하는 것처럼 오류 이벤트를 전달할 수도 있습니다. 
대부분의 스트림은 첫 번째 오류 후에 중지되지만, 두 개 이상의 오류를 전달하는 스트림과, 
오류 이벤트 후에 더 많은 데이터를 전달하는 스트림이 있을 수 있습니다. 
이 문서에서는 최대 하나의 오류를 전달하는 스트림에 대해서만 설명합니다.

**await for**를 사용하여, 스트림을 읽을 때 루프 문에서 오류가 발생합니다. 
이렇게 하면 루프도 종료됩니다. 
**try-catch**를 사용하여 오류를 catch 할 수 있습니다. 
다음 예제는 루프 반복자가 4와 같을 때 오류를 발생시킵니다.

<?code-excerpt "misc/lib/tutorial/sum_stream_with_catch.dart"?>
```dartpad
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  try {
    await for (final value in stream) {
      sum += value;
    }
  } catch (e) {
    return -1;
  }
  return sum;
}

Stream<int> countStream(int to) async* {
  for (int i = 1; i <= to; i++) {
    if (i == 4) {
      throw Exception('Intentional exception');
    } else {
      yield i;
    }
  }
}

void main() async {
  var stream = countStream(10);
  var sum = await sumStream(stream);
  print(sum); // -1
}
```

:::note
**Run**을 클릭하여 **Console**에서 결과를 확인하세요.
:::


## 스트림으로 작업 {:#working-with-streams}

Stream 클래스에는, [Iterable][]의 메서드와 비슷하게 스트림에서 일반적인 연산을 대신 수행할 수 있는, 
여러 헬퍼 메서드가 포함되어 있습니다. 
예를 들어, Stream API의 `lastWhere()`를 사용하여, 스트림에서 마지막 양의 정수를 찾을 수 있습니다.

<?code-excerpt "misc/lib/tutorial/misc.dart (last-positive)"?>
```dart
Future<int> lastPositive(Stream<int> stream) =>
    stream.lastWhere((x) => x >= 0);
```

## 두 종류의 스트림 {:#two-kinds-of-streams}

스트림에는 두 가지 종류가 있습니다.

### 단일 구독 스트림 {:#single-subscription-streams}

가장 일반적인 종류의 스트림에는 더 큰 전체의 일부인 일련의 이벤트가 포함됩니다. 
이벤트는 올바른 순서로 전달되어야 하며, 하나도 놓치지 않아야 합니다. 
이는 파일을 읽거나 웹 요청을 받을 때 얻는 종류의 스트림입니다.

이러한 스트림은 한 번만 수신할 수 있습니다. 
나중에 다시 수신할 경우 초기 이벤트를 놓치게 되고, 나머지 스트림은 의미가 없게 됩니다. 
수신을 시작할 때, 데이터가 페치(fetched)되어 청크(chunks)로 제공됩니다.

### 브로드캐스트 스트림 {:#broadcast-streams}

다른 종류의 스트림은 한 번에 하나씩 처리할 수 있는 개별 메시지를 위한 것입니다. 
이러한 종류의 스트림은 예를 들어, 브라우저의 마우스 이벤트에 사용할 수 있습니다.

언제든지 이러한 스트림을 수신하기 시작할 수 있으며, 수신하는 동안 발생하는 이벤트를 받게 됩니다. 
여러 리스너가 동시에 수신할 수 있으며, 이전 구독을 취소한 후 나중에 다시 수신할 수 있습니다.

## 스트림을 처리하는 메서드 {:#process-stream-methods}

[Stream\<T>][Stream]의 다음 메서드는, 스트림을 처리하고 결과를 반환합니다.

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (main-stream-members)" remove="/^\s*Stream/"?>
```dart
Future<T> get first;
Future<bool> get isEmpty;
Future<T> get last;
Future<int> get length;
Future<T> get single;
Future<bool> any(bool Function(T element) test);
Future<bool> contains(Object? needle);
Future<E> drain<E>([E? futureValue]);
Future<T> elementAt(int index);
Future<bool> every(bool Function(T element) test);
Future<T> firstWhere(bool Function(T element) test, {T Function()? orElse});
Future<S> fold<S>(S initialValue, S Function(S previous, T element) combine);
Future forEach(void Function(T element) action);
Future<String> join([String separator = '']);
Future<T> lastWhere(bool Function(T element) test, {T Function()? orElse});
Future pipe(StreamConsumer<T> streamConsumer);
Future<T> reduce(T Function(T previous, T element) combine);
Future<T> singleWhere(bool Function(T element) test, {T Function()? orElse});
Future<List<T>> toList();
Future<Set<T>> toSet();
```

(`drain()` 및 `pipe()`를 제외한) 이러한 모든 함수는, [Iterable][]의 유사한 함수에 대응합니다. 
각각은 `async` 함수를 **await for** 루프와 함께 사용하여(또는 다른 메서드 중 하나를 사용하여) 쉽게 작성할 수 있습니다. 
예를 들어, 일부 구현은 다음과 같습니다.

<?code-excerpt "misc/lib/tutorial/misc.dart (mock-stream-method-implementations)"?>
```dart
Future<bool> contains(Object? needle) async {
  await for (final event in this) {
    if (event == needle) return true;
  }
  return false;
}

Future forEach(void Function(T element) action) async {
  await for (final event in this) {
    action(event);
  }
}

Future<List<T>> toList() async {
  final result = <T>[];
  await forEach(result.add);
  return result;
}

Future<String> join([String separator = '']) async =>
    (await toList()).join(separator);
```

(실제 구현은 조금 더 복잡하지만, 주로 역사적인(historical) 이유 때문입니다.)

## 스트림을 수정하는 메서드 {:#modify-stream-methods}

Stream의 다음 메서드는 원본 스트림을 기반으로 새 스트림을 반환합니다. 
각각은 원본 스트림을 수신하기 전에, 누군가가 새 스트림을 수신할 때까지 기다립니다.

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (main-stream-members)" remove="/async\w+|distinct|transform/" retain="/^\s*Stream/"?>
```dart
Stream<R> cast<R>();
Stream<S> expand<S>(Iterable<S> Function(T element) convert);
Stream<S> map<S>(S Function(T event) convert);
Stream<T> skip(int count);
Stream<T> skipWhile(bool Function(T element) test);
Stream<T> take(int count);
Stream<T> takeWhile(bool Function(T element) test);
Stream<T> where(bool Function(T event) test);
```

앞의 메서드는 반복 가능한 객체를 다른 반복 가능한 객체로 변환하는 [Iterable][]의 유사한 메서드에 해당합니다. 
이 모든 것은 `async` 함수를 사용하여, **await for** 루프와 함께 쉽게 작성할 수 있습니다.

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (main-stream-members)" remove="/transform/" retain="/async\w+|distinct/"?>
```dart
Stream<E> asyncExpand<E>(Stream<E>? Function(T event) convert);
Stream<E> asyncMap<E>(FutureOr<E> Function(T event) convert);
Stream<T> distinct([bool Function(T previous, T next)? equals]);
```

`asyncExpand()`와 `asyncMap()` 함수는 `expand()`와 `map()`와 비슷하지만, 
함수 인수가 비동기 함수가 될 수 있습니다. 
`distinct()` 함수는 `Iterable`에 없지만, 있을 수 있습니다.

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (special-stream-members)"?>
```dart
Stream<T> handleError(Function onError, {bool Function(dynamic error)? test});
Stream<T> timeout(Duration timeLimit,
    {void Function(EventSink<T> sink)? onTimeout});
Stream<S> transform<S>(StreamTransformer<T, S> streamTransformer);
```

마지막 세 가지 함수는 더 특별합니다. 
이 함수는 **await for** 루프가 할 수 없는 오류 처리를 포함합니다. 
루프에 도달하는 첫 번째 오류는 루프와 스트림에서 해당 구독을 종료합니다. 
복구할 수 없습니다. 
다음 코드는 `handleError()`를 사용하여 스트림에서 오류를 제거한 다음, 
**await for** 루프에서 사용하는 방법을 보여줍니다.

<?code-excerpt "misc/lib/tutorial/misc.dart (map-log-errors)"?>
```dart
Stream<S> mapLogErrors<S, T>(
  Stream<T> stream,
  S Function(T event) convert,
) async* {
  var streamWithoutErrors = stream.handleError((e) => log(e));
  await for (final event in streamWithoutErrors) {
    yield convert(event);
  }
}
```

### transform() 함수 {:#transform-function}

`transform()` 함수는 오류 처리를 위한 것이 아니라, 스트림을 위한 보다 일반화된 "맵"입니다. 
일반적인 맵은 들어오는 이벤트마다 하나의 값이 필요합니다. 
그러나, 특히 I/O 스트림의 경우, 출력 이벤트를 생성하려면 여러 개의 들어오는 이벤트가 필요할 수 있습니다. 
[StreamTransformer][]는 이런 식으로 작동할 수 있습니다. 
예를 들어, [Utf8Decoder][]와 같은 디코더는 변환기(transformers)입니다. 
변환기는, `async` 함수로 쉽게 구현할 수 있는, [bind()][]라는 하나의 함수만 필요합니다.

### 파일 읽기 및 디코딩 {:#reading-decoding-file}

다음 코드는 파일을 읽고 스트림에서 두 개의 변환을 실행합니다. 
먼저 UTF8에서 데이터를 변환한 다음, [LineSplitter][]를 통해 실행합니다. 
해시태그 `#`로 시작하는 줄을 제외한, 모든 줄이 출력됩니다.

<?code-excerpt "misc/bin/cat_no_hash.dart"?>
```dart
import 'dart:convert';
import 'dart:io';

void main(List<String> args) async {
  var file = File(args[0]);
  var lines = utf8.decoder
      .bind(file.openRead())
      .transform(const LineSplitter());
  await for (final line in lines) {
    if (!line.startsWith('#')) print(line);
  }
}
```

## listen() 메서드 {:#listen-method}

Stream의 마지막 메서드는 `listen()`입니다. 
이것은 "낮은 레벨" 메서드입니다. 
다른 모든 스트림 함수는 `listen()`에 따라 정의됩니다.

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (listen)"?>
```dart
StreamSubscription<T> listen(void Function(T event)? onData,
    {Function? onError, void Function()? onDone, bool? cancelOnError});
```

새로운 `Stream` 타입을 만들려면, `Stream` 클래스를 확장하고, `listen()` 메서드를 구현하면 됩니다. 
`Stream`의 다른 모든 메서드는 작동하기 위해 `listen()`을 호출합니다.

`listen()` 메서드를 사용하면 스트림에서 수신을 시작할 수 있습니다. 
수신하기 전까지, 스트림은 보고 싶은 이벤트를 설명하는 비활성 객체입니다. 
수신하면, 활성 스트림을 생성하는 이벤트를 나타내는 [StreamSubscription][] 객체가 반환됩니다. 
이는 `Iterable`이 객체 컬렉션일 뿐인 방식과 비슷하지만, iterator는 실제 반복을 수행합니다.

스트림 구독을 사용하면, 구독을 일시 중지하고, 일시 중지 후 다시 시작하고, 완전히 취소할 수 있습니다. 
각 데이터 이벤트 또는 오류 이벤트와, 스트림이 닫힐 때 호출되는 콜백을 설정할 수 있습니다.

## 기타 리소스 {:#other-resources}

Dart에서 스트림과 비동기 프로그래밍을 사용하는 방법에 대한 자세한 내용은 다음 문서를 읽어보세요.

* [Dart에서 스트림 만들기](/libraries/async/creating-streams), 
  자체 스트림을 만드는 방법에 대한 문서
* [Futures 및 오류 처리](/libraries/async/futures-error-handling), 
  Future API를 사용하여 오류를 처리하는 방법을 설명하는 문서
* [비동기 지원](/language/async), [언어 투어](/language)의 섹션
* [스트림 API 참조]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html)

[bind()]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/StreamTransformer/bind.html
[LineSplitter]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-convert/LineSplitter-class.html
[Future]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future-class.html
[Iterable]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Iterable-class.html
[Stream]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html
[StreamSubscription]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/StreamSubscription-class.html
[StreamTransformer]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/StreamTransformer-class.html
[Utf8Decoder]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-convert/Utf8Decoder-class.html
