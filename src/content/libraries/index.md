---
# title: Dart's core libraries
title: Dart의 코어 라이브러리
# description: Learn about Dart's core libraries and APIs.
description: Dart의 코어 라이브러리와 API에 대해 알아보세요.
# short-title: Core libraries
short-title: Core 라이브러리
nextpage:
  url: /libraries/dart-core
  title: dart:core
---

<style>
  th:first-child {
    width: 80%;
  }
</style>

Dart에는 객체 컬렉션 작업(`dart:collection`), 계산(`dart:math`), 데이터 인코딩/디코딩(`dart:convert`)과 같은, 많은 일상적인 프로그래밍 작업에 필수적인 코어 라이브러리가 풍부하게 포함되어 있습니다. 
추가 API는 [일반적으로 사용되는 패키지](/resources/useful-packages)에서 사용할 수 있습니다.

## 라이브러리 투어 {:#library-tour}

다음 가이드에서는 Dart 코어 라이브러리의 주요 기능을 사용하는 방법을 다룹니다. 
이 가이드는 개요만 제공하며, 포괄적이지는 않습니다. 
라이브러리나 멤버에 대한 자세한 정보가 필요할 때마다, [Dart API 참조][Dart API]를 참조하세요.

[dart:core](/libraries/dart-core)
: 빌트인 타입, 컬렉션 및 기타 코어 기능.
  이 라이브러리는 모든 Dart 프로그램에 자동으로 import 됩니다.

[dart:async](/libraries/dart-async)
: Future 및 Stream과 같은 클래스를 사용하여, 비동기 프로그래밍을 지원합니다.

[dart:math](/libraries/dart-math)
: 수학 상수 및 함수와 난수 생성기.

[dart:convert](/libraries/dart-convert)
: JSON 및 UTF-8을 포함하여, 다양한 데이터 표현 간을 변환하기 위한 인코더 및 디코더.

[dart:io](/libraries/dart-io)
: Flutter 앱, 서버, 명령줄 스크립트를 포함하여, Dart VM을 사용할 수 있는 프로그램에 대한 I/O.

[dart:html](/libraries/dart-html)
: 브라우저 기반 앱을 위한 DOM 및 기타 API.
  이제 `dart:html` 대신 `package:web`을 사용하는 것이 좋습니다.

언급했듯이 이 페이지는 개요일 뿐입니다. 
몇 가지 dart:* 라이브러리만 다루고, 타사 라이브러리는 다루지 않습니다.

