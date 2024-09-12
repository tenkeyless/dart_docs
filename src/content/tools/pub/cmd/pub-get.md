---
title: dart pub get
# description: >-
#   Use dart pub get to retrieve the dependencies used by your Dart application.
description: >-
  dart pub get을 사용하면, Dart 애플리케이션에서 사용하는 종속성을 검색할 수 있습니다.
---

_Get_ 은 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub get [options]
```

이 명령은 현재 작업 디렉토리의 [`pubspec.yaml`](/tools/pub/pubspec) 파일에 나열된, 
모든 종속성과 해당 [전이적 종속성](/tools/pub/glossary#transitive-dependency)을 가져옵니다. 
예를 들어:

```console
$ dart pub get
Resolving dependencies...
Got dependencies!
```

[시스템 캐시](/tools/pub/glossary#system-cache)에 종속성이 이미 포함되어 있지 않으면, 
`dart pub get`이 캐시를 업데이트하고 필요한 경우 종속성을 다운로드합니다. 
패키지를 시스템 캐시로 다시 매핑하기 위해, 
이 명령은 `.dart_tool/` 디렉터리에 `package_config.json` 파일을 만듭니다.

종속성을 획득하면 Dart 코드에서 참조할 수 있습니다. 
예를 들어, 패키지가 `test`에 종속된 경우:

```dart
import 'package:test/test.dart';
```

`dart pub get`이 새로운 종속성을 가져오면, 
이후의 gets가 해당 종속성의 동일한 버전을 사용하도록 하기 위해, 
[lockfile](/tools/pub/glossary#lockfile)을 작성합니다. 
[애플리케이션 패키지][Application packages]는 소스 제어에 대한 lockfile을 체크인해야 합니다. 
이렇게 하면, 애플리케이션이 모든 개발자와 프로덕션에 배포될 때, 
모든 종속성의 정확히 동일한 버전을 사용하게 됩니다. 
그러나, 일반 패키지는 다양한 종속성 버전에서 작동할 것으로 예상되므로, 
lockfile을 체크인해서는 안 됩니다.

lockfile이 이미 있는 경우, 
`dart pub get`은 가능한 경우 해당 lockfile에 잠긴 종속성의 버전을 사용합니다. 
종속성이 잠기지 않은 경우, pub은 모든 [버전 제약 조건](/tools/pub/glossary#version-constraint)을 충족하는 해당 종속성의 최신 버전을 가져옵니다. 
이것이 `dart pub get`과, 항상 모든 종속성의 최신 버전을 가져오려고 하는, 
[`dart pub upgrade`](/tools/pub/cmd/pub-upgrade)의 주요 차이점입니다.

[Application packages]: /tools/pub/glossary#application-package

## 패키지 resolution {:#package-resolution}

기본적으로, pub는 패키지 이름을 위치 URI로 매핑하는, 
`.dart_tool/` 디렉토리에 `package_config.json` 파일을 생성합니다.

:::note
생성된 `.dart_tool/` 디렉토리를 저장소에 체크인하지 마세요.
저장소의 `.gitignore` 파일에 추가하세요. 
자세한 내용은 [커밋하지 말아야 할 것](/guides/libraries/private-files)을 참조하세요.
:::


## 새로운 종속성 얻기 {:#getting-a-new-dependency}

종속성이 pubspec에 추가된 후 `dart pub get`이 실행되면, 
새 종속성과 그 전이적 종속성을 가져옵니다. 
그러나, pub은 새 종속성을 가져오는 데 필요하지 않는 한, 
이미 획득한 종속성의 버전을 변경하지 않습니다.


## 종속성 제거 {:#removing-a-dependency}

종속성이 pubspec에서 제거된 후, `dart pub get`이 실행되면, 
종속성은 더 이상 import 할 수 없습니다. 
제거된 종속성의 모든 전이 종속성도 제거되며, 
남아 있는 즉각적인 종속성도 이에 종속되지 않는 한 제거됩니다. 
종속성을 제거해도 이미 획득한 종속성의 버전은 변경되지 않습니다.


## 시스템 패키지 캐시 {:#the-system-package-cache}

인터넷을 통해 다운로드한 종속성(예: Git 및 [pub.dev 사이트]({{site.pub}}))은 [시스템 전체 캐시](/tools/pub/glossary#system-cache)에 저장됩니다. 
즉, 여러 패키지가 동일한 종속성의 동일한 버전을 사용하는 경우, 
한 번만 다운로드하여 로컬에 저장하면 됩니다.

기본적으로, 시스템 패키지 캐시는 홈 디렉토리의 `.pub-cache` 하위 디렉토리(macOS 및 Linux) 또는 `%LOCALAPPDATA%\Pub\Cache`(Windows의 경우, 위치는 Windows 버전에 따라 다를 수 있음)에 있습니다. 
pub를 실행하기 전에 [`PUB_CACHE`](/tools/pub/environment-variables) 환경 변수를 설정하여, 
캐시의 위치를 ​​구성할 수 있습니다.


## 오프라인 상태에서 얻기 {:#getting-while-offline}

네트워크 액세스가 없어도, `dart pub get`을 실행할 수 있습니다. 
pub은 시스템의 모든 패키지가 공유하는 중앙 캐시에 패키지를 다운로드하기 때문에, 
네트워크를 사용하지 않고도 이전에 다운로드한 패키지를 찾을 수 있습니다.

그러나, 기본적으로, `dart pub get`은 호스팅된 종속성이 있는 경우, 
온라인으로 전환하려고 시도하므로, pub은 종속성의 최신 버전을 감지할 수 있습니다. 
pub에서 이를 수행하지 않으려면, `--offline` 플래그를 전달합니다. 
오프라인 모드에서 pub은 로컬 패키지 캐시에서만 검색하여, 
이미 사용 가능한 버전 중에서 패키지와 작동하는 버전 세트를 찾으려고 합니다.

pub은 lockfile을 생성한다는 점에 유의하세요. 
캐시에 있는 일부 종속성의 유일한 버전이 오래된 경우, 
오프라인 `dart pub get`은 앱을 해당 이전 버전으로 잠급니다. 
다음에 온라인이 되면, 
[`dart pub upgrade`](/tools/pub/cmd/pub-upgrade)를 실행하여, 
최신 버전으로 업그레이드해야 할 것입니다.


## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은, [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `--[no-]offline` {:#no-offline}

{% render 'tools/pub-option-no-offline.md' %}

### `--dry-run` 또는 `-n` {:#dry-run-or-n}

변경될 종속성을 보고하지만 변경하지는 않습니다. 
업데이트를 하기 전에 분석하려는 경우 유용합니다.

### `--[no-]precompile` {:#no-precompile}

기본적으로, pub는 직접 종속성에서 실행 파일을 사전 컴파일합니다. (`--precompile`)
사전 컴파일을 방지하려면, `--no-precompile`을 사용합니다.

### `--enforce-lockfile` {:#enforce-lockfile}

`pubspec.lock` 파일이 벗어나거나 누락된 경우, 
오류 메시지와 함께 종속성을 해결하지 않습니다.

{% render 'pub-problems.md' %}
