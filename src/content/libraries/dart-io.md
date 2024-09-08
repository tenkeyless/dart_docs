---
title: dart:io
# description: Learn about the major features in Dart's dart:io library.
description: Dart의 dart:io 라이브러리의 주요 기능에 대해 알아보세요.
prevpage:
  url: /libraries/dart-convert
  title: dart:convert
nextpage:
  url: /libraries/dart-html
  title: dart:html
---

<?code-excerpt plaster="none"?>

[dart:io][] 라이브러리는 파일, 디렉토리, 프로세스, 소켓, 웹 소켓, HTTP 클라이언트와 서버를 처리하기 위한 API를 제공합니다.

:::important
웹 앱이 아닌 [Flutter 앱]({{site.flutter}}) 명령줄 스크립트, 
및 서버만 `dart:io`를 import하여 사용할 수 있으며, 웹 앱은 사용할 수 없습니다.
:::

일반적으로, dart:io 라이브러리는 비동기 API를 구현하고 promotes 합니다. 
동기 메서드는 애플리케이션을 쉽게 차단하여, 확장하기 어렵게 만들 수 있습니다. 
따라서, 대부분의 작업은 Future 또는 Stream 객체를 통해 결과를 반환하는데, 
이는 Node.js와 같은 최신 서버 플랫폼에서 일반적인 패턴입니다.

dart:io 라이브러리의 몇 가지 동기 메서드는 메서드 이름에 Sync 접미사로 명확하게 표시됩니다. 
동기 메서드는 여기에서 다루지 않습니다.

dart:io 라이브러리를 사용하려면 다음과 같이 import 해야 합니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (import)"?>
```dart
import 'dart:io';
```

## 파일 및 디렉토리 {:#files-and-directories}

