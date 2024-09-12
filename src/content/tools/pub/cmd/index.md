---
title: dart pub
description: Dart용 패키지 관리 도구인 pub의 명령줄 인터페이스입니다.
---

[pub 패키지 관리자](/guides/packages)에는, 
[`flutter` 도구][flutter-cli] 또는 [`dart` 도구][dart-cli]와 함께 작동하는, 
명령줄 인터페이스가 있습니다. 
두 도구 중 하나를 사용하여, `pub` 명령 다음에 `get`과 같은 하위 명령을 추가합니다.

```console
$ dart pub get    # Flutter가 아닌 패키지에 대한 종속성을 가져옵니다.
$ flutter pub get # Flutter 패키지에 대한 종속성을 가져옵니다.
```

이 사이트는 예시로 `dart pub <subcommand>`를 사용하지만, 
현재 디렉토리에 Flutter 앱이나 기타 Flutter 관련 코드가 있는 경우, 
`flutter pub <subcommand>`를 대신 사용합니다. 
자세한 내용은 [Flutter 웹사이트]({{site.flutter}})에서 [패키지 사용]({{site.flutter-docs}}/development/packages-and-plugins/using-packages)을 참조하세요.

[flutter-cli]: {{site.flutter-docs}}/reference/flutter-cli
[dart-cli]: /tools/dart-tool

:::version-note
`dart pub` 명령은 Dart 2.10에서 처음 선보였습니다. 
`dart pub` 또는 `flutter pub` 대신, 독립형 `pub` 명령을 사용하는 예를 여전히 찾을 수 있지만, 
독립형 `pub` 명령은 제거되었습니다.
:::

Pub 도구 사용 시 문제가 발생하면, [Pub 문제 해결](/tools/pub/troubleshoot)을 참조하세요.

## 하위 명령 리스트 {:#list-of-subcommands}

다음 각 pub 하위 명령에 대한 자세한 문서가 있습니다.

{% render 'pub-subcommands.md' %}

## 하위 명령 개요 {:#overview-of-subcommands}

Pub의 하위 명령은 다음 범주로 나뉩니다.

