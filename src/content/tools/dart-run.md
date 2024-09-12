---
title: dart run
# description: Command-line tool for running a Dart program.
description: Dart 프로그램을 실행하기 위한 명령줄 도구.
---

`dart run` 명령은 명령줄에서 파일, 현재 패키지 또는 현재 패키지의 종속성 중 하나에 있는, 
Dart 프로그램을 실행하는 것을 지원합니다. 
이 명령은 이전에 `pub run` 및 Dart VM 도구에 있었던 기능을 제공합니다. 
임의의 위치에서 프로그램을 실행하려면, [pub global](/tools/pub/cmd/pub-global) 명령을 사용합니다.

```plaintext
dart run [options] [<DART_FILE> | <PACKAGE_TARGET>] [args]
```

새로운 앱을 만들고 실행하는 예는 다음과 같습니다.

```console
$ dart create myapp
$ cd myapp
$ dart run
```

{% render 'tools/dart-tool-note.md' %}

## Dart 파일 실행 {:#running-a-dart-file}

Dart 파일의 상대 경로를 전달하여 실행할 수 있습니다.

```console
$ dart run tool/debug.dart
```

## 패키지에 있는 프로그램 실행 {:#running-a-program-thats-in-a-package}

이 섹션의 지침은 Dart 패키지(_현재 패키지_)의 맨 위에 있는 디렉토리에서, 
`dart run` 명령을 실행한다고 가정합니다. 
Dart 패키지의 디렉토리 구조에 대한 정보는 [패키지 레이아웃 규칙](/guides/libraries/create-packages)을 참조하세요.

### 의존하는 패키지에서 {:#in-a-depended-on-package}

현재 패키지가 종속된 모든 패키지의 `bin` 디렉토리에 배포된 프로그램을 실행할 수 있습니다. 
이러한 프로그램을 실행하려면 종속된 패키지 이름과 프로그램 이름을 지정합니다. 
패키지 이름과 동일한 경우 프로그램 이름을 생략할 수 있습니다.

예를 들어 `bar` 패키지에 종속된 패키지의 최상위 디렉토리에 있다고 가정해 보겠습니다. 
`bar` 패키지(`bin/bar.dart`)에 있는 메인 프로그램을 실행하려면, 다음 명령을 사용할 수 있습니다.

```console
$ dart run bar
```

프로그램 이름이 패키지 이름과 일치하지 않으면, `<패키지 이름>:<프로그램 이름>` 형식을 사용합니다. 
예를 들어, `bar` 패키지에 있는 프로그램 `bin/baz.dart`를 실행하려면, 다음 명령을 사용합니다.

```console
$ dart run bar:baz
```

`bin` 디렉토리는 보이는 프로그램이 있는 유일한 장소입니다. 
종속된 패키지의 다른 모든 디렉토리는 private 입니다.

### 현재 패키지에서 {:#in-the-current-package}

현재 디렉토리가 패키지 이름과 일치할 때(즉, pubspec의 `name` 속성과 일치하는 디렉토리에 있을 때), 
패키지 이름을 생략할 수 있습니다. 
프로그램 이름이 패키지 이름과 일치할 때(즉, 메인 프로그램일 때), 프로그램 이름도 생략할 수 있습니다.

다음은 현재 패키지의 메인 프로그램을 실행하는 `dart run`의 가장 짧은 형식입니다. 
예를 들어, `foo` 패키지의 최상위 디렉토리에 있을 때, 이 명령은 `bin/foo.dart`를 실행합니다.

```console
$ dart run
```

프로그램 이름이 패키지 이름과 일치하지 않으면 콜론과 프로그램 이름을 추가합니다. 
예를 들어, 이 명령은 현재 패키지에서 `bin/baz.dart`를 실행합니다.

```console
$ dart run :baz
```

현재 패키지에 있지만 `bin` 디렉토리에는 없는 프로그램을 실행하려면, 상대 경로를 전달합니다. (앞서 보여준 대로)

```console
$ dart run tool/debug.dart
```

## main()에 인수 제공 {:#supplying-arguments-to-main}

[`main()` 함수에 인수][args]를 제공하려면, 명령의 끝에 다음과 같이 입력하세요.

```console
$ dart run tool/debug.dart arg1 arg2
```

현재 패키지의 메인 프로그램을 실행할 때 패키지 이름을 추가합니다. 
`foo` 패키지의 최상위 디렉토리에 있는 동안 인수와 함께, 
`bin/foo.dart`를 실행하는 예는 다음과 같습니다.

```console
$ dart run foo arg1 arg2
```

[args]: /language/functions#the-main-function

## 디버깅 {:#debugging}

디버깅을 활성화하려면, `dart run` 명령에 다음 일반적인 디버깅 옵션 중 하나 이상을 추가합니다.

- [`assert` 문][assert]을 활성화하려면, `--enable-asserts` 플래그를 추가합니다.

  ```console
  $ dart run --enable-asserts tool/debug.dart
  ```

- [Dart DevTools](/tools/dart-devtools)를 통해 디버깅 및 성능 분석을 활성화하려면, 
  `--observe` 플래그를 추가합니다.

  ```console
  $ dart run --observe tool/debug.dart
  ```

  Dart DevTools로 디버깅하는 방법에 대해 자세히 알아보려면, 
  [명령줄 앱에서 DevTools 사용][Using DevTools with a command-line app]을 참조하세요.

다른 디버깅 옵션에 대해 자세히 알아보려면, `dart run --help`를 실행하세요.

[assert]: /language/error-handling#assert
[Using DevTools with a command-line app]: /tools/dart-devtools#using-devtools-with-a-command-line-app

## 실험적 기능 활성화 {:#enabling-experimental-features}

현재 개발 중인 새로운 기능과 향상된 기능을 사용하려면, 
[실험 플래그](/tools/experiment-flags)를 사용하세요.
