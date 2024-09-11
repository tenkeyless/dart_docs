---
# title: Command-line and server libraries and packages
title: 명령줄 및 서버 라이브러리와 패키지
# short-title: CLI & server libraries
short-title: CLI 및 서버 라이브러리
# description: Libraries and packages that can help you write Dart command-line & server apps.
description: Dart 명령줄 및 서버 앱을 작성하는 데 도움이 되는 라이브러리와 패키지입니다.
---

[Dart SDK][]에는 낮은 레벨 명령줄 및 서버 API를 제공하는, 
[dart:io][] 및 기타 라이브러리가 포함되어 있습니다.

[Dart SDK]: /tools/sdk
[dart:io]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/dart-io-library.html

## SDK 라이브러리 {:#sdk-libraries}

Dart SDK에는 dart:io 및 낮은 레벨 웹 API를 제공하는 다른 라이브러리가 포함되어 있습니다.

[dart:io 설명서](/libraries/dart-io)
: dart:io 라이브러리 사용에 대한 예제 중심 투어. 
  주제에는 파일 및 디렉터리 작업, HTTP 요청 만들기 및 처리가 포함됩니다.

[dart:io API 참조][dart:io]
: dart:io 라이브러리에 대한 전체 참조 문서.

## 커뮤니티 패키지 {:#community-packages}

[pub.dev 사이트]({{site.pub}})를 사용하면, 
앱에서 지원해야 하는 플랫폼을 지정하여, 
명령줄 및 서버 앱을 지원하는 패키지를 검색할 수 있습니다. 
필요한 기능을 설명하는 단어를 검색할 수도 있습니다.

### 명령줄 패키지 {:#command-line-packages}

명령줄 앱은 `archive`, `intl`, `yaml`과 같은 [일반 용도 패키지][general-purpose packages] 외에도, 
다음 패키지를 사용하는 경우가 많습니다.

| **패키지**                               | **설명**                                                           |
|-------------------------------------------|---------------------------------------------------------------------------|
| [args]({{site.pub-pkg}}/args)             | raw 명령줄 인수를 구문 분석하여, 일련의 옵션과 값을 생성합니다.       |
| [cli_util]({{site.pub-pkg}}/cli_util)     | 명령줄 앱을 구축하는 데 필요한 유틸리티를 제공합니다.                         |
| [completion]({{site.pub-pkg}}/completion) | `args` 패키지를 사용하는 앱에 명령줄 완성 기능을 추가합니다.         |
| [path]({{site.pub-pkg}}/path)             | 경로 조작을 위한 포괄적인, 크로스 플랫폼 연산을 제공합니다. |
| [usage]({{site.pub-pkg}}/usage)           | Google Analytics를 래핑합니다.                                                   |

{:.table .table-striped .nowrap}

### 서버 패키지 {:#server-packages}

서버 앱은 이전 표에 나열된 패키지 및 `logging`과 같은, 
[일반 용도 패키지][general-purpose packages] 외에도 다양한 패키지 중에서 선택할 수 있습니다.

| **패키지**                             | **설명**                                                                                                       |
|-----------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| [crypto]({{site.pub-pkg}}/crypto)       | SHA-1, SHA-256, MD5, HMAC와 같은 알고리즘에 대한 암호화 해싱 함수를 구현합니다. |
| [grpc]({{site.pub-pkg}}/grpc)           | 모바일과 HTTP/2를 우선시하는 고성능, 오픈 소스 일반 RPC 프레임워크인 [gRPC][]를 구현합니다.  |
| [shelf]({{site.pub-pkg}}/shelf)         | 구성과 쉬운 재사용을 장려하는 웹 서버 미들웨어 모델을 제공합니다.  |
| [dart_frog]({{site.pub-pkg}}/dart_frog) | Shelf를 기반으로 구축된 Dart용 빠르고 단순한 백엔드 프레임워크입니다.  |
| [serverpod]({{site.pub-pkg}}/serverpod) | 코드 생성, 인증, 실시간 통신, 데이터베이스 및 캐싱을 지원하는 확장 가능한 앱 서버입니다. |

{:.table .table-striped .nowrap}

[general-purpose packages]: /resources/useful-packages#general-purpose-packages
[gRPC]: https://grpc.io/
