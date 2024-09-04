---
title: Isolates
# description: Information on writing isolates in Dart.
description: Dart에서 isolates을 작성하는 방법에 대한 정보입니다.
short-title: Isolates
lastVerified: 2024-01-04
prevpage:
  url: /language/async
  # title: Asynchronous support
  title: 비동기 지원
nextpage:
  url: /null-safety
  # title: Sound Null Safety
  title: Sound Null 세이프티
---

<?code-excerpt path-base="concurrency"?>

<style>
  article img {
    padding: 15px 0;
  }
</style>

이 페이지에서는 `Isolate` API를 사용하여 isolates를 구현하는 몇 가지 예를 설명합니다.

애플리케이션에서 다른 계산을 일시적으로 차단할 만큼 큰 계산을 처리할 때마다, 
isolates를 사용해야 합니다. 
가장 일반적인 예는 [Flutter][] 애플리케이션에서, 
UI가 응답하지 않을 수 있는 큰 계산을 수행해야 할 때입니다.

isolates를 _반드시_ 사용해야 하는 경우에 대한 규칙은 없지만, 
다음은 isolates를 사용할 수 있는 몇 가지 상황입니다.

- 매우 큰 JSON 블롭을 파싱하고 디코딩합니다.
- 사진, 오디오 및 비디오를 처리하고 압축합니다.
- 오디오 및 비디오 파일을 변환합니다.
- 대규모 리스트나 파일 시스템 내에서 복잡한 검색 및 필터링을 수행합니다.
- 데이터베이스와 통신하는 것과 같은 I/O를 수행합니다.
- 대량의 네트워크 요청을 처리합니다.

[Flutter]: {{site.flutter-docs}}/perf/isolates

## 간단한 워커 isolate 구현 {:#implementing-a-simple-worker-isolate}

이 예제는 간단한 워커 isolate를 생성하는 메인 isolate를 구현합니다. 
[`Isolate.run()`][]은 워커 isolate를 설정하고, 관리하는 단계를 간소화합니다.

1. isolate를 생성합니다. (시작 및 생성)
2. 생성된 isolate에서 함수를 실행합니다.
3. 결과를 캡처합니다.
4. 결과를 메인 isolate에 반환합니다.
5. 작업이 완료되면 isolate를 종료합니다.
6. 예외와 오류를 확인, 캡처하고 메인 isolate에 다시 throw합니다.

