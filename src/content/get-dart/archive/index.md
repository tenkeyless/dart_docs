---
# title: Dart SDK archive
title: Dart SDK 아카이브
# short-title: Archive
short-title: 아카이브
# description: Download specific stable, beta, dev, and main channel versions of the Dart SDK and the Dart API documentation.
description: Dart SDK와 Dart API 문서의 특정 stable, beta, dev 및 main 채널 버전을 다운로드하세요.
js:
- url: /assets/js/get-dart/download_archive.dart.js
  defer: true
- url: /assets/js/get-dart/install.js
  defer: true
---

이 아카이브를 사용하여 [특정 버전](/get-dart#release-channels)의 [Dart SDK](/tools/sdk)와 [Dart API 설명서]({{site.dart-api}}/{{site.sdkInfo.channel}})를 다운로드하세요.

OS의 패키지 관리자로 Dart를 설치하고 싶으신가요? [Dart 받기](/get-dart)

:::warning 공지
{% include './_sdk-terms.md' %}
:::

## Stable 채널 {:#stable-channel}

Stable 채널 빌드는 프로덕션 사용을 위해 테스트되고 승인됩니다.

{% include './_archives_table.html', channel:'stable' %}

## Beta 채널 {:#beta-channel}

Beta 채널 빌드는 stable 채널에 대한 미리보기 빌드입니다. 
베타에 대해 앱을 테스트하지만, 릴리스하지 않는 것이 좋습니다. 
이는 새로운 기능을 미리 보거나, 향후 릴리스와의 호환성을 테스트하기 위한 것입니다. 
베타 채널 빌드는 프로덕션 사용에 적합하지 않습니다.

{% include './_archives_table.html', channel:'beta' %}

## Dev 채널 {:#dev-channel}

Dev 채널 빌드는 새로운 기능에 대한 조기 액세스를 제공할 수 있지만, 버그가 포함될 수 있습니다. 
Dev 채널 빌드는 프로덕션 사용에 적합하지 않습니다.

{% include './_archives_table.html', channel:'dev' %}

## Main 채널 {:#main-channel}

Main 채널 빌드는 Dart SDK 저장소의 `main` 브랜치에서 나온 최신 raw 빌드입니다. 
이는 사용 가능한 가장 최신 빌드이며 버그가 포함될 가능성이 높습니다. 
메인 채널 빌드는 프로덕션 용도가 아닌 실험적 개발 용도에만 적합합니다.

:::note
Main 채널 빌드는 서명되지 않았습니다.
:::

메인 채널 빌드를 다운로드하려면, [메인 채널 URL](#main-channel-url-scheme)을 사용하세요.

## 다운로드 URL {:#download-urls}

모든 채널의 zip 파일을 다운로드할 수 있습니다.

### Stable, Beta 및 Dev 채널 URL 스킴 {:#stable-beta-and-dev-channel-url-scheme}

Stable, Beta 및 Dev 채널 릴리스는 다음과 같은 URL에서 제공됩니다.

```plaintext
https://storage.googleapis.com/dart-archive/channels/<[!stable|beta|dev!]>/release/<[!version!]>/sdk/dartsdk-<[!platform!]>-<[!architecture!]>-release.zip
```

예를 들어,

```plaintext
https://storage.googleapis.com/dart-archive/channels/stable/release/2.7.2/sdk/dartsdk-windows-ia32-release.zip
https://storage.googleapis.com/dart-archive/channels/stable/release/2.1.1/sdk/dartsdk-macos-x64-release.zip
https://storage.googleapis.com/dart-archive/channels/beta/release/2.8.0-20.11.beta/sdk/dartsdk-linux-x64-release.zip
https://storage.googleapis.com/dart-archive/channels/dev/release/2.9.0-1.0.dev/sdk/dartsdk-linux-x64-release.zip
```

### Main 채널 URL 스킴 {:#main-channel-url-scheme}

최신 메인 채널 빌드는 다음 URL에서 확인할 수 있습니다.

```plaintext
https://storage.googleapis.com/dart-archive/channels/main/raw/latest/sdk/dartsdk-<[!platform!]>-<[!architecture!]>-release.zip
```

예를 들어,

```plaintext
https://storage.googleapis.com/dart-archive/channels/main/raw/latest/sdk/dartsdk-windows-x64-release.zip
```

:::note
Main 채널 빌드는 서명되지 않았습니다.
:::
