---
# title: Numbers in Dart
title: Dart에서의 숫자
# description: Learn how Dart numbers are slightly different on the web, when that might matter, and how you might adjust your code.
description: Dart 숫자가 웹에서 어떻게 약간씩 다른지, 언제 이것이 중요한지, 그리고 어떻게 코드를 조정할 수 있는지 알아보세요.
---

Dart 앱은 종종 여러 플랫폼을 대상으로 합니다. 
예를 들어, Flutter 앱은 iOS, Android 및 웹을 대상으로 할 수 있습니다. 
앱이 플랫폼별 라이브러리에 의존하거나 플랫폼에 따라 달라지는 방식으로 숫자를 사용하지 않는 한, 
코드는 동일할 수 있습니다.

이 페이지에는 네이티브 및 웹 숫자 구현의 차이점과, 
이러한 차이점이 중요하지 않도록 코드를 작성하는 방법에 대한 자세한 내용이 있습니다.

:::secondary Dart 및 기타 언어의 숫자 구현
Dart는 성능, 코드 크기 및 플랫폼 상호 운용성의 이유로, 
숫자에 대한 플랫폼별 표현 및 시맨틱 체계를 항상 허용했습니다.

마찬가지로, C/C++에서 정수 값에 일반적으로 사용되는 `int` 타입은 네이티브 머신 아키텍처(16비트, 32비트 또는 64비트)에 가장 잘 매핑되도록 플랫폼별로 다릅니다. 
Java에서, 분수 값에 대한 `float` 및 `double` 타입은 원래 모든 플랫폼에서 IEEE 754를 엄격히 따르도록 설계되었지만, 
효율성의 이유로 이 제약이 거의 즉시 완화되었습니다. (정확한 일관성을 위해서는 `strictfp`가 필요함)
:::


## Dart 숫자 표현 {:#dart-number-representation}

Dart에서는, 모든 숫자가 공통적인 `Object` 타입 계층에 속하며, 
정수 값을 나타내는 `int`와 분수 값을 나타내는 `double`이라는, 
두 가지 구체적이고 사용자에게 표시되는 숫자 타입이 있습니다.

<img 
  src="/assets/img/number-class-hierarchy.svg" 
  alt="Object is the parent of num, which is the parent of int and double">

플랫폼에 따라 이러한 숫자 타입은 서로 다르고 숨겨진 구현을 갖습니다. 
특히 Dart는 컴파일하는 두 가지 매우 다른 타입의 대상이 있습니다.

* **네이티브:** 가장 자주. 64비트 모바일 또는 데스크톱 프로세서.
* **웹:** 기본 실행 엔진으로 JavaScript.

다음 표는 Dart 숫자가 일반적으로 구현되는 방식을 보여줍니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
      <th>표현</th>
      <th>네이티브 <code>int</code></th>
      <th>네이티브 <code>double</code></th>
      <th>웹 <code>int</code></th>
      <th>웹 <code>double</code></th>
    </tr>
    <tr>
      <td><a href="https://en.wikipedia.org/wiki/Two%27s_complement">
        64비트 부호 있는(signed) 2의 보수</a>
      </td>
      <td>✅</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>
        <a href="https://en.wikipedia.org/wiki/Double-precision_floating-point_format">64비트 부동 소수점</a>
      </td>
      <td></td>
      <td>✅</td>
      <td>✅</td>
      <td>✅</td>
    </tr>
  </table>
</div>

네이티브 대상의 경우, `int`가 부호 있는(signed) 64비트 정수 표현에 매핑되고, 
`double`이 기본 프로세서와 일치하는 64비트 IEEE 부동 소수점 표현에 매핑된다고 가정할 수 있습니다.

하지만 Dart가 JavaScript로 컴파일되고 JavaScript와 상호 운용되는 웹에서는, 
단일 숫자 표현인 64비트 배정밀도 부동 소수점 값이 있습니다. 
효율성을 위해 Dart는 `int`와 `double`을 모두 이 단일 표현에 매핑합니다. 
보이는 타입 계층은 동일하지만, 기본 숨겨진 구현 타입은 다르고 서로 얽혀 있습니다.

다음 그림은 네이티브 및 웹 대상의 플랫폼별 타입(파란색)을 보여줍니다. 
그림에서 알 수 있듯이, 네이티브의 `int`에 대한 구체적 타입은 `int` 인터페이스만 구현합니다. 
하지만 웹의 `int`에 대한 구체적 타입은 `int`와 `double`을 모두 구현합니다.

