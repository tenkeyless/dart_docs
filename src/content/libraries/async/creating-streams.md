---
# title: Creating streams in Dart
title: Dart에서 스트림 만들기
# description: A stream is a sequence of results; learn how to create your own.
description: 스트림은 일련의 결과입니다. 스트림을 직접 만드는 방법을 알아보세요.
original-date: 2013-04-08
date: 2021-05-16
---

<style>
.comment {color:red;}
</style>

_Lasse Nielsen 작성 <br>
2013년 4월(2021년 5월 업데이트)_

dart:async 라이브러리에는 많은 Dart API에 중요한 두 가지 타입이 포함되어 있습니다. 
(1) [Stream]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html)과 (2) [Future]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future-class.html)가 그것입니다. 
Future가 단일 계산의 결과를 나타내는 반면, Stream은 결과의 _시퀀스_ 입니다. 
스트림을 수신하여 결과(데이터와 오류 모두)와 스트림 종료에 대한 알림을 받습니다. 
수신하는 동안 일시 중지하거나 스트림이 완료되기 전에 수신을 중지할 수도 있습니다.

하지만 이 문서는 스트림을 _사용_ 하는 것에 대한 것이 아닙니다. 
자신의 스트림을 만드는 것에 대한 것입니다. 
다음과 같은 몇 가지 방법으로 스트림을 만들 수 있습니다.

* 기존 스트림 변환.
* `async*` 함수를 사용하여 처음부터 스트림 만들기.
* `StreamController`를 사용하여 스트림 만들기.

이 문서에서는 각 접근 방식에 대한 코드를 보여주고, 
스트림을 올바르게 구현하는 데 도움이 되는 팁을 제공합니다.

스트림 사용에 대한 도움말은 [비동기 프로그래밍: 스트림](/libraries/async/using-streams)을 참조하세요.

## 기존 스트림 변환 {:#transforming-an-existing-stream}

스트림을 만드는 일반적인 케이스는, 
이미 스트림이 있고 원본 스트림의 이벤트를 기반으로 새 스트림을 만들고자 하는 경우입니다. 
예를 들어, 입력을 UTF-8로 디코딩하여, 바이트 스트림을 문자열 스트림으로 변환하려는 경우가 있습니다. 
가장 일반적인 방법은 원본 스트림에서 이벤트를 기다린 다음, 
새 이벤트를 출력하는 새 스트림을 만드는 것입니다. 예:

<?code-excerpt "misc/lib/articles/creating-streams/line_stream_generator.dart (split-into-lines)"?>
```dart
/// 연속된 문자열 스트림을 라인으로 분할합니다.
///
/// 입력 문자열은 `source` 스트림을 통해 더 작은 청크로 제공됩니다.
Stream<String> lines(Stream<String> source) async* {
  // 이전 청크의 일부 라인을 저장합니다.
  var partial = '';
  // 새로운 청크가 나올 때까지 기다렸다가, 처리합니다.
  await for (final chunk in source) {
    var lines = chunk.split('\n');
    lines[0] = partial + lines[0]; // partial line를 앞에 추가합니다.
    partial = lines.removeLast(); // 새로운 partial line를 제거합니다.
    for (final line in lines) {
      yield line; // 출력 스트림에 라인을 추가합니다.
    }
  }
  // 마지막 partial line이 있으면, 출력 스트림에 추가합니다.
  if (partial.isNotEmpty) yield partial;
}
```

많은 일반적인 변환의 경우, (`map()`, `where()`, `expand()`, `take()`와 같은) 
`Stream`에서 제공하는 변환 메서드를 사용할 수 있습니다.

예를 들어, 매초 증가하는 카운터를 방출하는, 
스트림 `counterStream`이 있다고 가정해 보겠습니다. 
구현 방법은 다음과 같습니다.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (basic-usage)"?>
```dart
var counterStream =
    Stream<int>.periodic(const Duration(seconds: 1), (x) => x).take(15);
```

이벤트를 빠르게 보려면, 다음과 같은 코드를 사용할 수 있습니다.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (basic-for-each)"?>
```dart
counterStream.forEach(print); // 1초마다 정수를 15번 출력합니다.
```

