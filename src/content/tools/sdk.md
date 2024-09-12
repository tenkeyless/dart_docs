---
# title: Dart SDK overview
title: Dart SDK 개요
# description: Dart libraries and command-line tools.
description: Dart 라이브러리 및 명령줄 도구.
---

Dart SDK에는 Dart 웹, 명령줄 및 서버 앱을 개발하는 데 필요한 라이브러리와 명령줄 도구가 있습니다. 
Dart SDK를 설치하려면 [Dart 받기](/get-dart)를 참조하세요. 
Flutter 앱을 개발하는 경우, [Flutter SDK 설치][flutter]를 참조하세요. 
Flutter SDK에는 Dart SDK가 포함되어 있습니다.

Dart 개발에 사용할 수 있는 다른 도구에 대해 알아보려면, [Dart 도구](/tools) 페이지를 확인하세요.

:::version-note
이 사이트의 문서와 예제에서는 **Dart SDK** {% if site.sdkInfo.channel == 'dev' %} **dev 채널** {% endif -%} 버전 [{{site.sdkInfo.version}}][site SDK version]을 사용합니다.
:::

{% comment %}
  IMPORTANT: After each release, EDIT src/_data/pkg-vers.json
  to update the SDK version number.
  More info: https://github.com/dart-lang/site-www/wiki/Updating-to-new-SDK-releases
{% endcomment %}

## Dart SDK에는 무엇이 들어있나요? {:#whats-in-the-dart-sdk}

Dart SDK에는 두 개의 디렉토리가 포함되어 있습니다.

* `lib`에는 [Dart 라이브러리][Dart libraries]가 포함되어 있습니다.
* `bin`에는 다음 명령줄 도구가 포함되어 있습니다.

[`dart`](/tools/dart-tool)
: Dart 코드를 만들고, 포맷하고, 분석하고, 테스트하고, 문서화하고, 컴파일하고, 실행하는 명령줄 인터페이스입니다.

[`dartaotruntime`](/tools/dartaotruntime)
: AOT 컴파일 스냅샷을 위한 Dart 런타임입니다.

{% render 'tools/utf-8.md' %}

SDK에 대해 자세히 알아보려면, [README 파일][readme]을 확인하세요.

## 버그 제출 및 기능 요청 {:#filing-bugs-and-feature-requests}

기존 문제를 보거나 새 문제를 만들려면, [SDK 문제 추적기][sdk-issues]로 이동하세요.

[Dart libraries]: /libraries
[flutter]: {{site.flutter-docs}}/get-started/install
[site SDK version]: {{site.dart-api}}/{{site.sdkInfo.channel}}/{{site.sdkInfo.version}}/index.html
[readme]: {{site.repo.dart.sdk}}/blob/main/README.dart-sdk
[sdk-issues]: {{site.repo.dart.sdk}}/issues
