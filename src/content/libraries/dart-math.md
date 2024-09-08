---
title: dart:math
# description: Learn about the major features in Dart's dart:math library.
description: Dart의 dart:math 라이브러리의 주요 기능에 대해 알아보세요.
prevpage:
  url: /libraries/dart-async
  title: dart:async
nextpage:
  url: /libraries/dart-convert
  title: dart:convert
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

dart:math 라이브러리([API 참조][dart:math])는 
사인과 코사인, 최대값과 최소값, *pi*와 *e*와 같은 상수와 같은 일반적인 기능을 제공합니다. 
Math 라이브러리의 대부분 기능은 최상위 함수로 구현됩니다.

앱에서 이 라이브러리를 사용하려면, dart:math를 import 합니다.

<?code-excerpt "misc/test/library_tour/math_test.dart (import)"?>
```dart
import 'dart:math';
```


## 삼각법 {:#trigonometry}

Math 라이브러리는 기본적인 삼각 함수를 제공합니다.

<?code-excerpt "misc/test/library_tour/math_test.dart (trig)"?>
```dart
// 코사인
assert(cos(pi) == -1.0);

// 사인
var degrees = 30;
var radians = degrees * (pi / 180);
// radians은 이제 0.52359입니다.
var sinOf30degrees = sin(radians);
// sin 30° = 0.5
assert((sinOf30degrees - 0.5).abs() < 0.01);
```

:::note
이 함수는 degrees가 아닌, radians을 사용합니다!
:::


## 최대 및 최소 {:#maximum-and-minimum}

Math 라이브러리는 `max()` 및 `min()` 메서드를 제공합니다.

<?code-excerpt "misc/test/library_tour/math_test.dart (min-max)"?>
```dart
assert(max(1, 1000) == 1000);
assert(min(1, -1000) == -1000);
```


## 수학 상수 {:#math-constants}

Math 라이브러리에서 좋아하는 상수(*pi*, *e* 등)를 찾아보세요.

<?code-excerpt "misc/test/library_tour/math_test.dart (constants)"?>
```dart
// 추가적인 상수는 Math 라이브러리를 참조하세요.
print(e); // 2.718281828459045
print(pi); // 3.141592653589793
print(sqrt2); // 1.4142135623730951
```


## 랜덤 숫자 {:#random-numbers}

[Random][] 클래스로 랜덤 숫자를 생성합니다. 
선택적으로 Random 생성자에 시드를 제공할 수 있습니다.

<?code-excerpt "misc/test/library_tour/math_test.dart (random)"?>
```dart
var random = Random();
random.nextDouble(); // 0.0과 1.0 사이: [0, 1)
random.nextInt(10); // 0과 9 사이.
```

랜덤 booleans 값을 생성할 수도 있습니다.

<?code-excerpt "misc/test/library_tour/math_test.dart (random-bool)"?>
```dart
var random = Random();
random.nextBool(); // true 또는 false
```

:::warning
`Random`의 기본 구현은 암호화 목적에 적합하지 않은, 의사 난수 비트 스트림을 제공합니다. 
암호화적으로 안전한 난수 생성기를 만들려면, 
[`Random.secure()`][] 생성자를 사용하세요.
:::

## 더 많은 정보 {:#more-information}

전체 메서드 목록은 [Math API 참조][dart:math]를 참조하세요.
또한 [num][], [int][] 및 [double][]에 대한 API 참조도 참조하세요.

[Random]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-math/Random-class.html
[`Random.secure()`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-math/Random/Random.secure.html
[dart:math]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-math/dart-math-library.html
[double]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/double-class.html
[int]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/int-class.html
[num]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/num-class.html
