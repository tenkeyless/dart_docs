---
# title: Write command-line apps
title: 명령줄 앱 작성
# description: Explore a command-line application written in Dart.
description: Dart로 작성된 명령줄 애플리케이션을 살펴보세요.
nextpage:
  url: /tutorials/server/fetch-data
  # title: Fetch data from the internet
  title: 인터넷에서 데이터 가져오기
prevpage:
  url: /tutorials/server/get-started
  # title: "Get started: Command-line and server apps"
  title: "시작하기: 명령줄 및 서버 앱"
---

{% assign _api = site.dart-api | append: '/' | append: site.sdkInfo.channel -%}
{% assign argsAPI = site.pub-api | append: '/args/latest/args' -%}
{% assign ioAPI = _api | append: '/dart-io' -%}

<?code-excerpt replace="/ ?\/\/!tip.*//g"?>

:::secondary 요점은 무엇인가요?
* 명령줄 애플리케이션은 입력과 출력을 해야 합니다.
* `dart:io` 라이브러리는 I/O 기능을 제공합니다.
* args 패키지는 명령줄 인수를 정의하고, 구문 분석하는 데 도움이 됩니다.
* `Future` 객체는 미래의 어느 시점에 사용할 수 있는 값을 나타냅니다.
* 스트림은 일련의 비동기 데이터 이벤트를 제공합니다.
* 대부분의 입력과 출력에는 스트림을 사용해야 합니다.
:::

:::note
이 튜토리얼은 `async` 및 `await` 언어 기능을 사용하는데, 
이는 비동기 지원을 위해 [`Future`]({{_api}}/dart-async/Future-class.html) 및 [`Stream`]({{_api}}/dart-async/Stream-class.html) 클래스에 의존합니다. 
이러한 기능에 대해 자세히 알아보려면, 
[비동기 프로그래밍 튜토리얼](/libraries/async/async-await) 및 [스트림 튜토리얼](/libraries/async/using-streams)을 참조하세요.
:::

이 튜토리얼은 명령줄 앱을 빌드하는 방법을 알려주고, 몇 가지 작은 명령줄 애플리케이션을 보여줍니다. 
이러한 프로그램은 표준 출력, 오류 및 입력 스트림, 명령줄 인수, 파일 및 디렉터리 등을 포함하여, 
대부분의 명령줄 애플리케이션에 필요한 리소스를 사용합니다.

## 독립 실행형 Dart VM으로 앱 실행 {:#running-an-app-with-the-standalone-dart-vm}

Dart VM에서 명령줄 앱을 실행하려면 `dart run`을 사용합니다. 
`dart` 명령은 [Dart SDK](/tools/sdk)에 포함되어 있습니다.

:::important
SDK 설치 디렉토리의 위치(`<sdk-install-dir>`라고 부르겠습니다)는 플랫폼과 SDK를 설치한 방법에 따라 달라집니다. 
`dart`는 `<sdk-install-dir>/bin`에서 찾을 수 있습니다. 
이 디렉토리를 PATH에 넣으면, `dart` 명령을 이름으로 참조할 수 있습니다.
:::

작은 프로그램을 실행해 보겠습니다.

 1. `hello_world.dart`라는 파일을 만들고 여기에 다음 코드를 넣으세요.

    <?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
    ```dart
    void main() {
      print('Hello, World!');
    }
    ```

 2. 방금 만든 파일이 있는 디렉토리에서, 프로그램을 실행합니다.

    ```console
    $ dart run hello_world.dart
    Hello, World!
    ```

Dart 도구는 많은 명령과 옵션을 지원합니다. 
`dart --help`를 사용하여 일반적으로 사용되는 명령과 옵션을 확인하세요. 
`dart --verbose`를 사용하여 모든 옵션을 확인하세요.

## dcat 앱 코드 개요 {:#overview-of-the-dcat-app-code}