* [패키지 종속성 관리](#managing-package-dependencies)
* [명령줄 앱 실행](#running-command-line-apps)
* [패키지 및 앱 배포](#deploying-packages-and-apps)

<a id="managing-apps"></a>
### 패키지 종속성 관리 {:#managing-package-dependencies}

Pub은 [코드가 종속된 패키지](/tools/pub/dependencies)를 관리하기 위한 여러 하위 명령을 제공합니다.

이 그룹에서 가장 일반적으로 사용되는 하위 명령은 `get`과 `upgrade`로, 
패키지에서 사용하는 종속성을 검색하거나 업그레이드합니다. 
pubspec 파일을 수정할 때마다 `dart pub get` 또는 `flutter pub get`을 실행하여, 
종속성이 최신 상태인지 확인합니다. 
일부 IDE는 프로젝트를 만들거나 pubspec을 수정할 때 이 단계를 자동으로 수행합니다.

[`cache`](/tools/pub/cmd/pub-cache)
: pub의 로컬 패키지 캐시를 관리합니다. 
  이 하위 명령을 사용하여 캐시에 패키지를 추가하거나, 
  캐시에 있는 모든 패키지를 깨끗하게 다시 설치합니다.

[`deps`](/tools/pub/cmd/pub-deps)
: 현재 패키지에서 사용되는 모든 종속성을 나열합니다.

[`downgrade`](/tools/pub/cmd/pub-downgrade)
: 현재 패키지에서 사용하는 종속성으로 나열된 모든 패키지의 가장 낮은 버전을 검색합니다. 
  패키지 종속성의 하위 범위를 테스트하는 데 사용됩니다.

[`get`](/tools/pub/cmd/pub-get)
: 현재 패키지의 종속성으로 나열된 패키지를 검색합니다. 
  `pubspec.lock` 파일이 이미 있는 경우, 잠금 파일에 나열된 각 종속성의 버전(가능한 경우)을 가져옵니다. 
  필요에 따라 잠금 파일을 생성하거나 업데이트합니다.

[`outdated`](/tools/pub/cmd/pub-outdated)
: 현재 패키지가 종속된 모든 패키지를 살펴보고, 
  어떤 패키지 종속성이 오래되었는지 확인하고, 
  이를 업데이트하는 방법에 대한 조언을 제공합니다. 
  패키지 종속성을 업데이트하려면, 이 하위 명령을 사용합니다.

[`upgrade`](/tools/pub/cmd/pub-upgrade)
: 현재 패키지에서 사용하는 종속성으로 나열된 각 패키지의 최신 버전을 검색합니다. 
  `pubspec.lock` 파일이 있는 경우, 잠금 파일에 나열된 버전을 무시하고, 
  pubspec의 제약 조건을 준수하는 최신 버전을 가져옵니다. 
  필요에 따라 잠금 파일을 생성하거나 업데이트합니다.


### 명령줄 앱 실행 {:#running-command-line-apps}

[`global`](/tools/pub/cmd/pub-global) 하위 명령을 사용하면, 
패키지를 전역적으로 사용할 수 있으므로, 
해당 패키지의 `bin` 디렉토리에서 스크립트를 실행할 수 있습니다. 
전역적으로 사용할 수 있는 스크립트를 실행하려면, 
[경로에 시스템 캐시 `bin` 디렉토리를 추가][add-path]해야 합니다.

[add-path]: /tools/pub/cmd/pub-global#running-a-script-from-your-path

### 패키지 및 앱 배포 {:#deploying-packages-and-apps}

pub를 사용하면 패키지와 명령줄 앱을 게시할 수 있습니다.

#### 패키지 {:#packages}

Dart 패키지를 전 세계와 공유하려면, 
[`publish`](/tools/pub/cmd/pub-lish) 하위 명령을 사용하여, 
패키지를 [pub.dev 사이트]({{site.pub}})에 업로드할 수 있습니다. 
다른 사용자가 패키지의 새 버전을 수정하고 업로드할 수 있도록 허용하는 방법에 대한 자세한 내용은, 
[Uploaders](/tools/pub/publishing#uploaders)를 참조하세요.


#### 명령줄 앱 {:#command-line-apps}

스크립트가 포함된 모든 패키지(`bin/` 디렉토리에 있는 모든 것)의 경우, 
`executables` 태그를 pubspec 파일에 추가하는 것을 고려하세요. 
스크립트가 `executables`에 나열되면, 
사용자는 [`dart pub global activate`](/tools/pub/cmd/pub-global#activating-a-package)를 실행하여, 
명령줄에서 직접 사용할 수 있도록 할 수 있습니다.

## 글로벌 옵션 {:#global-options}

여러 명령줄 옵션이 모든 pub 하위 명령과 함께 작동합니다. 여기에는 다음이 포함됩니다.

### `--help` 또는 `-h` {:#help-or-h}

사용 정보를 출력합니다.

### `--trace` {:#trace}

오류가 발생하면 디버깅 정보를 출력합니다.

### `--verbose` 또는 `-v` {:#verbose-or-v}

`--verbosity=all`과 동일합니다.

### `--directory=<dir>` 또는 `-C <dir>` {:#directorydir-or-c-dir}

지정된 디렉토리에서 명령을 실행합니다.

### `--[no-]color` {:#no-color}

강조를 위해 출력에 색상을 추가합니다. (`--color`)
기본값은 터미널에서 이 명령을 사용하는지 여부에 따라 달라집니다. 
터미널에서는 `--color`가 기본값이고, 그렇지 않으면 `--no-color`가 기본값입니다. 
모든 환경에서 색상을 비활성화하려면, `--no-color`를 사용합니다.
