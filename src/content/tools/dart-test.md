---
title: dart test
# description: Command-line tool for testing Dart projects.
description: Dart 프로젝트를 테스트하기 위한 명령줄 도구.
toc: false
---

`dart test` 명령은 [`test` 패키지][`test` package]에 의존하고, 
현재 Dart 프로젝트의 `test` 디렉토리에 있는 테스트를 실행합니다. 
테스트 작성에 대한 정보는 [테스트 문서][testing documentation]를 ​​참조하세요. 
Flutter 코드에서 작업하는 경우, 
[Flutter 앱 테스트][Testing Flutter apps]에 설명된 대로 `flutter test` 명령을 대신 사용하세요.

[testing documentation]: /guides/testing
[`test` package]: {{site.pub-pkg}}/test
[Testing Flutter apps]: {{site.flutter-docs}}/testing

{% render 'tools/dart-tool-note.md' %}

다음은 `dart test`를 사용하여 현재 프로젝트의 `test` 디렉토리에 있는 모든 테스트를 실행하는 예입니다.

```console
$ cd my_app
$ dart test
```

어떤 테스트를 실행할지 제어하려면, `test` 디렉토리 아래의 디렉토리나 파일에 대한 경로를 추가할 수 있습니다.

{% comment %}
  I ran these commands in site-www/misc
{% endcomment %}

```console
$ dart test test/library_tour/io_test.dart
00:00 +0: readAsString, readAsLines
00:00 +1: readAsBytes
...
```

테스트 하위 집합을 실행하는 또 다른 방법은, 
`--name` (`-n`), `--tags` (`-t`), 또는 `--exclude-tags` (`-x`) 플래그를 사용하여, 
일치시킬 문자열의 일부 또는 전체를 추가하는 것입니다.

```console
$ dart test --name String
00:00 +0: test/library_tour/io_test.dart: readAsString, readAsLines
00:00 +1: test/library_tour/core_test.dart: print: print(nonString)
00:00 +2: test/library_tour/core_test.dart: print: print(String)
00:00 +3: test/library_tour/core_test.dart: numbers: toString()
...
```

동일한 명령줄에서 이러한 플래그를 두 번 이상 사용하는 경우, 
_모든_ 조건과 일치하는 테스트만 실행됩니다.

```console
$ dart test --name String --name print
00:00 +0: test/library_tour/core_test.dart: print: print(nonString)
00:00 +1: test/library_tour/core_test.dart: print: print(String)
00:00 +2: All tests passed!
```

`dart test` 명령에는 어떤 테스트를 실행할지, 어떻게 실행할지(예: 동시성 및 시간 초과), 
그리고 출력이 어디에 어떻게 나타날지 제어하는 ​​플래그가 더 많이 있습니다. 
명령줄 옵션에 대한 자세한 내용은 [`test` 패키지][`test` package]를 참조하거나, `--help` 플래그를 사용하세요.

```console
$ dart test --help
```
