---
# title: How to use packages
title: 패키지 사용 방법
# short-title: Packages
short-title: 패키지
# description: Learn more about pub, Dart's tool for managing packages.
description: 패키지 관리를 위한 Dart 도구인, pub에 대해 자세히 알아보세요.
---

Dart 생태계는 라이브러리 및 도구와 같은 공유 소프트웨어를 관리하기 위해 _패키지_ 를 사용합니다. 
Dart 패키지를 얻으려면, **pub 패키지 관리자**를 사용합니다. 
[**pub.dev 사이트**]({{site.pub}})에서 공개적으로 사용 가능한 패키지를 찾을 수 있거나, 
로컬 파일 시스템이나 (Git 저장소와 같은) 다른 곳에서 패키지를 로드할 수 있습니다. 
패키지가 어디에서 왔든, pub는 버전 종속성을 관리하여, 
서로 작동하고 SDK 버전과 함께 작동하는 패키지 버전을 가져오는 데 도움이 됩니다.

대부분의 [Dart에 정통한 IDE][Dart-savvy IDEs]는 패키지 생성, 다운로드, 업데이트 및 게시를 포함하여 pub 사용에 대한 지원을 제공합니다. 
또는 명령줄에서 [`dart pub`](/tools/pub/cmd)를 사용할 수 있습니다.

최소한의 Dart 패키지는 [pubspec 파일](/tools/pub/pubspec)이 포함된 디렉터리입니다. 
pubspec에는 패키지에 대한 일부 메타데이터가 포함되어 있습니다. 
또한, 패키지에는 종속성(pubspec에 나열됨), Dart 라이브러리, 앱, 리소스, 테스트, 이미지 및 예제가 포함될 수 있습니다.

패키지를 사용하려면, 다음을 수행합니다.

* pubspec(패키지 종속성을 나열하고, 버전 번호와 같은 다른 메타데이터를 포함하는 `pubspec.yaml`이라는 이름의 파일)을 만듭니다.
* [`dart pub get`][get]을 사용하여 패키지의 종속성을 검색합니다.
* Dart 코드가 패키지의 라이브러리에 종속된 경우, 라이브러리를 import 합니다.

## Pubspec 생성 {:#creating-a-pubspec}

pubspec은 애플리케이션의 최상위 디렉토리에 있는 `pubspec.yaml`이라는 파일입니다. 
가능한 가장 간단한 pubspec은 패키지 이름만 나열합니다.

```yaml
name: my_app
```

다음은 pub.dev 사이트에 호스팅된, 
두 패키지(`js`와 `intl`)에 대한 종속성을 선언하는 pubspec의 예입니다.

```yaml
name: my_app

dependencies:
  js: ^0.6.0
  intl: ^0.17.0
```

`pubspec.yaml` 파일을 수동 편집 없이 업데이트하려면, 
`dart pub add` 명령을 실행할 수 있습니다. 
다음 예는 `vector_math`에 대한 종속성을 추가합니다.

```console
$ dart pub add vector_math
Resolving dependencies... 
+ vector_math 2.1.3
Downloading vector_math 2.1.3...
Changed 1 dependency!
```

Pubspec 생성에 대한 자세한 내용은, 
[pubspec 문서](/tools/pub/pubspec)와 사용하려는 패키지에 대한 문서를 참조하세요.

## 패키지 얻기 {:#getting-packages}

pubspec이 있으면, 애플리케이션의 최상위 디렉토리에서 [`dart pub get`][get]을 실행할 수 있습니다.

```console
$ cd <path-to-my_app>
$ dart pub get
```

이 프로세스를 _종속성 가져오기_ 라고 합니다.

