---
title: dart compile
# description: Command-line tool for compiling Dart source code.
description: Dart 소스 코드를 컴파일하기 위한 명령줄 도구.
---

`dart compile` 명령을 사용하여 Dart 프로그램을 [대상 플랫폼](/overview#platform)으로 컴파일합니다. 
하위 명령을 사용하여 지정하는 출력은, 
[Dart 런타임][Dart runtime]을 포함하거나, 
_모듈_(_스냅샷_ 이라고도 함)이 될 수 있습니다.

{% render 'tools/dart-tool-note.md' %}

다음은 `exe` 하위 명령을 사용하여, 자체 실행 파일(`myapp.exe`)을 생성하는 예입니다.

```console
$ dart compile exe bin/myapp.dart
Generated: /Users/me/myapp/bin/myapp.exe
```

다음 예에서는 `aot-snapshot` 하위 명령을 사용하여, 
사전 컴파일된 모듈(`myapp.aot`)을 생성합니다. 
그런 다음, [`dartaotruntime` 명령](/tools/dartaotruntime)([Dart 런타임][Dart runtime]을 제공함)을 사용하여, 
AOT 모듈을 실행합니다.

```console
$ dart compile aot-snapshot bin/myapp.dart
Generated: /Users/me/myapp/bin/myapp.aot
$ dartaotruntime bin/myapp.aot
```

출력 파일의 경로를 지정하려면, `-o` 또는 `--output` 옵션을 사용하세요.

```console
$ dart compile exe bin/myapp.dart -o bin/runme
```

더 많은 옵션과 사용법에 대한 정보를 보려면, `dart compile [<subcommand>] --help`를 실행하세요.

```console
$ dart compile exe --help
```

`dart compile` 명령은 `dart2native`, `dart2aot`, `dart2js` 명령을 대체합니다.

:::note
Dart 프로그램을 실행하기 전에 컴파일할 필요는 없습니다. 
대신, Dart VM의 JIT(Just-in-time) 컴파일러를 사용하는 [`dart run` 명령][dart-run]을 사용할 수 있습니다. 
이 기능은 개발 중에 특히 유용합니다. 
AOT 및 JIT 컴파일에 대한 자세한 내용은 [플랫폼 토론](/overview#platform)을 참조하세요.
:::

`dart compile`을 사용하여 네이티브 앱을 컴파일하는 간단한 예와 앱을 실행하는 예를 보려면, 
[native_app][] 샘플을 참조하세요.

[native_app]: {{site.repo.dart.org}}/samples/tree/main/native_app
[dart-run]: /tools/dart-run

## 하위 명령 (Subcommand) {:#subcommands}

다음 표는 `dart compile`의 하위 명령을 보여줍니다.

<table class="table table-striped nowrap">
  <tr>
    <th> 하위 명령 </th> <th> 출력 </th> <th> 더 많은 정보 </th>
  </tr>
  <tr>
    <td> <code>exe</code> </td>
    <td> <span style="white-space: nowrap">독립 실행형</span> 실행 파일 </td>
    <td> 소스 코드가 기계 코드로 컴파일되고, 
      작은 <a href="/overview#runtime">Dart 런타임</a>이 포함된 독립 실행형 아키텍처별 실행 파일입니다.
      <br><em><a href="#exe">더 알아보기</a></em>
    </td>
  </tr>
  <tr>
    <td style="white-space: nowrap"> <code>aot-snapshot</code> </td>
    <td style="white-space: nowrap"> AOT 모듈 </td>
    <td> 소스 코드를 기계 코드로 컴파일한 아키텍처별 파일이지만, <b>Dart 런타임은 없습니다</b>.
      <br><em><a href="#aot-snapshot">더 알아보기</a></em>
    </td>
  </tr>
  <tr>
    <td> <code>jit-snapshot</code> </td>
    <td> JIT 모듈 </td>
    <td> 모든 소스 코드의 중간 표현과 프로그램의 트레이닝 실행 중에 실행된(executed) 
      소스 코드의 최적화된 표현이 포함된 아키텍처별 파일. 트레이닝 데이터가 양호하면, 
      JIT 컴파일된 코드는 AOT 코드보다 피크 성능이 더 빠를 수 있습니다.
      <br><em><a href="#jit-snapshot">더 알아보기</a></em>
    </td>
  </tr>
  <tr>
    <td> <code>kernel</code> </td>
    <td> Kernel 모듈 </td>
    <td> 소스 코드의 portable, 
      <a href="{{site.repo.dart.sdk}}/blob/main/pkg/kernel/binary.md">중간 표현</a>입니다.
      <br><em><a href="#kernel">더 알아보기</a></em>
    </td>
  </tr>
  <tr>
    <td> <code>js</code> </td>
    <td> JavaScript </td>
    <td> 소스 코드에서 컴파일된 배포 가능한 JavaScript 파일입니다.
      <br><em><a href="#js">더 알아보기</a></em>
    </td>
  </tr>
  <tr>
    <td> <code>wasm</code> </td>
    <td> WebAssembly </td>
    <td> 스택 기반 가상 머신을 위한 portable 바이너리 명령어 형식. ​​현재 개발 중입니다.
      <br><em><a href="/web/wasm">더 알아보기</a></em>
    </td>
  </tr>
</table>


## 출력 타입 {:#types-of-output}

다음 섹션에서는 `dart compile`이 생성할 수 있는, 각 출력 타입에 대한 자세한 내용을 설명합니다.


### 독립 실행형 실행 파일 (exe) {:#exe}

`exe` 하위 명령은 Windows, macOS 또는 Linux용 독립 실행형 실행 파일을 생성합니다. 
**독립 실행형 실행 파일**은, 
지정된 Dart 파일과 해당 종속성에서 컴파일된 네이티브 머신 코드와 타입 검사 및 가비지 수집을 처리하는, 
작은 [Dart 런타임][Dart runtime]입니다.

다른 실행 파일과 마찬가지로, 출력 파일을 배포하고 실행할 수 있습니다.

앱을 컴파일하고 출력 파일을 설정합니다.

```console
$ dart compile exe bin/myapp.dart -o /tmp/myapp
```

성공하면, 이 명령은 다음을 출력합니다.

```console
Generated: /tmp/myapp
```

`/tmp` 디렉토리에서 컴파일된 앱을 실행합니다.

```console
$ ./tmp/myapp
```

#### 서명 {:#signing}

`dart compile exe`로 만든 실행 파일은 macOS 및 Windows에서 서명을 지원합니다.

플랫폼별 코드 서명에 대해 자세히 알아보려면, 해당 운영 체제의 플랫폼 문서를 참조하세요.

* Windows [`SignTool.exe` 문서][`SignTool.exe` documentation]
* [Apple 코드 서명 가이드][Apple Code Signing guide]

[`SignTool.exe` documentation]: https://docs.microsoft.com/dotnet/framework/tools/signtool-exe
[Apple Code Signing guide]: {{site.apple-dev}}/support/code-signing/

#### 알려진 제한 사항 {:#known-limitations}

`exe` 하위 명령에는 알려진 몇 가지 제한 사항이 있습니다.

{% include 'known-issues/compile-ki.md' %}

### AOT 모듈 (aot-snapshot) {:#aot-snapshot}

여러 명령줄 앱을 배포할 때 디스크 공간 요구 사항을 줄이려면, AOT 모듈을 사용합니다. 
`aot-snapshot` 하위 명령은 앱을 컴파일하는 현재 아키텍처에 맞는 출력 파일을 생성합니다.

예를 들어, macOS를 사용하여 `.aot` 파일을 만드는 경우, 
해당 파일은 macOS에서만 실행할 수 있습니다. 
Dart는 Windows, macOS 및 Linux에서 AOT 모듈을 지원합니다.

```console
$ dart compile aot-snapshot bin/myapp.dart
Generated: /Users/me/myapp/bin/myapp.aot
$ dartaotruntime bin/myapp.aot
```

`aot-snapshot` 하위 명령에는 몇 가지 알려진 제한이 있습니다.

{% include 'known-issues/compile-ki.md' %}

자세한 내용은 [`dartaotruntime` 문서](/tools/dartaotruntime)를 참조하세요.

{% comment %}
  TODO: Get info from https://github.com/dart-lang/sdk/wiki/Snapshots
{% endcomment %}


### JIT 모듈 (jit-snapshot) {:#jit-snapshot}

JIT 모듈에는 프로그램의 트레이닝 실행 중에 생성되는 모든 구문 분석된 클래스와 컴파일된 코드가 포함됩니다.

```console
$ dart compile jit-snapshot bin/myapp.dart
Compiling bin/myapp.dart to jit-snapshot file bin/myapp.jit.
Hello world!
$ dart run bin/myapp.jit
Hello world!
```

애플리케이션 모듈에서 실행할 때, 
Dart VM은 트레이닝 실행 중에 이미 사용된 클래스와 함수를 구문 분석하거나 컴파일할 필요가 없으므로, 
VM이 더 빨리 사용자 코드를 실행하기 시작합니다.

이러한 모듈은 [`kernel` 하위 명령](#kernel)을 사용하여, 생성된 모듈과 달리 아키텍처에 따라 다릅니다.


### 휴대용 모듈 (kernel) {:#kernel}

`kernel` 하위 명령을 사용하여, 모든 운영 체제와 CPU 아키텍처에서 실행할 수 있는, 
단일 이식 가능한(portable) 파일로 앱을 패키징합니다. 
커널 모듈에는 Dart 프로그램의 추상 구문 트리([Kernel AST][])의 바이너리 형식이 포함되어 있습니다.

다음은 커널 모듈을 만들고 실행하는 예입니다.

```console
$ dart compile kernel bin/myapp.dart
Compiling bin/myapp.dart to kernel file bin/myapp.dill.
$ dart run bin/myapp.dill
```

커널 모듈은 Dart 코드에 비해 시작 시간이 짧지만, 
아키텍처별 AOT 출력 형식보다 시작 속도가 훨씬 느릴 수 있습니다.

[Kernel AST]: {{site.repo.dart.sdk}}/blob/main/pkg/kernel/README.md


### JavaScript (js) {:#js}

`js` 하위 명령은 Dart 코드를 배포 가능한 JavaScript로 컴파일합니다.

:::note
Dart-to-JavaScript 컴파일러를 실행하는 대신 [`webdev` 도구][webdev]를 사용하세요.

* 기본적으로 [`webdev build`][] 명령은 축소되고 배포 가능한 JavaScript를 생성합니다.

* 기본적으로 [`webdev serve`][] 명령은 개발 중에 실행하고 디버깅하기 위한 JavaScript 모듈을 생성합니다.
:::

{% include 'tools/dart-compile-js-options.md' %}

#### 웹 앱 컴파일 예제 {:#compiling-web-app-example}

예를 들어, Dart 애플리케이션을 최적화된 JavaScript로 컴파일하려면 다음 명령을 실행합니다.

```console
$ dart compile js -O2 -o out/main.js web/main.dart
```


#### 프로덕션 웹 컴파일 개선 {:#helping-generate-efficient-code}

다음 방법을 따르면 타입 추론을 개선하고, 파일 크기를 줄이고, JavaScript 성능을 개선할 수 있습니다.

* `Function.apply()`를 사용하지 마세요.
* `noSuchMethod()`를 재정의하지 마세요.
* 변수를 `null`로 설정하지 마세요.
* 각 함수나 메서드에 전달하는 인수 타입과 일관성을 유지하세요.

:::tip
앱에 포함된 라이브러리의 크기에 대해 걱정하지 마세요. 
프로덕션 컴파일러는 트리 셰이킹을 수행하여, 사용되지 않는 클래스, 함수, 메서드 등을 생략합니다. 
필요하다고 생각되는 라이브러리를 import 하고, 컴파일러가 필요 없는 것을 제거하도록 하세요.
:::

JavaScript 애플리케이션 빌드 및 배포에 대해 자세히 알아보려면, [웹 배포](/web/deployment)를 확인하세요.

[webdev]: /tools/webdev
[`webdev build`]: /tools/webdev#build
[`webdev serve`]: /tools/webdev#serve
[Dart runtime]: /overview#runtime