<img 
  src="/assets/img/number-platform-specific.svg" 
  alt="Implementation classes vary by platform; for JavaScript, the class that implements int also implements double">


:::note
Dart는 효율성을 위해 `int`와 `double`을 여러 가지 다른 방식으로 표현하지만, 
이러한 구현 클래스(위의 파란색)는 숨겨져 있습니다. 
일반적으로 플랫폼별 타입을 무시하고, 
`int`와 `double`을 구체적인 타입으로 생각할 수 있습니다.
:::

웹에서 `int`는 소수 부분이 없는 이중 정밀도 부동 소수점 값으로 표현됩니다. 
실제로는 꽤 잘 작동합니다. 
이중 정밀도 부동 소수점은 53비트의 정수 정밀도를 제공합니다. 
그러나, `int` 값은 항상 `double` 값이기도 하며, 이는 몇 가지 놀라움으로 이어질 수 있습니다.


## 동작의 차이점 {:#differences-in-behavior}

대부분의 정수 및 실수 산술은 본질적으로 동일한 동작을 합니다.
그러나 중요한 차이점이 있습니다. 
특히 코드에 정밀도, 문자열 형식 또는 기본 런타임 타입에 대한 엄격한 기대가 있는 경우입니다.

이 섹션에서 설명한 대로, 산술 결과가 다를 경우, 
해당 동작은 **플랫폼에 따라 달라지며** **변경될 수 있습니다**.

:::note
이 페이지에서 설명하는 플랫폼별 동작은 덜 놀랍거나, 더 일관적이거나, 더 나은 성능으로 변경될 수 있습니다.
:::

### 정밀도 {:#precision}

다음 표는 일부 숫자 표현식이 정밀도로 인해 어떻게 다른지 보여줍니다. 
여기서, `math`는 `dart:math` 라이브러리를 나타내고, `math.pow(2, 53)`는 2<sup>53</sup>입니다.

웹에서 정수는 53비트를 지나면 정밀도가 떨어집니다. 
특히 2<sup>53</sup>과 2<sup>53</sup>+1은 잘림으로 인해 동일한 값으로 매핑됩니다. 
네이티브에서 이러한 값은 네이티브 숫자가 64비트(값에 63비트, 부호에 1비트)를 가지고 있기 때문에, 
여전히 구별할 수 있습니다.

오버플로의 효과는 2<sup>63</sup>-1과 2<sup>63</sup>을 비교할 때 볼 수 있습니다. 
네이티브에서 후자는 2의 보수 산술에서 예상대로 -2<sup>63</sup>으로 오버플로됩니다. 
웹 상에서 이러한 값은 다르게 표현되기 때문에 오버플로되지 않습니다. 정밀도 손실로 인한 근사치일 뿐입니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
      <th>표현</th>
      <th>네이티브</th>
      <th>웹</th>
    </tr>
    <tr>
      <td><code>math.pow(2, 53) - 1</code></td>
      <td><code>9007199254740991</code></td>
      <td><code>9007199254740991</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 53)</code></td>
      <td><code>9007199254740992</code></td>
      <td><code>9007199254740992</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 53) + 1</code></td>
      <td><code>9007199254740993</code></td>
      <td><code>9007199254740992</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 62)</code></td>
      <td><code>4611686018427387904</code></td>
      <td><code>4611686018427388000</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 63) - 1</code></td>
      <td><code>9223372036854775807</code></td>
      <td><code>9223372036854776000</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 63)</code></td>
      <td><code>-9223372036854775808</code></td>
      <td><code>9223372036854776000</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 64)</code></td>
      <td><code>0</code></td>
      <td><code>18446744073709552000</code></td>
    </tr>
  </table>
</div>

### 동일성 (Identity) {:#identity}

네이티브 플랫폼에서, `double`과 `int`는 별개의 타입입니다. 
어떤 값도 동시에 `double`과 `int`가 될 수 없습니다. 
웹에서는 그렇지 않습니다. 이러한 차이로 인해 플랫폼 간에 동일성이 다를 수 있지만, 동등성(`==`)은 다릅니다.

