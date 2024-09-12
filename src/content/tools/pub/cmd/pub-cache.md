---
title: dart pub cache
description: 시스템 캐시를 관리하려면 dart pub cache를 사용하세요.
---

_Cache_ 는 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub cache add <package> [--version <constraint>] [--all]
$ dart pub cache repair
$ dart pub cache clean
```

`dart pub cache` 명령은 [시스템 캐시](/tools/pub/glossary#system-cache)와 함께 작동합니다.

## 시스템 캐시에 패키지 추가 {:#adding-a-package-to-the-system-cache}

수동으로 시스템 캐시에 패키지를 추가할 수 있습니다.

```console
$ dart pub cache add <package>
```

## 시스템 캐시에 있는 모든 패키지를 다시 설치합니다. {:#reinstalling-all-packages-in-the-system-cache}

시스템 캐시에 있는 모든 패키지를 새로 다시 설치할 수 있습니다.

```console
$ dart pub cache repair
```

이 명령은 시스템 캐시의 패키지가 어떻게든 변경되거나 손상되었을 때 유용할 수 있습니다.

예를 들어, 일부 편집기는 시스템 캐시에서 패키지의 구현 파일을 쉽게 찾을 수 있게 해주며, 
실수로 해당 파일 중 하나를 편집할 수도 있습니다.

## 글로벌 시스템 캐시 지우기 {:#clearing-the-global-system-cache}

추가 디스크 공간을 확보하거나 문제가 있는 패키지를 제거하려면, 전체 시스템 캐시를 비울 수 있습니다.

```console
$ dart pub cache clean
```

:::version-note
`clean` 하위 명령은 Dart 2.14에서 도입되었습니다. 
이전 SDK로 시스템 캐시를 지우려면, [`PUB_CACHE`][] 폴더를 수동으로 삭제할 수 있습니다.
:::

[`PUB_CACHE`]: /tools/pub/environment-variables

## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은, [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `--all` {:#all}

`dart pub cache add --all`을 사용하여 라이브러리의 일치하는 모든 버전을 설치합니다.

### `--version `_`<constraint>`_ {:#version-constraint}

`dart pub cache add`와 함께 사용하여, 지정된 제약 조건에 가장 잘 맞는 버전을 설치합니다. 예를 들어:

```console
$ dart pub cache add http --version "0.12.2"
```

`--version`을 생략하면, pub는 알려진 모든 버전 중 가장 좋은 버전을 설치합니다.


{% render 'pub-problems.md' %}