스트림 이벤트를 변환하려면, 스트림을 수신하기 전에, 
스트림에서 `map()`과 같은 변환 메서드를 호출할 수 있습니다. 
이 메서드는 새 스트림을 반환합니다.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (use-map)"?>
```dart
// 각 이벤트의 정수를 두 배로 늘립니다.
var doubleCounterStream = counterStream.map((int x) => x * 2);
doubleCounterStream.forEach(print);
```

`map()` 대신, 다음과 같이, 다른 변환 메서드를 사용할 수 있습니다.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (use-where)"?>
```dart
.where((int x) => x.isEven) // 짝수 정수 이벤트만 유지합니다.
.expand((var x) => [x, x]) // 각 이벤트를 복제합니다.
.take(5) // 처음 5개 이벤트가 발생한 후에는 중지합니다.
```

종종, 변환 메서드만으로 충분합니다. 
그러나, 변환에 대한 제어를 더 많이 해야 하는 경우, 
`Stream`의 `transform()` 메서드로 [StreamTransformer]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/StreamTransformer-class.html)를 지정할 수 있습니다. 
플랫폼 라이브러리는 많은 일반적인 작업에 대한 스트림 변환기를 제공합니다. 
예를 들어, 다음 코드는 dart:convert 라이브러리에서 제공하는, 
`utf8.decoder` 및 `LineSplitter` 변환기를 사용합니다.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (use-transform)"?>
```dart
Stream<List<int>> content = File('someFile.txt').openRead();
List<String> lines = await content
    .transform(utf8.decoder)
    .transform(const LineSplitter())
    .toList();
```

## 처음부터 스트림 만들기 {:#creating-a-stream-from-scratch}

새로운 스트림을 만드는 한 가지 방법은 비동기 생성기(`async*`) 함수를 사용하는 것입니다. 
스트림은 함수가 호출될 때 생성되고, 스트림이 수신되면 함수의 본문이 실행되기 시작합니다. 
함수가 반환되면, 스트림이 닫힙니다. 
함수가 반환될 때까지, `yield` 또는 `yield*` 문을 사용하여 스트림에서 이벤트를 방출할 수 있습니다.

다음은 일정한 간격으로 숫자를 방출하는 기본 예입니다.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (async-generator)" replace="/timedCounterGenerator/timedCounter/g"?>
```dart
Stream<int> timedCounter(Duration interval, [int? maxCount]) async* {
  int i = 0;
  while (true) {
    await Future.delayed(interval);
    yield i++;
    if (i == maxCount) break;
  }
}
```

{% comment %}
[PENDING: show code that uses it, so we have some context for
the mention of StreamSubscription?]
{% endcomment %}

이 함수는 `Stream`을 반환합니다.
해당 스트림을 수신하면, 본문이 실행되기 시작합니다. 
요청된 간격 동안 반복적으로 지연한 다음 다음 숫자를 생성합니다. 
`maxCount` 매개변수가 생략되면, 루프에 중지 조건이 없으므로, 
스트림은 점점 더 큰 숫자를 영원히 출력하거나, 리스너가 구독을 취소할 때까지 출력합니다.

리스너가 취소하면(`listen()` 메서드에서 반환된 `StreamSubscription` 객체에서 `cancel()`을 호출하여), 
본문이 다음에 `yield` 문에 도달하면, `yield`가 대신 `return` 문으로 작동합니다. 
둘러싼 `finally` 블록이 실행되고, 함수가 종료됩니다. 
함수가 종료되기 전에 값을 생성하려고 하면, 실패하고 반환으로 작동합니다.

함수가 마침내 종료되면, `cancel()` 메서드에서 반환된 future가 완료됩니다. 
함수가 오류로 종료되면, future가 해당 오류로 완료되고, 그렇지 않으면 `null`로 완료됩니다.

더욱 유용한 또 다른 예는, futures 시퀀스를 stream으로 변환하는 함수입니다.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (stream-from-futures)"?>
```dart
Stream<T> streamFromFutures<T>(Iterable<Future<T>> futures) async* {
  for (final future in futures) {
    var result = await future;
    yield result;
  }
}
```

