---
# title: "dart: The Dart command-line tool"
title: "dart: Dart 명령줄 도구"
# description: "The reference page for using 'dart' in a terminal window."
description: "터미널 창에서 'dart'를 사용하는 방법에 대한 참조 페이지입니다."
toc: false
---

`dart` 도구(`bin/dart`)는 [Dart SDK](/tools/sdk)에 대한 명령줄 인터페이스입니다. 
이 도구는 Dart SDK를 어떻게 받든 사용할 수 있습니다. 
즉, Dart SDK를 명시적으로 다운로드하든 [Flutter SDK]({{site.flutter}})만 다운로드하든 상관없습니다.

`dart` 도구를 사용하여 앱을 만들고, 분석하고, 테스트하고, 실행하는 방법은 다음과 같습니다.

```console
$ dart create -t console my_app
$ cd my_app
$ dart analyze
$ dart test
$ dart run bin/my_app.dart
```

`dart` 도구를 사용하여 [`pub` 명령][pub]을 실행할 수도 있습니다.

```console
$ dart pub get
$ dart pub outdated
$ dart pub upgrade
```

다음 표는 `dart` 도구와 함께 사용할 수 있는 명령을 보여줍니다. 
Flutter를 위해 개발하는 경우 대신, [`flutter` 도구][`flutter` tool]를 사용할 수 있습니다.

[`flutter` tool]: {{site.flutter-docs}}/reference/flutter-cli

<!-- <code>&#124;</code> -->

| 명령   | 사용 예시                                         | 더 많은 정보                                                                                                   |
|-----------|--------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| `analyze` | <code>dart analyze [<DIRECTORY&#124;DART_FILE>]</code> | 프로젝트의 Dart 소스 코드를 분석합니다.<br>[더 알아보기][analyze]                                                 |
| `compile` | `dart compile exe <DART_FILE>`                         | Dart를 다양한 포맷으로 컴파일합니다.<br>`dart2js`와 `dart2native`를 대체합니다.<br>[더 알아보기][compile]               | 
| `create`  | `dart create <DIRECTORY>`                              | 새로운 프로젝트를 만듭니다.<br>[더 알아보기][create]                                                                    | 
| `doc`     | `dart doc <DIRECTORY>`                                 | API 참조 문서를 생성합니다.<br>[`dartdoc`][]을 대체합니다.<br>[더 알아보기][doc]                            |
| `fix`     | <code>dart fix <DIRECTORY&#124;DART_FILE></code>       | Dart 소스 코드에 자동화된 수정 사항을 적용합니다.<br>[더 알아보기][fix]                                                 | 
| `format`  | <code>dart format <DIRECTORY&#124;DART_FILE></code>    | Dart 소스 코드를 포맷합니다.<br>[더 알아보기][format]                                                                 |
| `info`    | `dart info`                                            | Dart 툴링 진단 정보를 출력합니다.<br>[더 알아보기][info]                                                |
| `pub`     | `dart pub <PUB_COMMAND>`                               | 패키지와 함께 작동합니다.<br>`pub`를 대체합니다.<br>[더 알아보기][pub]                                                      | 
| `run`     | `dart run <DART_FILE>`                                 | Dart 프로그램을 실행합니다.<br>기존 Dart VM 명령(`dart`는 명령 없음)을 대체합니다.<br>[더 알아보기][run] | 
| `test`    | <code>dart test <DIRECTORY&#124;DART_FILE></code>      | 이 패키지에서 테스트를 실행합니다.<br>`pub run test`를 대체합니다.<br>[더 알아보기][test]                                     |
| _(none)_  | `dart <DART_FILE>`                                     | Dart 프로그램을 실행합니다. 기존 Dart VM 명령과 동일합니다.<br>[`dart run`][run]을 선호합니다.                   |

{:.table .table-striped .nowrap}

[analyze]: /tools/dart-analyze
[compile]: /tools/dart-compile
[create]: /tools/dart-create
[doc]: /tools/dart-doc
[fix]: /tools/dart-fix
[format]: /tools/dart-format
[info]: /tools/dart-info
[pub]: /tools/pub/cmd
[run]: /tools/dart-run
[test]: /tools/dart-test

명령어에 대한 도움말을 보려면, `dart help <command>`를 입력하세요. 
`pub` 명령어에 대한 세부 정보도 얻을 수 있습니다.

```console
$ dart help pub outdated
```

[`dartdoc`]: {{site.pub-pkg}}/dartdoc