이 튜토리얼은 명령줄에 나열된 모든 파일의 내용을 표시하는, 
`dcat`이라는 작은 샘플 앱의 세부 사항을 다룹니다. 
이 앱은 명령줄 앱에서 사용할 수 있는 다양한 클래스, 함수 및 속성을 사용합니다. 
튜토리얼을 계속 진행하여 앱의 각 부분과 사용되는 다양한 API에 대해 알아보세요.

<?code-excerpt "cli/bin/dcat.dart (dcat-app)"?>
```dart
import 'dart:convert';
import 'dart:io';

import 'package:args/args.dart';

const lineNumber = 'line-number';

void main(List<String> arguments) {
  exitCode = 0; // 성공을 가정합니다
  final parser = ArgParser()..addFlag(lineNumber, negatable: false, abbr: 'n');

  ArgResults argResults = parser.parse(arguments);
  final paths = argResults.rest;

  dcat(paths, showLineNumbers: argResults[lineNumber] as bool);
}

Future<void> dcat(List<String> paths, {bool showLineNumbers = false}) async {
  if (paths.isEmpty) {
    // 인수로 제공된 파일이 없습니다. stdin에서 읽고 각 줄을 인쇄합니다.
    await stdin.pipe(stdout);
  } else {
    for (final path in paths) {
      var lineNumber = 1;
      final lines = utf8.decoder
          .bind(File(path).openRead())
          .transform(const LineSplitter());
      try {
        await for (final line in lines) {
          if (showLineNumbers) {
            stdout.write('${lineNumber++} ');
          }
          stdout.writeln(line);
        }
      } catch (_) {
        await _handleError(path);
      }
    }
  }
}

Future<void> _handleError(String path) async {
  if (await FileSystemEntity.isDirectory(path)) {
    stderr.writeln('error: $path is a directory');
  } else {
    exitCode = 2;
  }
}
```

### 종속성 얻기 {:#getting-dependencies}

dcat이 **args**라는 패키지에 종속되어 있다는 것을 알 수 있습니다. 
args 패키지를 얻으려면, [pub 패키지 관리자](/guides/packages)를 사용하세요.

실제 앱에는 테스트, 라이선스 파일, 종속성 파일, 예제 등이 있습니다. 
하지만 첫 번째 앱의 경우, 
[`dart create`](/tools/dart-create) 명령으로 필요한 것만 쉽게 만들 수 있습니다.

1. 디렉토리 내에서, dart 도구로 dcat 앱을 만듭니다.
   
   ```console
   $ dart create dcat
   ```
   
2. 생성된 디렉토리로 변경합니다.

   ```console
   $ cd dcat
   ```
   
3. `dcat` 디렉토리 내부에서, 
   [`dart pub add`](/tools/pub/cmd/pub-add)를 사용하여 `args` 패키지를 종속성으로 추가합니다. 
   이렇게 하면, `pubspec.yaml` 파일에서 찾은 종속성 리스트에 `args`가 추가됩니다.

   ```console
   $ dart pub add args
   ```

4. `bin/dcat.dart` 파일을 열고 이전 코드를 복사합니다.

:::note
패키지 사용과 코드 구성에 대한 자세한 내용은, 
[패키지 사용 방법](/guides/packages) 및 
[패키지 레이아웃 규칙](/tools/pub/package-layout)을 참조하세요.
:::

### dcat 실행 {:#running-dcat}

앱의 종속성이 있으면, `pubspec.yaml`과 같은 텍스트 파일을 통해, 명령줄에서 앱을 실행할 수 있습니다.

```console
$ dart run bin/dcat.dart -n pubspec.yaml
1 name: dcat
2 description: A sample command-line application.
3 version: 1.0.0
4 # repository: https://github.com/my_org/my_repo
5 
6 environment:
7   sdk: ^3.5.0
8 
9 # Add regular dependencies here.
10 dependencies:
11   args: ^2.4.2
12   # path: ^1.8.0
13 
14 dev_dependencies:
15   lints: ^4.0.0
16   test: ^1.24.0
```

이 명령은 지정된 파일의 각 줄을 표시합니다. 
`-n` 옵션을 지정했기 때문에 각 줄 앞에 줄 번호가 표시됩니다.

