---
title: dart pub downgrade
# description: Use dart pub downgrade to get the lowest versions of all dependencies used by your Dart application.
description: Dart 애플리케이션에서 사용하는 모든 종속성의 가장 낮은 버전을 얻으려면, dart pub downgrade를 사용하세요.
---

_Downgrade_ 는 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub downgrade [--[no-]offline] [-n|--dry-run] [dependencies...] 
```

추가 인수 없이, 
`dart pub downgrade`는 현재 작업 디렉토리의 [`pubspec.yaml`](/tools/pub/pubspec) 파일에 나열된,
모든 종속성의 가장 낮은 버전과 해당 [전이적 종속성](/tools/pub/glossary#transitive-dependency)을 가져옵니다. 
예를 들어:

```console
$ dart pub downgrade
Resolving dependencies... (1.2s)
+ barback 0.13.0
+ collection 0.9.1
+ path 1.2.0
+ source_maps 0.9.0
+ source_span 1.0.0
+ stack_trace 0.9.1
Changed 6 dependencies!
```

`dart pub downgrade` 명령은 잠금 파일을 만듭니다. 
이미 잠금 파일이 있으면, pub은 해당 파일을 무시하고, 
모든 종속성의 가장 낮은 버전을 사용하여 처음부터 새 파일을 생성합니다.

패키지 해결 및 시스템 패키지 캐시에 대한 자세한 내용은, 
[`dart pub get` 문서](/tools/pub/cmd/pub-get)를 참조하세요.

## 특정 종속성 다운그레이드 {:#downgrading-specific-dependencies}

`dart pub downgrade`에 특정 종속성을 가장 낮은 버전으로 다운그레이드하고, 
나머지 종속성은 가능한 한 그대로 두라고 지시할 수 있습니다. 예를 들어:

```console
$ dart pub downgrade test
Resolving dependencies...
  barback 0.15.2+2
  bot 0.27.0+2
  browser 0.10.0+2
  chrome 0.6.5
  collection 1.1.0
  path 1.3.0
  pool 1.0.1
  source_span 1.0.2
< stack_trace 0.9.2 (was 1.1.1)
  stagexl 0.10.2
< test 0.10.0 (was 0.11.4)
These packages are no longer being depended on:
- matcher 0.11.3
Changed 3 dependencies!
```

특정 종속성을 다운그레이드하는 경우, 
pub은 새로운 종속성 제약 조건에 맞는 모든 전이 종속성의 가장 높은 버전을 찾으려고 합니다. 
모든 전이 종속성도 결과적으로 다운그레이드됩니다.


## 새로운 종속성 얻기 {:#getting-a-new-dependency}

`dart pub downgrade`가 실행되기 전에 종속성이 pubspec에 추가되면, 
새 종속성과 그 전이적 종속성을 모두 가져옵니다. 
이는 `dart pub get`과 동일한 동작을 공유합니다.


## 종속성 제거 {:#removing-a-dependency}

`dart pub downgrade`를 실행하기 전에 pubspec에서 종속성이 제거되면, 
종속성은 더 이상 import 할 수 없습니다. 
제거된 종속성의 모든 전이 종속성도 제거되며, 
남아 있는 즉각적인 종속성도 이에 종속되지 않는 한 제거됩니다. 
이는 `dart pub get`과 동일한 동작입니다.


## 오프라인 상태에서 다운그레이드 {:#downgrading-while-offline}

네트워크 액세스가 없어도, `dart pub downgrade`를 실행할 수 있습니다. 
pub는 시스템의 모든 패키지가 공유하는 중앙 캐시에 패키지를 다운로드하기 때문에, 
네트워크를 사용하지 않고도 이전에 다운로드한 패키지를 찾을 수 있습니다.

그러나, 기본적으로 `dart pub downgrade`는 호스팅된 종속성이 있는 경우, 온라인으로 전환하려고 합니다. 
pub에서 이를 수행하지 않으려면, `--offline` 플래그를 전달합니다. 
오프라인 모드에서 pub는 로컬 패키지 캐시에서만 찾고, 
이미 사용 가능한 버전 중에서 패키지와 작동하는 버전 세트를 찾으려고 합니다.


## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은, [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `--[no-]offline` {:#no-offline}

{% render 'tools/pub-option-no-offline.md' %}

### `--dry-run` 또는 `-n` {:#dry-run-or-n}

어떤 종속성이 변경될지 보고하지만, 아무것도 변경하지 않습니다.

### `--tighten` {:#tighten}

:::version-note
Dart 3.5에서는 `--tighten` 옵션에 대한 지원이 추가되었습니다.
:::

`pubspec.yaml`의 종속성 하한을 해결된 버전과 일치하도록 업데이트하고, 
변경된 제약 조건 리스트를 반환합니다. 
[특정 종속성](#downgrading-specific-dependencies)에 적용할 수 있습니다.

{% render 'pub-problems.md' %}
