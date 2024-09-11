---
# title: Fetch data from the internet
title: 인터넷에서 데이터 가져오기
# description: Fetch data over the internet using the http package.
description: http 패키지를 사용하여 인터넷에서 데이터를 가져옵니다.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
prevpage:
  url: /tutorials/server/cmdline
  # title: Write command-line apps
  title: 명령줄 앱 작성
nextpage:
  url: /tutorials/server/httpserver
  # title: Write HTTP servers
  title: HTTP 서버 작성
---

<?code-excerpt path-base="fetch_data"?>

:::secondary 학습할 내용
* HTTP 요청과 URI의 기본 사항과 용도
* `package:http`를 사용하여, HTTP 요청 만들기
* `dart:convert`를 사용하여, JSON 문자열을 Dart 객체로 디코딩
* JSON 객체를 클래스 기반 구조로 변환
:::

대부분의 애플리케이션은 인터넷에서 어떤 형태의 통신이나 데이터 검색이 필요합니다. 
많은 앱은 HTTP 요청을 통해 이를 수행하는데, 이 요청은 클라이언트에서 서버로 전송되어, 
[URI][](Uniform Resource Identifier)를 통해 식별된 리소스에 대한 특정 작업을 수행합니다.

HTTP를 통해 통신되는 데이터는 기술적으로 어떤 형태이든 가능하지만, 
[JSON][](JavaScript Object Notation)을 사용하는 것이, 
사람이 읽기 쉽고 언어에 독립적인 특성이 있어 인기 있는 선택입니다. 
Dart SDK와 생태계는 또한 앱의 요구 사항을 가장 잘 충족시키는 여러 옵션으로, 
JSON에 대한 광범위한 지원을 제공합니다.

이 튜토리얼에서는, HTTP 요청, URI, JSON에 대해 자세히 알아봅니다. 
그런 다음, [`package:http`][http-pub]와 [`dart:convert`][convert-docs] 라이브러리에서 Dart의 JSON 지원을 사용하여, 
HTTP 서버에서 검색된 JSON 형식의 데이터를 페치, 디코딩한 다음 사용하는 방법을 알아봅니다.

[JSON]: https://www.json.org/

## 백그라운드 개념 {:#background-concepts}

다음 섹션에서는 튜토리얼에서 서버에서 데이터를 가져오는 것을 용이하게 하기 위해, 
사용된 기술과 개념에 대한 몇 가지 추가 배경과 정보를 제공합니다. 
튜토리얼 콘텐츠로 바로 건너뛰려면 [필요한 종속성 검색][Retrieve the necessary dependencies]을 참조하세요.

[Retrieve the necessary dependencies]: #retrieve-the-necessary-dependencies

### JSON {:#json}

JSON(JavaScript Object Notation)은 애플리케이션 개발과 클라이언트-서버 통신에서 널리 사용되는 데이터 교환 형식입니다. 
가볍지만, 텍스트 기반이기 때문에 사람이 읽고 쓰기도 쉽습니다. 
JSON을 사용하면, 다양한 데이터 타입과 리스트 및 맵과 같은 간단한 데이터 구조를 직렬화하여, 
문자열로 표현할 수 있습니다.

대부분 언어에는 구현이 많고 파서는 매우 빠르기 때문에, 
상호 운용성이나 성능에 대해 걱정할 필요가 없습니다. 
JSON 형식에 대한 자세한 내용은 [JSON 소개][Introducing JSON]를 참조하세요. 
Dart에서 JSON 작업에 대한 자세한 내용은 [JSON 사용][Using JSON] 가이드를 참조하세요.

:::secondary
모바일 플랫폼별 구현을 갖춘 다른 두 패키지가 있습니다.

* [cronet_http]({{site.pub-pkg}}/cronet_http)는 
  Android [Cronet][] HTTP 클라이언트에 대한 액세스를 제공합니다.
