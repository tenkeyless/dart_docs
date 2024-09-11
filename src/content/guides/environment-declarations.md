---
# title: Configuring apps with compilation environment declarations
title: 컴파일 환경 선언으로 앱 구성
# description: >-
#   Learn about using compilation environment declarations 
#   to customize application behavior.
description: >-
  컴파일 환경 선언을 사용하여, 애플리케이션 동작을 커스터마이즈하는 방법에 대해 알아보세요.
lastVerified: 2023-02-23
---

Dart 애플리케이션을 빌드하거나 실행할 때 컴파일 환경 선언을 지정할 수 있습니다. 
컴파일 환경 선언은 컴파일 타임에 액세스하고 평가되는 키-값 쌍으로 구성 옵션을 지정합니다.

:::note
이 페이지에서는 "환경(environment)"이라는 용어를 Dart 컴파일 환경을 지칭하는 데 사용합니다. 
이 용어의 일반적인 사용은 대신 운영 체제 환경을 지칭합니다.
:::

앱은 환경 선언의 값을 사용하여 기능이나 동작을 변경할 수 있습니다. 
Dart 컴파일러는 환경 선언 값을 사용하여 제어 흐름으로 인해 도달할 수 없게 된 코드를 제거할 수 있습니다.

환경 선언을 정의하고 사용하여 다음을 수행할 수 있습니다.

* (로깅 활성화와 같이) 디버깅 중에 기능을 추가합니다.
* 애플리케이션의 별도 플레이버를 만듭니다.
* (HTTP 서버 포트와 같은) 애플리케이션 동작을 구성합니다.
* 테스트를 위해 애플리케이션의 실험 모드를 활성화합니다.
* 테스트 및 프로덕션 백엔드 간을 전환합니다.

Dart 애플리케이션을 실행하거나 컴파일할 때 환경 선언을 지정하려면, 
`--define` 옵션이나 그 약어인 `-D`를 사용합니다. 
`<NAME>=<VALUE>` 형식을 사용하여 선언 키-값 쌍을 지정합니다.

```console
$ dart run --define=DEBUG=true -DFLAVOR=free
```

다른 도구로 이러한 선언을 설정하는 방법을 알아보려면, 
이 가이드의 [환경 선언 지정][specifying environment declarations] 섹션을 확인하세요. 
해당 섹션에서는 선언 구문과 명령줄, IDE 및 편집기에서 이를 지정하는 방법을 설명합니다.

[`dart run`]: /tools/dart-run
[`dart compile`]: /tools/dart-compile
[specifying environment declarations]: #specifying-environment-declarations

## 환경 선언에 접근하기 {:#accessing-environment-declarations}

지정된 환경 선언 값에 액세스하려면, 
`const`를 사용하거나 상수 컨텍스트 내에서 `fromEnvironment` 생성자 중 하나를 사용합니다. 
`true` 또는 `false` 값에는 [`bool.fromEnvironment`][bool-from]을 사용하고, 
정수 값에는 [`int.fromEnvironment`][int-from]을 사용하고, 
그 외의 값에는 [`String.fromEnvironment`][string-from]을 사용합니다.

:::note
환경 선언 생성자는 `const`로 호출될 때만 작동이 보장됩니다. 
대부분의 컴파일러는 컴파일 타임에 값을 평가할 수 있어야 합니다.
:::

각 `fromEnvironment` 생성자는 환경 선언의 이름이나 키가 필요합니다. 
또한 기본 폴백 값(default fallback value)을 재정의하기 위해, 
선택적 `defaultValue` 명명된 인수를 허용합니다. 
기본 폴백 값은 선언이 정의되지 않았거나, 
지정된 값을 예상 타입으로 구문 분석할 수 없는 경우에 사용됩니다.

예를 들어, 환경 선언 `DEBUG`가 `true`로 설정된 경우에만, 로그 메시지를 출력하려면 다음을 수행합니다.

<?code-excerpt "misc/lib/development/environment_declarations.dart (debug-log)"?>
```dart
void log(String message) {
  // 환경 선언 'DEBUG'가 `true`이면, 디버그 메시지를 log 합니다.
  // 값이 지정되지 않은 경우, log 하지 않습니다.
  if (const bool.fromEnvironment('DEBUG', defaultValue: false)) {
    print('Debug: $message');
  }
}
```

