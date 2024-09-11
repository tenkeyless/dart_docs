---
# title: Configuring pub environment variables
title: Pub 환경 변수 구성
# short-title: Pub environment variables
short-title: Pub 환경 변수
# description: How to configure your environment for Dart's package management tool, pub.
description: Dart의 패키지 관리 도구인 pub를 위한 환경을 구성하는 방법.
toc: false
---

환경 변수를 사용하면 사용자의 필요에 맞게 pub를 커스터마이즈할 수 있습니다.

`PUB_CACHE`
: pub의 일부 종속성은 pub 캐시에 다운로드됩니다. 
  기본적으로, 이 디렉토리는 `$HOME/.pub-cache`(macOS 및 Linux) 또는 `%LOCALAPPDATA%\Pub\Cache`(Windows)에 있습니다. (캐시의 정확한 위치는 Windows 버전에 따라 다를 수 있습니다.) 
  `PUB_CACHE` 환경 변수를 사용하여, 다른 위치를 지정할 수 있습니다. 
  자세한 내용은 [시스템 패키지 캐시](/tools/pub/cmd/pub-get#the-system-package-cache)를 참조하세요.

`PUB_HOSTED_URL`
: Pub은 [pub.dev 사이트]({{site.pub}})에서 종속성을 다운로드합니다. 
  특정 미러 서버의 위치를 ​​지정하려면, `PUB_HOSTED_URL` 환경 변수를 사용합니다. 예:

  ```bash
  PUB_HOSTED_URL = https://pub.example.com
  ```

private 패키지 저장소 사용에 대한 자세한 내용은, [기본 패키지 저장소 재정의][Overriding the default package repository]를 참조하세요.

:::note
회사 방화벽 뒤에서 `pub get`을 사용하려고 시도했지만 실패한 경우, 
[`pub get`이 회사 방화벽 뒤에서 실패함][`pub get` fails from behind a corporate firewall]을 참조하여, 
플랫폼에 대한 프록시 환경 변수를 설정하는 방법에 대한 정보를 확인하세요.
:::

[`pub get` fails from behind a corporate firewall]: /tools/pub/troubleshoot#pub-get-fails-from-behind-a-corporate-firewall
[Overriding the default package repository]: /tools/pub/custom-package-repositories#default-override