* [cupertino_http]({{site.pub-pkg}}/cupertino_http)는 
  Apple의 [Foundation URL Loading System(furl)][furl]에 대한 액세스를 제공합니다.

해당 기능에 대한 자세한 내용은 패키지 문서를 참조하세요.
:::

[Cronet]: {{site.android-dev}}/develop/connectivity/cronet
[furl]: {{site.apple-dev}}/documentation/foundation/url_loading_system
[Introducing JSON]: https://www.json.org/

### HTTP 요청 {:#http-requests}

HTTP(Hypertext Transfer Protocol)는, 원래 웹 클라이언트와 웹 서버 간에 문서를 전송하도록 설계된, 상태 없는 프로토콜입니다. 
브라우저가 HTTP `GET` 요청을 사용하여, 
웹 서버에서 페이지의 내용을 검색하기 때문에, 
이 페이지를 로드하기 위해 프로토콜과 상호 작용했습니다. 
HTTP 프로토콜과 다양한 버전이 도입된 이후로, 
웹 외부의 애플리케이션에도 사용이 확대되었으며, 
기본적으로 클라이언트에서 서버로 통신해야 하는 모든 곳에서 사용됩니다.

서버와 통신하기 위해 클라이언트에서 보낸 HTTP 요청은 여러 구성 요소로 구성됩니다. 
`package:http`와 같은 HTTP 라이브러리를 사용하면, 다음과 같은 종류의 통신을 지정할 수 있습니다.

* (데이터를 검색하기 위한 `GET` 또는 새 데이터를 제출하기 위한 `POST`와 같이) 
  원하는 작업을 정의하는 HTTP 메서드.
* URI를 통한 리소스의 위치.
* 사용 중인 HTTP의 버전.
* 서버에 추가 정보를 제공하는 헤더.
* 요청이 데이터를 검색할 뿐만 아니라 서버로 데이터를 보낼 수 있도록 하는 선택적 본문.

HTTP 프로토콜에 대해 자세히 알아보려면, mdn 웹 문서의 [HTTP 개요][An overview of HTTP]를 확인하세요.

[An overview of HTTP]: https://developer.mozilla.org/docs/Web/HTTP/Overview

### URIs 및 URLs {:#uris-and-urls}

HTTP 요청을 하려면, 리소스에 [URI][] (Uniform Resource Identifier)를 제공해야 합니다. 
URI는 리소스를 고유하게 식별하는 문자열입니다. 
URL(Uniform Resource Locator)은 리소스의 위치도 제공하는 특정 종류의 URI입니다. 
웹의 리소스에 대한 URL에는 세 가지 정보가 들어 있습니다. 
현재 페이지의 URL은 다음으로 구성됩니다.

* 사용된 프로토콜을 결정하는 데 사용된 체계: `https`
* 서버의 권한 또는 호스트 이름: `dart.dev`
* 리소스 경로: `/tutorials/server/fetch-data.html`

현재 페이지에서 사용하지 않는, 다른 선택적 매개변수도 있습니다.

* 추가 동작을 커스터마이즈하는 매개변수: `?key1=value1&key2=value2`
* 리소스의 특정 위치를 가리키는, 서버로 전송되지 않는 앵커: `#uris`

URL에 대해 자세히 알아보려면, mdn 웹 문서의 [URL이란?][What is a URL?]을 참조하세요.

[What is a URL?]: https://developer.mozilla.org/docs/Learn/Common_questions/What_is_a_URL

## 필요한 종속성을 검색합니다 {:#retrieve-the-necessary-dependencies}

`package:http` 라이브러리는 선택적으로 세부적인 제어를 통해, 
구성 가능한 HTTP 요청을 만드는 크로스 플랫폼 솔루션을 제공합니다.

:::note
HTTP 요청을 하기 위해 `dart:io` 또는 `dart:html`을 직접 사용하는 것은 피해야 합니다. 
이러한 라이브러리는 플랫폼에 따라 달라지며 단일 구현에 묶여 있습니다.
:::