다음 표는 동등성(equality)과 동일성(identity)을 사용하는 몇 가지 표현식을 보여줍니다. 
동등성 표현식은 네이티브와 웹에서 동일하지만, 동일성 표현식은 일반적으로 다릅니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
      <th>표현</th>
      <th>네이티브</th>
      <th>웹</th>
    </tr>
    <tr>
      <td><code>1.0 == 1</code></td>
      <td><code>true</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>identical(1.0, 1)</code></td>
      <td><code>false</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>0.0 == -0.0</code></td>
      <td><code>true</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>identical(0.0, -0.0)</code></td>
      <td><code>false</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>double.nan == double.nan</code></td>
      <td><code>false</code></td>
      <td><code>false</code></td>
    </tr>
    <tr>
      <td><code>identical(double.nan, double.nan)</code></td>
      <td><code>true</code></td>
      <td><code>false</code></td>
    </tr>
    <tr>
      <td><code>double.infinity == double.infinity</code></td>
      <td><code>true</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>identical(double.infinity, double.infinity)</code></td>
      <td><code>true</code></td>
      <td><code>true</code></td>
    </tr>
  </table>
</div>

### 타입 및 타입 검사 {:#types-and-type-checking}

웹에서 기본 `int` 타입은 `double`의 하위 타입과 같습니다. 
소수 부분(fractional part)이 없는 더블-정밀도(double-precision) 값입니다. 
사실, `x is int` 형태의 웹에서 타입 검사는, `x`가 소수 부분이 0인 숫자(`double`)인 경우, true를 반환합니다.

결과적으로 웹에서 다음 사항이 true 입니다.

* 모든 Dart 숫자(`num` 타입의 값)는 `double`입니다.
* Dart 숫자는 동시에 `double`과 `int`가 될 수 있습니다.

이러한 사실은 `is` 검사와 `runtimeType` 속성에 영향을 미칩니다. 
부작용으로 `double.infinity`가 `int`로 해석됩니다. 
이는 플랫폼별 동작이므로 나중에 변경될 수 있습니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
      <th>표현</th>
      <th>네이티브</th>
      <th>웹</th>
    </tr>
    <tr>
      <td><code>1 is int</code></td>
      <td><code>true</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>1 is double</code></td>
      <td><code>false</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>1.0 is int</code></td>
      <td><code>false</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>1.0 is double</code></td>
      <td><code>true</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>(0.5 + 0.5) is int</code></td>
      <td><code>false</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>(0.5 + 0.5) is double</code></td>
      <td><code>true</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>3.14 is int</code></td>
      <td><code>false</code></td>
      <td><code>false</code></td>
    </tr>
    <tr>
      <td><code>3.14 is double</code></td>
      <td><code>true</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>double.infinity is int</code></td>
      <td><code>false</code></td>
      <td><code>true</code></td>
    </tr>
    <tr>
      <td><code>double.nan is int</code></td>
      <td><code>false</code></td>
      <td><code>false</code></td>
    </tr>
    <tr>
      <td><code>1.0.runtimeType</code></td>
      <td><code>double</code></td>
      <td><code>int</code></td>
    </tr>
    <tr>
      <td><code>1.runtimeType</code></td>
      <td><code>int</code></td>
      <td><code>int</code></td>
    </tr>
    <tr>
      <td><code>1.5.runtimeType</code></td>
      <td><code>double</code></td>
      <td><code>double</code></td>
    </tr>
  </table>
</div>

### 비트 연산 {:#bitwise-operations}

웹에서 성능상의 이유로 `int`의 비트연산자(`&`, `|`, `^`, `~`)와 시프트 연산자(`<<`,`>>`, `>>>`)는 
네이티브 JavaScript의 것을 사용합니다. 
JavaScript에서, 피연산자는 부호 없는 것으로 처리되는 32비트 정수로 잘립니다. 
이러한 처리로 인해, 큰 숫자에서 놀라운 결과가 발생할 수 있습니다. 
특히, 피연산자가 음수이거나 32비트에 맞지 않으면, 네이티브와 웹 간에 다른 결과가 생성될 가능성이 높습니다.