## 명령줄 인수 구문 분석 {:#parsing-command-line-arguments}

[args 패키지]({{site.pub-pkg}}/args)는 
명령줄 인수를 옵션, 플래그 및 추가 값 세트로 변환하기 위한 파서 지원을 제공합니다. 
다음과 같이, 패키지의 [args 라이브러리]({{argsAPI}}/args-library.html)를 import 합니다.

<?code-excerpt "cli/bin/dcat.dart" retain="package:args"?>
```dart
import 'package:args/args.dart';
```

`args` 라이브러리에는 다음과 같은 클래스가 포함되어 있습니다.

| 클래스                                           | 설명                                                     |
|-------------------------------------------------|-----------------------------------------------------------------|
| [ArgParser]({{argsAPI}}/ArgParser-class.html)   | 명령줄 인수 파서.                                 |
| [ArgResults]({{argsAPI}}/ArgResults-class.html) | `ArgParser`를 사용하여 명령줄 인수를 구문 분석한 결과. |

{: .table }

`dcat` 앱의 다음 코드는 이러한 클래스를 사용하여, 
지정된 명령줄 인수를 구문 분석하고 저장합니다.

<?code-excerpt "cli/bin/dcat.dart (arg-processing)" plaster="none" replace="/(ArgR.*|List[^\)]*|\..*|parser.*|argResults\S[^);]+)/[!$&!]/g"?>
```dart
void main([!List<String> arguments!]) {
  exitCode = 0; // 성공을 가정합니다.
  final [!parser = ArgParser()..addFlag(lineNumber, negatable: false, abbr: 'n');!]

  [!ArgResults argResults = parser.parse(arguments);!]
  final paths = [!argResults.rest!];

  dcat(paths, showLineNumbers: [!argResults[lineNumber] as bool!]);
}
```

Dart 런타임은 명령줄 인수를 문자열 리스트로 앱의 `main` 함수에 전달합니다. 
`ArgParser`는 `-n` 옵션을 구문 분석하도록 구성됩니다. 
그런 다음, 명령줄 인수를 구문 분석한 결과가 `argResults`에 저장됩니다.

다음 다이어그램은 위에서 사용된 `dcat` 명령줄이 `ArgResults` 객체로 구문 분석되는 방식을 보여줍니다.

![Run dcat from the command-line](/assets/img/tutorials/server/dcat-dart-run.svg){:width="350"}

`ArgResults`를 `Map`처럼 취급하여, 
이름으로 플래그와 옵션에 액세스할 수 있습니다. 
`rest` 속성을 사용하여 다른 값에 액세스할 수 있습니다.

`args` 라이브러리의 [API 참조]({{argsAPI}}/args-library.html)는 
`ArgParser` 및 `ArgResults` 클래스를 사용하는 데 도움이 되는 자세한 정보를 제공합니다.

## stdin, stdout 및 stderr을 사용하여 읽기 및 쓰기 {:#reading-and-writing-with-stdin-stdout-and-stderr}

다른 언어와 마찬가지로, Dart에는 표준 출력, 표준 오류 및 표준 입력 스트림이 있습니다. 
표준 I/O 스트림은 `dart:io` 라이브러리의 최상위 레벨에서 정의됩니다.

| Stream                          | 설명         |
|---------------------------------|---------------------|
| [stdout]({{ioAPI}}/stdout.html) | 표준 출력 |
| [stderr]({{ioAPI}}/stderr.html) | 표준 오류  |
| [stdin]({{ioAPI}}/stdin.html)   | 표준 입력  |

{: .table }

다음과 같이 `dart:io` 라이브러리를 import 합니다.

<?code-excerpt "cli/bin/dcat.dart" retain="dart:io"?>
```dart
import 'dart:io';
```

:::note
웹 앱(`dart:html`에 의존하는 앱)은 `dart:io` 라이브러리를 사용할 수 없습니다.
:::

### stdout {:#stdout}

`dcat` 앱의 다음 코드는 줄 번호를 `stdout`에 쓰고( `-n` 옵션이 지정된 경우), 
그 뒤에 파일의 줄 내용을 씁니다.

