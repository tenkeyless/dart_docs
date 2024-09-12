---
title: dart info
# description: Command-line tool for outputting Dart tooling diagnostic information.
description: Dart 툴링 진단 정보를 출력하기 위한 명령줄 도구입니다.
toc: false
---

`dart info` 명령은, 
설치된 `dart` 툴링, 실행 중인 Dart 프로세스, `pubspec.yaml`이 있는 디렉토리에 있는 경우, 
프로젝트 정보에 대한 진단 정보를 출력합니다. 
출력 정보는 툴링 문제를 디버깅하거나 버그를 보고하는 데 사용할 수 있습니다.

{% render 'tools/dart-tool-note.md' %}

:::warning
버그 보고서에 `dart info` 출력을 포함하는 경우, 
공개적으로 게시해도 괜찮은 세부 정보만 포함되어 있는지 출력을 검토하세요.
:::

Dart 프로세스 실행을 포함하여 시스템 및 Dart 설치와 관련된 일반 정보를 출력하려면, 
모든 디렉토리에서 `dart info`를 실행하세요.

```console
$ dart info
```

예를 들어, macOS에서는 출력이 다음과 비슷하게 표시됩니다.

```markdown
#### General info

- Dart 2.19.2 (stable) (Tue Feb 7 18:37:17 2023 +0000) on "macos_arm64"
- on macos / Version 13.1 (Build 22C65)
- locale is en-US

#### Process info

| Memory |   CPU | Elapsed time | Command line                      |
| -----: | ----: | -----------: | ----------------------------------|
| 253 MB | 49.7% |        00:00 | analysis_server.dart.snapshot ... |
|  69 MB | 18.7% |        00:00 | dart analyze                      |   
```

출력에 프로젝트 정보를 포함하려면, `pubspec.yaml` 파일이 있는 디렉토리에서 `dart info`를 실행합니다. 
결과 출력에는 추가 **Project info** 섹션이 포함됩니다.

```markdown
#### Project info

- sdk constraint: '>=2.19.2 <3.0.0'
- dependencies: path
- dev_dependencies: lints, test
```

표시된 프로젝트와 프로세스 정보에 파일 경로와 경로 종속성을 포함하려면, 
`--no-remove-file-paths` 옵션을 추가하세요.

```console
$ dart info --no-remove-file-paths
```