I/O 라이브러리를 사용하면 명령줄 앱에서 파일을 읽고 쓰고 디렉토리를 탐색할 수 있습니다. 
파일의 내용을 읽는 데는 두 가지 선택이 있습니다. (1) 한 번에 모두 또는 (2) 스트리밍입니다. 
한 번에 파일을 읽으려면, 파일의 모든 내용을 저장할 만큼 충분한 메모리가 필요합니다. 
파일이 매우 크거나 읽는 동안 처리하려는 경우, 
[파일 내용 스트리밍](#streaming-file-contents)에 설명된 대로, 
Stream을 사용해야 합니다.

### [Future] 파일을 텍스트로 읽기 {:#reading-a-file-as-text}

UTF-8을 사용하여 인코딩된 텍스트 파일을 읽을 때, 
`readAsString()`로 전체 파일 내용을 읽을 수 있습니다. 
개별 줄이 중요한 경우, `readAsLines()`를 사용할 수 있습니다. 
두 경우 모두, 파일의 내용을 하나 이상의 문자열로 제공하는 Future 객체가 반환됩니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (read-as-string)" replace="/\btest_data\///g"?>
```dart
void main() async {
  var config = File('config.txt');

  // 전체 파일을 하나의 문자열로 넣습니다.
  var stringContents = await config.readAsString();
  print('The file is ${stringContents.length} characters long.');

  // 파일의 각 줄을 개별 문자열로 만듭니다.
  var lines = await config.readAsLines();
  print('The file is ${lines.length} lines long.');
}
```


### [Future] 파일을 바이너리로 읽기 {:#reading-a-file-as-binary}

다음 코드는 전체 파일을 바이트로 읽어서 int 리스트로 만듭니다. 
`readAsBytes()`를 호출하면 Future가 반환되고, 
Future는 available 할 때 결과를 제공합니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (read-as-bytes)" replace="/\btest_data\///g"?>
```dart
void main() async {
  var config = File('config.txt');

  var contents = await config.readAsBytes();
  print('The file is ${contents.length} bytes long.');
}
```

### [Future] 오류 처리 {:#handling-errors}

catch 되지 않는 예외가 발생하지 않도록 오류를 포착하려면, 
Future에 `catchError` 핸들러를 등록하거나, 
(`async` 함수에서) try-catch를 사용할 수 있습니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (try-catch)" replace="/does-not-exist/config/g"?>
```dart
void main() async {
  var config = File('config.txt');
  try {
    var contents = await config.readAsString();
    print(contents);
  } catch (e) {
    print(e);
  }
}
```

### [Stream] 스트리밍 파일 컨텐츠 {:#streaming-file-contents}

Stream을 사용하여 파일을 조금씩 읽습니다. 
[Stream API](/libraries/dart-async#stream) 또는, 
Dart의 [비동기 지원](/language/async)의 일부인 `await for`를 사용할 수 있습니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (read-from-stream)" replace="/_?test_\w*\/?//g"?>
```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  var config = File('config.txt');
  Stream<List<int>> inputStream = config.openRead();

  var lines = utf8.decoder.bind(inputStream).transform(const LineSplitter());
  try {
    await for (final line in lines) {
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
  } catch (e) {
    print(e);
  }
}
```

### 파일 컨텐츠 쓰기 {:#writing-file-contents}

[IOSink][]를 사용하여 파일에 데이터를 쓸 수 있습니다. 
File `openWrite()` 메서드를 사용하여 쓸 수 있는 IOSink를 가져옵니다. 
기본 모드인, `FileMode.write`는, 파일에 있는 기존 데이터를 완전히 덮어씁니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (write-file)" replace="/\btest_data\///g"?>
```dart
var logFile = File('log.txt');
var sink = logFile.openWrite();
sink.write('FILE ACCESSED ${DateTime.now()}\n');
await sink.flush();
await sink.close();
```

파일 끝에 추가하려면, 선택적 `mode` 매개변수를 사용하여, 
`FileMode.append`를 지정합니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (append)" replace="/_?test_\w*\/?//g"?>
```dart
var sink = logFile.openWrite(mode: FileMode.append);
```

바이너리 데이터를 작성하려면, `add(List<int> data)`를 사용합니다.


### 디렉토리에 있는 파일 나열하기 {:#listing-files-in-a-directory}

디렉토리의 모든 파일과 하위 디렉토리를 찾는 것은 비동기 연산입니다. 
`list()` 메서드는 파일이나 디렉토리가 발견되면, 
객체를 방출하는 Stream을 반환합니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (list-dir)" replace="/\btest_data\b/tmp/g"?>
```dart
void main() async {
  var dir = Directory('tmp');

  try {
    var dirList = dir.list();
    await for (final FileSystemEntity f in dirList) {
      if (f is File) {
        print('Found file ${f.path}');
      } else if (f is Directory) {
        print('Found dir ${f.path}');
      }
    }
  } catch (e) {
    print(e.toString());
  }
}
```

### 기타 일반적인 기능 {:#other-common-functionality}

File 및 Directory 클래스에는 다음을 포함하되, 이에 국한되지 않는 다른 기능이 포함되어 있습니다.

- 파일 또는 디렉토리 생성: File 및 Directory의 `create()`
- 파일 또는 디렉토리 삭제: File 및 Directory의 `delete()`
- 파일 길이 가져오기: File의 `length()`
- 파일에 대한 랜덤 액세스 가져오기: File의 `open()`

전체 메서드 리스트는 [File][] 및 [Directory][]의 API 문서를 참조하세요.

## HTTP 클라이언트 및 서버 {:#http-clients-and-servers}

dart:io 라이브러리는 명령줄 앱이 HTTP 리소스에 액세스하고, 
HTTP 서버를 실행하는 데 사용할 수 있는 클래스를 제공합니다.

### HTTP 서버 {:#http-server}

[HttpServer][] 클래스는 웹 서버를 구축하기 위한 낮은 레벨 기능을 제공합니다. 
요청 핸들러를 매치시키고, 헤더를 설정하고, 데이터를 스트리밍하는 등의 작업을 할 수 있습니다.

다음 샘플 웹 서버는 간단한 텍스트 정보를 반환합니다. 
이 서버는 포트 8888과 주소 127.0.0.1(localhost)에서 수신 대기하며, 
경로 `/dart`에 대한 요청에 응답합니다. 
다른 경로에 대한 응답은, 상태 코드 404(페이지를 찾을 수 없음)입니다.

<?code-excerpt "misc/lib/library_tour/io/http_server.dart (process-requests)" replace="/Future<\w+\W/void/g; /\b_//g"?>
```dart
void main() async {
  final requests = await HttpServer.bind('localhost', 8888);
  await for (final request in requests) {
    processRequest(request);
  }
}

void processRequest(HttpRequest request) {
  print('Got request for ${request.uri.path}');
  final response = request.response;
  if (request.uri.path == '/dart') {
    response
      ..headers.contentType = ContentType(
        'text',
        'plain',
      )
      ..write('Hello from the server');
  } else {
    response.statusCode = HttpStatus.notFound;
  }
  response.close();
}
```

### HTTP 클라이언트 {:#http-client}

`dart:io`를 직접 사용하여 HTTP 요청을 하는 것은 피해야 합니다. 
`dart:io`의 [HttpClient][] 클래스는 플랫폼에 따라 다르며, 단일 구현에 연결되어 있습니다. 
대신, [`package:http`]({{site.pub-pkg}}/http)와 같은 높은 레벨 라이브러리를 사용하세요.

[인터넷에서 데이터 가져오기][Fetch data from the internet] 튜토리얼은 `package:http`를 사용하여 HTTP 요청을 하는 방법을 설명합니다.

## 더 많은 정보 {:#more-information}

이 페이지에서는 [dart:io][] 라이브러리의 주요 기능을 사용하는 방법을 보여주었습니다. 
이 섹션에서 논의된 API 외에도, 
dart:io 라이브러리는 [Process][], [Socket][] 및 [웹 소켓][WebSocket]에 대한 API도 제공합니다. 
서버 측 및 명령줄 앱 개발에 대한 자세한 내용은, [서버 측 Dart 개요](/server)를 참조하세요.


[dart:io]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/dart-io-library.html
[Directory]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/Directory-class.html
[Fetch data from the internet]: /tutorials/server/fetch-data
[File]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/File-class.html
[HttpClient]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/HttpClient-class.html
[HttpRequest]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-html/HttpRequest-class.html
[HttpServer]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/HttpServer-class.html
[IOSink]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/IOSink-class.html
[Process]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/Process-class.html
[Socket]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/Socket-class.html
[WebSocket]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-io/WebSocket-class.html