<?code-excerpt "cli/bin/dcat.dart (show-line-numbers)" replace="/stdout\..*/[!$&!]/g"?>
```dart
if (showLineNumbers) {
  [!stdout.write('${lineNumber++} ');!]
}
[!stdout.writeln(line);!]
```

`write()` 및 `writeln()` 메서드는 모든 타입의 객체를 가져와, 문자열로 변환한 다음 출력합니다. 
`writeln()` 메서드는 줄 바꿈 문자도 출력합니다. 
`dcat` 앱은 `write()` 메서드를 사용하여 줄 번호를 출력하므로, 줄 번호와 텍스트가 같은 줄에 나타납니다.

`writeAll()` 메서드를 사용하여 객체 리스트를 출력하거나, 
`addStream()`을 사용하여 스트림의 모든 요소를 ​​비동기적으로 출력할 수도 있습니다.

`stdout`은 `print()` 함수보다 더 많은 기능을 제공합니다. 
예를 들어, `stdout`으로 스트림의 내용을 표시할 수 있습니다. 
그러나, 웹에서 실행되는 앱의 경우 `stdout` 대신 `print()`를 사용해야 합니다.

### stderr {:#stderr}

`stderr`를 사용하여 콘솔에 오류 메시지를 씁니다. 
표준 오류 스트림은 `stdout`와 동일한 메서드를 가지고 있으며, 같은 방식으로 사용합니다. 
`stdout`와 `stderr`는 모두 콘솔에 출력하지만, 
출력은 별개이며 명령줄이나 프로그래밍 방식으로 다른 대상으로 리디렉션되거나 파이프될 수 있습니다.

`dcat` 앱의 다음 코드는, 사용자가 파일 대신 디렉토리의 줄을 출력하려고 하면, 오류 메시지를 출력합니다.

<?code-excerpt "cli/bin/dcat.dart (await-entity)" replace="/stderr\..*/[!$&!]/g"?>
```dart
if (await FileSystemEntity.isDirectory(path)) {
  [!stderr.writeln('error: $path is a directory');!]
} else {
  exitCode = 2;
}
```

### stdin {:#stdin}

표준 입력 스트림은 일반적으로 키보드에서 동기적으로 데이터를 읽지만, 
비동기적으로 읽고 다른 프로그램의 표준 출력에서 ​​파이프로 입력을 받을 수도 있습니다.

`stdin`에서 한 줄을 읽는 작은 프로그램은 다음과 같습니다.

<?code-excerpt "cli/bin/dcat_stdin.dart"?>
```dart
import 'dart:io';

void main() {
  stdout.writeln('Type something');
  final input = stdin.readLineSync();
  stdout.writeln('You typed: $input');
}
```

`readLineSync()` 메서드는 표준 입력 스트림에서, 
텍스트를 읽고 사용자가 텍스트를 입력하고 리턴을 누를 때까지, 차단(blocking)합니다. 
이 작은 프로그램은 입력된 텍스트를 출력합니다.

`dcat` 앱에서, 사용자가 명령줄에 파일 이름을 제공하지 않으면, 
프로그램은 대신 `pipe()` 메서드를 사용하여 stdin에서 읽습니다. 
`pipe()`는 비동기적(이 코드는 해당 반환 값을 사용하지 않지만 `Future`를 반환)이기 때문에, 
이를 호출하는 코드는 `await`를 사용합니다.

<?code-excerpt "cli/bin/dcat.dart (pipe)" replace="/pipe/[!$&!]/g"?>
```dart
await stdin.[!pipe!](stdout);
```

이 경우, 사용자는 텍스트 줄을 입력하고, 앱은 이를 stdout에 복사합니다. 
사용자는 <kbd>Control</kbd>+<kbd>D</kbd>(또는 Windows에서는 <kbd>Control</kbd>+<kbd>Z</kbd>)를 눌러 입력 종료를 알립니다.