`package:http`에 종속성을 추가하려면, 
리포지토리 상단에서 다음 [`dart pub add`][] 명령을 실행하세요.

```console
$ dart pub add http
```

코드에서 `package:http`를 사용하려면, 
이를 import 하고 선택적으로 [라이브러리 접두사 지정][specify a library prefix]을 수행하세요.

<?code-excerpt "lib/fetch_data.dart (http-import)"?>
```dart
import 'package:http/http.dart' as http;
```

`package:http`에 대한 자세한 내용을 알아보려면, 
[pub.dev 사이트의 페이지][http-pub]와 [API 문서][http-docs]를 참조하세요.

[`dart pub add`]: /tools/pub/cmd/pub-add
[specify a library prefix]: /language/libraries#specifying-a-library-prefix

## URL 빌드 {:#build-a-url}

이전에 언급했듯이 HTTP 요청을 하려면, 먼저 요청되는 리소스나 액세스되는 엔드포인트를 식별하는 URL이 필요합니다.

Dart에서, URL은 [`Uri`][] 객체를 통해 표현됩니다. 
`Uri`를 빌드하는 방법은 여러 가지가 있지만, 유연성 때문에, 
`Uri.parse`로 문자열을 구문 분석하여 하나를 만드는 것이 일반적인 솔루션입니다.

다음 스니펫은 이 사이트에 호스팅된 `package:http`에 대한 mock JSON 형식 정보를 가리키는, 
`Uri` 객체를 만드는 두 가지 방법을 보여줍니다.

<?code-excerpt "lib/fetch_data.dart (build-uris)"?>
```dart
// 스키마를 포함한, 전체 URI를 구문 분석합니다.
Uri.parse('https://dart.dev/f/packages/http.json');

// 특히 https 체계를 사용하여 URI를 생성합니다.
Uri.https('dart.dev', '/f/packages/http.json');
```

URI를 빌드하고 상호 작용하는 다른 방법에 대해 알아보려면, [`URI` 문서][`URI` documentation]를 ​​참조하세요.

[`Uri`]: {{site.dart-api}}/dart-core/Uri-class.html
[`URI` documentation]: /libraries/dart-core#uris

## 네트워크 요청하기 {:#make-a-network-request}

요청된 리소스의 문자열 표현을 빠르게 페치해야 하는 경우, 
`package:http`에서 찾을 수 있는 최상위 [`read`][http-read] 함수를 사용할 수 있습니다. 
이 함수는 `Future<String>`를 반환하거나, 
요청이 성공하지 못한 경우 [`ClientException`][http-client-exc]를 throw합니다. 
다음 예제에서는 `read`를 사용하여, 
`package:http`에 대한 mock JSON 형식의 정보를 문자열로 검색한 다음 출력합니다.

:::note
`read`를 포함한 `package:http`의 많은 함수는, 
네트워크에 액세스하고 잠재적으로 시간이 많이 걸리는 작업을 수행하므로, 
비동기적으로 이를 수행하고 [`Future`][]를 반환합니다. 
아직 future를 접하지 못했다면, [비동기 프로그래밍 튜토리얼](/libraries/async/async-await)에서, 
future와 `async` 및 `await` 키워드에 대해 알아볼 수 있습니다.
:::

<?code-excerpt "lib/fetch_data.dart (http-read)" replace="/readMain/main/g; /(http\.read)/[!$1!]/g"?>
```dart
void main() async {
  final httpPackageUrl = Uri.https('dart.dev', '/f/packages/http.json');
  final httpPackageInfo = await [!http.read!](httpPackageUrl);
  print(httpPackageInfo);
}
```

그러면 다음과 같은 JSON 형식의 출력이 생성되고, 
브라우저에서 [`/f/packages/http.json`][mock-http-json]으로 볼 수 있습니다.