이 함수는 `futures` iterable에 새 future를 요청하고, 
해당 future를 기다리고, 결과 값을 내보내고, 루프를 돌립니다. 
future가 오류로 완료되면, 스트림도 해당 오류로 완료됩니다.

`async*` 함수가 아무것도 없는 곳에서 스트림을 빌드하는 경우는 드뭅니다. 
어딘가에서 데이터를 가져와야 하며, 대부분 그 어딘가는 다른 스트림입니다. 
위의 future 시퀀스와 같은 일부 경우에는, 데이터가 다른 비동기 이벤트 소스에서 제공됩니다. 
그러나 많은 경우, `async*` 함수는 너무 단순해서 여러 데이터 소스를 쉽게 처리할 수 없습니다. 
바로 여기서 `StreamController` 클래스가 등장합니다.

## StreamController 사용 {:#using-a-streamcontroller}

스트림의 이벤트가 프로그램의 다른 부분에서 발생하고, 
`async` 함수로 탐색할 수 있는 스트림이나 퓨처에서 발생하는 것이 아니라면, 
[StreamController]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/StreamController-class.html)를 사용하여 스트림을 만들고 채웁니다.

`StreamController`는 새 스트림을 제공하고, 
언제 어디서나 스트림에 이벤트를 추가할 수 있는 방법을 제공합니다. 
스트림에는 리스너와 일시 중지를 처리하는 데 필요한 모든 로직이 있습니다. 
스트림을 반환하고 컨트롤러는 본인만 사용합니다.

다음 예제([stream_controller_bad.dart][]에서 가져옴)는, 
이전 예제의 `timedCounter()` 함수를 구현하기 위한 `StreamController`의 기본적이지만, 
결함이 있는 사용법을 보여줍니다. 
이 코드는 반환할 스트림을 만든 다음, 퓨처도 스트림 이벤트도 아닌, 타이머 이벤트에 기반하여 데이터를 공급합니다.

[stream_controller_bad.dart]: {{site.repo.this}}/blob/main/examples/misc/lib/articles/creating-streams/stream_controller_bad.dart

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller_bad.dart (flawed-stream)"?>
```dart tag=bad
// NOTE: 이 구현에는 결함이 있습니다!
// 구독자(subscribers)가 생기기 전에 시작하며, 일시 중지 기능을 구현하지 않습니다.
Stream<int> timedCounter(Duration interval, [int? maxCount]) {
  var controller = StreamController<int>();
  int counter = 0;
  void tick(Timer timer) {
    counter++;
    controller.add(counter); // 스트림에 카운터 값을 이벤트로서 전송하도록 요청합니다.
    if (maxCount != null && counter >= maxCount) {
      timer.cancel();
      controller.close(); // 스트림을 종료하고, 수신자에게 알리도록 요청합니다.
    }
  }

  Timer.periodic(interval, tick); // BAD: 구독자(subscribers)가 생기기 전에 시작합니다.
  return controller.stream;
}
```

이전과 마찬가지로, `timedCounter()`에서 반환된 스트림을 다음과 같이 사용할 수 있습니다.
{% comment %}
**[PENDING: Did we show this before?]**
{% endcomment %}

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller_bad.dart (using-stream)"?>
```dart
var counterStream = timedCounter(const Duration(seconds: 1), 15);
counterStream.listen(print); // 매 초마다 정수를 15번 출력합니다.
```

`timedCounter()`의 이 구현에는 몇 가지 문제가 있습니다.

* 구독자가 있기 전에, 이벤트를 생성하기 시작합니다.
* 구독자가 일시 중지를 요청하더라도, 이벤트를 계속 생성합니다.

다음 섹션에서 볼 수 있듯이, 
`StreamController`를 생성할 때 `onListen` 및 `onPause`와 같은 콜백을 지정하여, 
이 두 가지 문제를 모두 해결할 수 있습니다.

### 구독을 기다립니다. {:#waiting-for-a-subscription}