```console
$ dart run bin/dcat.dart
The quick brown fox jumps over the lazy dog.
The quick brown fox jumps over the lazy dog.
```

## 파일 정보 얻기 {:#getting-info-about-a-file}

`dart:io` 라이브러리의 [`FileSystemEntity`]({{ioAPI}}/FileSystemEntity-class.html) 클래스는,
파일 시스템을 검사하고 조작하는 데 도움이 되는 속성과 정적 메서드를 제공합니다.

예를 들어, 경로가 있는 경우, `FileSystemEntity` 클래스의 `type()` 메서드를 사용하여, 
경로가 파일인지, 디렉토리인지, 링크인지 또는 찾을 수 없는지 확인할 수 있습니다. 
`type()` 메서드는 파일 시스템에 액세스하므로 비동기적으로 검사를 수행합니다.

`dcat` 앱의 다음 코드는 `FileSystemEntity`를 사용하여, 명령줄에 제공된 경로가 디렉토리인지 확인합니다. 
반환된 `Future`는 경로가 디렉토리인지 여부를 나타내는 boolean으로 완료됩니다. 
검사가 비동기적이므로 코드는 `await`를 사용하여, `isDirectory()`를 호출합니다.

<?code-excerpt "cli/bin/dcat.dart (await-entity)" replace="/await.*path\)/[!$&!]/g"?>
```dart
if ([!await FileSystemEntity.isDirectory(path)!]) {
  stderr.writeln('error: $path is a directory');
} else {
  exitCode = 2;
}
```

`FileSystemEntity` 클래스의 다른 흥미로운 메서드로는, 
`isFile()`, `exists()`, `stat()`, `delete()`, `rename()`가 있으며, 
이 모두는 `Future`를 사용하여 값을 반환합니다.

`FileSystemEntity`는 `File`, `Directory`, `Link` 클래스의 슈퍼클래스입니다.

## 파일 읽기 {:#reading-a-file}

`dcat` 앱은 명령줄에 나열된 각 파일을 `openRead()` 메서드로 열고, 이 메서드는 `Stream`을 반환합니다. 
`await for` 블록은 파일이 비동기적으로 읽히고 디코딩될 때까지 기다립니다. 
스트림에서 데이터를 사용할 수 있게 되면, 앱은 이를 stdout에 출력합니다.

<?code-excerpt "cli/bin/dcat.dart (for-path)" remove="/^\s*\/\/!tip.*/" replace="/(    )((await for| *stdout| *if| *}).*)/$1[!$2!]/g"?>
```dart
for (final path in paths) {
  var lineNumber = 1;
  final lines = utf8.decoder
      .bind(File(path).openRead())
      .transform(const LineSplitter());
  try {
    [!await for (final line in lines) {!]
    [!  if (showLineNumbers) {!]
    [!    stdout.write('${lineNumber++} ');!]
    [!  }!]
    [!  stdout.writeln(line);!]
    [!}!]
  } catch (_) {
    await _handleError(path);
  }
}
```

다음은 나머지 코드를 강조한 것으로, 
`await for` 블록에서 사용 가능하게 하기 전에 데이터를 변환하는 두 개의 디코더를 사용합니다. 
UTF8 디코더는 데이터를 Dart 문자열로 변환합니다. 
`LineSplitter`는 줄바꿈에서 데이터를 분할합니다.

<?code-excerpt "cli/bin/dcat.dart (for-path)" remove="/^\s*\/\/!tip.*/" replace="/utf8.decoder|LineSplitter()/[!$&!]/g"?>
```dart
for (final path in paths) {
  var lineNumber = 1;
  final lines = [!utf8.decoder!]
      .bind(File(path).openRead())
      .transform(const [!LineSplitter!]());
  try {
    await for (final line in lines) {
      if (showLineNumbers) {
        stdout.write('${lineNumber++} ');
      }
      stdout.writeln(line);
    }
  } catch (_) {
    await _handleError(path);
  }
}
```

`dart:convert` 라이브러리는 JSON을 포함한 이러한 데이터 변환기와 다른 데이터 변환기를 제공합니다. 
이러한 변환기를 사용하려면 `dart:convert` 라이브러리를 import 해야 합니다.

