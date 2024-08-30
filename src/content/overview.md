---
# title: Dart overview
title: Dart 개요
# description: A short introduction to Dart.
description: Dart에 대한 간략한 소개.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
---

<img 
  style="padding: 30px; float: right; width: 300px" 
  src="/assets/img/logo_lockup_dart_horizontal.png" 
  alt="Dart product logo">

Dart는 모든 플랫폼에서 빠른 앱을 개발하기 위한 클라이언트 최적화 언어입니다. 
이 언어의 목표는 앱 프레임워크를 위한 [유연한 실행 런타임 플랫폼](#platform)과 함께, 
다중 플랫폼 개발을 위한 가장 생산적인 프로그래밍 언어를 제공하는 것입니다.

언어는 _기술적 범위(technical envelope)_ 로 정의됩니다. 
이는 개발 중에 언어의 기능과 강점을 형성하는 선택입니다. 
Dart는 클라이언트 개발에 특히 적합한 기술적 범위에 맞게 설계되어, 
다양한 컴파일 대상(웹, 모바일, 데스크톱)에서 개발(1초 미만의 상태 저장 핫 리로드)과 
고품질 프로덕션 경험을 모두 우선시합니다.

Dart는 또한 [Flutter]({{site.flutter}})의 기반을 형성합니다. 
Dart는 Flutter 앱을 구동하는 언어와 런타임을 제공하지만, 
코드 포맷, 분석, 테스트와 같은 많은 핵심 개발자 작업도 지원합니다.

## Dart: 언어 {:#language}

Dart 언어는 타입 세이프입니다. 정적 타입 검사를 사용하여, 변수 값이 _항상_ 변수의 정적 타입과 일치하는지 확인합니다. 이를 사운드 타이핑(sound typing)이라고도 합니다. 
타입은 필수이지만, 타입 주석은 타입 추론 덕분에 선택 사항입니다. 
Dart 타이핑 시스템도 유연하여, `dynamic` 타입과 런타임 검사를 결합하여 사용할 수 있으며, 
이는 실험 중이나 특히 동적으로 동작해야 하는 코드에 유용할 수 있습니다.

Dart에는 내장된 [사운드 널 세이프티(sound null safety)](/null-safety)가 있습니다. 
즉, 사용자가 허용한다고 명시하지 않는 한, 값은 null이 될 수 없습니다. 
사운드 null 세이프티로, Dart는 정적 코드 분석을 통해, 런타임에 null 예외로부터 사용자를 보호할 수 있습니다. 
다른 많은 null 세이프 언어와 달리, Dart가 변수가 null이 아니라고 판단하면, 
해당 변수는 결코 null이 될 수 없습니다. 
디버거에서 실행 중인 코드를 검사하면, 
런타임에 null이 될 수 없음이 유지되므로, _사운드_ null 세이프티가 있습니다.

다음 코드 샘플은 라이브러리, 비동기 호출, null 가능 및 null 불가능 타입, 화살표 구문, 
생성기(generators), 스트림 및 getters를 포함한 여러 Dart 언어 기능을 보여줍니다. 
언어에 대해 자세히 알아보려면, [Dart 언어 투어](/language)를 확인하세요.

<?code-excerpt "misc/lib/overview_pi.dart"?>
```dartpad
import 'dart:math' show Random;

void main() async {
  print('Compute π using the Monte Carlo method.');
  await for (final estimate in computePi().take(100)) {
    print('π ≅ $estimate');
  }
}

/// π에 대한 점점 더 정확한 추정치 스트림을 생성합니다.
Stream<double> computePi({int batch = 100000}) async* {
  var total = 0; // Inferred to be of type int
  var count = 0;
  while (true) {
    final points = generateRandom().take(batch);
    final inside = points.where((p) => p.isInsideUnitCircle);

    total += batch;
    count += inside.length;
    final ratio = count / total;

    // 원의 면적은 A = π⋅r²이므로 π = A/r²입니다. 
    // 따라서, x ∈ <0,1>, y ∈ <0,1>인 임의의 점이 주어졌을 때, 
    // 단위 원 안에 있는 점들의 비율은 π / 4에 가까워야 합니다. 
    // 따라서 π의 값은 다음과 같아야 합니다.
    yield ratio * 4;
  }
}

Iterable<Point> generateRandom([int? seed]) sync* {
  final random = Random(seed);
  while (true) {
    yield Point(random.nextDouble(), random.nextDouble());
  }
}

class Point {
  final double x;
  final double y;

  const Point(this.x, this.y);

  bool get isInsideUnitCircle => x * x + y * y <= 1;
}
```

:::note
이 예제는 내장된 [DartPad](/tools/dartpad)에서 실행됩니다. 
또한, <a href="{{site.dartpad}}/?id=bc63d212c3252e44058ff76f34ef5730" target="_blank" rel="noopener">이 예제를 자체 창에서 열 수 있습니다</a>.
:::


## Dart: 라이브러리 {:#libraries}

Dart에는 [풍부한 코어 라이브러리 세트](/libraries)가 있어, 
많은 일상적인 프로그래밍 작업에 필수적인 기능을 제공합니다.

* 모든 Dart 프로그램에 대한 빌트인 타입, 컬렉션 및 기타 코어 함수 
  (`dart:core`)
* 큐, 링크드 리스트, 해시맵 및 이진 트리와 같은 더 풍부한 컬렉션 타입 
  (`dart:collection`)
* JSON 및 UTF-8을 포함하여, 다양한 데이터 표현 간 변환을 위한 인코더 및 디코더 
  (`dart:convert`)
* 수학 상수 및 함수, 난수 생성 
  (`dart:math`)
* `Future` 및 `Stream`과 같은 클래스를 사용한 비동기 프로그래밍 지원 
  (`dart:async`)
* 고정 크기 데이터(예: 부호 없는 8바이트 정수)와 SIMD 숫자 타입을 효율적으로 처리하는 리스트 
  (`dart:typed_data`)
* 웹이 아닌 애플리케이션을 위한 파일, 소켓, HTTP 및 기타 I/O 지원
  (`dart:io`)
* C 스타일 인터페이스를 제공하는 다른 코드와의 상호 운용성을 위한 외부 함수 인터페이스(Foreign function interfaces)
  (`dart:ffi`)
* 스레드와 유사하지만 메모리를 공유하지 않고, 메시지를 통해서만 통신하는, 
  독립적인 작업자인 _isolates_ 를 사용한 동시성 프로그래밍
  (`dart:isolate`)
* 브라우저 및 문서 개체 모델(DOM)과 상호 작용해야 하는 웹 기반 애플리케이션을 위한 HTML 요소 및 기타 리소스
  (`dart:html`)

코어 라이브러리 외에도, 많은 API가 포괄적인 패키지 세트를 통해 제공됩니다. 
Dart 팀은 다음과 같은 많은 유용한 보충 패키지를 게시합니다.

* [characters]({{site.pub-pkg}}/characters)
* [intl]({{site.pub-pkg}}/intl)
* [http]({{site.pub-pkg}}/http)
* [crypto]({{site.pub-pkg}}/crypto)
* [markdown]({{site.pub-pkg}}/markdown)

또한, 타사 게시자와 더 광범위한 커뮤니티는, 다음과 같은 기능을 지원하는, 수천 개의 패키지를 게시합니다.

* [XML]({{site.pub-pkg}}/xml) 
* [Windows 통합]({{site.pub-pkg}}/win32)
* [SQLite]({{site.pub-pkg}}/sqflite_common)
* [압축]({{site.pub-pkg}}/archive)

Dart 코어 라이브러리를 특징으로 하는 일련의 작업 예제를 보려면, [코어 라이브러리 문서](/libraries)를 읽어보세요. 
추가 API를 찾으려면, [일반적으로 사용되는 패키지 페이지](/resources/useful-packages)를 확인하세요.

## Dart: 플랫폼 {:#platform}

Dart의 컴파일러 기술을 사용하면, 다양한 방식으로 코드를 실행할 수 있습니다.

* **네이티브 플랫폼**: 모바일 및 데스크톱 기기를 대상으로 하는 앱의 경우, 
  Dart에는 JIT(Just-in-Time) 컴파일이 포함된 Dart VM과 
  머신 코드를 생성하기 위한 AOT(Ahead-of-Time) 컴파일러가 모두 포함되어 있습니다.

* **웹 플랫폼**: 웹을 대상으로 하는 앱의 경우, Dart는 개발 또는 프로덕션 목적으로 컴파일할 수 있습니다. 
  웹 컴파일러는 Dart를 JavaScript 또는 WebAssembly로 변환합니다.

<img 
  src="/assets/img/Dart-platforms.svg" 
  width="800" 
  alt="An illustration of the targets supported by Dart">

[Flutter 프레임워크]({{site.flutter}})는 Dart 플랫폼을 기반으로 하는 인기 있는 다중 플랫폼 UI 툴킷으로, 
iOS, Android, macOS, Windows, Linux 및 웹에서 실행되는 UI 환경을 구축하기 위한 도구와 UI 라이브러리를 제공합니다.

#### Dart 네이티브 (머신 코드 JIT 및 AOT) {:#native-platform}

개발 중에, 빠른 개발자 주기는 반복에 필수적입니다. 
Dart VM은 증가분 재컴파일(핫 리로드 사용 가능), 라이브 메트릭 수집([DevTools](/tools/dart-devtools) 지원) 및 풍부한 디버깅 지원이 포함된 적시(JIT, just-in-time) 컴파일러를 제공합니다.

(앱 스토어에 게시하든 프로덕션 백엔드에 배포하든) 앱을 프로덕션에 배포할 준비가 되면, 
Dart 사전(AOT, ahead-of-time) 컴파일러가 네이티브 ARM 또는 x64 머신 코드로 컴파일할 수 있습니다. 
AOT로 컴파일된 앱은 일관되고 짧은 시작 시간으로 실행됩니다.

AOT로 컴파일된 코드는 사운드 Dart 타입 시스템을 적용하고, 빠른 객체 할당 및 [세대별 가비지 수집기](https://medium.com/flutter-io/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30)를 사용하여, 
메모리를 관리하는 효율적인 Dart 런타임 내에서 실행됩니다.

자세한 정보:

* [시작하기: 명령줄 및 서버 앱](/tutorials/server/get-started)
* [JIT 또는 AOT 컴파일을 머신 코드로 실행하기 위한 `dart` 도구](/tools/dart-tool)
* [명령줄 앱 작성](/tutorials/server/cmdline)
* [HTTP 서버 작성](/tutorials/server/httpserver)

#### Dart 웹 (JavaScript dev & prod 및 WebAssembly) {:#web-platform}

Dart Web은 JavaScript로 구동되는 웹 플랫폼에서 Dart 코드를 실행할 수 있도록 합니다. 
Dart Web을 사용하면, Dart 코드를 JavaScript 코드로 컴파일하여, 브라우저에서 실행합니다. 
예를 들어, [Chrome](https://www.google.com/chrome/) 내의 [V8](https://v8.dev/)입니다. 
또는 Dart 코드를 WebAssembly로 컴파일할 수 있습니다.

Dart Web에는 세 가지 컴파일 모드가 있습니다.

* 빠른 개발자 주기를 가능하게 하는 증분(incremental) JavaScript 개발 컴파일러.
* Dart 코드를 빠르고 컴팩트하며 배포 가능한 JavaScript로 컴파일하는 최적화 JavaScript 프로덕션 컴파일러. 
  이러한 효율성은 데드 코드 제거와 같은 기술에서 비롯됩니다.
* Dart 코드를 초고속 배포 가능한 WebAssembly GC 코드로 컴파일하는 
  최적화 WebAssembly (WasmGC) 프로덕션 컴파일러.

자세한 정보:

* [Dart로 웹 앱 빌드](/web/get-started)
* [`dart compile js`](/tools/dart-compile#js)
* [`webdev` 도구](/tools/webdev)
* [웹 배포 팁](/web/deployment)
* [WebAssembly 컴파일](/web/wasm)

#### Dart 런타임 {:#runtime}

어떤 플랫폼을 사용하든 코드를 어떻게 컴파일하든, 코드를 실행하려면 Dart 런타임이 필요합니다. 
이 런타임은 다음과 같은 중요한 작업을 담당합니다.

* 메모리 관리:
  Dart는 관리되는 메모리 모델을 사용하며, 사용되지 않는 메모리는 가비지 콜렉터(GC)에서 회수합니다.

* Dart 타입 시스템 적용:
  Dart의 대부분 타입 검사는 정적(컴파일 타임)이지만, 일부 타입 검사는 동적(런타임)입니다. 
  예를 들어, Dart 런타임은 [타입 검사 및 캐스트 연산자](/language/operators#type-test-operators)를 통해,
  동적 검사를 적용합니다.

* [isolates](/language/concurrency) 관리:
  Dart 런타임은 기본 isolates(코드가 일반적으로 실행되는 곳)와 앱에서 만드는 다른 isolates를 제어합니다.

네이티브 플랫폼에서, Dart 런타임은 자체 포함 실행 파일에 자동으로 포함되며, 
[`dart run`](/tools/dart-run) 명령에서 제공하는 Dart VM의 일부입니다.

## Dart 배우기 {:#learning-dart}

Dart를 배우는 데는 여러 가지 선택 사항이 있습니다. 다음은 추천하는 몇 가지입니다.

* [브라우저에서 Dart를 탐색]({{site.dartpad}}/)
  * Dart 코드용 웹 기반 실행 환경인 DartPad 제공합니다.
* [Dart 언어 투어](/language)
  * 각 주요 Dart 기능을 사용하는 방법을 보여줍니다.
* [Dart 튜토리얼](/tutorials/server/cmdline)
  * 명령줄을 빌드하기 위해, Dart를 사용하는 기본 사항을 다룹니다.
* [Dart 전문가의 광범위한 온라인 교육][udemy]
* [Dart 핵심 라이브러리를 설명하는 API 문서를 탐색]({{site.dart-api}})
* [Dart 프로그래밍에 대한 책](/resources/books)

[udemy]: https://www.udemy.com/course/complete-dart-guide/?couponCode=NOV-20