`dart pub get` 명령은 앱이 종속된 패키지를 확인하고, 
이를 중앙 [시스템 캐시](/tools/pub/glossary#system-cache)에 저장합니다. 
앱이 게시된 패키지에 종속된 경우, 
pub는 [pub.dev 사이트]({{site.pub}})에서 해당 패키지를 다운로드합니다. 
[Git 종속성](/tools/pub/dependencies#git-packages)의 경우, 
pub는 Git 저장소를 복제합니다. 
전이적 종속성도 포함됩니다. 
예를 들어, `js` 패키지가 `test` 패키지에 종속된 경우, 
`pub`는 `js` 패키지와 `test` 패키지를 모두 가져옵니다.

Pub는 앱이 종속된 각 패키지 이름을 시스템 캐시의 해당 패키지에 매핑하는, 
`package_config.json` 파일(`.dart_tool/` 디렉터리 아래)을 만듭니다.

## 패키지에서 라이브러리 import {:#importing-libraries-from-packages}

패키지에서 찾은 라이브러리를 import 하려면, `package:` 접두사를 사용합니다.

```dart
import 'package:js/js.dart' as js;
import 'package:intl/intl.dart';
```

Dart 런타임은 `package:` 뒤에 있는 모든 것을 가져와서, 
앱의 `package_config.json` 파일에서 찾습니다.

이 스타일을 사용하여 자신의 패키지 내에서 라이브러리를 import 할 수도 있습니다. 
`transmogrify` 패키지가 다음과 같이 배치되어 있다고 가정해 보겠습니다.

```plaintext
transmogrify/
  lib/
    transmogrify.dart
    parser.dart
  test/
    parser/
      parser_test.dart
```

`parser_test.dart` 파일은 다음과 같이 `parser.dart`를 import 할 수 있습니다.

```dart
import 'package:transmogrify/parser.dart';
```


## 종속성 업그레이드 {:#upgrading-a-dependency}

패키지에 대한 새로운 종속성을 처음 받으면, pub는 다른 종속성과 호환되는 최신 버전을 다운로드합니다. 
그런 다음, **lockfile**을 만들어 패키지가 *항상* 해당 버전을 사용하도록 잠급니다. 
이 파일은 pub가 생성하여 pubspec 옆에 저장하는 `pubspec.lock`이라는 파일입니다. 
패키지에서 사용하는 각 종속성(즉각적 및 전이적)의 특정 버전을 나열합니다.

패키지가 [애플리케이션 패키지](/tools/pub/glossary#application-package)인 경우, 
이 파일을 [소스 제어](/guides/libraries/private-files)에 체크인해야 합니다. 
이렇게 하면 앱에서 작업하는 모든 사람이 모든 종속성의 동일한 버전을 사용합니다. 
잠금 파일을 체크인하면, 배포된 앱에서 동일한 버전의 코드를 사용합니다.

종속성을 최신 버전으로 업그레이드할 준비가 되면, [`dart pub upgrade`][upgrade] 명령을 사용합니다.

```console
$ dart pub upgrade
```

`dart pub upgrade` 명령은 pub에 패키지 종속성의 최신 버전을 사용하여, 
lockfile을 재생성하라고 지시합니다. 
종속성을 하나만 업그레이드하려는 경우, 업그레이드할 패키지를 지정할 수 있습니다.

```console
$ dart pub upgrade transmogrify
```

해당 명령은 `transmogrify`를 최신 버전으로 업그레이드하지만, 다른 모든 것은 그대로 둡니다.

`dart pub upgrade` 명령은 pubspec의 충돌하는 버전 제약 조건으로 인해, 
항상 모든 패키지를 최신 버전으로 업그레이드할 수 없습니다. 
pubspec을 편집해야 하는 오래된 패키지를 식별하려면, 
[`dart pub outdated`][outdated]를 사용합니다.

## 더 많은 정보 {:#more-information}

다음 페이지에서는 패키지와 pub 패키지 관리자에 대한 자세한 정보를 제공합니다.

### 방법 {:#how-to}

* [패키지 생성](/guides/libraries/create-packages)
* [패키지 게시](/tools/pub/publishing)

### 참조 {:#reference}

* [Pub 종속성](/tools/pub/dependencies)
* [Pub 환경 변수](/tools/pub/environment-variables)
* [Pub 용어집](/tools/pub/glossary)
* [Pub 패키지 레이아웃 규칙](/tools/pub/package-layout)
* [Pub 버전 관리 철학](/tools/pub/versioning)
* [Pubspec 형식](/tools/pub/pubspec)

### Pub 하위 명령 {:#pub-subcommands}

`dart pub` 도구는 다음과 같은 하위 명령을 제공합니다.

{% render 'pub-subcommands.md' %}

모든 `dart pub` 하위 명령에 대한 개요는, [pub 도구 문서](/tools/pub/cmd)를 참조하세요.

### 문제 해결 {:#troubleshooting}

[문제 해결 pub](/tools/pub/troubleshoot)는 pub 사용 시 발생할 수 있는 문제에 대한 솔루션을 제공합니다.

[Dart-savvy IDEs]: /tools#editors
[get]: /tools/pub/cmd/pub-get
[upgrade]: /tools/pub/cmd/pub-upgrade
[outdated]: /tools/pub/cmd/pub-outdated