<?code-excerpt "cli/bin/dcat.dart" retain="dart:convert"?>
```dart
import 'dart:convert';
```

## 파일에 쓰기 {:#writing-to-a-file}

파일에 텍스트를 쓰는 가장 쉬운 방법은 [`File`]({{ioAPI}}/File-class.html) 객체를 생성하고, 
`writeAsString()` 메서드를 사용하는 것입니다.

<?code-excerpt "cli/lib/cmdline.dart (write-quote)"?>
```dart
final quotes = File('quotes.txt');
const stronger = 'That which does not kill us makes us stronger. -Nietzsche';

await quotes.writeAsString(stronger, mode: FileMode.append);
```

`writeAsString()` 메서드는 비동기적으로 데이터를 씁니다. 
쓰기 전에 파일을 열고, 완료되면 파일을 닫습니다. 
기존 파일에 데이터를 추가하려면, 선택적 명명된 매개변수 `mode`를 사용하고, 
해당 값을 `FileMode.append`로 설정할 수 있습니다. 
그렇지 않으면, 모드가 기본적으로 `FileMode.write`로 설정되고, 
파일의 이전 내용(있는 경우)이 덮어쓰여집니다.

더 많은 데이터를 쓰려면 쓰기 위해 파일을 열 수 있습니다. 
`openWrite()` 메서드는 stdin 및 stderr와 동일한 타입을 가진 `IOSink`를 반환합니다. 
`openWrite()`에서 반환된 `IOSink`를 사용하는 경우, 
완료될 때까지 파일에 계속 쓸 수 있으며, 완료되면 파일을 수동으로 닫아야 합니다. 
`close()` 메서드는 비동기적이며 `Future`를 반환합니다.

<?code-excerpt "cli/lib/cmdline.dart (write-quotes)"?>
```dart
final quotes = File('quotes.txt').openWrite(mode: FileMode.append);

quotes.write("Don't cry because it's over, ");
quotes.writeln('smile because it happened. -Dr. Seuss');
await quotes.close();
```

## 환경 정보 얻기 {:#getting-environment-information}

{% assign PlatformAPI = ioAPI | append: '/Platform' -%}

[`Platform`]({{PlatformAPI}}-class.html) 클래스를 사용하여, 
앱이 실행되는 머신과 운영 체제에 대한 정보를 가져옵니다.

정적 [`Platform.environment`]({{PlatformAPI}}/environment.html) 속성은, 
불변(immutable) 맵에서 환경 변수의 복사본을 제공합니다. 
변경 가능한(mutable) 맵(수정 가능한 복사본)이 필요한 경우, 
`Map.of(Platform.environment)`를 사용할 수 있습니다.

<?code-excerpt "cli/lib/cmdline.dart (env)"?>
```dart
final envVarMap = Platform.environment;

print('PWD = ${envVarMap['PWD']}');
print('LOGNAME = ${envVarMap['LOGNAME']}');
print('PATH = ${envVarMap['PATH']}');
```

`Platform`은 머신, OS, 현재 실행 중인 앱에 대한 정보를 제공하는 다른 유용한 속성을 제공합니다. 예를 들어:

- [`Platform.isMacOS()`]({{PlatformAPI}}/isMacOS.html)
- [`Platform.numberOfProcessors`]({{PlatformAPI}}/numberOfProcessors.html)
- [`Platform.script`]({{PlatformAPI}}/script.html)

## 종료 코드 설정 {:#setting-exit-codes}

`dart:io` 라이브러리는, Dart VM의 현재 호출에 대한 종료 코드를 설정하기 위해 변경할 수 있는, 
최상위 속성인 `exitCode`를 정의합니다. 
종료 코드는 Dart 앱에서 부모 프로세스로 전달되는 숫자로, 
앱 실행의 성공, 실패 또는 기타 상태를 나타냅니다.

`dcat` 앱은 `_handleError()` 함수에서 종료 코드를 설정하여, 실행 중에 오류가 발생했음을 나타냅니다.

