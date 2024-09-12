---
# title: Customizing static analysis
title: 정적 분석 커스터마이즈
# description: >-
#   Use an analysis options file and code comments to customize static analysis.
description: >-
  분석 옵션 파일과 코드 주석을 사용하여, 정적 분석을 커스터마이즈합니다.
body_class: highlight-diagnostics
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore: (stable|beta|dev)[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore: (stable|beta|dev)[^\n]+\n/$1\n/g; /. • (lib|test)\/\w+\.dart:\d+:\d+//g"?>

정적 분석을 사용하면, 코드 한 줄을 실행하기 전에 문제를 찾을 수 있습니다. 
버그를 방지하고 코드가 스타일 가이드라인을 준수하는지 확인하는 데 사용되는 강력한 도구입니다.

분석기의 도움으로 간단한 오타를 찾을 수 있습니다. 
예를 들어, 실수로 세미콜론이 `if` 문에 들어갔을 수 있습니다.

<blockquote class="ml-3">

<?code-excerpt "analysis/lib/lint.dart (empty_statements)" replace="/(if .*?)(;)/$1[!$2!]/g"?>
```dart showLineNumbers=8
void increment() {
  if (count < 10) [!;!]
  count++;
}
```

올바르게 구성된 경우, 분석기는 세미콜론을 가리키고 다음 경고를 생성합니다.

<?code-excerpt "analysis/analyzer-results-stable.txt" retain="empty_statements" replace="/lib\/lint.dart/example.dart/g"?>
```plaintext
info - example.dart:9:19 - Unnecessary empty statement. Try removing the empty statement or restructuring the code. - empty_statements
```

</blockquote>

분석기는 또한 더 미묘한 문제를 찾는 데 도움이 될 수 있습니다. 
예를 들어, sink 메서드를 닫는 것을 잊었을 수 있습니다.

<blockquote class="ml-3">

<?code-excerpt "analysis/lib/lint.dart (close_sinks)" replace="/(contr.*?)(;)/[!$1!]$2/g"?>
```dart
var [!controller = StreamController<String>()!];
```

<?code-excerpt "analysis/analyzer-results-stable.txt" retain="close_sinks" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
info - Unclosed instance of 'Sink'. Try invoking 'close' in the function in which the 'Sink' was created. - close_sinks
```

</blockquote>

Dart 생태계에서, Dart Analysis Server와 다른 도구는 [analyzer 패키지]({{site.pub-pkg}}/analyzer)를 사용하여 정적 분석을 수행합니다.

정적 분석을 커스터마이즈하여, [Dart 언어 사양](/guides/language/spec)에 지정된 오류 및 경고를 포함한, 
다양한 잠재적 문제를 찾을 수 있습니다. 
또한 linter 규칙을 구성하여, 
코드가 [Dart 스타일 가이드](/effective-dart/style) 및 [효과적인 Dart][Effective Dart]의 다른 제안된 가이드라인을 준수하도록 할 수 있습니다. 
[`dart analyst`](/tools/dart-analyze), 
[`flutter analyst`]({{site.flutter-docs}}/testing/debugging#the-dart-analyzer) 및 
[IDE 및 편집기](/tools#editors)와 같은 도구는, analyzer 패키지를 사용하여 코드를 평가합니다.

이 문서에서는 분석 옵션 파일이나, Dart 소스 코드의 주석을 사용하여, 
analyzer의 동작을 커스터마이즈하는 방법을 설명합니다. 
도구에 정적 분석을 추가하려면, 
[analyzer 패키지]({{site.pub-pkg}}/analyzer) 문서와 
[분석 서버 API 사양](https://htmlpreview.github.io/?{{site.repo.dart.sdk}}/blob/main/pkg/analysis_server/doc/api.html)을 참조하세요.

:::note
다양한 분석기 진단을 설명과 일반적인 수정 사항과 함께 보려면, [진단 메시지][diagnostics]을 참조하세요.
:::

## 분석 옵션 파일 {:#the-analysis-options-file}

패키지 루트에 pubspec 파일과 같은 디렉토리에, 
분석 옵션 파일 `analysis_options.yaml`을 배치합니다.

다음은 샘플 분석 옵션 파일입니다.

<?code-excerpt "analysis_options.yaml" from="include" remove="implicit-dynamic" retain="/^$|\w+:|- cancel/" remove="https:"?>
```yaml title="analysis_options.yaml"
include: package:lints/recommended.yaml

analyzer:
  exclude: [build/**]
  language:
    strict-casts: true
    strict-raw-types: true

linter:
  rules:
    - cancel_subscriptions
```

샘플은 가장 일반적인 최상위 엔트리를 보여줍니다.

- <code>include: <em>url</em></code>을 사용하여, 지정된 URL에서 옵션을 가져옵니다. 
  이 경우, `lints` 패키지의 파일에서 가져옵니다. 
  YAML은 중복 키를 허용하지 않으므로, 최대 하나의 파일만 포함할 수 있습니다.
- `analyzer:` 항목을 사용하여, 정적 분석을 커스터마이즈합니다.
  [더 엄격한 타입 검사 활성화](#enabling-additional-type-checks),
  [파일 제외](#excluding-files),
  [특정 규칙 무시](#ignoring-rules),
  [규칙의 심각도 변경](#changing-the-severity-of-rules), 또는
  [실험 활성화](/tools/experiment-flags#using-experiment-flags-with-the-dart-analyzer-command-line-and-ide).
- `linter:` 항목을 사용하여, [linter 규칙](#enabling-linter-rules)을 구성합니다.

:::warning
**YAML은 공백을 민감하게 사용합니다.** 
YAML 파일에서는 탭을 사용하지 말고, 
들여쓰기 각 레벨을 나타내기 위해 공백 2개를 사용하세요.
:::

analyzer가 패키지 루트에서 분석 옵션 파일을 찾을 수 없는 경우, 
디렉토리 트리를 올라가서 찾습니다. 
사용 가능한 파일이 없는 경우, analyzer는 표준 검사를 기본으로 합니다.

대규모 프로젝트의 경우, 다음 디렉토리 구조를 고려하세요.

<img 
  src="/assets/img/guides/analysis-options-directory-structure.png"
  alt="project root contains analysis_options.yaml (#1) and 3 packages, one of which (my_package) contains an analysis_options.yaml file (#2).">

분석기는 파일 #1을 사용하여 `my_other_package` 및 `my_other_other_package`의 코드를 분석하고, 
파일 #2를 사용하여 `my_package`의 코드를 분석합니다.


## 더 엄격한 타입 검사 활성화 {:#enabling-additional-type-checks}

[Dart 타입 시스템][type-system]이 요구하는 것보다 더 엄격한 정적 검사를 원하는 경우, 
`strict-casts`, `strict-inference` 및 `strict-raw-types` 언어 모드를 활성화하는 것을 고려하세요.

<?code-excerpt "analysis/analysis_options.yaml" from="analyzer" to="strict-raw-types" remove="exclude"?>
```yaml title="analysis_options.yaml"
analyzer:
  language:
    strict-casts: true
    strict-inference: true
    strict-raw-types: true
```

모드를 함께 또는 개별적으로 사용할 수 있습니다. 모두 기본값은 `false`입니다.

`strict-casts: <bool>`
: `true` 값은 유형 추론 엔진이 `dynamic`에서 더 구체적인 타입으로 암묵적으로 캐스트하지 않도록 합니다. 
  다음의 유효한 Dart 코드에는 `jsonDecode`에서 반환된 `dynamic` 값에서, 
  `List<String>`로의 암묵적 다운캐스트가 포함되어 있으며, 
  이는 런타임에 실패할 수 있습니다. 
  이 모드는 잠재적 오류를 보고하므로, 명시적 캐스트를 추가하거나 코드를 조정해야 합니다.

<?code-excerpt "analysis/lib/strict_modes.dart (strict-casts)" replace="/jsonDecode\(jsonText\)/[!$&!]/g"?>
```dart tag=fails-sa
void foo(List<String> lines) {
  ...
}

void bar(String jsonText) {
  foo([!jsonDecode(jsonText)!]); // 암묵적 캐스트
}
```

<?code-excerpt "analysis/analyzer-results-stable.txt" retain="The argument type 'dynamic' can't be assigned"  replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
error - The argument type 'dynamic' can't be assigned to the parameter type 'List<String>'. - argument_type_not_assignable
```

:::version-note
`strict-casts` 모드는 Dart 2.16에서 도입되었습니다. 
이전 SDK 릴리스에서 유사한 검사를 활성화하려면, 
더 이상 사용되지 않는 `implicit-casts` 옵션을 사용하는 것을 고려하세요.

```yaml
analyzer:
  strong-mode:
    implicit-casts: false
```
:::

`strict-inference: <bool>`
: `true` 값은 타입 추론 엔진이 정적 타입을 결정할 수 없을 때, 
  `dynamic` 타입을 선택하지 않도록 보장합니다. 
  다음의 유효한 Dart 코드는 타입 인수를 추론할 수 없는 `Map`을 생성하여, 
  이 모드에서 추론 실패 힌트를 생성합니다.

<?code-excerpt "analysis/lib/strict_modes.dart (strict-inference)" replace="/{}/[!$&!]/g"?>
```dart tag=fails-sa
final lines = [!{}!]; // 추론 실패
lines['Dart'] = 10000;
lines['C++'] = 'one thousand';
lines['Go'] = 2000;
print('Lines: ${lines.values.reduce((a, b) => a + b)}'); // 런타임 에러
```
{:analyzer}

<?code-excerpt "analysis/analyzer-results-stable.txt" retain="The type argument(s) of 'Map'"  replace="/. Use.*'Map'. / /g; /-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
warning - The type argument(s) of 'Map' can't be inferred - inference_failure_on_collection_literal
```

:::tip
`strict-inference` 모드는 추론 실패로 이어지는 많은 상황을 식별할 수 있습니다.

추론 실패 조건의 전체 리스트는 [엄격한 추론 실패 조건][Conditions for strict inference failure]을 참조하세요.
:::

[Conditions for strict inference failure]: {{site.repo.dart.lang}}/blob/main/resources/type-system/strict-inference.md#conditions-for-strict-inference-failure

`strict-raw-types: <bool>`
: `true` 값은 생략된 타입 인수로 인해, 정적 타입을 결정할 수 없을 때, 
  타입 추론 엔진이 `dynamic` 타입을 선택하지 않도록 보장합니다. 
  다음 유효한 Dart 코드에는 raw 타입이 있는 `List` 변수가 있어, 
  이 모드에서 raw 타입 힌트가 생성됩니다.

<?code-excerpt "analysis/lib/strict_modes.dart (strict-raw-types)" replace="/List n/[!List!] n/g"?>
```dart tag=fails-sa
[!List!] numbers = [1, 2, 3]; // raw 타입이 있는 리스트
for (final n in numbers) {
  print(n.length); // 런타임 에러
}
```

<?code-excerpt "analysis/analyzer-results-stable.txt" retain="The generic type" replace="/. Use explicit.*\. / /g; /-(.*?):(.*?):(.*?)-/-/g"?>
```plaintext
warning - The generic type 'List<dynamic>' should have explicit type arguments but doesn't - strict_raw_type
```

## 린터 규칙 활성화 및 비활성화 {:#enabling-linter-rules}

analyzer 패키지는 코드 린터도 제공합니다. 
다양한 [린터 규칙][linter rules]을 사용할 수 있습니다. 
린터는 일반적으로 초종교적—규칙(nondenominational—rules)입니다. 즉, 규칙이 서로 일치할 필요가 없습니다. 
예를 들어, 일부 규칙은 일반 Dart 패키지에 더 적합하고, 
다른 규칙은 Flutter 앱에 맞게 설계되었습니다. 
정적 분석과 달리, 린터 규칙에는 거짓 양성이 있을 수 있습니다.

### Dart 팀에서 권장하는 린터 규칙 활성화 {:#lints}

Dart 팀은 [lints 패키지][lints package]에서 두 가지 권장 linter 규칙 세트를 제공합니다.

코어 규칙
: Dart 코드를 실행하거나 사용할 때, 문제가 발생할 가능성이 있는 중요한 문제를 식별하는 데 도움이 됩니다. 
  모든 코드는 이러한 린터 규칙을 통과해야 합니다. 
  [pub.dev]({{site.pub}})에 업로드된 패키지는, 
  이러한 규칙을 통과하는 것에 따라 부분적으로 [패키지 점수]({{site.pub}}/help/scoring)를 갖습니다.

권장 규칙
: Dart 코드를 실행하거나 사용할 때, 문제가 발생할 수 있는 추가 문제를 식별하고, 
  단일하고 관용적인 스타일과 형식을 적용하는 데 도움이 됩니다. 
  모든 Dart 코드에서 핵심 규칙의 상위 집합인 이러한 규칙을 사용하는 것이 좋습니다.

:::tip
Flutter 코드 작업을 하는 경우, `lints` 패키지를 사용하는 대신, 
권장 규칙의 상위 집합을 제공하는 [`flutter_lints`]({{site.pub-pkg}}/flutter_lints)를 사용하세요.
:::

두 가지 린트 세트를 모두 활성화하려면, [lints 패키지][lints package]를 개발 종속성으로 추가하세요.

```console
$ dart pub add --dev lints
```

그런 다음 `analysis_options.yaml` 파일을 편집하여, 원하는 규칙 세트를 포함시킵니다.

```yaml
include: package:lints/<RULE_SET>.yaml
```

예를 들어, 다음과 같이 권장되는 규칙 세트를 포함할 수 있습니다.

```yaml
include: package:lints/recommended.yaml
```

:::important
**새로운 버전의 `lints`**가 게시되면, 
이전에 분석을 통과한 코드가 **분석에 실패하기 시작할 수 있습니다.** 
새 규칙에 맞게 코드를 업데이트하는 것이 좋습니다. 
다른 옵션은 개별 린터 규칙을 명시적으로 활성화하거나, 
[개별 규칙 비활성화][disable individual rules]하는 것입니다.
:::

[lints package]: {{site.pub-pkg}}/lints

### 개별 규칙 활성화 {:#individual-rules}

단일 린터 규칙을 활성화하려면, 분석 옵션 파일에 `linter:`를 최상위 키로 추가한 다음, 
`rules:`를 두 번째 키로 추가합니다. 
이후 줄에서는 적용하려는 규칙을 대시(YAML 목록의 구문)로 접두사로 지정합니다. 
예를 들어:

<?code-excerpt "analysis_options.yaml" from="linter:" take="12" remove="https:"?>
```yaml
linter:
  rules:
    - always_declare_return_types
    - cancel_subscriptions
    - close_sinks
    - combinators_ordering
    - comment_references
    - invalid_case_patterns
    - library_annotations
    - one_member_abstracts
    - only_throw_errors
```


### 개별 규칙 비활성화 {:#disabling-individual-rules}

`lints`에 있는 것과 같은 분석 옵션 파일을 포함하는 경우, 포함된 규칙 중 일부를 비활성화할 수 있습니다. 
개별 규칙을 비활성화하는 것은 규칙을 활성화하는 것과 비슷하지만, 
`rules:` 항목의 값으로 리스트가 아닌 맵을 사용해야 하므로, 
각 줄에는 규칙 이름 뒤에 `: false` 또는 `: true`가 포함되어야 합니다.

`avoid_shadowing_type_parameters`를 제외한, 
`lints`의 모든 권장 규칙을 사용하는 분석 옵션 파일의 예는 다음과 같습니다. 
또한 lint `await_only_futures`도 활성화합니다.

<?code-excerpt "analysis_alt/analysis_options_linter.yaml"?>
```yaml title="analysis_options.yaml"
include: package:lints/recommended.yaml

linter:
  rules:
    avoid_shadowing_type_parameters: false
    await_only_futures: true
```

:::note
YAML 제한으로 인해, 
**동일한 `rules` 항목에서 리스트 및 키-값 구문을 혼합하여 사용할 수 없습니다.** 
포함된 파일의 규칙에 다른 구문을 사용할 수 있습니다.
:::

## analyzer 플러그인 활성화 (experimental) {:#plugins}

analyzer는 플러그인에 대한 실험적 지원을 제공합니다. 
이러한 플러그인은 analyzer와 통합되어, 새로운 진단, 빠른 수정, 커스텀 코드 완성과 같은 기능을 추가합니다. 
`analysis_options.yaml` 파일당 하나의 플러그인만 활성화할 수 있습니다. 
analyzer 플러그인을 활성화하면, analyzer가 사용하는 메모리 양이 늘어납니다.

상황이 다음 조건 중 하나를 충족하는 경우 analyzer 플러그인을 사용하지 마세요.

* 메모리가 16GB 미만인 개발 머신을 사용합니다.
* `pubspec.yaml` 및 `analysis_options.yaml` 파일이 10개 이상인 모노 리포를 사용합니다.

[pub.dev]({{site.pub-pkg}}?q=dependency%3Aanalyzer_plugin)에서, 
몇 가지 analyzer 플러그인을 찾을 수 있습니다.

플러그인을 활성화하려면 다음을 수행합니다.

1. 플러그인이 포함된 패키지를 개발 종속성으로 추가합니다.

    ```console
    $ dart pub add --dev <your_favorite_analyzer_plugin_package>
    ```

2. 플러그인을 활성화하려면, `analysis_options.yaml` 파일을 편집합니다.

    ```yaml
    analyzer:
      plugins:
        - your_favorite_analyzer_plugin_package
    ```

    새로운 진단 등의 특정 플러그인 기능을 활성화하려면, 추가 설정이 필요할 수 있습니다.

## 분석에서 코드 제외 {:#excluding-code-from-analysis}

어떤 코드는 분석에 실패해도 괜찮습니다. 
예를 들어, 소유하지 않은 패키지에서 생성된 코드에 의존할 수 있습니다. 
생성된 코드는 작동하지만, 정적 분석 중에 경고를 생성합니다. 
또는 린터 규칙으로 인해 억제하려는 거짓 양성이 발생할 수 있습니다.

분석에서 코드를 제외하는 방법은 여러 가지가 있습니다.

* 전체 파일을 분석에서 제외합니다.
* 특정 오류가 아닌 규칙이 개별 파일에 적용되지 않도록 합니다.
* 특정 오류가 아닌 규칙이 개별 코드 줄에 적용되지 않도록 합니다.

또한 모든 파일에 대해 [특정 규칙을 비활성화][disable individual rules]하거나, 
[규칙의 심각도를 변경][change the severity of rules]할 수도 있습니다.


### 파일 제외 {:#excluding-files}

정적 분석에서 파일을 제외하려면, `exclude:` 분석기 옵션을 사용합니다. 
개별 파일을 나열하거나, [glob]({{site.pub-pkg}}/glob) 패턴 구문을 사용할 수 있습니다. 
모든 glob 패턴 사용은 `analysis_options.yaml` 파일이 포함된 디렉토리를 기준으로 해야 합니다.

<?code-excerpt "analysis_alt/analysis_options.yaml (exclude)" plaster="none"?>
```yaml
analyzer:
  exclude:
    - lib/client.dart
    - lib/server/*.g.dart
    - test/_data/**
```

<a id="suppressing-rules-for-a-file"></a>
### 파일 진단 억제 {:#suppressing-diagnostics-for-a-file}

특정 파일에 대한 특정 비오류 진단을 무시하려면, 
파일에 `ignore_for_file` 주석을 추가하세요.

<?code-excerpt "analysis/lib/assignment.dart (ignore_for_file)" replace="/, \w+//g"?>
```dart
// ignore_for_file: unused_local_variable
```

이것은 주석 앞이나 뒤에 있는 전체 파일에 적용되며, 생성된 코드에 특히 유용합니다.

두 개 이상의 진단을 억제하려면, 쉼표로 구분된 리스트를 사용합니다.

<?code-excerpt "analysis/lib/assignment.dart (ignore_for_file)"?>
```dart
// ignore_for_file: unused_local_variable, duplicate_ignore, dead_code
```

모든 linter 규칙을 억제하려면, `type=lint` 지정자를 추가합니다.

<?code-excerpt "analysis/lib/ignore_lints.dart (ignore_type_for_file)"?>
```dart
// ignore_for_file: type=lint
```

:::version-note
Dart 2.15에서 `type=lint` 지정자에 대한 지원이 추가되었습니다.
:::

<a id="suppressing-rules-for-a-line-of-code"></a>
### 코드 줄에 대한 진단 억제 {:#suppressing-diagnostics-for-a-line-of-code}

Dart 코드의 특정 줄에서 특정 비오류 진단을 억제하려면, 
코드 줄 위에 `ignore` 주석을 넣습니다. 
다음은 언어 테스트에서 할 수 있듯이, 
런타임 오류를 일으키는 코드를 무시하는 예입니다.

<?code-excerpt "analysis/lib/assignment.dart (invalid_assignment)"?>
```dart
// ignore: invalid_assignment
int x = '';
```

두 개 이상의 진단을 억제하려면, 쉼표로 구분된 리스트를 제공하세요.

<?code-excerpt "analysis/lib/assignment.dart (ignore-more)"?>
```dart
// ignore: invalid_assignment, const_initialized_with_non_constant_value
const x = y;
```

또는, 해당 줄에 ignore 주석을 추가합니다.

<?code-excerpt "analysis/lib/assignment.dart (single-line)"?>
```dart
int x = ''; // ignore: invalid_assignment
```

### pubspec 파일에서 진단 억제 {:#suppressing-diagnostics-in-a-pubspec-file}

`pubspec.yaml` 파일에서 analyzer에서 오류가 아닌 진단을 억제해야 하는 경우, 
영향을 받는 줄 위에 `ignore` 주석을 추가합니다.

다음 예제는 `flutter` 종속성을 먼저 넣으려고 하기 때문에, 
[`sort_pub_dependencies`][] 린트를 igonre 합니다.

```yaml title="pubspec.yaml".
dependencies:
  flutter:
    sdk: flutter

  # ignore: sort_pub_dependencies
  collection: ^1.19.0
```

:::version-note
`pubspec.yaml` 파일에서 ignore 주석에 대한 지원이 Dart 3.3에 추가되었습니다. 
Dart 3.2 이하를 사용하는 경우, ignore 된 진단이 여전히 트리거됩니다.
:::

[`sort_pub_dependencies`]: /tools/linter-rules/sort_pub_dependencies

## 분석 규칙 커스터마이즈 {:#customizing-analysis-rules}

각 [analyzer 진단][analyzer diagnostics] 및 [린터 규칙][linter rules]에는 기본 심각도가 있습니다. 
분석 옵션 파일을 사용하여 개별 규칙의 심각도를 변경하거나, 일부 규칙을 항상 ignore 할 수 있습니다.

analyzer는 세 가지 심각도 수준을 지원합니다.

`info`
: 분석 실패를 유발하지 않는, 정보 메시지입니다.
  예: [`dead_code`][dead_code]

`warning`
: analyzer가 경고를 오류로 처리하도록 구성되지 않은 한, 분석 실패를 유발하지 않는, 경고입니다.
  예: [`invalid_null_aware_operator`][invalid_null_aware_operator]

`error`
: 분석 실패를 유발하는 오류입니다.
  예: [`invalid_assignment`][invalid_assignment]


### 규칙 무시 {:#ignoring-rules}

`errors:` 필드를 사용하여, 특정 [analyzer 진단][analyzer diagnostics] 및 [린터 규칙][linter rules]을 무시할 수 있습니다. 
규칙을 나열한 다음, <code>:&nbsp;ignore</code>를 추가합니다. 
예를 들어, 다음 분석 옵션 파일은 분석 도구에 TODO 규칙을 무시하도록 지시합니다.

<?code-excerpt "analysis_alt/analysis_options.yaml (errors)" to="ignore"?>
```yaml
analyzer:
  errors:
    todo: ignore
```


### 규칙의 심각도 변경 {:#changing-the-severity-of-rules}

특정 규칙의 심각도를 전역적으로 변경할 수 있습니다. 
이 기술은 일반 분석 문제뿐만 아니라 린트에도 효과적입니다. 
예를 들어, 다음 분석 옵션 파일은 분석 도구에, 
잘못된 할당을 경고로, 누락된 반환을 오류로 처리하고, 
데드 코드에 대한 정보(경고나 오류는 아님)를 제공하도록 지시합니다.

<?code-excerpt "analysis_alt/analysis_options.yaml (errors)" remove="ignore"?>
```yaml
analyzer:
  errors:
    invalid_assignment: warning
    missing_return: error
    dead_code: info
```


## 리소스 {:#resources}

다음 리소스를 사용하여, Dart의 정적 분석에 대해 자세히 알아보세요.

* [Dart의 타입 시스템][type-system]
* [Dart linter 규칙][linter rules]
* [analyzer 패키지]({{site.pub-pkg}}/analyzer)

[invalid_null_aware_operator]: /tools/diagnostic-messages#invalid_null_aware_operator
[analyzer diagnostics]: /tools/diagnostic-messages
[change the severity of rules]: #changing-the-severity-of-rules
[diagnostics]: /tools/diagnostic-messages
[invalid_assignment]: /tools/diagnostic-messages#invalid_assignment
[language version]: /guides/language/evolution#language-versioning
[linter rules]: /tools/linter-rules
[type-system]: /language/type-system
[dead_code]: /tools/diagnostic-messages#dead_code
[disable individual rules]: #disabling-individual-rules
[Effective Dart]: /effective-dart
