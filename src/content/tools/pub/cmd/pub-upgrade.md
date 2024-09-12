---
title: dart pub upgrade
# description: Use dart pub upgrade to get the latest versions of all dependencies used by your Dart app.
description: Dart 앱에서 사용하는 모든 종속성의 최신 버전을 얻으려면, dart pub upgrade를 사용하세요.
---

_Upgrade_ 는 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub upgrade [options] [dependencies]
```

[`dart pub get`](/tools/pub/cmd/pub-get)과 마찬가지로, 
`dart pub upgrade`는 종속성을 가져옵니다. 
차이점은 `dart pub upgrade`는 기존의 [lockfile](/tools/pub/glossary#lockfile)을 무시하여, 
pub이 모든 종속성의 최신 버전을 가져올 수 있다는 것입니다. 
관련 명령은 [`dart pub outdated`](/tools/pub/cmd/pub-outdated)이며, 
이를 실행하여 오래된 종속성을 찾을 수 있습니다.

추가 인수 없이 `dart pub upgrade`는, 
현재 작업 디렉터리의 [`pubspec.yaml`](/tools/pub/pubspec) 파일에 나열된 모든 종속성의 최신 버전과, 
해당 [전이적 종속성](/tools/pub/glossary#transitive-dependency)을 가져옵니다. 예를 들어:

```console
$ dart pub upgrade
Dependencies upgraded!
```

`dart pub upgrade`가 종속성 버전을 업그레이드할 때, 
[`dart pub get`](/tools/pub/cmd/pub-get)이 해당 종속성의 동일한 버전을 사용하도록, 
lockfile을 작성합니다. 
[애플리케이션 패키지][application packages]의 경우, lockfile을 소스 제어에 체크인합니다. 
이렇게 하면, 애플리케이션이 모든 개발자와 프로덕션에 배포될 때, 
모든 종속성의 정확히 동일한 버전을 갖게 됩니다. 
일반 패키지의 경우, 패키지가 다양한 종속성 버전에서 작동할 것으로 예상되므로, 
lockfile을 체크인하지 마세요.

lockfile이 이미 있는 경우, `dart pub upgrade`는 이를 무시하고, 
모든 종속성의 최신 버전을 사용하여 처음부터 새 lockfile을 생성합니다.

패키지 resolution 및 시스템 패키지 캐시에 대한 자세한 내용은, 
[`dart pub get` 문서](/tools/pub/cmd/pub-get)를 참조하세요.

[application packages]: /tools/pub/glossary#application-package

## 특정 종속성 업그레이드 {:#upgrading-specific-dependencies}

`dart pub upgrade`에 특정 종속성을 최신 버전으로 업그레이드하고, 
나머지 종속성은 가능한 한 그대로 두라고 지시할 수 있습니다. 
예를 들어:

```console
$ dart pub upgrade test args
Dependencies upgraded!
```

종속성을 업그레이드하면 전이 종속성도 최신 버전으로 업그레이드됩니다. 
일반적으로 다른 종속성은 업데이트되지 않으며, lockfile에 잠긴 버전에 그대로 유지됩니다. 
그러나, 요청된 업그레이드로 인해 이러한 잠긴 버전과 호환되지 않는 경우, 
호환되는 버전 세트가 발견될 때까지 선택적으로 잠금이 해제됩니다.

## 새로운 종속성 얻기 {:#getting-new-dependency}

`dart pub upgrade`가 실행되기 전에 종속성이 pubspec에 추가되면, 
새 종속성과 그 전이적 종속성을 모두 가져옵니다. 
이는 `dart pub get`과 동일한 동작을 공유합니다.


## 종속성 제거 {:#removing-a-dependency}

`dart pub upgrade`를 실행하기 전에 pubspec에서 종속성이 제거되면, 
종속성은 더 이상 import 할 수 없습니다. 
제거된 종속성의 모든 전이 종속성도 제거되며, 
남아 있는 즉각적인 종속성도 이에 종속되지 않는 한 제거됩니다. 
이는 `dart pub get`과 동일한 동작입니다.

## 오프라인 상태에서 업그레이드 {:#upgrading-while-offline}

네트워크 액세스가 없어도 `dart pub upgrade`를 실행할 수 있습니다. 
pub는 시스템의 모든 패키지가 공유하는 중앙 캐시에 패키지를 다운로드하기 때문에, 
네트워크를 사용하지 않고도 이전에 다운로드한 패키지를 찾을 수 있습니다.

그러나 기본적으로 `dart pub upgrade`는 호스팅된 종속성이 있는 경우 온라인으로 전환하려고 시도하므로, 
pub는 종속성의 새 버전을 감지할 수 있습니다. 
pub가 이를 수행하지 않도록 하려면 `--offline` 플래그를 전달합니다. 
오프라인 모드에서 pub는 로컬 패키지 캐시에서만 검색하여, 
이미 사용 가능한 버전 중에서 패키지와 작동하는 버전 세트를 찾으려고 합니다.

pub는 잠금 파일을 생성한다는 점에 유의하세요. 
캐시에 있는 종속성의 유일한 버전이 오래된 경우, 
오프라인 `dart pub upgrade`는 앱을 해당 이전 버전으로 잠급니다. 
다음에 온라인이 되면 `dart pub upgrade`를 다시 실행하여, 
최신 버전으로 업그레이드해야 할 것입니다.


## 옵션 {:#options}

`dart pub upgrade` 명령은 [`dart pub get` 옵션](/tools/pub/cmd/pub-get#options) 등을 지원합니다. 
모든 pub 명령에 적용되는 옵션은 [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `--[no-]offline`

{% render 'tools/pub-option-no-offline.md' %}

### `--dry-run` 또는 `-n` {:#dry-run-or-n}

변경될 종속성을 보고하지만 변경하지는 않습니다. 
업데이트를 하기 전에 분석하려는 경우 유용합니다.

### `--[no-]precompile`

기본적으로 pub는 직접 종속성에서 실행 파일을 사전 컴파일합니다. (`--precompile`)
사전 컴파일을 방지하려면, `--no-precompile`을 사용합니다.

### `--major-versions`

`pubspec.yaml` 파일에 있는 상한 제약 조건을 무시하고, 
[`dart pub outdated`][]가 _resolvable_ 으로 나열한 패키지를 가져옵니다. 
또한 `pubspec.yaml`을 새 제약 조건으로 업데이트합니다.

[`dart pub outdated`]: /tools/pub/cmd/pub-outdated

:::tip
이 명령을 실행하기 전에 `pubspec.yaml` 파일을 커밋하면, 
필요한 경우 변경 사항을 취소할 수 있습니다.
:::

어떤 종속성이 업그레이드될지 확인하려면, 
`dart pub upgrade --major-versions --dry-run`을 사용할 수 있습니다.

### `--tighten`

`pubspec.yaml`의 종속성 하한을 해결된 버전과 일치하도록, 
업데이트하고 변경된 제약 조건 리스트를 반환합니다. 
[특정 종속성](#upgrading-specific-dependencies)에 적용할 수 있습니다.


{% render 'pub-problems.md' %}
