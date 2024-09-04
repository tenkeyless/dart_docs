---
# title: Concurrency in Dart
title: Dart에서의 동시성
# description: >-
#   Use isolates to enable parallel code execution on multiple processor cores.
description: >-
  여러 프로세서 코어에서 병렬 코드 실행을 가능하게 하려면, isolates를 사용하세요.
short-title: Concurrency
lastVerified: 2023-12-14
prevpage:
  url: /language/modifier-reference
  # title: Class modifiers reference
  title: 클래스 수정자 참조
nextpage:
  url: /language/async
  title: 비동기
---

<?code-excerpt path-base="concurrency"?>

<style>
  article img {
    padding: 15px 0;
  }
</style>

이 페이지에는 Dart에서 동시성 프로그래밍이 작동하는 방식에 대한 개념적 개요가 포함되어 있습니다. 
이벤트 루프, 비동기 언어 기능 및 높은 레벨에서 isolates를 설명합니다. 
Dart에서 동시성을 사용하는 더 실용적인 코드 예제는 [비동기 지원](/language/async) 페이지와 
[Isolates](/language/isolates) 페이지를 읽어보세요.

Dart의 동시성 프로그래밍은 (`Future` 및 `Stream`과 같은) 비동기 API와, 
프로세스를 별도의 코어로 이동할 수 있는 *isolates*를 모두 말합니다.

모든 Dart 코드는 기본 메인 isolates에서 시작하여, 
선택적으로 명시적으로 만든 후속 isolates로 확장되는, isolates에서 실행됩니다. 
새 isolate을 생성(spawn)하면, 격리된(isolated) 메모리와 이벤트 루프가 있습니다. 
이벤트 루프는 Dart에서 비동기 및 동시성 프로그래밍을 가능하게 하는 것입니다.

## 이벤트 루프 {:#event-loop}

Dart의 런타임 모델은 이벤트 루프를 기반으로 합니다. 
이벤트 루프는 프로그램 코드 실행, 이벤트 수집 및 처리 등을 담당합니다.

애플리케이션이 실행되면, 모든 이벤트가 *이벤트 큐*라고 하는 큐에 추가됩니다. 
이벤트는 UI를 다시 그리는 요청부터, 사용자 탭 및 키 입력, 디스크에서의 I/O까지 다양합니다. 
애플리케이션은 이벤트가 발생하는 순서를 예측할 수 없으므로, 
이벤트 루프는 큐에 추가된 순서대로 이벤트를 한 번에 하나씩 처리합니다.

![A figure showing events being fed, one by one, into the event loop](/assets/img/language/concurrency/event-loop.png)

이벤트 루프가 작동하는 방식은 다음 코드와 유사합니다.

```dart
while (eventQueue.waitForEvent()) {
  eventQueue.processNextEvent();
}
```

이 예제 이벤트 루프는 동기식이며, 단일 스레드에서 실행됩니다. 
그러나, 대부분의 Dart 애플리케이션은 한 번에 여러 작업을 수행해야 합니다. 
예를 들어, 클라이언트 애플리케이션은 HTTP 요청을 실행하면서, 사용자가 버튼을 탭하는 것을 수신해야 할 수 있습니다. 
이를 처리하기 위해, 
Dart는 [Futures, Streams, async-await](/language/async)와 같은 많은 비동기 API를 제공합니다. 
이러한 API는 이 이벤트 루프를 중심으로 구축되었습니다.

예를 들어, 네트워크 요청을 하는 것을 고려합니다.

```dart
http.get('https://example.com').then((response) {
  if (response.statusCode == 200) {
    print('Success!')'
  }  
}
```

이 코드가 이벤트 루프에 도달하면, 
첫 번째 절인 `http.get`을 즉시 호출하고 `Future`를 반환합니다. 
또한, 이벤트 루프에 `then()` 절에서, HTTP 요청이 해결될 때까지, 콜백을 유지하라고 지시합니다. 
해결되면, 해당 콜백을 실행하여, 요청의 결과를 인수로 전달해야 합니다.

![Figure showing async events being added to an event loop and holding onto a callback to execute later .](/assets/img/language/concurrency/async-event-loop.png)

