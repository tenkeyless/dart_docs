---
title: dart analyze
description: 정적 분석을 위한 명령줄 도구
toc: false
---

`dart analyst` 명령은 Dart를 지원하는 IDE나 편집기를 사용할 때 수행되는 것과 동일한 
[정적 분석][static analysis]을 수행합니다.

{% render 'tools/dart-tool-note.md' %}

다음은 현재 디렉토리 아래의 모든 Dart 파일에 대한 정적 분석을 수행하는 예입니다.

```console
$ dart analyze
```

분석기가 경고 및 정보 레벨 문제를 처리하는 방식을 커스터마이즈할 수 있습니다. 
일반적으로 분석기는 오류나 경고를 발견하면 실패를 보고하지만, 
정보 레벨 문제를 발견하면 실패를 보고하지 않습니다. 
`--fatal-infos` 및 `--no-fatal-warnings` 플래그를 사용하여 이 동작을 커스터마이즈할 수 있습니다. 
예를 들어, `--fatal-infos` 플래그가 있는 문제가 있을 때 분석기가 실패하도록 하려면, 다음과 같이 합니다.

```console
$ dart analyze --fatal-infos
```

디렉토리나 단일 파일 인수를 추가할 수 있습니다.

```console
$ dart analyze [<DIRECTORY> | <DART_FILE>]
```

예를 들어, `bin` 디렉토리를 분석하는 명령은 다음과 같습니다.

```console
$ dart analyze bin
```

:::version-note
Dart 2.13 이전에는, `dart analyst`가 디렉토리 인수만 지원했습니다.
:::

분석을 커스터마이즈하려면, [정적 분석 커스터마이즈][static analysis]에 설명된 대로, 
Dart 소스 코드의 분석 옵션 파일이나 특수 주석을 사용합니다.

명령줄 옵션에 대한 정보는 `--help` 플래그를 사용합니다.

```console
$ dart analyze --help
```

[static analysis]: /tools/analysis

{% comment %}
```
Usage: dart analyze [arguments] [<directory>]
-h, --help                   Print this usage information.
    --fatal-infos            Treat info level issues as fatal.
    --[no-]fatal-warnings    Treat warning level issues as fatal.
                             (defaults to on)
```
{% endcomment %}
