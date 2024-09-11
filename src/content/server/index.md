---
# title: Command-line and server apps
title: 명령줄 및 서버 앱
# short-title: CLI & server apps
short-title: CLI & 서버 앱
# description: All things relating to command-line and server apps.
description: 명령줄 및 서버 앱과 관련된 모든 것.
toc: false
---

이 페이지에서는 명령줄과 서버 앱을 개발하는 데 도움이 되는 도구와 문서를 소개합니다.

<p class="text-center">
  <a href="/tutorials/server/get-started" class="btn btn-primary btn-lg">시작하기</a>
</p>


## 도구 {:#tools}

[DartPad](/tools/dartpad)
: 초보자와 전문가 모두에게 편리한 DartPad를 사용하면,
  언어 기능과 dart:* API를 시도해 볼 수 있습니다.

  :::note
  DartPad는 `dart:io`와 같은 VM 라이브러리를 사용하거나, 
  [현재 지원되는 패키지][currently supported packages] 외의 패키지에서 라이브러리를 import 하는 것을 지원하지 않습니다.
  :::

[currently supported packages]: {{site.repo.dart.org}}/dart-pad/wiki/Package-and-plugin-support#currently-supported-packages

[Dart SDK](/tools/sdk)
: Dart 핵심 라이브러리와 [도구](/tools)를 받으려면, [Dart SDK를 설치](/get-dart)하세요.

## 프레임워크 {:#frameworks}

Dart로 작성된 서버 측 프레임워크는 다음과 같습니다.

[Serverpod](https://serverpod.dev)
: 코드 생성, 인증, 실시간 통신, 데이터베이스 및 캐싱을 지원하는 확장 가능한 앱 서버입니다.

[Dart Frog](https://dartfrog.vgv.dev/)
: Dart용 빠르고 미니멀한 백엔드 프레임워크입니다.

추가 도구
: [도구](/tools) 페이지는 일반적으로 유용한 도구(예: 좋아하는 IDE 또는 편집기용 Dart 플러그인)에 대한 링크입니다.

추가 옵션은 [pub.dev의 #server 패키지]({{site.pub-pkg}}?q=topic%3Aserver)를 참조하세요.

## 튜토리얼 {:#tutorials}

다음 튜토리얼이 도움이 될 수 있습니다.

[시작하기](/tutorials/server/get-started)
: Dart SDK를 사용하여 명령줄 및 서버 앱을 개발하는 방법을 알아보세요.

[gRPC 빠른 시작](https://grpc.io/docs/languages/dart/quickstart/)
: gRPC 프레임워크를 사용하는 클라이언트-서버 예제를 실행하고 수정하는 방법을 안내합니다.

[명령줄 앱 작성](/tutorials/server/cmdline)
: dart:io 및 args 패키지를 소개합니다.

[HTTP 서버 작성](/tutorials/server/httpserver)
: shelf 패키지를 소개합니다.

## 더 많은 리소스 {:#more-resources}

[Dart API]({{site.dart-api}}/{{site.sdkInfo.channel}})
: dart:* 라이브러리에 대한 API 참조.

[dart:io 문서](/libraries/dart-io)
: dart:io 라이브러리의 주요 기능을 사용하는 방법을 보여줍니다. 
  명령줄 스크립트, 서버 및 웹이 아닌 [Flutter 앱]({{site.flutter}})에서 
  dart:io 라이브러리를 사용할 수 있습니다.