원칙적으로, 스트림은 작업을 시작하기 전에 구독자를 기다려야 합니다. 
`async*` 함수는 이를 자동으로 수행하지만, `StreamController`를 사용하면, 
모든 제어권을 갖고 있어서 이벤트를 추가할 필요가 없을 때에도 이벤트를 추가할 수 있습니다. 
스트림에 구독자가 없으면, `StreamController`가 이벤트를 버퍼링하는데, 
스트림이 구독자를 전혀 받지 못하면 메모리 누수가 발생할 수 있습니다.

스트림을 사용하는 코드를 다음과 같이 변경해 보세요.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller_bad.dart (pre-subscribe-problem)"?>
```dart
void listenAfterDelay() async {
  var counterStream = timedCounter(const Duration(seconds: 1), 15);
  await Future.delayed(const Duration(seconds: 5));

  // 5초 후에 리스너를 추가합니다.
  await for (final n in counterStream) {
    print(n); // 매 초마다 정수를 15번 출력합니다.
  }
}
```

이 코드를 실행하면, 스트림이 작동하고 있지만, 처음 5초 동안은 아무것도 출력되지 않습니다. 
그런 다음 리스너가 추가되고, 처음 5개 정도의 이벤트가 `StreamController`에 의해 버퍼링되었기 때문에, 
한꺼번에 모두 출력됩니다.

구독 알림을 받으려면, `StreamController`를 만들 때 `onListen` 인수를 지정합니다. 
스트림이 첫 번째 구독자를 받으면, `onListen` 콜백이 호출됩니다. 
`onCancel` 콜백을 지정하면, 컨트롤러가 마지막 구독자를 잃을 때 호출됩니다. 
이전 예에서, `Timer.periodic()`은 다음 섹션에 표시된 대로, `onListen` 핸들러로 이동해야 합니다.

### 일시 정지 상태를 존중합니다. {:#honoring-the-pause-state}

리스너가 일시 중지를 요청했을 때 이벤트를 생성하지 마세요. 
`async*` 함수는 스트림 구독이 일시 중지된 동안, `yield` 문에서 자동으로 일시 중지합니다. 
반면 `StreamController`는, 일시 중지 중에 이벤트를 버퍼링합니다. 
이벤트를 제공하는 코드가 일시 중지를 따르지 않으면, 버퍼 크기가 무한정 커질 수 있습니다. 
또한, 리스너가 일시 중지 직후에 수신을 중단하면, 버퍼를 만드는 데 사용한 작업이 낭비됩니다.

일시 중지 지원 없이 어떤 일이 일어나는지 보려면, 스트림을 사용하는 코드를 다음과 같이 변경해 보세요.

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller_bad.dart (pause-problem)"?>
```dart
void listenWithPause() {
  var counterStream = timedCounter(const Duration(seconds: 1), 15);
  late StreamSubscription<int> subscription;

  subscription = counterStream.listen((int counter) {
    print(counter); // 매 초마다 정수를 출력합니다.
    if (counter == 5) {
      // 5틱 후, 5초간 멈췄다가, 다시 시작합니다.
      subscription.pause(Future.delayed(const Duration(seconds: 5)));
    }
  });
}
```

5초의 일시 정지가 끝나면, 그 시간 동안 발생한 이벤트가 모두 한꺼번에 수신됩니다. 
이는 스트림의 소스가 일시 정지를 존중하지 않고, 스트림에 이벤트를 계속 추가하기 때문에 발생합니다. 
따라서 스트림은 이벤트를 버퍼링한 다음, 스트림이 일시 정지가 해제되면 버퍼를 비웁니다.

다음 버전의 `timedCounter()`([stream_controller.dart][]에서 가져옴)는 
`StreamController`에서 `onListen`, `onPause`, `onResume`, `onCancel` 콜백을 사용하여, 
일시 정지를 구현합니다.