다음 표는 피연산자가 음수이거나 32비트에 가까울 때, 
네이티브 및 웹 플랫폼에서 비트연산자와 시프트 연산자를 처리하는 방식을 보여줍니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
      <th>표현</th>
      <th>네이티브</th>
      <th>웹</th>
    </tr>
    <tr>
      <td><code>-1 >> 0</code></td>
      <td><code>-1</code></td>
      <td><code>4294967295</code></td>
    </tr>
    <tr>
      <td><code>-1 ^ 2</code></td>
      <td><code>-3</code></td>
      <td><code>4294967293</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 32).toInt()</code></td>
      <td><code>4294967296</code></td>
      <td><code>4294967296</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 32).toInt() >> 1</code></td>
      <td><code>2147483648</code></td>
      <td><code>0</code></td>
    </tr>
    <tr>
      <td><code>(math.pow(2, 32).toInt()-1) >> 1</code></td>
      <td><code>2147483647</code></td>
      <td><code>2147483647</code></td>
    </tr>
  </table>
</div>

### 문자열 표현 {:#string-representation}

웹에서, Dart는 일반적으로 숫자를 문자열로 변환하기 위해 JavaScript를 사용합니다. (예: `print`)
다음 표는 첫 번째 열의 표현식을 변환하면 어떻게 다른 결과가 나올 수 있는지 보여줍니다.

<div class="table-wrapper">
  <table class="table table-striped nowrap">
    <tr>
      <th>표현</th>
      <th>네이티브 <code>toString()</code></th>
      <th>웹 <code>toString()</code></th>
    </tr>
    <tr>
      <td><code>1</code></td>
      <td><code>"1"</code></td>
      <td><code>"1"</code></td>
    </tr>
    <tr>
      <td><code>1.0</code></td>
      <td><code>"1.0"</code></td>
      <td><code>"1"</code></td>
    </tr>
    <tr>
      <td><code>(0.5 + 0.5)</code></td>
      <td><code>"1.0"</code></td>
      <td><code>"1"</code></td>
    </tr>
    <tr>
      <td><code>1.5</code></td>
      <td><code>"1.5"</code></td>
      <td><code>"1.5"</code></td>
    </tr>
    <tr>
      <td><code>-0</code></td>
      <td><code>"0"</code></td>
      <td><code>"-0.0"</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 0)</code></td>
      <td><code>"1"</code></td>
      <td><code>"1"</code></td>
    </tr>
    <tr>
      <td><code>math.pow(2, 80)</code></td>
      <td><code>"0"</code></td>
      <td><code>"1.2089258196146292e+24"</code></td>
    </tr>
  </table>
</div>

## 어떻게 해야 할까요? {:#what-should-you-do}

일반적으로, 숫자 코드를 변경할 필요는 없습니다. 
Dart 코드는 수년 동안 네이티브와 웹 플랫폼에서 실행되어 왔으며, 
숫자 구현 차이는 거의 문제가 되지 않습니다.
(작은 정수 범위를 반복하고 리스트를 인덱싱하는 것과 같은) 일반적이고 전형적인 코드는 동일하게 동작합니다.

문자열 결과를 비교하는 테스트나 어설션이 있는 경우, 플랫폼에 탄력적인 방식으로 작성하세요. 
예를 들어, 숫자가 포함된 문자열 표현식의 값을 테스트한다고 가정해 보겠습니다.

```dart
void main() {
  var count = 10.0 * 2;
  var message = "$count cows";
  if (message != "20.0 cows") throw Exception("Unexpected: $message");
}
```

앞의 코드는 네이티브 플랫폼에서는 성공하지만, 
웹에서는 `message`가 웹에서 `"20 cows"`(소수점 없음)이기 때문에 오류가 발생합니다. 
대안으로, 다음과 같이 조건을 작성하여 네이티브와 웹 플랫폼 모두에서 통과하도록 할 수 있습니다.

```dart
if (message != "${20.0} cows") throw ... 
```

비트 조작의 경우, 모든 플랫폼에서 일관된, 32비트 청크에서 명시적으로 작동하는 것을 고려하세요. 
32비트 청크의 부호 있는 해석을 강제하려면, `int.toSigned(32)`를 사용하세요.

정밀도가 중요한 다른 경우, 다른 숫자 타입을 고려하세요. 
[`BigInt`][] 타입은 네이티브와 웹에서 모두 임의 정밀도 정수를 제공합니다. 
[`fixnum`][] 패키지는 웹에서도 엄격한 64비트 부호 있는 숫자를 제공합니다. 
하지만, 이러한 타입을 주의해서 사용하세요. 
종종 상당히 크고 느린 코드가 생성되기 때문입니다.

[`BigInt`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/BigInt-class.html
[`fixnum`]: {{site.pub-pkg}}/fixnum