이 스니펫에서, 컴파일 중에 `DEBUG`가 `false`로 설정되거나, 
전혀 지정되지 않으면, 프로덕션 컴파일러는 조건과 본문을 완전히 제거할 수 있습니다.

`fromEnvironment` 생성자는 선언이 지정되지 않았거나, 
지정된 값을 구문 분석할 수 없는 경우 기본값으로 대체합니다. 
따라서 환경 선언이 지정되었는지 구체적으로 확인하려면, 
[`bool.hasEnvironment`][bool-has] 생성자를 사용합니다.

<?code-excerpt "misc/lib/development/environment_declarations.dart (has-debug)"?>
```dart
if (const bool.hasEnvironment('DEBUG')) {
  print('Debug behavior was configured!');
}
```

[string-from]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/String/String.fromEnvironment.html
[int-from]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/int/int.fromEnvironment.html
[bool-from]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/bool/bool.fromEnvironment.html
[bool-has]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/bool/bool.hasEnvironment.html

## 환경 선언 지정 {:#specifying-environment-declarations}

:::warning
Dart 도구와 컴파일러는 현재 쉼표로 구분된 값을 가진 환경 선언을 일관되게 처리하지 않습니다. 
이 처리의 표준화를 추적하려면, [SDK 문제 44995][SDK issue 44995]를 참조하세요.
:::

[SDK issue 44995]: {{site.repo.dart.sdk}}/issues/44995

### Dart CLI {:#dart-cli}

`dart run`과 `dart compile` 하위 명령은 모두 환경 선언 값을 지정하기 위해, 
`-D` 또는 `--define` 옵션을 허용합니다.

```console
$ dart run --define=DEBUG=true -DFLAVOR=free main.dart
$ dart compile exe --define=DEBUG=true -DFLAVOR=free main.dart
$ dart compile js --define=DEBUG=true -DFLAVOR=free main.dart
$ dart compile aot-snapshot --define=DEBUG=true -DFLAVOR=free main.dart
$ dart compile jit-snapshot --define=DEBUG=true -DFLAVOR=free main.dart
$ dart compile kernel --define=DEBUG=true -DFLAVOR=free main.dart
```

#### `webdev` {:#webdev}

To learn about configuring `webdev` to pass environment declarations
to both the development and production web compilers,
check out [the `webdev` configuration documentation][webdev-config].

[webdev-config]: {{site.pub-pkg}}/build_web_compilers#configuring--d-environment-variables

### Visual Studio Code {:#visual-studio-code}

`configurations` 아래의 시작 구성(`launch.json`)에서, 
원하는 환경 선언을 포함하는 새 `toolArgs` 키를 추가합니다.

```json
"configurations": [
    {
        "name": "Dart",
        "request": "launch",
        "type": "dart",
        "toolArgs": [
          "--define=DEBUG=true"
        ]
    }
]
```

자세한 내용은, [VS Code 실행 구성][VSC instructions]에 대한 문서를 확인하세요.

[VSC instructions]: https://code.visualstudio.com/docs/editor/debugging#_launch-configurations

### JetBrains IDEs {:#jetbrains-ides}

프로젝트의 **Run/Debug Configurations**에서 원하는 환경 선언을 **VM options**에 추가합니다.

![Adding define option to Jetbrains IDE](/assets/img/env-decl-jetbrains.png){:width="500"}

자세한 내용은 [Dart 실행/디버그 구성][jetbrains-run-debug]에 대한 JetBrains 문서를 확인하세요.

[jetbrains-run-debug]: https://www.jetbrains.com/help/webstorm/run-debug-configuration-dart-command-line-application.html

### Flutter {:#flutter}

Flutter 도구에 환경 선언을 지정하려면 대신, `--dart-define` 옵션을 사용하세요.

```console
$ flutter run --dart-define=DEBUG=true
```

{%- comment %}
  TODO: Once Flutter adds `--dart-define` documentation:
  To learn more, check out Flutter's documentation on `--dart-define`.
{% endcomment -%}