[stream_controller.dart]: {{site.repo.this}}/blob/main/examples/misc/lib/articles/creating-streams/stream_controller.dart

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (better-stream)"?>
```dart
Stream<int> timedCounter(Duration interval, [int? maxCount]) {
  late StreamController<int> controller;
  Timer? timer;
  int counter = 0;

  void tick(_) {
    counter++;
    controller.add(counter); // 스트림에 카운터 값을 이벤트로서 전송하도록 요청합니다.
    if (counter == maxCount) {
      timer?.cancel();
      controller.close(); // 스트림을 종료하고, 수신자에게 알리도록 요청합니다.
    }
  }

  void startTimer() {
    timer = Timer.periodic(interval, tick);
  }

  void stopTimer() {
    timer?.cancel();
    timer = null;
  }

  controller = StreamController<int>(
      onListen: startTimer,
      onPause: stopTimer,
      onResume: startTimer,
      onCancel: stopTimer);

  return controller.stream;
}
```

위의 `listenWithPause()` 함수로 이 코드를 실행합니다. 
일시 중지된 동안 계산을 멈추고, 그 후에 멋지게 다시 시작하는 것을 볼 수 있습니다.

일시 중지 상태의 변경 사항을 알리려면 `onListen`, `onCancel`, `onPause`, `onResume` 등 모든 리스너를 사용해야 합니다. 
그 이유는 구독과 일시 중지 상태가 동시에 변경되는 경우, 
`onListen` 또는 `onCancel` 콜백만 호출되기 때문입니다.

## 마지막 힌트 {:#final-hints}

async* 함수를 사용하지 않고 스트림을 생성할 때, 다음 팁을 염두에 두십시오.

* 동기 컨트롤러를 사용할 때는 주의하세요. 
  예를 들어, `StreamController(sync: true)`를 사용하여 만든 컨트롤러를 사용할 때는 주의하세요. 
  일시 정지되지 않은 동기 컨트롤러에서 이벤트를 보낼 때(예: [EventSink]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/EventSink-class.html)에서 정의한 `add()`, `addError()` 또는 `close()` 메서드 사용), 
  해당 이벤트는 스트림의 모든 리스너에 즉시 전송됩니다. 
  `Stream` 리스너는 리스너를 추가한 코드가 완전히 반환될 때까지 절대로 호출해서는 안되며, 
  잘못된 시간에 동기 컨트롤러를 사용하면 이 약속이 깨지고 좋은 코드가 실패할 수 있습니다. 
  동기 컨트롤러 사용을 피하세요.

* `StreamController`를 사용하는 경우, 
  `listen` 호출이 `StreamSubscription`을 반환하기 전에 `onListen` 콜백이 호출됩니다. 
  `onListen` 콜백이 이미 존재하는 구독에 의존하도록 하지 마세요. 
  예를 들어, 다음 코드에서는, `subscription` 변수가 유효한 값을 가지기 전에, 
  `onListen` 이벤트가 발생하고 `handler`가 호출됩니다.

  <?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (stream-listen-hint)"?>
  ```dart
  subscription = stream.listen(handler);
  ```

* `StreamController`에서 정의한 `onListen`, `onPause`, `onResume`, `onCancel` 콜백은, 
  스트림의 리스너 상태가 변경될 때 스트림에서 호출되지만, 
  이벤트가 발생하거나 다른 상태 변경 핸들러가 호출되는 동안에는 호출되지 않습니다. 
  이러한 경우, 상태 변경 콜백은 이전 콜백이 완료될 때까지 지연됩니다.

* `Stream` 인터페이스를 직접 구현하려고 하지 마세요. 
  이벤트, 콜백, 리스너 추가 및 제거 간의 상호 작용을 미묘하게 잘못 이해하기 쉽습니다. 
  항상 기존 스트림을 사용하세요. 
  가능하면 `StreamController`에서 가져와, 새 스트림의 `listen` 호출을 구현하세요.

* `Stream` 클래스를 확장하고 `listen` 메서드와 그 위에 추가 기능을 구현하여, 
  `Stream`을 더 많은 기능으로 확장하는 클래스를 만들 수 있지만, 
  일반적으로는 사용자가 고려해야 할 새로운 타입을 도입하기 때문에 권장하지 않습니다. 
  _`Stream` (and more)인 클래스_ 대신, 
  _`Stream` (and more)을 갖는_ 클래스를 만들 수도 있습니다.

{% comment %}
The tests for this article are at /src/tests/site/articles/creating-streams.
{% endcomment %}