<?code-excerpt "cli/bin/dcat.dart (handle-error)" remove="/^\s*\/\/!tip.*/" replace="/exit.*;/[!$&!]/g"?>
```dart
Future<void> _handleError(String path) async {
  if (await FileSystemEntity.isDirectory(path)) {
    stderr.writeln('error: $path is a directory');
  } else {
    [!exitCode = 2;!]
  }
}
```

종료 코드 `2`는 앱에 오류가 발생했음을 나타냅니다.

`exitCode`를 사용하는 것의 대안은, 최상위 `exit()` 함수를 사용하는 것입니다. 
이 함수는 종료 코드를 설정하고 앱을 즉시 종료합니다. 
예를 들어, `_handleError()` 함수는 `exitCode`를 2로 설정하는 대신, `exit(2)`를 호출할 수 있지만, 
`exit()`는 프로그램을 종료하고, 실행 중인 명령에서 지정한 모든 파일을 처리하지 못할 수 있습니다.

:::note
일반적으로, `exitCode` 속성을 사용하는 것이 더 좋습니다. 
이 속성은 종료 코드를 설정하지만, 프로그램이 자연스러운 완료까지 계속 진행되도록 허용합니다.
:::

종료 코드에는 아무 숫자나 사용할 수 있지만, 관례에 따라, 아래 표의 코드는 다음과 같은 의미를 갖습니다.

| 코드 | 의미  |
|------|----------|
| 0    | 성공  |
| 1    | 경고 |
| 2    | 에러   |


{: .table }

## 요약 {:#summary}

이 튜토리얼에서는 `dart:io` 라이브러리의 다음 클래스에서 발견되는 몇 가지 기본 API를 설명했습니다.

| API | 설명 |
|---|---|
| [`IOSink`]({{ioAPI}}/IOSink-class.html) | 스트림에서 데이터를 사용하는 객체를 위한 헬퍼 클래스 |
| [`File`]({{ioAPI}}/File-class.html) | 네이티브 파일 시스템의 파일을 나타냅니다 |
| [`Directory`]({{ioAPI}}/Directory-class.html) | 네이티브 파일 시스템의 디렉토리를 나타냅니다. |
| [`FileSystemEntity`]({{ioAPI}}/FileSystemEntity-class.html) | 파일 및 디렉토리의 슈퍼클래스 |
| [`Platform`]({{ioAPI}}/Platform-class.html) | 기계 및 운영 체제에 대한 정보를 제공합니다 |
| [`stdout`]({{ioAPI}}/stdout.html) | 표준 출력 스트림 |
| [`stderr`]({{ioAPI}}/stderr.html) | 표준 오류 스트림 |
| [`stdin`]({{ioAPI}}/stdin.html) | 표준 입력 스트림 |
| [`exitCode`]({{ioAPI}}/exitCode.html) | 종료 코드에 접근하여 설정하세요 |
| [`exit()`]({{ioAPI}}/exit.html) | 종료 코드를 설정하고 종료합니다. |

{: .table }

또한, 이 튜토리얼에서는 명령줄 인수를 구문 분석하고 사용하는 데 도움이 되는 `package:args`의 두 클래스인, 
[`ArgParser`]({{argsAPI}}/ArgParser-class.html)와 
[`ArgResults`]({{argsAPI}}/ArgResults-class.html)를 다루었습니다.

더 많은 클래스, 함수 및 속성은 [`dart:io`]({{ioAPI}}/dart-io-library.html), [`dart:convert`]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-convert/dart-convert-library.html), [`package:args`]({{argsAPI}}/args-library.html)의 API 문서를 참조하세요.

명령줄 앱의 또 다른 예는 [`command_line`][] 샘플을 확인하세요.

[`command_line`]: {{site.repo.dart.org}}/samples/tree/main/command_line

## 다음은 무엇입니까? {:#what-next}

서버 사이드 프로그래밍에 관심이 있으시다면, [다음 튜토리얼](/tutorials/server/httpserver)을 확인해 보세요.
