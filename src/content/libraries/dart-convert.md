---
title: dart:convert
# description: Learn about the major features in Dart's dart:convert library.
description: Dart의 dart:convert 라이브러리의 주요 기능에 대해 알아보세요.
prevpage:
  url: /libraries/dart-math
  title: dart:math
nextpage:
  url: /libraries/dart-io
  title: dart:io
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

dart:convert 라이브러리([API 참조][dart:convert])에는 JSON 및 UTF-8 변환기가 있으며, 
추가적인 변환기를 만드는 데 대한 지원도 있습니다. 
[JSON][]은 구조화된 객체와 컬렉션을 나타내는 간단한 텍스트 형식입니다. 
[UTF-8][]은 유니코드 문자 집합의 모든 문자를 나타낼 수 있는 일반적인 가변 너비 인코딩입니다.

이 라이브러리를 사용하려면, dart:convert를 import 합니다.

<?code-excerpt "misc/test/library_tour/convert_test.dart (import)"?>
```dart
import 'dart:convert';
```

## JSON 디코딩 및 인코딩 {:#decoding-and-encoding-json}

`jsonDecode()`를 사용하여 JSON으로 인코딩된 문자열을 Dart 객체로 디코딩합니다.

<?code-excerpt "misc/test/library_tour/convert_test.dart (json-decode)"?>
```dart
// 참고: JSON 문자열 안에는 작은 따옴표(')가 아닌 큰 따옴표(")를 사용해야 합니다.
// 이 문자열은 JSON이고, Dart가 아닙니다.
var jsonString = '''
  [
    {"score": 40},
    {"score": 80}
  ]
''';

var scores = jsonDecode(jsonString);
assert(scores is List);

var firstScore = scores[0];
assert(firstScore is Map);
assert(firstScore['score'] == 40);
```

`jsonEncode()`를 사용하여 지원되는 Dart 객체를 JSON 형식의 문자열로 인코딩합니다.

<?code-excerpt "misc/test/library_tour/convert_test.dart (json-encode)"?>
```dart
var scores = [
  {'score': 40},
  {'score': 80},
  {'score': 100, 'overtime': true, 'special_guest': null}
];

var jsonText = jsonEncode(scores);
assert(jsonText ==
    '[{"score":40},{"score":80},'
        '{"score":100,"overtime":true,'
        '"special_guest":null}]');
```

int, double, String, bool, null, List 또는 Map(문자열 키 포함) 타입의 객체만, 
JSON으로 직접 인코딩할 수 있습니다. 
List 및 Map 객체는 재귀적으로 인코딩됩니다.

직접 인코딩할 수 없는 객체를 인코딩하는 데는 두 가지 옵션이 있습니다. 

- (1) 첫 번째 옵션은 두 번째 인수와 함께 `jsonEncode()`를 호출하는 것입니다. 
  - 두 번째 인수는 직접 인코딩할 수 있는 객체를 반환하는 함수입니다. 
- (2) 두 번째 옵션은 두 번째 인수를 생략하는 것입니다. 
  - 이 경우, 인코더가 객체의 `toJson()` 메서드를 호출합니다.

JSON 관련 패키지에 대한 더 많은 예와 링크는, 
[JSON 사용](/guides/json)을 참조하세요.

## UTF-8 문자 디코딩 및 인코딩 {:#decoding-and-encoding-utf-8-characters}

`utf8.decode()`를 사용하여, UTF8로 인코딩된 바이트를, Dart 문자열로 디코딩합니다.

<?code-excerpt "misc/test/library_tour/convert_test.dart (utf8-decode)" replace="/ \/\/line-br.*//g"?>
```dart
List<int> utf8Bytes = [
  0xc3, 0x8e, 0xc3, 0xb1, 0xc5, 0xa3, 0xc3, 0xa9,
  0x72, 0xc3, 0xb1, 0xc3, 0xa5, 0xc5, 0xa3, 0xc3,
  0xae, 0xc3, 0xb6, 0xc3, 0xb1, 0xc3, 0xa5, 0xc4,
  0xbc, 0xc3, 0xae, 0xc5, 0xbe, 0xc3, 0xa5, 0xc5,
  0xa3, 0xc3, 0xae, 0xe1, 0xbb, 0x9d, 0xc3, 0xb1
];

var funnyWord = utf8.decode(utf8Bytes);

assert(funnyWord == 'Îñţérñåţîöñåļîžåţîờñ');
```

UTF-8 문자 스트림을 Dart 문자열로 변환하려면, 
Stream의 `transform()` 메서드에 `utf8.decoder`를 지정합니다.

<?code-excerpt "misc/test/library_tour/io_test.dart (utf8-decoder)" replace="/utf8.decoder/[!$&!]/g"?>
```dart
var lines = [!utf8.decoder!].bind(inputStream).transform(const LineSplitter());
try {
  await for (final line in lines) {
    print('Got ${line.length} characters from stream');
  }
  print('file is now closed');
} catch (e) {
  print(e);
}
```

`utf8.encode()`를 사용하여, Dart 문자열을 UTF8로 인코딩된 바이트 리스트로 인코딩합니다.

<?code-excerpt "misc/test/library_tour/convert_test.dart (utf8-encode)" replace="/ \/\/line-br.*//g"?>
```dart
Uint8List encoded = utf8.encode('Îñţérñåţîöñåļîžåţîờñ');

assert(encoded.length == utf8Bytes.length);
for (int i = 0; i < encoded.length; i++) {
  assert(encoded[i] == utf8Bytes[i]);
}
```


## 기타 기능 {:#other-functionality}

dart:convert 라이브러리에는 ASCII 및 ISO-8859-1(Latin1) 변환기도 있습니다. 
자세한 내용은 [dart:convert 라이브러리에 대한 API 참조][dart:convert]를 참조하세요.

[JSON]: https://www.json.org/
[UTF-8]: https://en.wikipedia.org/wiki/UTF-8
[dart:convert]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-convert/dart-convert-library.html
