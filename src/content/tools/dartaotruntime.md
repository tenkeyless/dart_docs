---
title: dartaotruntime
# description: Command-line tool for running AOT-compiled snapshots of Dart code.
description: Dart 코드의 AOT 컴파일된 스냅샷을 실행하기 위한 명령줄 도구입니다.
toc: false
---

Dart를 사용하면 *AOT 스냅샷*이라는 미리 컴파일된 Dart 애플리케이션을 만들 수 있습니다.

## AOT 스냅샷 앱 생성 {:#create-aot-snapshot-app}

AOT 스냅샷을 생성하려면, 
[`dart compile` 명령][dart compile]의 `aot-snapshot` 하위 명령을 사용하세요.

## AOT 스냅샷 앱 실행 {:#run-aot-snapshot-app}

AOT 프로그램을 실행하려면 `dartaotruntime` 명령을 사용합니다. 
이 도구는 Windows, macOS, Linux를 지원합니다.

:::note
`dartaotruntime` 명령을 사용하여 실행하려면, 
`PATH` 환경 변수에 Dart `bin` 디렉토리 경로를 추가합니다.
:::

[dart compile]: /tools/dart-compile

## 예시 검토 {:#review-an-example}

다음은 AOT 스냅샷을 만들고 실행하는 예입니다.

```console
$ dart compile aot-snapshot bin/myapp.dart
```

```console
Generated: /Users/me/simpleapp/bin/myapp.aot
```

```console
$ dartaotruntime bin/simpleapp.aot
```

## 더 많은 옵션 알아보기 {:#learn-more-options}

명령줄 옵션에 대해 자세히 알아보려면 `--help` 플래그를 사용하세요.

```console
$ dartaotruntime --help
```