이 동일한 모델은 일반적으로 Dart의 모든 비동기 이벤트(예: [`Stream`][] 객체)를 이벤트 루프가 처리하는 방식입니다.

[`Stream`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Stream-class.html

## 비동기 프로그래밍 {:#asynchronous-programming}

이 섹션에서는 Dart에서 비동기 프로그래밍의 다양한 타입과 구문을 요약합니다. 
이미 `Future`, `Stream`, async-await에 익숙하다면, 
[isolates 섹션][isolates section]으로 건너뛸 수 있습니다.

[isolates section]: #isolates

### Futures {:#futures}

`Future`는 결국 값이나 오류로 완료되는 비동기 작업의 결과를 나타냅니다.

이 샘플 코드에서, `Future<String>`의 반환 타입은 결국 `String` 값(또는 오류)을 제공하겠다는 약속을 나타냅니다.

<?code-excerpt "lib/future_syntax.dart (read-async)"?>
```dart
Future<String> _readFileAsync(String filename) {
  final file = File(filename);

  // .readAsString()은 Future를 반환합니다.
  // .then()은 `readAsString`이 해결될 때, 실행될 콜백을 등록합니다.
  return file.readAsString().then((contents) {
    return contents.trim();
  });
}
```

### async-await 문법 {:#the-async-await-syntax}

`async` 및 `await` 키워드는 비동기 함수를 정의하고, 그 결과를 사용하는 선언적 방법을 제공합니다.

다음은 파일 I/O를 기다리는 동안, 차단(blocks)되는 일부 동기 코드의 예입니다.

<?code-excerpt "lib/sync_number_of_keys.dart (blocking)"?>
```dart
const String filename = 'with_keys.json';

void main() {
  // 데이터를 읽습니다.
  final fileData = _readFileSync();
  final jsonData = jsonDecode(fileData);

  // 해당 데이터를 활용합니다.
  print('Number of JSON keys: ${jsonData.length}');
}

String _readFileSync() {
  final file = File(filename);
  final contents = file.readAsStringSync();
  return contents.trim();
}
```

비슷한 코드지만, 비동기적으로 만들기 위해 변경한 부분(강조 표시)이 있습니다.

<?code-excerpt "lib/async_number_of_keys.dart (non-blocking)" replace="/async|await|readAsString\(\)/[!$&!]/g; /Future<\w+\W/[!$&!]/g;"?>
```dart
const String filename = 'with_keys.json';

void main() [!async!] {
  // 데이터를 읽습니다.
  final fileData = [!await!] _readFileAsync();
  final jsonData = jsonDecode(fileData);

  // 해당 데이터를 활용합니다.
  print('Number of JSON keys: ${jsonData.length}');
}

[!Future<String>!] _readFileAsync() [!async!] {
  final file = File(filename);
  final contents = [!await!] file.[!readAsString()!];
  return contents.trim();
}
```

`main()` 함수는 `_readFileAsync()` 앞에 `await` 키워드를 사용하여, 
네이티브 코드(파일 I/O)가 실행되는 동안, 
다른 Dart 코드(예: 이벤트 핸들러)가 CPU를 사용하도록 합니다. 
`await`를 사용하면, `_readFileAsync()`에서 반환된 `Future<String>`를, 
`String`으로 변환하는 효과도 있습니다. 
결과적으로, `contents` 변수는 암묵적 타입 `String`을 갖습니다.

:::note
`await` 키워드는 함수 본문 앞에 `async`가 있는 함수에서만 작동합니다.
:::

다음 그림에서 보듯이, 
Dart 코드는 `readAsString()`가 Dart 런타임이나 운영 체제에서, 
Dart가 아닌 코드를 실행하는 동안 일시 중지됩니다. 
`readAsString()`가 값을 반환하면, Dart 코드 실행이 재개됩니다.

![Flowchart-like figure showing app code executing from start to exit, waiting for native I/O in between](/assets/img/language/concurrency/basics-await.png)

### Streams {:#streams}

Dart는 스트림 형태의 비동기 코드도 지원합니다. 
스트림은 미래(future)에 값을 제공하고 시간이 지남에 따라 반복적으로 제공합니다. 
시간이 지남에 따라 일련의 `int` 값을 제공하겠다는 약속(promise)은 `Stream<int>` 타입을 갖습니다.

다음 예에서, `Stream.periodic`으로 생성된 스트림은 1초마다 새로운 `int` 값을 반복적으로 방출(emits)합니다.

<?code-excerpt "lib/stream_syntax.dart"?>
```dart
Stream<int> stream = Stream.periodic(const Duration(seconds: 1), (i) => i * i);
```

#### await-for 및 yield {:#await-for-and-yield}

Await-for는 새로운 값이 제공될 때마다 루프의 각 후속 반복을 실행하는 for 루프의 한 타입입니다. 
즉, 스트림을 "루프 오버"하는 데 사용됩니다. 
이 예에서, 인수로 제공된 스트림에서 새 값이 방출되면, 함수 `sumStream`에서 새 값이 방출됩니다. 
값 스트림을 반환하는 함수에서는 `return` 대신 `yield` 키워드가 사용됩니다.

<?code-excerpt "lib/await_for_syntax.dart"?>
```dart
Stream<int> sumStream(Stream<int> stream) async* {
  var sum = 0;
  await for (final value in stream) {
    yield sum += value;
  }
}
```

`async`, `await`, `Stream` 및 `Future`의 사용에 대해 자세히 알아보려면, 
[비동기 프로그래밍 튜토리얼][asynchronous programming tutorial]을 확인하세요.

[asynchronous programming tutorial]: /libraries/async/async-await

## Isolates {:#isolates}

Dart는 [비동기 API](#asynchronous-programming) 외에도 isolates를 통해 동시성을 지원합니다. 
대부분의 최신 기기에는 멀티코어 CPU가 있습니다. 
개발자는 여러 코어를 활용하기 위해, 때때로 동시에 실행되는 공유 메모리 스레드를 사용합니다. 
그러나, 공유 상태 동시성은 [오류가 발생하기 쉬우며(error prone)](https://en.wikipedia.org/wiki/Race_condition#In_software), 복잡한 코드로 이어질 수 있습니다.

스레드 대신, 모든 Dart 코드는 isolates 내부에서 실행됩니다. 
isolates를 사용하면, Dart 코드는 사용 가능한 경우 추가 프로세서 코어를 사용하여, 
여러 독립적인 작업을 한 번에 수행할 수 있습니다. 
isolates는 스레드나 프로세스와 비슷하지만, 
각 isolates에는 고유한 메모리와 이벤트 루프를 실행하는 단일 스레드가 있습니다.

각 isolate에는 고유한 글로벌 필드가 있어, 
isolate의 어떤 상태도 다른 isolate에서 액세스할 수 없습니다. 
isolate는 메시지 전달을 통해서만 서로 통신할 수 있습니다. 
isolates 간에 공유 상태가 없다는 것은, 
Dart에서 [mutexes나 locks](https://en.wikipedia.org/wiki/Lock_(computer_science)) 및 [데이터 경쟁(data races)](https://en.wikipedia.org/wiki/Race_condition#Data_race)과 같은, 
동시성 복잡성이 발생하지 않는다는 것을 의미합니다. 
그러나, isolates는 경쟁 조건(race conditions)을 완전히 방지하지 못합니다. 
이 동시성 모델에 대한 자세한 내용은 [Actor 모델](https://en.wikipedia.org/wiki/Actor_model)을 읽어보세요.

:::note Platform note
[Dart Native 플랫폼][Dart Native platform]만 isolates를 구현합니다. 
Dart Web 플랫폼에 대해 자세히 알아보려면, [웹에서의 동시성](#concurrency-on-the-web) 섹션을 참조하세요.
:::

[Dart Native platform]: /overview#platform

### 메인 isolate {:#the-main-isolate}

대부분의 경우, isolates에 대해 전혀 생각할 필요가 없습니다. 
Dart 프로그램은 기본적으로 메인 isolates에서 실행됩니다. 
다음 그림에서 볼 수 있듯이, 프로그램이 실행(run)되고 실행(execute)되는 스레드입니다.

![A figure showing a main isolate, which runs `main()`, responds to events, and then exits](/assets/img/language/concurrency/basics-main-isolate.png)

단일 isolate 프로그램도 원활하게 실행할 수 있습니다. 
다음 코드 줄로 넘어가기 전에, 
이러한 앱은 [async-await][]를 사용하여 비동기 작업이 완료될 때까지 기다립니다. 
잘 동작하는 앱은 빠르게 시작되어, 가능한 한 빨리 이벤트 루프에 도달합니다. 
그런 다음, 앱은 필요에 따라 비동기 작업을 사용하여, 대기 중인 각 이벤트에 즉시 응답합니다.

[async-await]: /libraries/async/async-await

### isolate 수명주기 {:#the-isolate-life-cycle}

다음 그림에서 보듯이, 모든 isolate는 (`main()` 함수와 같은) Dart 코드를 실행하여 시작합니다. 
이 Dart 코드는 일부 이벤트 리스너를 등록할 수 있습니다. 
예를 들어, 사용자 입력이나 파일 I/O에 응답하기 위해 등록할 수 있습니다. 
isolate의 초기 함수가 반환되면, 
isolate는 이벤트를 처리해야 하는 경우 남아 있습니다. 
이벤트를 처리한 후, isolate는 종료됩니다.

![A more general figure showing that any isolate runs some code, optionally responds to events, and then exits](/assets/img/language/concurrency/basics-isolate.png)

### 이벤트 처리 {:#event-handling}

클라이언트 앱에서, 메인 isolate의 이벤트 큐에는 다시 그리기 요청과, 
탭 및 기타 UI 이벤트 알림이 포함될 수 있습니다. 
예를 들어, 다음 그림은 다시 그리기 이벤트, 탭 이벤트, 두 개의 다시 그리기 이벤트를 보여줍니다. 
이벤트 루프는 큐에서 선입선출(first in, first out) 순서로 이벤트를 가져옵니다.

![A figure showing events being fed, one by one, into the event loop](/assets/img/language/concurrency/event-loop.png)

이벤트 처리가 `main()`이 종료된 후, 메인 isolate에서 발생합니다. 
다음 그림에서, `main()`이 종료된 후, 메인 isolate가 첫 번째 다시 그리기 이벤트를 처리합니다. 
그 후, 메인 isolate가 탭 이벤트를 처리한 다음, 다시 그리기 이벤트를 처리합니다.

동기 작업에 처리 시간이 너무 오래 걸리면, 앱이 응답하지 않을 수 있습니다. 
다음 그림에서, 탭 처리 코드가 너무 오래 걸리므로, 후속 이벤트가 너무 늦게 처리됩니다. 
앱이 정지된 것처럼 보일 수 있으며, 수행하는 애니메이션이 끊길 수 있습니다.

![A figure showing a tap handler with a too-long execution time](/assets/img/language/concurrency/event-jank.png)

클라이언트 앱에서, 너무 긴 동기 작업의 결과는 종종 [Janky(부드럽지 않은) UI 애니메이션][Jank]입니다. 
더 나쁜 것은 UI가 완전히 반응하지 않을 수 있습니다.

[jank]: {{site.flutter-docs}}/perf/rendering-performance

### 백그라운드 워커 {:#background-workers}

앱의 UI가 시간 소모적인 계산(예: [대용량 JSON 파일 구문 분석][json])으로 인해 응답하지 않는 경우, 
해당 계산을 종종 _백그라운드 워커_ 라고 하는 워커 isolate에 오프로드하는 것을 고려하세요. 
다음 그림에 표시된 일반적인 사례는, 계산을 수행한 다음 종료되는 간단한 워커 isolate를 생성하는 것입니다. 
워커 isolate는 종료될 때 메시지로 결과를 반환합니다.

[json]: {{site.flutter-docs}}/cookbook/networking/background-parsing

![A figure showing a main isolate and a simple worker isolate](/assets/img/language/concurrency/isolate-bg-worker.png)

워커 isolate는 I/O(예: 파일 읽기 및 쓰기)를 수행하고, 타이머를 설정하는 등의 작업을 수행할 수 있습니다. 
자체 메모리가 있으며 메인 isolate와 상태를 공유하지 않습니다. 
워커 isolate는 다른 isolate에 영향을 미치지 않고, 차단할 수 있습니다.

### isolates 사용 {:#using-isolates}

Dart에서 isolates를 사용하는 방법은 사용 사례에 따라 두 가지가 있습니다.

* [`Isolate.run()`][]을 사용하여, 별도의 스레드에서 단일 계산을 수행합니다.
* [`Isolate.spawn()`][]을 사용하여, 
  시간이 지남에 따라 여러 메시지를 처리하는 isolate 또는 백그라운드 워커를 만듭니다. 
  장기 isolate 작업에 대한 자세한 내용은 [Isolates](/language/isolates) 페이지를 참조하세요.

대부분의 경우, `Isolate.run`은 백그라운드에서 프로세스를 실행하는 데 권장되는 API입니다.

#### `Isolate.run()` {:#isolate-run}

정적 `Isolate.run()` 메서드에는 하나의 인수가 필요합니다. 
새로 생성된 isolate에서 실행될 콜백입니다.

<?code-excerpt "lib/isolate_run_syntax.dart (slow)"?>
```dart
int slowFib(int n) => n <= 1 ? 1 : slowFib(n - 1) + slowFib(n - 2);

// 현재 isolate을 차단(blocking)하지 않고, 계산합니다.
void fib40() async {
  var result = await Isolate.run(() => slowFib(40));
  print('Fib(40) = $result');
}
```

### 성능 및 isolate 그룹 {:#performance-and-isolate-groups}

isolate가 [`Isolate.spawn()`][]을 호출하면, 
두 isolates는 동일한 실행 코드를 갖고 동일한 _isolate 그룹(isolate group)_ 에 있습니다. 
isolate 그룹은 코드 공유와 같은 성능 최적화를 가능하게 합니다. 
새로운 isolate는 isolate 그룹이 소유한 코드를 즉시 실행합니다. 
또한, `Isolate.exit()`는 isolate가 동일한 isolate 그룹에 있을 때만 작동합니다.

일부 특별한 경우에는, 지정된 URI에 있는 코드 사본으로 새 isolate를 설정하는, 
[`Isolate.spawnUri()`][]를 사용해야 할 수도 있습니다. 
그러나, `spawnUri()`는 `spawn()`보다 훨씬 느리고, 새 isolate는 스포너(spawner)의 isolate 그룹에 없습니다. 
또 다른 성능 결과는 isolate 개체가 다른 그룹에 있을 때 메시지 전달이 더 느리다는 것입니다.

[`Isolate.spawnUri()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/spawnUri.html

### isolates의 한계 {:#limitations-of-isolates}

#### Isolates는 스레드가 아닙니다. {:#isolates-arent-threads}

멀티스레딩이 있는 언어에서 Dart로 넘어왔다면, 
isolates가 스레드처럼 동작할 것이라고 기대하는 것이 합리적일 것입니다. 
하지만 그렇지 않습니다. 
각 isolate는 고유한 상태를 가지므로, isolate의 상태에 다른 isolate가 액세스할 수 없습니다. 
따라서, isolate는 자체 메모리에 대한 액세스로 제한됩니다.

예를 들어, 전역 가변(mutable) 변수가 있는 애플리케이션이 있는 경우, 
해당 변수는 생성된(spawned) isolate에서 별도의 변수가 됩니다. 
생성된 isolate에서 해당 변수를 변형(mutate)하면, 메인 isolate에서 그대로 유지(untouched)됩니다. 
이것이 isolate가 작동하도록 의도된 방식이며, isolate를 사용할 때 명심해야 할 중요한 사항입니다.

#### 메시지 타입 {:#message-types}

[`SendPort`][]를 통해 전송된 메시지는 거의 모든 타입의 Dart 객체가 될 수 있지만, 몇 가지 예외가 있습니다.

- ([`Socket`][]과 같은) 네이티브 리소스가 있는 객체.
- [`ReceivePort`][]
- [`DynamicLibrary`][]
- [`Finalizable`][]
- [`Finalizer`][]
- [`NativeFinalizer`][]
- [`Pointer`][]
- [`UserTag`][]
- `@pragma('vm:isolate-unsendable')`로 표시된 클래스의 인스턴스

이러한 예외를 제외하고, 모든 객체를 전송할 수 있습니다. 
자세한 내용은 [`SendPort.send`][] 문서를 확인하세요.

`Isolate.spawn()` 및 `Isolate.exit()`는 `SendPort` 객체를 추상화하므로, 동일한 제한이 적용됩니다.

[`SendPort.send`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/SendPort/send.html
[`Socket`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/Socket-class.html
[`DynamicLibrary`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-ffi/DynamicLibrary-class.html
[`Finalizable`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-ffi/Finalizable-class.html
[`Finalizer`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Finalizer-class.html
[`NativeFinalizer`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-ffi/NativeFinalizer-class.html
[`Pointer`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-ffi/Pointer-class.html
[`UserTag`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-developer/UserTag-class.html

<a id="web"></a>
## 웹에서의 동시성 {:#concurrency-on-the-web}

모든 Dart 앱은 비차단(non-blocking), 인터리브(interleaved) 계산을 위해, 
`async-await`, `Future` 및 `Stream`을 사용할 수 있습니다. 
그러나 [Dart 웹 플랫폼][Dart web platform]은 isolates를 지원하지 않습니다. 
Dart 웹 앱은 [웹 워커][web workers]를 사용하여, 
isolates와 유사한 백그라운드 스레드에서 스크립트를 실행할 수 있습니다. 
그러나, 웹 워커의 기능과 성능은 isolates와 다소 다릅니다.

예를 들어, 웹 워커가 스레드 간에 데이터를 보낼 때, 데이터를 앞뒤로 복사합니다. 
그러나 데이터 복사는 매우 느릴 수 있으며, 특히 대용량 메시지의 경우 그렇습니다. 
isolates는 동일한 작업을 수행하지만, 
대신 메시지를 보관하는 메모리를 보다 효율적으로 _전송_ 할 수 있는 API도 제공합니다.

웹 워커와 isolates를 만드는 것도 다릅니다. 
별도의 프로그램 진입점을 선언하고 별도로 컴파일해야만 웹 워커를 만들 수 있습니다. 
웹 워커를 시작하는 것은 `Isolate.spawnUri`를 사용하여 isolate를 시작하는 것과 비슷합니다. 
`Isolate.spawn`으로 isolate를 시작할 수도 있는데, 
이는 [spawning isolate과 동일한 코드와 데이터 중 일부를 재사용](#performance-and-isolate-groups)하기 때문에 리소스가 덜 필요합니다. 
웹 워커는 동등한 API가 없습니다.

[Dart web platform]: /overview#platform
[web workers]: https://developer.mozilla.org/docs/Web/API/Web_Workers_API/Using_web_workers


## 추가 자료 {:#additional-resources}

- 여러 개의 isolates를 사용하는 경우, 
  Flutter의 [`IsolateNameServer`][] 또는 
  Flutter가 아닌 Dart 애플리케이션에 유사한 기능을 제공하는 [`package:isolate_name_server`][]를 고려하세요.
- Dart의 isolates가 기반을 둔, [Actor 모델][Actor model]에 대해 자세히 알아보세요.
- `Isolate` API에 대한 추가 문서:
    - [`Isolate.exit()`][]
    - [`Isolate.spawn()`][]
    - [`ReceivePort`][]
    - [`SendPort`][]

[`IsolateNameServer`]: {{site.flutter-api}}/flutter/dart-ui/IsolateNameServer-class.html
[`package:isolate_name_server`]: {{site.pub-pkg}}/isolate_name_server
[Actor model]: https://en.wikipedia.org/wiki/Actor_model
[`Isolate.run()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/run.html
[`Isolate.exit()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/exit.html
[`Isolate.spawn()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/Isolate/spawn.html
[`ReceivePort`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/ReceivePort-class.html
[`SendPort`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/SendPort-class.html
