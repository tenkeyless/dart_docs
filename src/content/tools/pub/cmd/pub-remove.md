---
title: dart pub remove
description: 종속성을 제거하려면 dart pub remove를 사용하세요.
---

_Remove_ 는 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub remove <package> [options]
```

이 명령은 지정된 패키지를 종속성으로 pubspec에서 제거합니다.

예를 들어, 다음 명령은 `pubspec.yaml`을 편집(`dependencies` 또는 `dev_dependencies`에서 `http`를 제거)한 다음, 
`dart pub get`을 호출하는 것과 같습니다.

```console
$ dart pub remove http
```

## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은 [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `--[no-]offline`

{% render 'tools/pub-option-no-offline.md' %}

### `-n, --dry-run`

어떤 종속성이 변경될지 보고하지만, 아무것도 변경하지 않습니다.

### `--[no-]precompile`

기본적으로 pub는 직접 종속성에서 실행 파일을 사전 컴파일합니다. (`--precompile`)
사전 컴파일을 방지하려면, `--no-precompile`을 사용합니다.


{% render 'pub-problems.md' %}