```json
{
  "name": "http",
  "latestVersion": "1.1.2",
  "description": "A composable, multi-platform, Future-based API for HTTP requests.",
  "publisher": "dart.dev",
  "repository": "https://github.com/dart-lang/http"
}
```  

나중에 JSON을 디코딩할 때 필요하므로, 데이터 구조(이 경우 맵)를 기록해 두세요.

([상태 코드][status code] 또는 [헤더][headers]와 같이) 응답에서 다른 정보가 필요한 경우, 
대신 최상위 [`get`][http-get] 함수를 사용할 수 있습니다. 
이 함수는 [`Response`][http-response]와 함께 `Future`를 반환합니다.

다음 스니펫은 `get`을 사용하여 요청이 성공하지 못한 경우, 
조기에 종료하기 위해 전체 응답을 가져오며, 
이는 상태 코드 **200**으로 표시됩니다.

<?code-excerpt "lib/fetch_data.dart (http-get)" replace="/getMain/main/g"?>
```dart
void main() async {
  final httpPackageUrl = Uri.https('dart.dev', '/f/packages/http.json');
  final httpPackageResponse = await http.get(httpPackageUrl);
  if (httpPackageResponse.statusCode != 200) {
    print('Failed to retrieve the http package!');
    return;
  }
  print(httpPackageResponse.body);
}
```

**200** 외에도 많은 다른 상태 코드가 있으며, 앱에서는 이를 다르게 처리하고 싶을 수 있습니다. 
다양한 상태 코드가 무엇을 의미하는지 자세히 알아보려면, 
mdn 웹 문서에서 [HTTP 응답 상태 코드][HTTP response status codes]를 참조하세요.

일부 서버 요청은 인증이나 사용자 에이전트 정보와 같은 추가 정보가 필요합니다. 
이 경우 [HTTP 헤더][headers]를 포함해야 할 수 있습니다. 
`headers` 선택적 명명된 매개변수로, 
키-값 쌍의 `Map<String, String>`을 전달하여 헤더를 지정할 수 있습니다.

<?code-excerpt "lib/fetch_data.dart (http-headers)"?>
```dart
await http.get(Uri.https('dart.dev', '/f/packages/http.json'),
    headers: {'User-Agent': '<product name>/<product-version>'});
```