[`Isolate.run()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/run.html

:::flutter-note
Flutter를 사용하는 경우, `Isolate.run()` 대신, 
[Flutter의 `compute` 함수][Flutter's `compute` function]를 사용할 수 있습니다.
:::

[Flutter's `compute` function]: {{site.flutter-api}}/flutter/foundation/compute.html

### 새로운 isolate에서 기존 메서드 실행 {:#running-an-existing-method-in-a-new-isolate}

1. `main()`이 결과를 기다리는 동안, 
   `run()`을 호출하여, 새로운 isolate(백그라운드 워커)를 [메인 isolate][main isolate]에서 직접 생성합니다.

   <?code-excerpt "lib/simple_worker_isolate.dart (main)"?>
   ```dart
   const String filename = 'with_keys.json';

   void main() async {
     // 데이터를 읽습니다.
     final jsonData = await Isolate.run(_readAndParseJson);

     // 해당 데이터를 사용하세요.
     print('Number of JSON keys: ${jsonData.length}');
   }
   ```

2. 워커 isolate에 실행하고자 하는 함수를 첫 번째 인수로 전달합니다. 
   이 예에서는, 기존 함수 `_readAndParseJson()`입니다.

   <?code-excerpt "lib/simple_worker_isolate.dart (spawned)"?>
   ```dart
   Future<Map<String, dynamic>> _readAndParseJson() async {
     final fileData = await File(filename).readAsString();
     final jsonData = jsonDecode(fileData) as Map<String, dynamic>;
     return jsonData;
   }
   ```

3. `Isolate.run()`은 `_readAndParseJson()`의 결과를 반환하여, 
   값을 다시 메인 isolate로 보내, 워커 isolate를 종료합니다.

4. 워커 isolate는 결과를 보유한 메모리를 메인 isolate로 *전송*합니다. 
   데이터를 *복사*하지 않습니다. 
   워커 isolate는 검증 단계를 수행하여, 객체가 전송될 수 있는지 확인합니다.

`_readAndParseJson()`은 기존의 비동기 함수로, 메인 isolate에서 바로 실행할 수도 있습니다. 
대신, `Isolate.run()`을 사용하여 실행하면 동시성이 활성화됩니다. 
워커 isolate는 `_readAndParseJson()`의 계산을 완전히 추상화합니다. 
메인 isolate를 차단하지 않고 완료할 수 있습니다.

`Isolate.run()`의 결과는 항상 Future입니다. 
메인 isolate의 코드는 계속 실행되기 때문입니다. 
워커 isolate가 실행하는 계산이 동기식이든 비동기식이든 메인 isolate에 영향을 미치지 않습니다. 
어느 쪽이든 동시에 실행되기 때문입니다.

전체 프로그램은, [send_and_receive.dart][] 샘플을 확인하세요.

[send_and_receive.dart]: {{site.repo.dart.org}}/samples/blob/main/isolates/bin/send_and_receive.dart
[background worker]: /language/concurrency#background-workers
[main isolate]: /language/concurrency#the-main-isolate

### isolate으로 클로저 보내기 {:#sending-closures-with-isolates}

`run()`을 사용해 함수 리터럴이나 클로저를 메인 isolate에서 직접 사용하여, 
간단한 워커 isolate을 만들 수도 있습니다.

<?code-excerpt "lib/simple_isolate_closure.dart (worker)"?>
```dart
const String filename = 'with_keys.json';

void main() async {
  // 데이터를 읽습니다.
  final jsonData = await Isolate.run(() async {
    final fileData = await File(filename).readAsString();
    final jsonData = jsonDecode(fileData) as Map<String, dynamic>;
    return jsonData;
  });

  // 해당 데이터를 사용하세요.
  print('Number of JSON keys: ${jsonData.length}');
}
```

이 예제는 이전 예제와 동일한 것을 달성합니다. 
새로운 isolate가 생성되고, 무언가를 계산하고, 결과를 다시 보냅니다.

그러나, 이제 isolate는 [클로저][closure]를 보냅니다. 
클로저는 일반적인 명명된 함수보다 제한이 적습니다. 
기능하는 방식과 코드에 작성하는 방식 모두에서 그렇습니다. 
이 예제에서, `Isolate.run()`은 로컬 코드처럼 보이는 것을 동시에 실행합니다.
그런 의미에서, `run()`이 "병렬로 실행"을 위한 제어 흐름 연산자처럼 작동한다고 생각할 수 있습니다.

[closure]: /language/functions#anonymous-functions

## 포트를 사용하여 isolates 간에 여러 메시지 전송 {:#sending-multiple-messages-between-isolates-with-ports}

단기 isolates는 사용하기 편리하지만, 
새로운 isolates를 생성하고 한 isolate에서 다른 isolate로 객체를 복사하는 데 성능 오버헤드가 필요합니다. 
코드가 `Isolate.run`을 사용하여 동일한 계산을 반복적으로 실행하는 데 의존하는 경우, 
즉시 종료되지 않는 장기 isolates를 대신 생성하여 성능을 개선할 수 있습니다.

이를 위해, `Isolate.run`이 추상화하는 일부 낮은 레벨 isolate API를 사용할 수 있습니다.

* [`Isolate.spawn()`][] 및 [`Isolate.exit()`][]
* [`ReceivePort`][] 및 [`SendPort`][]
* [`SendPort.send()` 메서드][`SendPort.send()` method]

이 섹션에서는 새로 생성된 isolate와 [메인 isolate][main isolate] 간의 2방향 통신을 설정하는 데 필요한 단계를 살펴봅니다. 
첫 번째 예인 [기본 포트](#basic-ports-example)는 높은 레벨에서의 프로세스를 소개합니다. 
두 번째 예인 [강력한 포트](#robust-ports-example)는 점진적으로 첫 번째 예에 더 실용적이고 현실적인 기능을 추가합니다.

[`Isolate.exit()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/exit.html
[`Isolate.spawn()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/spawn.html
[`ReceivePort`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/ReceivePort-class.html
[`SendPort`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/SendPort-class.html
[`SendPort.send()` method]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/SendPort/send.html
[main isolate]: /language/concurrency#isolates

### `ReceivePort` 및 `SendPort` {:#receiveport-and-sendport}

isolates 간에 긴수명 통신을 설정하려면, 
`Isolate` 외에 `ReceivePort`와 `SendPort`라는 두 클래스가 필요합니다. 
이 포트는 isolates가 서로 통신할 수 있는 유일한 방법입니다.

`ReceivePort`는 다른 isolates에서 보낸 메시지를 처리하는 객체입니다. 
이러한 메시지는 `SendPort`를 통해 전송됩니다.

:::note
`SendPort` 객체는 정확히 하나의 `ReceivePort`와 연관되지만, 
하나의 `ReceivePort`는 여러 개의 `SendPort`를 가질 수 있습니다. 
`ReceivePort`를 만들면, 자체적으로 `SendPort`를 만듭니다. 
기존 `ReceivePort`로 메시지를 보낼 수 있는 추가 `SendPort`를 만들 수 있습니다.
:::

포트는 [`Stream`][] 객체와 비슷하게 동작합니다. (사실, 수신 포트는 `Stream`을 구현합니다!)
`SendPort`와 `ReceivePort`를 각각 Stream의 `StreamController`와 리스너로 생각할 수 있습니다. 
`SendPort`는 `StreamController`와 비슷합니다. 
왜냐하면 [`SendPort.send()` 메서드][`SendPort.send()` method]로 메시지를 "추가"하고, 
이 메시지는 리스너(이 경우 `ReceivePort`)에서 처리하기 때문입니다. 
그런 다음, `ReceivePort`는 사용자가 제공한 콜백에 인수로 전달하여, 수신한 메시지를 처리합니다.

#### 포트 설정 {:#setting-up-ports}

새로 생성된 isolate는 `Isolate.spawn` 호출을 통해 수신한 정보만 가집니다. 
초기 생성 이후에도 생성된 isolate와 계속 통신하려면, 
생성된 isolate가 메시지를 메인 isolate에 보낼 수 있는 통신 채널을 설정해야 합니다. 
Isolates는 메시지 전달을 통해서만 통신할 수 있습니다. 
서로의 메모리 내부를 "볼" 수 없으므로, "isolate"라는 이름이 유래되었습니다.

이 양방향 통신을 설정하려면, 먼저 메인 isolate에 [`ReceivePort`][]를 만든 다음, 
`Isolate.spawn`으로 생성할 때 해당 [`SendPort`][]를 새 isolate에 인수로 전달합니다. 
그런 다음 새 isolate는 자체 `ReceivePort`를 만들고, 
메인 isolate에서 전달한 `SendPort`로 _자신의_ `SendPort`를 다시 보냅니다. 
메인 isolate는 이 `SendPort`를 수신하고, 
이제 양쪽 모두 메시지를 보내고 받을 수 있는 열린 채널이 있습니다.

:::note
이 섹션의 다이어그램은 높은 레벨이며, isolates를 위해 포트를 사용하는 _개념_ 을 전달하기 위한 것입니다. 
실제 구현에는 약간 더 많은 코드가 필요하며, 
이는 [이 페이지의 후반부](#basic-ports-example)에서 찾을 수 있습니다.
:::

![A figure showing events being fed, one by one, into the event loop](/assets/img/language/concurrency/ports-setup.png)

1. 메인 isolate에서 `ReceivePort`를 만듭니다. 
   `SendPort`는 `ReceivePort`의 속성으로 자동 생성됩니다.
2. `Isolate.spawn()`로 워커 isolate를 생성합니다.
3. `ReceivePort.sendPort`에 대한 참조를 워커 isolate에 첫 번째 메시지로 전달합니다.
4. 워커 isolate에서 또 다른 새 `ReceivePort`를 만듭니다.
5. 워커 isolate의 `ReceivePort.sendPort`에 대한 참조를, 
   메인 isolate에 _돌아가는_ 첫 번째 메시지로 전달합니다.

포트를 만들고 통신을 설정하는 것과 함께, 포트가 메시지를 받을 때 수행할 작업을 알려야 합니다. 
이는 각각의 `ReceivePort`에서 `listen` 메서드를 사용하여 수행됩니다.

![A figure showing events being fed, one by one, into the event loop](/assets/img/language/concurrency/ports-passing-messages.png)

1. 메인 isolate의 참조를 통해, 워커 isolate의 `SendPort`로 메시지를 보냅니다.
2. 워커 isolate의 `ReceivePort`에서 리스너를 통해 메시지를 수신하고 처리합니다. 
   여기서 메인 isolate에서 옮기고 싶은 계산이 실행됩니다.
3. 워커 isolate의 참조를 통해 메인 isolate의 `SendPort`로 반환 메시지를 보냅니다.
4. 메인 isolate의 `ReceivePort`에서 리스너를 통해 메시지를 수신합니다.

### 기본 포트 예제 {:#basic-ports-example}

이 예제는 긴수명 워커 isolate를 설정하는 방법을 보여줍니다. 
이 isolate와 메인 isolate 간에 양방향 통신이 가능합니다. 
이 코드는 JSON 텍스트를 새 isolate로 보내는 예를 사용하는데, 
JSON은, 메인 isolate로 다시 전송되기 전에, 구문 분석 및 디코딩됩니다.

:::warning
이 예제는 시간이 지남에 따라 여러 메시지를 보내고 받을 수 있는 새로운 isolate를 생성하는 데 필요한, 
_최소한의_ 것을 가르치기 위한 것입니다.

(오류 처리, 포트 종료, 메시지 시퀀싱과 같이) 프로덕션 소프트웨어에서 기대되는 중요한 기능은 다루지 않습니다.

다음 섹션의 [강력한 포트 예제][Robust ports example]는 이 기능을 다루고, 
이 기능이 없으면 발생할 수 있는 몇 가지 문제에 대해 설명합니다.
:::

[robust ports example]: #robust-ports-example

#### 1단계: 워커 클래스 정의 {:#step-1-define-the-worker-class}

먼저 백그라운드 워커 isolate를 위한 클래스를 만듭니다. 
이 클래스에는 다음과 같은 필요한 모든 기능이 포함되어 있습니다.

- isolate를 생성합니다.
- isolate에 메시지를 보냅니다.
- isolate가 일부 JSON을 디코딩하도록 합니다.
- 디코딩된 JSON을 다시 메인 isolate로 보냅니다.

이 클래스는 두 개의 공개 메서드를 노출합니다. 

- (1) 하나는 워커 isolate를 생성하는 메서드이고, 
- (2) 다른 하나는 워커 isolate에 메시지를 보내는 것을 처리하는 메서드입니다.

이 예제의 나머지 섹션에서는 클래스 메서드를 하나씩 채우는 방법을 보여줍니다.

<?code-excerpt "lib/basic_ports_example/start.dart (worker)"?>
```dart
class Worker {
  Future<void> spawn() async {
    // TODO: 워커 isolate를 생성하는 기능을 추가합니다.
  }

  void _handleResponsesFromIsolate(dynamic message) {
    // TODO: 워커 isolate에서 다시 보낸 메시지를 처리합니다.
  }

  static void _startRemoteIsolate(SendPort port) {
    // TODO: 워커 isolate에서 실행되어야 하는 코드를 정의합니다.
  }

  Future<void> parseJson(String message) async {
    // TODO: 워커 isolate에 메시지를 보내는 데 사용할 수 있는 공개 메서드를 정의합니다.
  }
}
```

#### 2단계: 워커 isolate 생성 {:#step-2-spawn-a-worker-isolate}

`Worker.spawn` 메서드는 워커 isolate를 생성하고, 
메시지를 수신하고 보낼 수 있도록 하는 코드를 그룹화하는 곳입니다.

- 먼저, `ReceivePort`를 만듭니다. 
  이렇게 하면, 메인 isolate가 새로 생성된 워커 isolate에서 보낸 메시지를 수신할 수 있습니다.
- 다음으로, 워커 isolate가 다시 보낼 메시지를 처리할 수신 포트에 리스너를 추가합니다. 
  리스너에 전달된 콜백인, `_handleResponsesFromIsolate`는 [4단계](#step-4-handle-messages-on-the-main-isolate)에서 다룹니다.
- 마지막으로, `Isolate.spawn`으로 워커 isolate를 생성합니다. 
  두 개의 인수를 예상합니다. 
  (1) 워커 isolate에서 실행되는 함수([3단계](#step-3-execute-code-on-the-worker-isolate)에서 다룹니다)와 
  (2) 수신 포트의 `sendPort` 속성입니다.

<?code-excerpt "lib/basic_ports_example/complete.dart (spawn)"?>
```dart
Future<void> spawn() async {
  final receivePort = ReceivePort();
  receivePort.listen(_handleResponsesFromIsolate);
  await Isolate.spawn(_startRemoteIsolate, receivePort.sendPort);
}
```

`receivePort.sendPort` 인수는 워커 isolate에서 호출될 때, 
인수로 콜백(`_startRemoteIsolate`)에 전달됩니다. 
이는 워커 isolate가 메인 isolate로 메시지를 다시 보낼 수 있는 방법을 확보하는 첫 번째 단계입니다.

#### 3단계: 워커 isolate에서 코드 실행 {:#step-3-execute-code-on-the-worker-isolate}

이 단계에서는, 워커 isolate가 생성될 때 실행되도록 
워커 isolate에 전송되는 메서드 `_startRemoteIsolate`를 정의합니다. 
이 메서드는 워커 isolate의 "main" 메서드와 같습니다.

- 먼저, 다른 새 `ReceivePort`를 만듭니다. 이 포트는 메인 isolate에서 향후 메시지를 수신합니다.
- 다음으로, 해당 포트의 `SendPort`를 다시 메인 isolate로 보냅니다.
- 마지막으로 새 `ReceivePort`에 리스너를 추가합니다. 
  이 리스너는 메인 isolate가 워커 isolate로 전송하는 메시지를 처리합니다.

<?code-excerpt "lib/basic_ports_example/complete.dart (start-remote-isolate)"?>
```dart
static void _startRemoteIsolate(SendPort port) {
  final receivePort = ReceivePort();
  port.send(receivePort.sendPort);

  receivePort.listen((dynamic message) async {
    if (message is String) {
      final transformed = jsonDecode(message);
      port.send(transformed);
    }
  });
}
```

워커의 `ReceivePort`에 있는 리스너는, 
메인 isolate에서 전달된 JSON을 디코딩한 다음, 
디코딩된 JSON을 메인 isolate로 다시 보냅니다.

이 리스너는 메인 isolate에서 워커 isolate로 전송된 메시지의 진입점입니다. 
**이것이 워커 isolate에 미래에 실행할 코드를 알려줄 수 있는 유일한 기회입니다.**

#### 4단계: 메인 isolate에서 메시지 처리 {:#step-4-handle-messages-on-the-main-isolate}

마지막으로, 메인 isolate에 워커 isolate에서 메인 isolate로 보낸 메시지를 처리하는 방법을 알려줘야 합니다. 
그러려면, `_handleResponsesFromIsolate` 메서드를 채워야 합니다. 
이 메서드는 [2단계](#step-2-spawn-a-worker-isolate)에서 설명한 대로, 
`receivePort.listen` 메서드에 전달된다는 점을 기억하세요.

<?code-excerpt "lib/basic_ports_example/complete.dart (spawn)"?>
```dart
Future<void> spawn() async {
  final receivePort = ReceivePort();
  receivePort.listen(_handleResponsesFromIsolate);
  await Isolate.spawn(_startRemoteIsolate, receivePort.sendPort);
}
```

또한 [3단계](#step-3-execute-code-on-the-worker-isolate)에서, 
`SendPort`를 메인 isolate로 다시 보냈다는 것을 기억하세요. 
이 메서드는 해당 `SendPort` 수신을 처리하고, 향후 메시지(디코딩된 JSON)도 처리합니다.

- 먼저, 메시지가 `SendPort`인지 확인합니다. 
  그렇다면, 해당 포트를 클래스의 `_sendPort` 속성에 할당하여, 
  나중에 메시지를 보내는 데 사용할 수 있도록 합니다.
- 다음으로, 메시지가 예상되는 디코딩된 JSON 타입인 `Map<String, dynamic>` 타입인지 확인합니다. 
  그렇다면, 애플리케이션별 로직으로 해당 메시지를 처리합니다. 
  이 예에서는 메시지가 출력됩니다.

<?code-excerpt "lib/basic_ports_example/complete.dart (handle-responses)"?>
```dart
void _handleResponsesFromIsolate(dynamic message) {
  if (message is SendPort) {
    _sendPort = message;
    _isolateReady.complete();
  } else if (message is Map<String, dynamic>) {
    print(message);
  }
}
```

#### 5단계: 완료자(completer)를 추가하여, isolate가 설정되었는지 확인  {:#step-5-add-a-completer-to-ensure-your-isolate-is-set-up}

클래스를 완성하려면, `parseJson`이라는 공개 메서드를 정의합니다. 
이 메서드는 워커 isolate에 메시지를 보내는 역할을 합니다. 
또한 isolate가 완전히 설정되기 전에, 메시지를 보낼 수 있어야 합니다. 
이를 처리하려면 [`Completer`][]를 사용합니다.

- 먼저, `Completer`라는 클래스 레벨 속성을 추가하고, `_isolateReady`라는 이름을 지정합니다.
- 다음으로, 메시지가 `SendPort`인 경우. 
  `_handleResponsesFromIsolate` 메서드([4단계](#step-4-handle-messages-on-the-main-isolate)에서 생성)에서, 
  완료자에 `complete()`에 대한 호출을 추가합니다.
- 마지막으로, `parseJson` 메서드에서, 
  `_sendPort.send`를 추가하기 전에 `await _isolateReady.future`를 추가합니다. 
  이렇게 하면 워커 isolate가 생성되고, 
  `SendPort`를 다시 메인 isolate로 보낼 때까지는, 
  어떤 메시지도 워커 isolate로 전송되지 않습니다.

<?code-excerpt "lib/basic_ports_example/complete.dart (parse-json)"?>
```dart
Future<void> parseJson(String message) async {
  await _isolateReady.future;
  _sendPort.send(message);
}
```

#### 전체 예시 {:#complete-example}

<details>
  <summary>전체 예를 보려면 확장하세요</summary>

  <?code-excerpt "lib/basic_ports_example/complete.dart"?>
  ```dart
  import 'dart:async';
  import 'dart:convert';
  import 'dart:isolate';
  
  void main() async {
    final worker = Worker();
    await worker.spawn();
    await worker.parseJson('{"key":"value"}');
  }
  
  class Worker {
    late SendPort _sendPort;
    final Completer<void> _isolateReady = Completer.sync();
  
    Future<void> spawn() async {
      final receivePort = ReceivePort();
      receivePort.listen(_handleResponsesFromIsolate);
      await Isolate.spawn(_startRemoteIsolate, receivePort.sendPort);
    }
  
    void _handleResponsesFromIsolate(dynamic message) {
      if (message is SendPort) {
        _sendPort = message;
        _isolateReady.complete();
      } else if (message is Map<String, dynamic>) {
        print(message);
      }
    }
  
    static void _startRemoteIsolate(SendPort port) {
      final receivePort = ReceivePort();
      port.send(receivePort.sendPort);
  
      receivePort.listen((dynamic message) async {
        if (message is String) {
          final transformed = jsonDecode(message);
          port.send(transformed);
        }
      });
    }
  
    Future<void> parseJson(String message) async {
      await _isolateReady.future;
      _sendPort.send(message);
    }
  }
  ```

</details>

### 강력한 포트 예제 {:#robust-ports-example}

[이전 예제][previous example]는 양방향 통신을 사용하여, 
긴 수명 isolate를 설정하는 데 필요한 기본 구성 요소를 설명했습니다. 
언급했듯이, 해당 예제에는 오류 처리, 더 이상 사용되지 않을 때 포트를 닫는 기능, 
일부 상황에서 메시지 순서에 대한 불일치와 같은 몇 가지 중요한 기능이 없습니다.

이 예제는 이러한 추가 기능 등을 갖추고, 더 나은 디자인 패턴을 따르는 긴 수명 워커 isolate를 만들어, 
첫 번째 예제의 정보를 확장합니다. 
이 코드는 첫 번째 예제와 유사하지만, 해당 예제의 확장은 아닙니다.

:::note
이 예제에서는 `Isolate.spawn`과 포트를 사용하여, 
isolates 간 통신을 설정하는 것에 이미 익숙하다고 가정합니다. 
이는 [이전 예제][previous example]에서 다루었습니다.
:::

#### 1단계: 워커 클래스 정의 {:#step-1-define-the-worker-class-1}

먼저 백그라운드 워커 isolate를 위한 클래스를 만듭니다. 
이 클래스에는 다음과 같은 데 필요한 모든 기능이 포함되어 있습니다.

- isolate를 생성합니다.
- isolate에 메시지를 보냅니다.
- isolate가 일부 JSON을 디코딩하도록 합니다.
- 디코딩된 JSON을 다시 메인 isolate로 보냅니다.

이 클래스는 세 가지 공개 메서드를 노출합니다. 

- (1) 하나는 워커 isolate를 만드는 메서드, 
- (2) 하나는 워커 isolate에 메시지를 보내는 것을 처리하는 메서드, 
- (3) 다른 하나는 더 이상 사용하지 않을 때 포트를 종료할 수 있는 메서드입니다.

<?code-excerpt "lib/robust_ports_example/start.dart (worker)"?>
```dart
class Worker {
  final SendPort _commands;
  final ReceivePort _responses;

  Future<Object?> parseJson(String message) async {
    // TODO: 포트가 여전히 열려 있는지 확인하세요.
    _commands.send(message);
  }

  static Future<Worker> spawn() async {
    // TODO: 생성된 isolate에 대한 연결을 사용하여, 새로운 Worker 객체를 생성하는 기능을 추가합니다.
    throw UnimplementedError();
  }

  Worker._(this._responses, this._commands) {
    // TODO: 메인 isolate 수신 포트 리스너를 초기화합니다.
  }

  void _handleResponsesFromIsolate(dynamic message) {
    // TODO: 워커 isolate에서 다시 보낸 메시지를 처리합니다.
  }

  static void _handleCommandsToIsolate(ReceivePort rp, SendPort sp) async {
    // TODO: 워커 isolate에서 다시 보낸 메시지를 처리합니다.
  }

  static void _startRemoteIsolate(SendPort sp) {
    // TODO: 워커 isolate의 포트를 초기화합니다.
  }
}
```

:::note
이 예에서, `SendPort` 및 `ReceivePort` 인스턴스는 모범 사례 명명 규칙을 따르며, 
여기서는 메인 isolate와 관련하여 이름이 지정됩니다. 
메인 isolate에서 워커 isolate로 `SendPort`를 통해 전송된 메시지를 _명령 (commands)_ 이라고 하며, 
메인 isolate로 다시 전송된 메시지를 _응답 (responses)_ 이라고 합니다.
:::

#### 2단계: `Worker.spawn` 메서드에서 `RawReceivePort` 생성 {:#step-2-create-a-rawreceiveport-in-the-worker-spawn-method}

isolate를 생성하기 전에, 낮은 레벨 `ReceivePort`인 [`RawReceivePort`][]를 생성해야 합니다. 
`RawReceivePort`를 사용하는 것은, 
isolate 시작 로직을 isolate에서 메시지 전달을 처리하는 로직과 분리할 수 있기 때문에, 
선호되는 패턴입니다.

`Worker.spawn` 메서드에서:

- 먼저 `RawReceivePort`를 생성합니다. 
  - 이 `ReceivePort`는 `SendPort`가 될 워커 isolate에서 초기 메시지를 수신하는 데만 책임이 있습니다.
- 다음으로, isolate가 메시지를 수신할 준비가 되었을 때를 나타내는 `Completer`를 생성합니다. 
  - 완료되면, `ReceivePort`와 `SendPort`가 있는 레코드가 반환됩니다.
- 다음으로, `RawReceivePort.handler` 속성을 정의합니다. 
  - 이 속성은 `ReceivePort.listener`처럼 동작하는 `Function?`입니다. 
  - 이 함수는 이 포트에서 메시지를 수신할 때 호출됩니다.
- 핸들러 함수 내에서, `connection.complete()`를 호출합니다. 
  - 이 메서드는 인수로 `ReceivePort`와 `SendPort`를 갖는 [레코드][record]를 기대합니다. 
  - `SendPort`는 워커 isolate에서 보낸 초기 메시지로, 
    다음 단계에서 `_commands`라는 클래스 레벨 `SendPort`에 할당됩니다.
- 그런 다음, `ReceivePort.fromRawReceivePort` 생성자로 새 `ReceivePort`를 만들고, 
  `initPort`를 전달합니다.

<?code-excerpt "lib/robust_ports_example/spawn_1.dart (worker-spawn)"?>
```dart
class Worker {
  final SendPort _commands;
  final ReceivePort _responses;

  static Future<Worker> spawn() async {
    // 수신 포트를 생성하고 초기 메시지 처리기를 추가합니다.
    final initPort = RawReceivePort();
    final connection = Completer<(ReceivePort, SendPort)>.sync();
    initPort.handler = (initialMessage) {
      final commandPort = initialMessage as SendPort;
      connection.complete((
        ReceivePort.fromRawReceivePort(initPort),
        commandPort,
      ));
    };
// ···
  }
```

먼저 `RawReceivePort`를 만들고, 그 다음에 `ReceivePort`를 만들면, 
나중에 `ReceivePort.listen`에 새 콜백을 추가할 수 있습니다. 
반대로, 바로 `ReceivePort`를 만들면, 
`ReceivePort`가 [`BroadcastStream`][]이 아니라 [`Stream`][]을 구현하기 때문에, 
`listener`를 하나만 추가할 수 있습니다.

실제로 이렇게 하면 통신 설정이 완료된 후, 
메시지 수신을 처리하는 로직에서 isolate 시작 로직을 분리할 수 있습니다. 
이 이점은 다른 메서드의 로직이 커질수록 더욱 분명해질 것입니다.

#### 3단계: `Isolate.spawn`으로 워커 isolate 생성 {:#step-3-spawn-a-worker-isolate-with-isolate-spawn}

이 단계에서는 `Worker.spawn` 메서드를 계속 채웁니다. 
isolate를 생성하는 데 필요한 코드를 추가하고, 이 클래스에서 `Worker` 인스턴스를 반환합니다. 
이 예에서, `Isolate.spawn`에 대한 호출은 [`try`/`catch` 블록][`try`/`catch` block]으로 래핑되어, 
isolate가 시작되지 않으면, `initPort`가 닫히고 `Worker` 객체가 생성되지 않습니다.

- 먼저, `try`/`catch` 블록에서 워커 isolate를 스폰하려고 시도합니다. 
  워커 isolate를 스폰하는 데 실패하면, 이전 단계에서 만든 수신 포트를 닫습니다. 
  `Isolate.spawn`에 전달된 메서드는 이후 단계에서 다룹니다.
- 다음으로, `connection.future`를 await 하고, 
  반환하는 레코드에서 송신 포트와 수신 포트를 구조 분해합니다.
- 마지막으로, private 생성자를 호출하고, 해당 완료자에서 포트를 전달하여, `Worker` 인스턴스를 반환합니다.

<?code-excerpt "lib/robust_ports_example/spawn_2.dart (worker-spawn)"?>
```dart
class Worker {
  final SendPort _commands;
  final ReceivePort _responses;

  static Future<Worker> spawn() async {
    // 수신 포트를 생성하고 초기 메시지 처리기를 추가합니다.
    final initPort = RawReceivePort();
    final connection = Completer<(ReceivePort, SendPort)>.sync();
    initPort.handler = (initialMessage) {
      final commandPort = initialMessage as SendPort;
      connection.complete((
        ReceivePort.fromRawReceivePort(initPort),
        commandPort,
      ));
    };
    // isolate를 생성합니다.
    try {
      await Isolate.spawn(_startRemoteIsolate, (initPort.sendPort));
    } on Object {
      initPort.close();
      rethrow;
    }

    final (ReceivePort receivePort, SendPort sendPort) =
        await connection.future;

    return Worker._(receivePort, sendPort);
  }
```

이 예에서(이전 예와 비교해서), 
`Worker.spawn`은 이 클래스에 대한 비동기 정적 생성자 역할을 하며, 
`Worker` 인스턴스를 만드는 유일한 방법입니다. 
이렇게 하면 API가 간소화되어, `Worker` 인스턴스를 만드는 코드가 더 깔끔해집니다.

#### 4단계: isolate 설정 프로세스 완료 {:#step-4-complete-the-isolate-setup-process}

이 단계에서는, 기본적인 isolate 설정 프로세스를 완료합니다. 
이는 [이전 예제][previous example]와 거의 완전히 연관되며, 새로운 개념은 없습니다. 
코드가 더 많은 메서드로 나뉜다는 점에서 약간의 변경 사항이 있는데, 
이는 이 예제의 나머지 부분에서 더 많은 기능을 추가할 수 있도록 하는 설계 관행입니다. 
isolate를 설정하는 기본 프로세스에 대한 자세한 연습은, 
[기본 포트 예제](#basic-ports-example)를 참조하세요.

먼저, `Worker.spawn` 메서드에서 반환되는 private 생성자를 만듭니다. 
생성자 본문에서 메인 isolate에서 사용하는 수신 포트에 리스너를 추가하고, 
아직 정의되지 않은 `_handleResponsesFromIsolate`라는 메서드를 해당 리스너에 전달합니다.

<?code-excerpt "lib/robust_ports_example/step_4.dart (constructor)"?>
```dart
class Worker {
  final SendPort _commands;
  final ReceivePort _responses;
// ···
  Worker._(this._responses, this._commands) {
    _responses.listen(_handleResponsesFromIsolate);
  }
```

다음으로, 워커 isolate에서 포트를 초기화하는 코드를 `_startRemoteIsolate`에 추가합니다. 
이 메서드는 `Worker.spawn` 메서드에서 `Isolate.spawn`에 전달되었으며, 
메인 isolate의 `SendPort`가 인수로 전달됩니다.

- 새 `ReceivePort`를 만듭니다.
- 해당 포트의 `SendPort`를 메인 isolate로 다시 보냅니다.
- `_handleCommandsToIsolate`라는 새 메서드를 호출하고, 
  메인 isolate에서 새 `ReceivePort`와 `SendPort`를 모두 인수로 전달합니다.

<?code-excerpt "lib/robust_ports_example/step_4.dart (start-isolate)"?>
```dart
static void _startRemoteIsolate(SendPort sendPort) {
  final receivePort = ReceivePort();
  sendPort.send(receivePort.sendPort);
  _handleCommandsToIsolate(receivePort, sendPort);
}
```

다음으로, 메인 isolate에서 메시지를 수신하고, 
워커 isolate에서 JSON을 디코딩하고, 
디코딩된 JSON을 응답으로 다시 보내는 역할을 하는 `_handleCommandsToIsolate` 메서드를 추가합니다.

- 먼저 워커 isolate의 `ReceivePort`에서 리스너를 선언합니다.
- 리스너에 추가된 콜백 내에서 [`try`/`catch` 블록][`try`/`catch` block] 내에서, 
  메인 isolate에서 전달된 JSON을 디코딩하려고 시도합니다. 
  디코딩이 성공하면 디코딩된 JSON을 메인 isolate로 다시 보냅니다.
- 오류가 있으면 [`RemoteError`][]를 다시 보냅니다.

<?code-excerpt "lib/robust_ports_example/step_4.dart (handle-commands)"?>
```dart
static void _handleCommandsToIsolate(ReceivePort receivePort, SendPort sendPort) {
  receivePort.listen((message) {
    try {
      final jsonData = jsonDecode(message as String);
      sendPort.send(jsonData);
    } catch (e) {
      sendPort.send(RemoteError(e.toString(), ''));
    }
  });
}
```

다음으로, `_handleResponsesFromIsolate` 메서드에 대한 코드를 추가합니다.

- 먼저 메시지가 `RemoteError`인지 확인합니다. 
  이 경우 해당 오류를 `throw`해야 합니다.
- 그렇지 않으면 메시지를 출력합니다. 
  이후 단계에서는, 이 코드를 업데이트하여 메시지를 출력하는 대신, 메시지를 반환하도록 합니다.

<?code-excerpt "lib/robust_ports_example/step_4.dart (handle-response)"?>
```dart
void _handleResponsesFromIsolate(dynamic message) {
  if (message is RemoteError) {
    throw message;
  } else {
    print(message);
  }
}
```

마지막으로, `parseJson` 메서드를 추가합니다. 
이 메서드는 외부 코드에서 JSON을 워커 isolate으로 전송하여, 
디코딩할 수 있도록 하는 공개 메서드입니다.

<?code-excerpt "lib/robust_ports_example/step_4.dart (parse-json)"?>
```dart
Future<Object?> parseJson(String message) async {
  _commands.send(message);
}
```

다음 단계에서 이 메서드를 업데이트합니다.

#### 5단계: 동시에 여러 메시지 처리 {:#step-5-handle-multiple-messages-at-the-same-time}

현재, 워커 isolate에 빠르게 메시지를 보내면, 
isolate는 디코딩된 JSON 응답을 보낸 순서가 아니라 _완료된 순서_ 로 보냅니다. 
어떤 응답이 어떤 메시지에 해당하는지 확인할 방법이 없습니다.

이 단계에서는, 각 메시지에 ID를 지정하고 `Completer` 객체를 사용하여, 
외부 코드에서 `parseJson`을 호출할 때, 
해당 호출자에게 반환되는 응답이 올바른 응답인지 확인하여 이 문제를 해결합니다.

먼저, `Worker`에 두 개의 클래스 레벨 속성을 추가합니다.

- `Map<int, Completer<Object?>> _activeRequests`
- `int _idCounter`

<?code-excerpt "lib/robust_ports_example/step_5_add_completers.dart (vars)"?>
```dart
class Worker {
  final SendPort _commands;
  final ReceivePort _responses;
  final Map<int, Completer<Object?>> _activeRequests = {};
  int _idCounter = 0;
```

`_activeRequests` 맵은 워커 isolate에 전송된 메시지를 `Completer`와 연결합니다. 
`_activeRequests`에서 사용된 키는 `_idCounter`에서 가져오며, 
더 많은 메시지가 전송될수록 증가합니다.

다음으로, `parseJson` 메서드를 업데이트하여, 워커 isolate에 메시지를 전송하기 전에, 완료자를 만듭니다.

- 먼저 `Completer`를 만듭니다.
- 다음으로, `_idCounter`를 증가시켜, 각 `Completer`가 고유한 숫자와 연결되도록 합니다.
- 키가 `_idCounter`의 현재 숫자이고, 값이 완료자인 항목을 `_activeRequests` 맵에 추가합니다.
- 메시지를 ID와 함께 워커 isolate에 전송합니다. 
  `SendPort`를 통해 하나의 값만 전송할 수 있으므로, 
  ID와 메시지를 [레코드][record]로 래핑합니다.
- 마지막으로 완료자의 Future를 반환합니다. 
  여기에는 결국 워커 isolate의 응답이 포함됩니다.

<?code-excerpt "lib/robust_ports_example/step_5_add_completers.dart (parse-json)"?>
```dart
Future<Object?> parseJson(String message) async {
  final completer = Completer<Object?>.sync();
  final id = _idCounter++;
  _activeRequests[id] = completer;
  _commands.send((id, message));
  return await completer.future;
}
```

또한 이 시스템을 처리하려면, 
`_handleResponsesFromIsolate`와 `_handleCommandsToIsolate`를 업데이트해야 합니다.

`_handleCommandsToIsolate`에서, 
`message`가 json 텍스트가 아닌, 두 개의 값이 있는 레코드라는 점을 고려해야 합니다. 
`message`에서 값을 구조 분해하여 이를 수행합니다.

그런 다음, json을 디코딩한 후, `sendPort.send` 호출을 업데이트하여, 
id와 디코딩된 json을 모두 다시 레코드를 사용하여, 메인 isolate로 전달합니다.

<?code-excerpt "lib/robust_ports_example/step_5_add_completers.dart (handle-commands)"?>
```dart
static void _handleCommandsToIsolate(ReceivePort receivePort, SendPort sendPort) {
  receivePort.listen((message) {
    final (int id, String jsonText) = message as (int, String); // New
    try {
      final jsonData = jsonDecode(jsonText);
      sendPort.send((id, jsonData)); // Updated
    } catch (e) {
      sendPort.send((id, RemoteError(e.toString(), '')));
    }
  });
}
```

마지막으로, `_handleResponsesFromIsolate`를 업데이트합니다.

- 먼저, message 인수에서 id와 response를 다시 구조 분해합니다.
- 그런 다음, `_activeRequests` 맵에서 이 요청에 해당하는 completer를 제거합니다.
- 마지막으로, 오류를 발생시키거나 디코딩된 json을 출력하는 대신, 
  completer를 완료하고 response를 전달합니다. 
  이것이 완료되면, 응답은 메인 isolate에서 `parseJson`을 호출한 코드로 반환됩니다.

<?code-excerpt "lib/robust_ports_example/step_5_add_completers.dart (handle-response)"?>
```dart
void _handleResponsesFromIsolate(dynamic message) {
  final (int id, Object? response) = message as (int, Object?); // New
  final completer = _activeRequests.remove(id)!; // New

  if (response is RemoteError) {
    completer.completeError(response); // 업데이트됨
  } else {
    completer.complete(response); // 업데이트됨
  }
}
```

#### 6단계: 포트를 닫는 기능 추가 {:#step-6-add-functionality-to-close-the-ports}

코드에서 isolate를 더 이상 사용하지 않는 경우, 
메인 isolate와 워커 isolate의 포트를 닫아야 합니다.

- 먼저, 포트가 닫혔는지 추적하는 클래스 레벨 boolean을 추가합니다.
- 그런 다음, `Worker.close` 메서드를 추가합니다. 이 메서드 내에서:
  - `_closed`를 true로 업데이트합니다.
  - 워커 isolate에 마지막 메시지를 보냅니다. 
    이 메시지는 "shutdown"이라는 `String`이지만, 원하는 객체일 수 있습니다. 
    다음 코드 조각에서 사용합니다.
- 마지막으로, `_activeRequests`가 비어 있는지 확인합니다. 
  비어 있으면, `_responses`라는 메인 isolate의 `ReceivePort`를 닫습니다.

  <?code-excerpt "lib/robust_ports_example/step_6_close_ports.dart (close)"?>
  ```dart
  class Worker {
    bool _closed = false;
  // ···
    void close() {
      if (!_closed) {
        _closed = true;
        _commands.send('shutdown');
        if (_activeRequests.isEmpty) _responses.close();
        print('--- port closed --- ');
      }
    }
  ```

- 다음으로, 워커 isolate에서 "shutdown" 메시지를 처리해야 합니다. 
  다음 코드를 `_handleCommandsToIsolate` 메서드에 추가합니다. 
  이 코드는 메시지가 "shutdown"을 읽는 `String`인지 확인합니다. 
  만약 그렇다면, 워커 isolate의 `ReceivePort`를 닫고 반환합니다.

  <?code-excerpt "lib/robust_ports_example/step_6_close_ports.dart (handle-commands)"?>
  ```dart
  static void _handleCommandsToIsolate(
    ReceivePort receivePort,
    SendPort sendPort,
  ) {
    receivePort.listen((message) {
      // 새로운 if 블록.
      if (message == 'shutdown') {
        receivePort.close();
        return;
      }
      final (int id, String jsonText) = message as (int, String);
      try {
        final jsonData = jsonDecode(jsonText);
        sendPort.send((id, jsonData));
      } catch (e) {
        sendPort.send((id, RemoteError(e.toString(), '')));
      }
    });
  }
  ```

- 마지막으로, 메시지를 보내기 전에 포트가 닫혔는지 확인하는 코드를 추가해야 합니다. 
  `Worker.parseJson` 메서드에 한 줄을 추가합니다.

  <?code-excerpt "lib/robust_ports_example/step_6_close_ports.dart (parse-json)"?>
  ```dart
  Future<Object?> parseJson(String message) async {
    if (_closed) throw StateError('Closed'); // New
    final completer = Completer<Object?>.sync();
    final id = _idCounter++;
    _activeRequests[id] = completer;
    _commands.send((id, message));
    return await completer.future;
  }
  ```

#### 전체 예시 {:#complete-example-1}

<details>
  <summary>전체 예를 보려면 여기를 확장하세요</summary>

<?code-excerpt "lib/robust_ports_example/complete.dart"?>
```dart
import 'dart:async';
import 'dart:convert';
import 'dart:isolate';

void main() async {
  final worker = await Worker.spawn();
  print(await worker.parseJson('{"key":"value"}'));
  print(await worker.parseJson('"banana"'));
  print(await worker.parseJson('[true, false, null, 1, "string"]'));
  print(
      await Future.wait([worker.parseJson('"yes"'), worker.parseJson('"no"')]));
  worker.close();
}

class Worker {
  final SendPort _commands;
  final ReceivePort _responses;
  final Map<int, Completer<Object?>> _activeRequests = {};
  int _idCounter = 0;
  bool _closed = false;

  Future<Object?> parseJson(String message) async {
    if (_closed) throw StateError('Closed');
    final completer = Completer<Object?>.sync();
    final id = _idCounter++;
    _activeRequests[id] = completer;
    _commands.send((id, message));
    return await completer.future;
  }

  static Future<Worker> spawn() async {
    // 수신 포트를 생성하고 초기 메시지 처리기를 추가합니다.
    final initPort = RawReceivePort();
    final connection = Completer<(ReceivePort, SendPort)>.sync();
    initPort.handler = (initialMessage) {
      final commandPort = initialMessage as SendPort;
      connection.complete((
        ReceivePort.fromRawReceivePort(initPort),
        commandPort,
      ));
    };

    // isolate를 생성합니다.
    try {
      await Isolate.spawn(_startRemoteIsolate, (initPort.sendPort));
    } on Object {
      initPort.close();
      rethrow;
    }

    final (ReceivePort receivePort, SendPort sendPort) =
        await connection.future;

    return Worker._(receivePort, sendPort);
  }

  Worker._(this._responses, this._commands) {
    _responses.listen(_handleResponsesFromIsolate);
  }

  void _handleResponsesFromIsolate(dynamic message) {
    final (int id, Object? response) = message as (int, Object?);
    final completer = _activeRequests.remove(id)!;

    if (response is RemoteError) {
      completer.completeError(response);
    } else {
      completer.complete(response);
    }

    if (_closed && _activeRequests.isEmpty) _responses.close();
  }

  static void _handleCommandsToIsolate(
    ReceivePort receivePort,
    SendPort sendPort,
  ) {
    receivePort.listen((message) {
      if (message == 'shutdown') {
        receivePort.close();
        return;
      }
      final (int id, String jsonText) = message as (int, String);
      try {
        final jsonData = jsonDecode(jsonText);
        sendPort.send((id, jsonData));
      } catch (e) {
        sendPort.send((id, RemoteError(e.toString(), '')));
      }
    });
  }

  static void _startRemoteIsolate(SendPort sendPort) {
    final receivePort = ReceivePort();
    sendPort.send(receivePort.sendPort);
    _handleCommandsToIsolate(receivePort, sendPort);
  }

  void close() {
    if (!_closed) {
      _closed = true;
      _commands.send('shutdown');
      if (_activeRequests.isEmpty) _responses.close();
      print('--- port closed --- ');
    }
  }
}
```

</details>

[`Isolate.exit()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/exit.html
[`Isolate.spawn()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/spawn.html
[`ReceivePort`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/ReceivePort-class.html
[`SendPort`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/SendPort-class.html
[`SendPort.send()` method]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/SendPort/send.html
[main isolate]: /language/concurrency#isolates
[`Stream`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html
[`BroadcastStream`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/BroadcastStream-class.html
[`Completer`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Completer-class.html
[`RawReceivePort`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/RawReceivePort-class.html
[record]: /language/records
[previous example]: #basic-ports-example
[`try`/`catch` block]: /language/error-handling#catch
[`RemoteError`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/RemoteError-class.html
