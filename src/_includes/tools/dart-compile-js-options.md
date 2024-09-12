#### 옵션 {:#prod-compile-options}

`dart compile js` 명령에는 자바스크립트 코드 컴파일을 커스터마이즈하는 여러 옵션이 있습니다.

###### 기본 옵션 {:#basic-options}

일반적인 옵션은 다음과 같습니다.

`-o <file>` 또는 `--output=<file>`
: 출력을 `<file>`에 생성합니다.
  지정하지 않으면, 출력은 `out.js`라는 파일에 저장됩니다.

`--enable-asserts`
: 어설션 검사를 활성화합니다.

`-O{0|1|2|3|4}`
: 파일 크기를 줄이고 코드 성능을 개선하기 위한 최적화를 제어합니다. 
  이러한 최적화에 대해 자세히 알아보려면, `dart compile js -hv`를 실행하세요.

  * `-O0`: 많은 최적화를 비활성화합니다.
  * `-O1`: 기본 최적화를 활성화합니다.
  * `-O2`: `-O1` 최적화와 언어 시맨틱을 존중하고. 모든 프로그램에 안전한 추가 최적화(예: 최소화)를 활성화합니다.

    :::note
    `-O2`를 사용하면, 개발 JavaScript 컴파일러로 컴파일한 경우, 
    타입의 문자열 표현이 더 이상 Dart VM의 문자열 표현과 동일하지 않습니다.
    :::
  * `-O3`: `-O2` 최적화를 활성화하고, 암묵적 타입 검사를 생략합니다.
    
    :::warning
    타입 검사를 생략하면, 타입 오류로 인해 앱이 충돌할 수 있습니다. 
    `-O3`를 사용하기 전에 **`-O2`를 사용하여 테스트**하여, 
    앱이 `Error` 하위 타입(예: `TypeError`)을 **절대로** throw하지 않는지 확인합니다.
    :::
  * `-O4`: `-O3`보다 더 공격적인 최적화가 가능하지만, 동일한 가정이 적용됩니다.
    
    :::warning
    `-O4` 최적화는 입력 데이터의 변화에 ​​취약합니다. 
    `-O4`에 의존하기 전에, **사용자 입력의 엣지 케이스를 테스트**하세요.
    :::

`--no-source-maps`
: 소스 맵 파일을 생성하지 않습니다.

`-h` 또는 `--help`
: 도움말을 표시합니다. 모든 옵션에 대한 정보를 얻으려면 `-hv`를 사용합니다.


###### 경로 및 환경 옵션 {:#path-and-environment-options}

다른 유용한 옵션은 다음과 같습니다.

`--packages=<path>`
: 패키지 해결 구성 파일에 대한 경로를 지정합니다. 
  자세한 내용은 [Dart 패키지 구성 파일][Dart package configuration file] 사양을 확인하세요.

`-D<flag>=<value>`
: [`String.fromEnvironment`][], [`int.fromEnvironment`][], [`bool.fromEnvironment`][], 
  또는 [`bool.hasEnvironment`][]로 액세스할 수 있는 환경 선언 및 값 쌍을 정의합니다. 
  환경 선언에 대해 자세히 알아보려면 [컴파일 환경 선언으로 앱 구성][Configuring apps with compilation environment declarations]을 참조하세요.

`--version`
: `dart`에 대한 버전 정보를 표시합니다.

[Dart package configuration file]: {{site.repo.dart.lang}}/blob/main/accepted/2.8/language-versioning/package-config-file-v2.md
[`String.fromEnvironment`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/String/String.fromEnvironment.html
[`int.fromEnvironment`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/int/int.fromEnvironment.html
[`bool.fromEnvironment`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/bool/bool.fromEnvironment.html
[`bool.hasEnvironment`]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/bool/bool.hasEnvironment.html
[Configuring apps with compilation environment declarations]: /guides/environment-declarations

###### 디스플레이 옵션 {:#display-options}

다음 옵션은 컴파일러 출력을 제어하는 ​​데 도움이 됩니다.

`--suppress-warnings`
: 경고를 표시하지 않습니다.

`--suppress-hints`
: 힌트를 표시하지 않습니다.

`--terse`
: 진단된 문제를 제거하는 방법을 제안하지 않고, 진단을 내보냅니다.

`-v` 또는 `--verbose`
: 많은 정보를 표시합니다.


###### 분석 옵션 {:#analysis-options}

다음 옵션은 Dart 코드에서 수행되는 분석을 제어합니다.

`--fatal-warnings`
: 경고를 컴파일 오류로 처리합니다.

`--enable-diagnostic-colors`
: 진단 메시지에 색상을 추가합니다.

`--show-package-warnings`
: 패키지에서 생성된 경고와 힌트를 표시합니다.

`--csp`
: 생성된 출력에서 ​​코드의 동적 생성을 비활성화합니다. 
  이는 CSP 제한을 충족하는 데 필요합니다.
  ([W3C 콘텐츠 보안 정책](https://www.w3.org/TR/CSP/) 참조)

`--dump-info`
: 생성된 코드에 대한 정보가 포함된 파일(접미사 `.info.json` 포함)을 생성합니다. 
  [dart2js_info](/go/dart2js-info)의 도구를 사용하여, 생성된 파일을 검사할 수 있습니다.