[http-read]: {{site.pub-api}}/http/latest/http/read.html
[http-client-exc]: {{site.pub-api}}/http/latest/http/ClientException-class.html
[mock-http-json]: /f/packages/http.json
[`Future`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future-class.html
[status code]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
[headers]: https://developer.mozilla.org/docs/Web/HTTP/Headers
[http-get]: {{site.pub-api}}/http/latest/http/get.html
[http-response]: {{site.pub-api}}/http/latest/http/Response-class.html
[HTTP response status codes]: https://developer.mozilla.org/docs/Web/HTTP/Status

### 여러 요청하기 {:#make-multiple-requests}

동일한 서버에 여러 요청을 하는 경우, 
대신 [`Client`][http-client]를 통해 지속적인 연결을 유지할 수 있습니다. 
이 연결은 최상위 연결과 유사한 메서드를 가지고 있습니다. 
완료되면, [`close`][http-close] 메서드로 정리하는 것을 잊지 마세요.

<?code-excerpt "lib/fetch_data.dart (http-client)" replace="/clientMain/main/g; /(http\.Cl.*?\))/[!$1!]/g; /(client\.c.*?\))/[!$1!]/g"?>
```dart
void main() async {
  final httpPackageUrl = Uri.https('dart.dev', '/f/packages/http.json');
  final client = [!http.Client()!];
  try {
    final httpPackageInfo = await client.read(httpPackageUrl);
    print(httpPackageInfo);
  } finally {
    [!client.close()!];
  }
}
```

클라이언트가 실패한 요청을 다시 시도할 수 있도록 하려면, 
`package:http/retry.dart`를 import 하고, 
생성된 `Client`를 [`RetryClient`][http-retry-client]로 래핑합니다.

<?code-excerpt "lib/fetch_data.dart (http-retry)" plaster="none" replace="/retryMain/main/g; /(i.*?retry.*)/[!$1!]/g; /(Retry.*?\)\))/[!$1!]/g"?>
```dart
import 'package:http/http.dart' as http;
[!import 'package:http/retry.dart';!]

void main() async {
  final httpPackageUrl = Uri.https('dart.dev', '/f/packages/http.json');
  final client = [!RetryClient(http.Client())!];
  try {
    final httpPackageInfo = await client.read(httpPackageUrl);
    print(httpPackageInfo);
  } finally {
    client.close();
  }
}
```

`RetryClient`는 재시도 횟수와 각 요청 사이에 기다리는 시간에 대한 기본 동작을 가지고 있지만, 
그 동작은 [`RetryClient()`][http-retry-client-cons] 또는 [`RetryClient.withDelays()`][http-retry-client-delay] 생성자에 대한 매개변수를 통해 수정될 수 있습니다.

`package:http`는 훨씬 더 많은 기능과 커스터마이즈를 가지고 있으므로, 
[pub.dev 사이트의 페이지][http-pub]와 [API 문서][http-docs]를 확인하세요.

[http-client]: {{site.pub-api}}/http/latest/http/Client-class.html
[http-close]: {{site.pub-api}}/http/latest/http/Client/close.html
[http-retry-client]: {{site.pub-api}}/http/latest/retry/RetryClient-class.html
[http-retry-client-cons]: {{site.pub-api}}/http/latest/retry/RetryClient/RetryClient.html
[http-retry-client-delay]: {{site.pub-api}}/http/latest/retry/RetryClient/RetryClient.withDelays.html

## 검색된 데이터를 디코딩합니다. {:#decode-the-retrieved-data}

이제 네트워크 요청을 하고 반환된 데이터를 문자열로 검색했지만, 
문자열에서 정보의 특정 부분에 액세스하는 것은 어려울 수 있습니다.

데이터가 이미 JSON 형식이므로, 
Dart의 내장 함수인 `dart:convert` 라이브러리에서, 
Dart의 빌트인 [`json.decode`][decode-docs] 함수를 사용하여, 
Dart 객체를 사용하여 raw 문자열을 JSON 표현으로 변환할 수 있습니다. 
이 경우 JSON 데이터는 맵 구조로 표현되고, JSON에서 맵 키는 항상 문자열이므로, 
`json.decode`의 결과를 `Map<String, dynamic>`으로 캐스팅할 수 있습니다.

<?code-excerpt "lib/fetch_data.dart (json-decode)" plaster="none" replace="/decodeMain/main/g; /(import 'd.*?;)/[!$1!]/g; /(json\.de.*?)\;/[!$1!];/g"?>
```dart
[!import 'dart:convert';!]

import 'package:http/http.dart' as http;

void main() async {
  final httpPackageUrl = Uri.https('dart.dev', '/f/packages/http.json');
  final httpPackageInfo = await http.read(httpPackageUrl);
  final httpPackageJson = [!json.decode(httpPackageInfo) as Map<String, dynamic>!];
  print(httpPackageJson);
}
```

[decode-docs]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-convert/JsonCodec/decode.html

### 데이터를 저장하기 위한 구조화된 클래스를 만듭니다. {:#create-a-structured-class-to-store-the-data}

디코딩된 JSON에 더 많은 구조를 제공하여, 작업하기 쉽게 만들려면, 
데이터 스키마에 따라 특정 타입을 사용하여, 검색된 데이터를 저장할 수 있는 클래스를 만듭니다.

다음 스니펫은 요청한 mock JSON 파일에서 반환된 패키지 정보를 저장할 수 있는, 
클래스 기반 표현을 보여줍니다. 
이 구조는 `repository`를 제외한, 모든 필드가 필수이며 매번 제공된다고 가정합니다.

<?code-excerpt "bin/fetch_http_package.dart (package-info)" plaster="none"?>
```dart
class PackageInfo {
  final String name;
  final String latestVersion;
  final String description;
  final String publisher;
  final Uri? repository;

  PackageInfo({
    required this.name,
    required this.latestVersion,
    required this.description,
    required this.publisher,
    this.repository,
  });
}
```

### 데이터를 클래스에 인코딩하세요. {:#encode-the-data-into-your-class}

이제 데이터를 저장할 클래스가 있으므로, 
디코딩된 JSON을 `PackageInfo` 객체로 변환하는 메커니즘을 추가해야 합니다.

이전 JSON 형식과 일치하는 `fromJson` 메서드를 수동으로 작성하고, 
필요에 따라 타입을 캐스팅하고 선택적 `repository` 필드를 처리하여 디코딩된 JSON을 변환합니다.

<?code-excerpt "bin/fetch_http_package.dart (from-json)"?>
```dart
class PackageInfo {
  // ···

  factory PackageInfo.fromJson(Map<String, dynamic> json) {
    final repository = json['repository'] as String?;

    return PackageInfo(
      name: json['name'] as String,
      latestVersion: json['latestVersion'] as String,
      description: json['description'] as String,
      publisher: json['publisher'] as String,
      repository: repository != null ? Uri.tryParse(repository) : null,
    );
  }
}
```

이전 예와 같은 손으로 쓴 방법은 비교적 간단한 JSON 구조에 종종 충분하지만, 더 유연한 옵션도 있습니다. 
변환 논리의 자동 생성을 포함하여, 
JSON 직렬화 및 역직렬화에 대해 자세히 알아보려면 [JSON 사용][Using JSON] 가이드를 참조하세요.

### 응답을 구조화된 클래스의 객체로 변환합니다. {:#convert-the-response-to-an-object-of-your-structured-class}

이제 데이터를 저장할 클래스와 디코딩된 JSON 객체를 해당 타입의 객체로 변환하는 방법이 있습니다. 
다음으로 모든 것을 하나로 모으는 함수를 작성할 수 있습니다.

1. 전달된 패키지 이름을 기반으로 `URI`를 만듭니다.
2. `http.get`을 사용하여, 해당 패키지의 데이터를 검색합니다.
3. 요청이 성공하지 못한 경우 `Exception` 또는 가급적이면 커스텀 `Exception` 하위 클래스를 throw합니다.
4. 요청이 성공한 경우 `json.decode`를 사용하여 응답 본문을 JSON 문자열로 디코딩합니다.
5. 만든 `PackageInfo.fromJson` 팩토리 생성자를 사용하여, 
   디코딩된 JSON 문자열을 `PackageInfo` 객체로 변환했습니다.

<?code-excerpt "bin/fetch_http_package.dart (get-package)" plaster="none"?>
```dart
Future<PackageInfo> getPackage(String packageName) async {
  final packageUrl = Uri.https('dart.dev', '/f/packages/$packageName.json');
  final packageResponse = await http.get(packageUrl);

  // 요청이 성공하지 못하면, 예외를 발생시킵니다.
  if (packageResponse.statusCode != 200) {
    throw PackageRetrievalException(
      packageName: packageName,
      statusCode: packageResponse.statusCode,
    );
  }

  final packageJson = json.decode(packageResponse.body) as Map<String, dynamic>;

  return PackageInfo.fromJson(packageJson);
}

class PackageRetrievalException implements Exception {
  final String packageName;
  final int? statusCode;

  PackageRetrievalException({required this.packageName, this.statusCode});
}
```

## 변환된 데이터 활용 {:#utilize-the-converted-data}

이제 데이터를 검색하여 더 쉽게 액세스할 수 있는 형식으로 변환했으므로, 
원하는 대로 사용할 수 있습니다. 
가능한 몇 가지 방법으로는 CLI에 정보를 출력하거나, 
[web][] 또는 [Flutter][] 앱에 표시하는 것이 있습니다.

다음은 `http` 및 `path` 패키지에 대한 mock 정보를 요청, 
디코딩한 다음 표시하는 완전한 실행 가능한 예입니다.

<?code-excerpt "bin/fetch_http_package.dart"?>
```dartpad
import 'dart:convert';

import 'package:http/http.dart' as http;

void main() async {
  await printPackageInformation('http');
  print('');
  await printPackageInformation('path');
}

Future<void> printPackageInformation(String packageName) async {
  final PackageInfo packageInfo;

  try {
    packageInfo = await getPackage(packageName);
  } on PackageRetrievalException catch (e) {
    print(e);
    return;
  }

  print('Information about the $packageName package:');
  print('Latest version: ${packageInfo.latestVersion}');
  print('Description: ${packageInfo.description}');
  print('Publisher: ${packageInfo.publisher}');

  final repository = packageInfo.repository;
  if (repository != null) {
    print('Repository: $repository');
  }
}

Future<PackageInfo> getPackage(String packageName) async {
  final packageUrl = Uri.https('dart.dev', '/f/packages/$packageName.json');
  final packageResponse = await http.get(packageUrl);

  // 요청이 성공하지 못하면, 예외를 발생시킵니다.
  if (packageResponse.statusCode != 200) {
    throw PackageRetrievalException(
      packageName: packageName,
      statusCode: packageResponse.statusCode,
    );
  }

  final packageJson = json.decode(packageResponse.body) as Map<String, dynamic>;

  return PackageInfo.fromJson(packageJson);
}

class PackageInfo {
  final String name;
  final String latestVersion;
  final String description;
  final String publisher;
  final Uri? repository;

  PackageInfo({
    required this.name,
    required this.latestVersion,
    required this.description,
    required this.publisher,
    this.repository,
  });

  factory PackageInfo.fromJson(Map<String, dynamic> json) {
    final repository = json['repository'] as String?;

    return PackageInfo(
      name: json['name'] as String,
      latestVersion: json['latestVersion'] as String,
      description: json['description'] as String,
      publisher: json['publisher'] as String,
      repository: repository != null ? Uri.tryParse(repository) : null,
    );
  }
}

class PackageRetrievalException implements Exception {
  final String packageName;
  final int? statusCode;

  PackageRetrievalException({required this.packageName, this.statusCode});

  @override
  String toString() {
    final buf = StringBuffer();
    buf.write('Failed to retrieve package:$packageName information');

    if (statusCode != null) {
      buf.write(' with a status code of $statusCode');
    }

    buf.write('!');
    return buf.toString();
  }
}
```

:::flutter-note
Flutter에서 데이터를 가져온 다음 표시하는 방법을 다루는 또 다른 예는, 
[인터넷에서 데이터 가져오기][Fetching data from the internet] Flutter 레시피를 참조하세요.
:::

[web]: /web
[Flutter]: {{site.flutter}}
[Fetching data from the internet]: {{site.flutter-docs}}/cookbook/networking/fetch-data

## 다음은 무엇입니까? {:#what-next}

이제 인터넷에서 데이터를 검색, 파싱, 사용했으니, 
[Dart의 동시성][Concurrency in Dart]에 대해 자세히 알아보는 것을 고려하세요. 
데이터가 크고 복잡한 경우, 검색 및 디코딩을 백그라운드 작업자로, 
다른 [isolate][]로 옮겨 인터페이스가 응답하지 않는 것을 방지할 수 있습니다.

[Concurrency in Dart]: /language/concurrency
[isolate]: /language/concurrency#isolates

[URI]: https://wikipedia.org/wiki/Uniform_Resource_Identifier
[Using JSON]: /guides/json
[convert-docs]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-convert/dart-convert-library.html
[http-pub]: {{site.pub-pkg}}/http
[http-docs]: {{site.pub-api}}/http
