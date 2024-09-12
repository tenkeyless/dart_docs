---
title: dart format
description: Dart 소스 코드를 포맷하기 위한 명령줄 도구.
---

[Dart 포맷 가이드라인][dart-guidelines]을 따르도록 코드를 업데이트하려면, 
`dart format` 명령을 사용하세요. 
이 포맷은 Dart를 지원하는 IDE나 편집기를 사용할 때 얻는 것을 따릅니다.

{% render 'tools/dart-tool-note.md' %}

## 포맷할 파일 지정 {:#specify-files-to-format}

하나 이상의 Dart 파일을 다시 포맷하려면, 
원하는 파일이나 디렉토리의 경로 리스트를 제공하세요.

### 하나의 경로를 지정하세요 {:#specify-one-path}

한 파일이나 디렉토리에 대한 경로를 제공합니다. 
디렉토리를 지정하는 경우, `dart format`은 바로 그 디렉토리에 있는 파일에만 영향을 미치며, 
하위 디렉토리를 재귀적으로 탐색하지 않습니다.

**예:** 현재 디렉토리나 그 아래에 있는 모든 Dart 파일을 포맷하려면:

```console
$ dart format .
```

### 여러 경로를 지정하세요 {:#specify-multiple-paths}

여러 파일이나 디렉토리를 지정하려면, 공백으로 구분된 목록을 사용합니다.

**예:** `lib` 디렉토리에 있는 모든 Dart 파일과 `bin` 디렉토리에 있는 하나의 Dart 파일을 포맷하려면:

```console
$ dart format lib bin/updater.dart 
```

### Dart 파일 덮어쓰기 방지 {:#prevent-overwriting-dart-files}

기본적으로, `dart format`은 Dart 파일을 **덮어씁니다.**

* 파일을 덮어쓰지 않으려면, `--output` 또는 `-o` 플래그를 추가합니다.
* 포맷된 파일의 내용을 가져오려면, `-o show` 또는 `-o json`을 추가합니다.
* 어떤 파일만 _변경될지_ 확인하려면, `-o none`을 추가합니다.

```console
$ dart format -o show bin/my_app.dart
```

## 변경 사항이 발생하면 알림 {:#notify-when-changes-occur}

`dart format`이 포맷 변경이 발생할 때 종료 코드를 반환하도록 하려면, 
`--set-exit-if-changed` 플래그를 추가합니다.

* 변경이 발생하면, `dart format` 명령은 종료 코드 `1`을 반환합니다.
* 변경이 발생하지 않으면, `dart format` 명령은 종료 코드 `0`을 반환합니다.

CI(Continuous Integration) 시스템에서 종료 코드를 사용하면, 
종료 코드에 대한 응답으로 다른 작업을 트리거할 수 있습니다.

```console
$ dart format -o none --set-exit-if-changed bin/my_app.dart
```

## 끝에 쉼표를 사용하기 {:#use-trailing-commas}

더 나은 자동 포맷을 위해 선택적 후행 쉼표를 사용합니다. 
함수, 메서드 및 생성자의 매개변수 리스트 끝에 후행 쉼표를 추가합니다. 
이렇게 하면, 포맷터가 Dart 스타일 코드에 적절한 양의 줄 바꿈을 삽입하는 데 도움이 됩니다.

## 공백에만 영향을 미칩니다 {:#affects-whitespace-only}

안전하지 않을 수 있는 변경을 하지 않으려면, `dart format`은 공백에만 영향을 미칩니다.

하지만 읽기 쉽고 일관된 코드를 작성하는 데는 공백 외에도 많은 요소가 있습니다. 
Dart 코드 작성 및 스타일링 모범 사례에 대해 자세히 알아보려면, [Dart 스타일 가이드][Dart style guide]를 확인하세요.

## 더 알아보기 {:#learn-more}

추가 명령줄 옵션에 대해 알아보려면, `dart help` 명령을 사용하거나, 
[dart_style 패키지][dart_style]에 대한 문서를 참조하세요.

```console
$ dart help format
```

[Dart style guide]: /effective-dart/style
[dart_style]: {{site.pub-pkg}}/dart_style
[dart-guidelines]: /effective-dart/style#formatting
