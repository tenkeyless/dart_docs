---
# title: Commonly used packages
title: 일반적으로 사용되는 패키지
# description: >-
#   Some of the most useful and popular packages, and where you can learn more.
description: >-
  가장 유용하고 인기 있는 패키지 중 일부와, 자세한 정보를 알아볼 수 있는 곳입니다.
---

{% assign pub = site.pub %}
{% assign pubpkg = site.pub-pkg %}

이 페이지는 Dart 개발자가 게시한 가장 인기 있고 유용한 [패키지](/guides/packages) 중 일부를 나열합니다. 
더 많은 패키지를 찾고, [코어 라이브러리](/libraries)도 검색하려면, [pub.dev 사이트]({{pub}})를 사용하세요.

일반적으로 사용되는 패키지는 세 그룹으로 나뉩니다.

- [일반 목적 패키지](#general-purpose-packages)
- [Dart 코어 라이브러리를 확장하는 패키지](#packages-that-correspond-to-sdk-libraries)
- [전문화된 패키지](#specialized-packages)
  - [Flutter 패키지](#flutter-packages)
  - [Web 패키지](#web-packages)
  - [명령줄 및 서버 패키지](#command-line-and-server-packages)

<!-- omit in toc -->
## 일반 목적 패키지 {:#general-purpose-packages}

다양한 프로젝트에 이 패키지를 활용하세요.

| 패키지 | 설명 | 일반적으로 사용되는 API |
|----|----|----|
| [archive][] | 다양한 아카이브 및 압축 형식을 인코딩하고 디코딩합니다. | Archive, ArchiveFile, TarEncoder, TarDecoder, ZipEncoder, ZipDecoder |
| [characters][] | 사용자가 인식하는 문자(Unicode 문자소 클러스터)에 대한 문자열을 조작합니다. | String.characters, Characters, CharacterRange |
| [cronet_http][] | `package:http`와 동일한 인터페이스를 사용하여, Android [Cronet][cronet] HTTP 클라이언트에 대한 액세스를 제공합니다. | |
| [cupertino_http][] | `package:http`와 동일한 인터페이스를 사용하여, Apple의 [Foundation URL 로딩 시스템][furl]에 대한 액세스를 제공합니다. | |
| [http][] | HTTP 리소스 사용을 단순화하기 위해 높은 레벨 함수와 클래스 세트를 제공합니다. | delete(), get(), post(), read() |
| [intl][] | 복수형과 성별, 날짜 및 숫자 형식 및 구문 분석, 양방향 텍스트 등을 지원하는 국제화 및 현지화 기능이 제공됩니다. | Bidi, DateFormat, MicroMoney, TextDirection |
| [json_serializable][] | JSON 조작 코드를 생성합니다. 자세한 내용은, [JSON 지원](/guides/json)을 참조하세요. | @JsonSerializable |
| [logging][] | 애플리케이션에 메시지 로깅을 추가합니다. | LoggerHandler, Level, LogRecord |
| [mockito][] | 테스트에서 객체를 Mock합니다. 종속성 주입을 위한 테스트를 작성할 때 도움이 됩니다. [test][] 패키지와 함께 사용합니다. | Answering, Expectation, Verification |
| [path][] | 다양한 타입의 경로를 조작합니다. 자세한 내용은 [언박싱 패키지: path]({{site.news}}/2016/06/unboxing-packages-path.html)를 참조하세요. | absolute(), basename(), extension(), join(), normalize(), relative(), split() |
| [quiver][] | 코어 Dart 라이브러리를 사용하여 간소화합니다. Quiver가 추가 지원을 제공하는 라이브러리에는 async, cache, collection, core, iterables, patterns, testing이 포함됩니다. | CountdownTimer (quiver.async); MapCache (quiver.cache); MultiMap, TreeSet (quiver.collection); EnumerateIterable (quiver.iterables); center(), compareIgnoreCase(), isWhiteSpace() (quiver.strings) |
| [shelf][] | Dart용 웹 서버 미들웨어를 제공합니다. Shelf를 사용하면, 웹 서버와 웹 서버 일부를 쉽게 만들고 구성할 수 있습니다. | Cascade, Pipeline, Request, Response, Server |
| [stack_trace][] | Dart가 생성하는 스택 추적을 파싱, 검사 및 조작합니다. 또한 스택 추적을 기본 StackTrace 구현보다 더 읽기 쉬운 형식으로 변환합니다. 자세한 내용은 [언박싱 패키지: stack_trace]({{site.news}}/2016/01/unboxing-packages-stacktrace.html)를 참조하세요. | Trace.current(), Trace.format(), Trace.from() |
| [test][] | Dart에서 테스트 작성 및 실행을 표준화합니다. | expect(), group(), test() |
| [yaml][] | YAML 마크업을 구문 분석합니다. | loadYaml(), loadYamlStream() |

{:.table .table-striped .nowrap}

[archive]: {{pubpkg}}/archive
[characters]: {{pubpkg}}/characters
[cronet_http]: {{pubpkg}}/cronet_http
[cupertino_http]: {{pubpkg}}/cupertino_http
[http]: {{pubpkg}}/http
[intl]: {{pubpkg}}/intl
[json_serializable]: {{pubpkg}}/json_serializable
[logging]: {{pubpkg}}/logging
[mockito]: {{pubpkg}}/mockito
[path]: {{pubpkg}}/path
[quiver]: {{pubpkg}}/quiver
[shelf]: {{pubpkg}}/shelf
[stack_trace]: {{pubpkg}}/stack_trace
[test]: {{pubpkg}}/test
[yaml]: {{pubpkg}}/yaml
[Cronet]: {{site.android-dev}}/develop/connectivity/cronet
[furl]: {{site.apple-dev}}/documentation/foundation/url_loading_system

<!-- omit in toc -->
## Dart 코어 라이브러리를 확장하는 패키지 {:#packages-that-correspond-to-sdk-libraries}

다음 패키지는 각각 [코어 라이브러리](/libraries)를 기반으로 기능을 추가하고 누락된 기능을 채웁니다.

| 패키지 | 설명 | 일반적으로 사용되는 API |
|----|----|----|
| [async][] | dart:async를 확장하여, 비동기 계산을 처리하는 유틸리티 클래스를 추가합니다. 자세한 내용은 [패키지 언박싱: async 1부][async-1], [2부][async-2] 및 [3부][async-3]을 참조하세요. | AsyncMemoizer, CancelableOperation, FutureGroup, LazyStream, Result, StreamCompleter, StreamGroup, StreamSplitter |
| [collection][] | dart:collection을 확장하여, 유틸리티 함수와 클래스를 추가하여 컬렉션 작업을 더 쉽게 만들었습니다. 자세한 내용은, [언박싱 패키지: collection][collect]를 참조하세요. | Equality, CanonicalizedMap, MapKeySet, MapValueSet, PriorityQueue, QueueList |
| [convert][] | dart:convert를 확장하여, 다양한 데이터 표현 간 변환을 위한 인코더와 디코더를 추가합니다. 데이터 표현 중 하나는 _퍼센트 인코딩(percent encoding)_ 으로, _URL 인코딩(URL encoding)_ 이라고도 합니다. | HexDecoder, PercentDecoder |
| [io][] | 파일, 표준 스트림 및 프로세스 작업을 간소화하기 위해, ansi와 io라는 두 개의 라이브러리를 포함합니다. ansi 라이브러리를 사용하여 터미널 출력을 커스터마이즈합니다. io 라이브러리에는 프로세스, stdin 및 파일 복제를 처리하기 위한 API가 있습니다. |  copyPath(), isExecutable(), ExitCode, ProcessManager, sharedStdIn |

{:.table .table-striped .nowrap}

[async]: {{pubpkg}}/async
[collection]: {{pubpkg}}/collection
[convert]: {{pubpkg}}/convert
[io]: {{pubpkg}}/io
[async-1]: {{site.news}}/2016/03/unboxing-packages-async-part-1.html
[async-2]: {{site.news}}/2016/03/unboxing-packages-async-part-2.html
[async-3]: {{site.news}}/2016/04/unboxing-packages-async-part-3.html
[collect]: {{site.news}}/2016/01/unboxing-packages-collection.html

<!-- omit in toc -->
## 전문화된 패키지 {:#specialized-packages}

Flutter와 웹 개발용 패키지 등의 특수 패키지를 찾으려면, 다음 섹션을 참조하세요.

<!-- omit in toc -->
### Flutter 패키지 {:#flutter-packages}

Flutter 패키지에 대해 자세히 알아보려면, 
Flutter 문서의 [패키지 사용][flutterpkg]을 참조하거나, 
pub.dev 사이트에서 [Flutter 패키지][fluttersearch]를 검색하세요.

[flutterpkg]: {{site.flutter-docs}}/development/packages-and-plugins/using-packages
[fluttersearch]: {{pub}}/flutter

<!-- omit in toc -->
### Web 패키지 {:#web-packages}

웹 패키지에 대해 자세히 알아보려면, 
[웹 라이브러리 및 패키지][webpkg]를 참조하거나, 
pub.dev 사이트에서 [웹 패키지][pkgsearch]를 검색하세요.

[webpkg]: /web/libraries
[pkgsearch]: {{pub}}/web

<!-- omit in toc -->
### 명령줄 및 서버 패키지 {:#command-line-and-server-packages}

CLI 또는 서버 패키지에 대해 자세히 알아보려면, 
[명령줄 및 서버 라이브러리와 패키지](/server/libraries)를 참조하세요. 
또는 pub.dev 사이트를 사용하여, [다른 패키지를 검색하세요]({{pub}}).