다양한 플랫폼에서 Dart가 지원하는 모든 라이브러리에 대한 개요는, 
아래의 [멀티 플랫폼 라이브러리](#multi-platform-libraries), 
[네이티브 플랫폼 라이브러리](#native-platform-libraries), 
[웹 플랫폼 라이브러리](#web-platform-libraries) 리스트를 확인하세요.

라이브러리 정보를 찾을 수 있는 다른 장소는, 
[pub.dev 사이트]({{site.pub}})와 [Dart 웹 개발자 라이브러리 가이드][webdev libraries]입니다. 
모든 dart:* 라이브러리에 대한 API 문서는, [Dart API 참조][Dart API]에서 찾을 수 있으며, 
Flutter를 사용하는 경우, [Flutter API 참조][api-flutter]에서 찾을 수 있습니다.

Dart 언어에 대해 자세히 알아보려면, [언어 문서 및 샘플](/language)을 확인하세요.

[Dart API]: {{site.dart-api}}/{{site.sdkInfo.channel}}
[webdev libraries]: /web/libraries
[api-flutter]: {{site.flutter-api}}

## 멀티 플랫폼 라이브러리 {:#multi-platform-libraries}

다음 표는 모든 [Dart 플랫폼](/overview#platform)에서 작동하는 Dart 코어 라이브러리를 나열합니다.

| 라이브러리                                       | 메모                         |
|-----------------------------------------------|-------------------------------|
| [`dart:core`][dart-core]<br>모든 Dart 프로그램에 빌트인 타입, 컬렉션 및 기타 코어 기능입니다. | |
| [`dart:async`][dart-async], [`package:async`][package-async]<br>`Future` 및 `Stream`과 같은 클래스를 사용하여, 비동기 프로그래밍을 지원합니다.<br>`package:async`는 `Future` 및 `Stream` 타입에 대한 추가 유틸리티를 제공합니다. | |
| [`dart:collection`][dart-collection], [`package:collection`][package-collection]<br>`dart:core`의 컬렉션 지원을 보완하는 클래스와 유틸리티입니다.<br>`package:collection`은 컬렉션을 다루기 위한 추가 컬렉션 구현과 함수를 제공합니다. | |
| [`dart:convert`][dart-convert], [`package:convert`][package-convert]<br>JSON 및 UTF-8을 포함하여, 서로 다른 데이터 표현 간 변환을 위한 인코더 및 디코더입니다.<br>`package:convert`는 추가 인코더 및 디코더를 제공합니다. ||
| [`dart:developer`][dart-developer]<br>디버거 및 검사기 같은 개발자 도구와의 상호작용.  | [네이티브 JIT][jit] 및 [개발용 JavaScript 컴파일러][development JavaScript compiler]만 해당됩니다. |
| [`dart:math`][dart-math]<br>수학 상수와 함수, 그리고 난수 생성기. | |
| [`dart:typed_data`][dart-typed_data], [`package:typed_data`][package-typed_data]<br>고정된 크기의 데이터(예: 부호 없는 8바이트 정수)와 SIMD 숫자 타입을 효율적으로 처리하는 리스트입니다.<br>`package:typed_data`는 타입화된 데이터를 처리하는 추가 클래스와 함수를 제공합니다. | |

{:.table .table-striped}

## 네이티브 플랫폼 라이브러리 {:#native-platform-libraries}

다음 표는 [Dart 네이티브 플랫폼](/overview#native-platform)에서 작동하는, 
Dart 코어 라이브러리를 나열합니다.(AOT 및 JIT로 컴파일된 코드)

| 라이브러리                                       | 메모                         |
|-----------------------------------------------|-------------------------------|
| [`dart:ffi`][dart-ffi], [`package:ffi`][package-ffi]<br>Dart 코드에서 네이티브 C API를 사용할 수 있게 해주는 외부 함수 인터페이스입니다.<br>`package:ffi`에는 Dart 문자열과 C 문자열을 변환하는 기능을 포함한 유틸리티가 포함되어 있습니다. | |
| [`dart:io`][dart-io], [`package:io`][package-io]<br>웹 애플리케이션이 아닌 경우, 파일, 소켓, HTTP 및 기타 I/O를 지원합니다.<br>`package:io`는 ANSI 색상, 파일 복사 및 표준 종료 코드 지원을 포함한 기능을 제공합니다. | |
| [`dart:isolate`][dart-isolate]<br>isolates를 사용한 동시 프로그래밍: 스레드와 유사한 독립적인 워커입니다. | |
| [`dart:mirrors`][dart-mirrors]<br>내성(introspection)과 동적 호출(dynamic invocation)을 지원하는 기본적인 반사(reflection)입니다. | 실험적<br>[네이티브 JIT][jit]만 해당(Flutter는 _아님_) |

{:.table .table-striped}

## 웹 플랫폼 라이브러리 {:#web-platform-libraries}

다음 표는 [Dart 웹 플랫폼](/overview#web-platform)에서 작동하는 Dart 코어 라이브러리를 나열합니다.(Javascript로 컴파일된 코드) 
최신 권장 도구는 **굵게** 표시되고, 레거시 도구는 *기울임체*로 표시됩니다.
(자세한 내용은 [Javascript 상호 운용성][Javascript interoperability]을 방문하세요)

| 라이브러리                                       | 메모                         |
|-----------------------------------------------|-------------------------------|
| [**`package:web`**][pkg-web] <br>JS 상호 운용성(interop)을 중심으로 빌드된 가벼운 브라우저 API 바인딩 | 모든 `dart:*` 웹 라이브러리를 대체합니다. [마이그레이션 가이드][html-web]을 읽어보세요. |
| [**`dart:js_interop`**][js-interop] <br>JavaScript 및 브라우저 API와의 상호 운용성 | `package:js`를 대체합니다. |
| [**`dart:js_interop_unsafe`**][js-interop-unsafe] <br>JavaScript 객체를 동적으로 조작하는 유틸리티 메서드. | `dart:js_util`를 대체합니다. |
| [*`dart:html`*][dart-html] *(legacy)* <br>웹 기반 애플리케이션을 위한 HTML 요소 및 기타 리소스.  | 대신 `package:web`을 사용하세요. |
| [*`dart:indexed_db`*][dart-indexed_db] *(legacy)* <br>인덱스를 지원하는 클라이언트 측 키-값 저장소입니다. | 대신 `package:web`을 사용하세요. |
| [*`dart:js`*][dart-js], [*`dart:js_util`*][dart-js_util], [*`package:js`*][package-js] *(legacy)* <br>JS 상호 운용성을 위한 낮은 레벨 primitives과 높은 레벨 어노테이션. | 대신 `dart:js_interop` 또는 `dart:js_interop_unsafe`를 사용하세요. |
| [*`dart:svg`*][dart-svg] *(legacy)* <br>확장 가능한 벡터 그래픽.  | 대신 `package:web`을 사용하세요. |
| [*`dart:web_audio`*][dart-web_audio] *(legacy)* <br>브라우저에서 고품질 오디오 프로그래밍을 즐겨보세요. | 대신 `package:web`을 사용하세요. |
| [*`dart:web_gl`*][dart-web_gl] *(legacy)* <br>브라우저에서의 3D 프로그래밍. | 대신 `package:web`을 사용하세요. |

{:.table .table-striped}


<!---
Multi-platform libraries
-->
[dart-core]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/dart-core-library.html
[dart-async]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/dart-async-library.html
[package-async]: {{site.pub-pkg}}/async
[dart-collection]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-collection/dart-collection-library.html
[package-collection]: {{site.pub-pkg}}/collection
[dart-convert]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-convert/dart-convert-library.html
[package-convert]: {{site.pub-pkg}}/convert
[dart-developer]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-developer/dart-developer-library.html
[dart-math]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-math/dart-math-library.html
[dart-typed_data]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-typed_data/dart-typed_data-library.html
[package-typed_data]: {{site.pub-pkg}}/typed_data

<!---
Native platform libraries
-->
[dart-ffi]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-ffi/dart-ffi-library.html
[package-ffi]: {{site.pub-pkg}}/ffi
[dart-io]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/dart-io-library.html
[package-io]: {{site.pub-pkg}}/io
[dart-isolate]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-isolate/dart-isolate-library.html
[dart-mirrors]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-mirrors/dart-mirrors-library.html

<!---
Web platform libraries
-->
[pkg-web]: {{site.pub-pkg}}/web
[js-interop]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-js_interop/dart-js_interop-library.html
[js-interop-unsafe]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-js_interop_unsafe/dart-js_interop_unsafe-library.html
[dart-html]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/dart-html-library.html
[dart-indexed_db]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-indexed_db/dart-indexed_db-library.html
[dart-js]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-js/dart-js-library.html
[package-js]: {{site.pub-pkg}}/js
[dart-js_util]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-js_util/dart-js_util-library.html
[dart-svg]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-svg/dart-svg-library.html
[dart-web_audio]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-web_audio/dart-web_audio-library.html
[dart-web_gl]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-web_gl/dart-web_gl-library.html

<!---
Misc
-->
[development JavaScript compiler]: /tools/webdev#serve
[jit]: /overview#native-platform
[JavaScript interoperability]: /interop/js-interop
[html-web]: /interop/js-interop/package-web