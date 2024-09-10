---
# title: Package layout conventions
title: 패키지 레이아웃 규칙
# description: >-
#   Learn more about the directory structure used by
#   Dart's package management tool, pub.
description: >-
  Dart의 패키지 관리 도구인 pub에서 사용하는 디렉토리 구조에 대해 자세히 알아보세요.
---

[pub 패키지](/guides/packages)를 빌드할 때, 
이 페이지에서 설명하는 규칙을 따르도록 권장합니다. 
이는 패키지 내에서 파일과 디렉토리를 구성하는 방법과, 
사물의 이름을 지정하는 방법을 설명합니다.

:::flutter-note
Flutter 앱은 assets에 커스텀 디렉토리를 사용할 수 있습니다. 
자세한 내용은 [Flutter 웹사이트]({{site.flutter-docs}})에서 
[assets 및 이미지 추가]({{site.flutter-docs}}/development/ui/assets-and-images)를 참조하세요.
:::

이러한 가이드라인의 모든 내용을 활용한 완전한 패키지(`enchilada`라는 이름)는 다음과 같습니다.

```plaintext
enchilada/
  .dart_tool/ *
  pubspec.yaml
  pubspec.lock **
  LICENSE
  README.md
  CHANGELOG.md
  benchmark/
    make_lunch.dart
  bin/
    enchilada
  doc/
    api/ ***
    getting_started.md
  example/
    main.dart
  hook/
    build.dart
  integration_test/
    app_test.dart
  lib/
    enchilada.dart
    tortilla.dart
    guacamole.css
    src/
      beans.dart
      queso.dart
  test/
    enchilada_test.dart
    tortilla_test.dart
  tool/
    generate_docs.dart
  web/
    index.html
    main.dart
    style.css
```    

\* `.dart_tool/` 디렉토리는 `dart pub get`을 실행한 후에 존재합니다. 
   소스 제어에 체크인하지 마세요. 
   자세한 내용은 [도구에 대한 프로젝트별 캐싱](#project-specific-caching-for-tools)을 참조하세요.

\** `pubspec.lock` 파일은 `dart pub get`을 실행한 후에 존재합니다. 
   패키지가 [애플리케이션 패키지](/tools/pub/glossary#application-package)가 아니면, 
   소스 제어에서 제외하세요.

\*** `doc/api` 디렉토리는 [`dart doc`](/tools/dart-doc)을 실행한 후에 로컬에 존재합니다. 
   `api` 디렉토리를 소스 제어에 체크인하지 마세요.

## pubspec {:#the-pubspec}

```plaintext
enchilada/
  pubspec.yaml
  pubspec.lock
```

모든 패키지에는 패키지의 루트 디렉토리에 `pubspec.yaml`이라는 파일인, 
[_pubspec_](/tools/pub/pubspec)이 있습니다. 
이것이 패키지를 *만드는* 것입니다.

패키지에서 [`dart pub get`](/tools/pub/cmd/pub-get), [`dart pub upgrade`](/tools/pub/cmd/pub-upgrade), 또는 [`dart pub downgrade`](/tools/pub/cmd/pub-downgrade)를 실행하면, 
`pubspec.lock`이라는 **lockfile**이 생성됩니다. 
패키지가 [애플리케이션 패키지](/tools/pub/glossary#application-package)인 경우, 
잠금 파일을 소스 제어에 체크인합니다. 
그렇지 않은 경우 체크인하지 마세요.

자세한 내용은 [pubspec 페이지](/tools/pub/pubspec)를 참조하세요.

## LICENSE {:#license}

```plaintext
enchilada/
  LICENSE
```

패키지를 게시하는 경우, `LICENSE`라는 라이선스 파일을 포함합니다. 
다른 사람이 당신의 작업을 재사용할 수 있도록, [BSD-3-Clause](https://opensource.org/licenses/BSD-3-Clause)와 같은 [OSI 승인 라이선스](https://opensource.org/licenses)를 사용하는 것이 좋습니다.

## README.md {:#readme-md}

```plaintext
enchilada/
  README.md
```

오픈 소스에서 매우 일반적인 파일 중 하나는 프로젝트를 설명하는 _README_ 파일입니다. 
이것은 pub에서 특히 중요합니다. 
[pub.dev 사이트]({{site.pub}})에 업로드하면, 
`README.md` 파일이 패키지 페이지에 [Markdown][]으로 렌더링되어 표시됩니다. 
이것은 사람들에게 코드를 소개하기에 완벽한 장소입니다.

훌륭한 README를 작성하는 방법에 대한 지침은, 
[패키지 페이지 작성](/tools/pub/writing-package-pages)을 참조하세요.

<a id="changelogmd"></a>
## CHANGELOG.md {:#changelog}

```plaintext
enchilada/
  CHANGELOG.md
```

패키지의 각 릴리스에 대한 섹션이 있는 `CHANGELOG.md` 파일을 포함시키고, 
패키지 사용자가 업그레이드하는 데 도움이 되는 메모를 포함합니다. 
패키지 사용자는 종종 변경 로그를 검토하여 버그 수정 및 새로운 기능을 발견하거나, 
패키지의 최신 버전으로 업그레이드하는 데 얼마나 많은 노력이 필요한지 확인합니다.

`CHANGELOG.md`를 구문 분석하는 도구를 지원하려면 다음 형식을 사용하세요.

* 각 버전에는 heading이 있는 자체 섹션이 있습니다.
* 버전 heading은 모두 레벨 1이거나 모두 레벨 2입니다.
* 버전 heading 텍스트에는 패키지 버전 번호가 포함되며, 선택적으로 "v"로 접두사가 붙습니다.

패키지를 [pub.dev 사이트]({{site.pub}})에 업로드하면, 
패키지의 `CHANGELOG.md` 파일(있는 경우)이 **Changelog** 탭에 [Markdown][]으로 렌더링되어 나타납니다.

다음은 `CHANGELOG.md` 파일의 예입니다. 예에서 알 수 있듯이, 하위 섹션을 추가할 수 있습니다.

```markdown
# 1.0.1

* Fixed missing exclamation mark in `sayHi()` method.

# 1.0.0

* **Breaking change:** Removed deprecated `sayHello()` method.
* Initial stable release.

## Upgrading from 0.1.x

Change all calls to `sayHello()` to instead be to `sayHi()`.

# 0.1.1

* Deprecated the `sayHello()` method; use `sayHi()` instead.

# 0.1.0

* Initial development release.
```


## 공개 디렉토리 {:#public-directories}

패키지의 두 디렉토리는 다른 패키지에 공개됩니다: `lib` 및 `bin`. 

`lib`에 [공개 라이브러리](#public-libraries)를 넣고, `bin`에 [공개 도구](#public-tools)를 넣습니다.

### 공개 라이브러리 {:#public-libraries}

다음 디렉토리 구조는 enchilada의 `lib` 부분을 보여줍니다.

```plaintext
enchilada/
  lib/
    enchilada.dart
    tortilla.dart
```

많은 [패키지](/tools/pub/glossary#package)는 다른 패키지가 import해서 사용할 수 있는, 
Dart 라이브러리를 정의합니다. 
이러한 공개 Dart 라이브러리 파일은 `lib`라는 디렉터리에 있습니다.

대부분의 패키지는 사용자가 import할 수 있는 단일 라이브러리를 정의합니다. 
이 경우, 이름은 일반적으로 여기 예에서 `enchilada.dart`와 같이 패키지 이름과 동일해야 합니다. 
하지만 패키지에 적합한 이름으로 다른 라이브러리를 정의할 수도 있습니다.

이렇게 하면, 사용자는 다음과 같이 패키지 이름과 라이브러리 파일을 사용하여 이러한 라이브러리를 import할 수 있습니다.

```dart
import 'package:enchilada/enchilada.dart';
import 'package:enchilada/tortilla.dart';
```

공개 라이브러리를 정리하고 싶다면, `lib` 내부에 하위 디렉토리를 만들 수도 있습니다. 
그렇게 하면, 사용자가 import할 때 해당 경로를 지정하게 됩니다. 
다음과 같은 파일 계층이 있다고 가정해 보겠습니다.

```plaintext
enchilada/
  lib/
    some/
      path/
        olives.dart
```

사용자는 다음과 같이 `olives.dart`를 import 합니다.

```dart
import 'package:enchilada/some/path/olives.dart';
```

*라이브러리*만 `lib`에 있어야 합니다. 
*진입점*—`main()` 함수가 있는 Dart 스크립트—은 `lib`에 들어갈 수 없습니다. 
Dart 스크립트를 `lib`에 넣으면, 포함된 모든 `package:` import가 해결되지 않는다는 것을 알게 될 것입니다. 
대신, 진입점은 적절한 [진입점 디렉터리](/tools/pub/glossary#entrypoint-directory)에 들어가야 합니다.

:::note 웹 앱을 위한 팁
웹 앱을 개발할 때 최상의 성능을 위해, 
[구현 파일](#implementation-files)을 `/lib` 아래의 다른 곳이 아닌, `/lib/src` 아래에 두세요. 
또한 <code>package:<em>package_name</em>/src/...</code>의 import를 피하세요.
:::

패키지에 대한 자세한 내용은, [패키지 생성](/guides/libraries/create-packages)을 참조하세요.

### 공개 도구 {:#public-tools}

`bin` 디렉토리에 배치된 Dart 스크립트는 공개됩니다. 
패키지 디렉토리에 있는 경우, [`dart run`](/tools/dart-run)을 사용하여, 
패키지가 종속된 다른 패키지의 `bin` 디렉토리에서 스크립트를 실행할 수 있습니다. 
_모든_ 디렉토리에서 [`dart pub global activate`][activate]를 사용하여, 
활성화한 패키지에서 [스크립트를 실행][run scripts]할 수 있습니다.

[run scripts]: /tools/pub/cmd/pub-global#running-a-script
[activate]: /tools/pub/cmd/pub-global#activating-a-package

패키지에 의존하도록 의도하고, 스크립트를 패키지에 private로 하려면, 최상위 `tool` 디렉토리에 두세요. 
패키지에 의존하도록 의도하지 않으면, 스크립트를 `bin`에 두어도 됩니다.


## 공개 assets {:#public-assets}

```plaintext
enchilada/
  lib/
    guacamole.css
```

대부분의 패키지는 Dart 코드를 재사용할 수 있도록 존재하지만, 다른 종류의 콘텐츠도 재사용할 수 있습니다. 
예를 들어, [Bootstrap](https://getbootstrap.com/) 패키지에는, 
패키지 소비자가 사용할 수 있는 여러 CSS 파일이 포함될 수 있습니다.

이것들은 최상위 `lib` 디렉터리에 있습니다. 
원하는 대로 모든 종류의 파일을 넣고 하위 디렉터리로 구성할 수 있습니다.


## 구현 파일 {:#implementation-files}

```plaintext
enchilada/
  lib/
    src/
      beans.dart
      queso.dart
```

`lib` 내부의 라이브러리는 공개적으로 볼 수 있습니다. 다른 패키지는 이를 자유롭게 import할 수 있습니다. 
하지만, 패키지 코드의 대부분은 패키지 자체에서만 import해서 사용해야 하는 내부 구현 라이브러리입니다. 
이러한 라이브러리는 `src`라는 `lib`의 하위 디렉터리에 있습니다. 
정리하는 데 도움이 된다면 거기에 하위 디렉터리를 만들 수 있습니다.

`lib/src`에 있는 라이브러리는 *같은* 패키지의 다른 Dart 코드에서 자유롭게 import 할 수 있습니다.
(예: `lib`의 다른 라이브러리, `bin`의 스크립트, 테스트) 
하지만, 다른 패키지의 `lib/src` 디렉터리에서 import 하면 안 됩니다. 
이러한 파일은 패키지의 공개 API에 속하지 않으며, 코드를 손상시킬 수 있는 방식으로 변경될 수 있습니다.

자신의 패키지 내에서 라이브러리를 가져오는 방법은 라이브러리의 위치에 따라 달라집니다.

* [`lib/` 내부 또는 외부에 도달할 때][reaching inside or outside `lib/`] 
  (lint: [_avoid_relative_lib_imports_][]) `package:`를 사용합니다.
* 그렇지 않은 경우, [상대적 import를 선호합니다][prefer relative imports].
 
 [reaching inside or outside `lib/`]: /effective-dart/usage#dont-allow-an-import-path-to-reach-into-or-out-of-lib
 [_avoid_relative_lib_imports_]: /tools/linter-rules/avoid_relative_lib_imports
 [prefer relative imports]: /effective-dart/usage#prefer-relative-import-paths

예를 들어:

```dart title="lib/beans.dart"
// lib 내부에서 import 할 때:
import 'src/beans.dart';
```

```dart title="test/beans_test.dart"
// lib 외부에서 import 할 때:
import 'package:enchilada/src/beans.dart';
```

여기에서 사용하는 이름(이 경우 `enchilada`)은, 
[pubspec](/tools/pub/pubspec)에서 패키지에 지정한 이름입니다.

## Web 파일 {:#web-files}

```plaintext
enchilada/
  web/
    index.html
    main.dart
    style.css
```

웹 패키지의 경우, `main()`과 (CSS 또는 HTML과 같은) 지원 파일을 포함하는 Dart 스크립트인, 
진입점 코드를 `web` 아래에 배치합니다. 
원하는 경우, `web` 디렉토리를 하위 디렉토리로 구성할 수 있습니다.

`lib` 아래에 [라이브러리 코드](#public-libraries)를 배치합니다. 
라이브러리가 `web` 아래의 코드나 다른 패키지에서 직접 import 하지 않는 경우, `lib/src` 아래에 배치합니다. 
`example` 아래에 [웹 기반 예제](#examples)를 배치합니다. 
이미지와 같은 에셋을 배치할 위치에 대한 팁은 [공개 에셋](#public-assets)을 참조하세요.

## Command-line 앱 {:#command-line-apps}

```plaintext
enchilada/
  bin/
    enchilada
```

일부 패키지는 명령줄에서 직접 실행할 수 있는 프로그램을 정의합니다. 
이는 셸 스크립트나 Dart를 포함한 다른 스크립팅 언어일 수 있습니다.

패키지가 이와 같은 코드를 정의하는 경우, `bin`이라는 이름의 디렉토리에 넣습니다. 
[`dart pub global`](/tools/pub/cmd/pub-global#running-a-script-from-your-path)을 사용하여 설정한 경우, 
명령줄의 어느 곳에서나 해당 스크립트를 실행할 수 있습니다.

## 테스트 및 벤치마크 {:#tests-and-benchmarks}

```plaintext
enchilada/
  test/
    enchilada_test.dart
    tortilla_test.dart
```

모든 패키지에는 테스트가 있어야 합니다. 
pub의 경우, 이러한 테스트의 대부분은 `test` 디렉토리(또는 원하는 경우 그 안의 디렉토리)에 들어가고, 
파일 이름 끝에 `_test`가 붙습니다.

일반적으로 이러한 테스트는 [test]({{site.pub-pkg}}/test) 패키지를 사용합니다.

```plaintext
enchilada/
  integration_test/
    app_test.dart
```

Flutter 앱 패키지에는 [integration_test]({{site.flutter-docs}}/cookbook/testing/integration/introduction) 패키지를 사용하는 특수 통합 테스트도 있을 수 있습니다. 
이러한 테스트는 자체 `integration_test` 디렉터리에 있습니다.

다른 패키지는 비슷한 패턴을 따라, 느린 통합 테스트를 유닛 테스트와 분리하기로 선택할 수 있지만, 
기본적으로 `dart test`는 이러한 테스트를 실행하지 않습니다. 
`dart test integration_test`로 명시적으로 실행해야 합니다.

```plaintext
enchilada/
  benchmark/
    make_lunch.dart
```

성능이 중요한 코드가 있는 패키지에는 *벤치마크*도 포함될 수 있습니다. 
이는 API의 정확성이 아니라 속도(또는 메모리 사용, 또는 다른 경험적 지표)를 테스트합니다.

## 문서 {:#documentation}

```plaintext
enchilada/
  doc/
    api/
    getting_started.md
```

코드와 테스트가 있다면, 다음으로 원하는 것은 좋은 문서일 것입니다. 
그것은 `doc`이라는 디렉토리에 들어갑니다.

[`dart doc`](/tools/dart-doc) 도구를 실행하면, 
기본적으로 API 문서가 `doc/api`에 저장됩니다. 
API 문서는 소스 코드에서 생성되므로, 소스 제어에 두어서는 안 됩니다.

생성된 `api` 외에는, 작성한 문서의 형식이나 구성에 대한 지침이 없습니다. 
원하는 마크업 형식을 사용하세요.

## 예제 {:#examples}

```plaintext
enchilada/
  example/
    main.dart
```

코드, 테스트, 문서, 그 밖에 사용자가 원하는 것이 무엇이 있을까요? 
물론 패키지를 사용하는 독립 실행형 예제 프로그램입니다! 
이러한 프로그램은 `example` 디렉토리에 있습니다. 
예제가 복잡하고 여러 파일을 사용하는 경우, 각 예제에 대한 디렉토리를 만드는 것을 고려하세요. 
그렇지 않으면, 각각을 `example` 바로 안에 넣을 수 있습니다.

예제에서, `package:`를 사용하여 자체 패키지에서 파일을 import 합니다. 
그러면, 패키지의 예제 코드가 패키지 외부의 코드와 정확히 동일하게 보입니다.

패키지를 게시할 경우, 다음 이름 중 하나로 예제 파일을 만드는 것을 고려하세요.

* <code>example/example[.md]</code>
* <code>example[/lib]/main.dart</code>
* <code>example[/lib]/<em>package_name</em>.dart</code>
* <code>example[/lib]/<em>package_name</em>_example.dart</code>
* <code>example[/lib]/example.dart</code>
* <code>example/README[.md]</code>

위에 나열된 파일 중 하나 이상을 포함하는 패키지를 게시하면, 
pub.dev 사이트는 찾은 첫 번째 파일을 표시하는 **Example** 탭을 만듭니다.
(위의 목록에 표시된 순서대로 검색) 
예를 들어, 패키지의 `example` 디렉토리에 `README.md`라는 파일을 포함하여 여러 파일이 있는 경우, 
패키지의 예제 탭은 `example/README.md`의 내용을 표시합니다.
([Markdown][]으로 구문 분석됨)

{% comment %}
To see how the example file is chosen,
search the dart-lang repos for exampleFileCandidates:
https://github.com/search?q=org%3Adart-lang+exampleFileCandidates&type=Code
{% endcomment %}

## 내부 도구 및 스크립트 {:#internal-tools-and-scripts}

```plaintext
enchilada/
  tool/
    generate_docs.dart
```

성숙한 패키지에는 종종 사람들이 패키지 자체를 개발하는 동안 실행하는 작은 도우미 스크립트와 프로그램이 있습니다. 
테스트 러너, 문서 생성기 또는 기타 자동화 비트와 같은 것을 생각해보세요.

`bin`의 스크립트와 달리, 이것들은 패키지의 외부 사용자를 위한 것이 *아닙니다*. 
이것들 중 하나라도 있다면, `tool`이라는 디렉토리에 두세요.

## 후크 (Hooks) {:#hooks}

```plaintext
enchilada/
  hook/
    build.dart
```

패키지는 Dart 및 Flutter SDK에서 호출할 후크(hooks)를 정의할 수 있습니다. 
이러한 후크에는 미리 정의된 CLI가 있으며, SDK 도구가 있는 경우 해당 도구에서 호출됩니다.

이러한 후크는 실행 및 빌드 시 `dart` 및 `flutter` 도구에서 호출되므로, 
이러한 후크의 종속성은 `dev_dependencies`가 아닌 일반 종속성이어야 합니다.

:::note
후크 지원은 **실험적**이며 활발하게 개발 중입니다.

후크를 정의하는 방법과 현재 상태에 대해 자세히 알아보려면, [`build.dart` 후크 문서][`build.dart` hook documentation]를 ​​참조하세요.
:::


[`build.dart` hook documentation]: {{site.repo.dart.org}}/native/blob/main/pkgs/native_assets_cli/README.md

## 도구에 대한 프로젝트별 캐싱 {:#project-specific-caching-for-tools}

:::note
`.dart_tool/` 디렉토리를 소스 제어에 체크인하지 마세요. 
대신 `.dart_tool/`를 `.gitignore`에 보관하세요.
:::

`.dart_tool/` 디렉토리는 `dart pub get`을 실행할 때 생성되며, 언제든지 삭제될 수 있습니다. 
다양한 도구가 이 디렉토리를 사용하여, 프로젝트 및/또는 로컬 머신에 특정한 파일을 캐싱합니다. 
`.dart_tool/` 디렉토리는 소스 제어에 체크인하거나, 머신 간에 복사해서는 안 됩니다.

일반적으로 `.dart_tool/` 디렉토리를 삭제하는 것도 안전하지만, 
일부 도구는 캐시된 정보를 다시 계산해야 할 수 있습니다.

**예시:** [`dart pub get`](/tools/pub/cmd/pub-get) 도구는 종속성을 글로벌 `$PUB_CACHE` 디렉토리에 다운로드하여 추출한 다음, _패키지 이름_ 을 글로벌 `$PUB_CACHE` 디렉토리의 디렉토리에 매핑하는 `.dart_tool/package_config.json` 파일을 작성합니다. 
`.dart_tool/package_config.json` 파일은 분석기 및 컴파일러와 같은 다른 도구에서, 
`import 'package:foo/foo.dart'`와 같은 명령문을 해결해야 할 때 사용됩니다.

프로젝트별 캐싱이 필요한 도구를 개발할 때, 대부분 사용자가 이미 `.gitignore`로 무시하고 있으므로, 
`.dart_tool/` 디렉토리를 사용하는 것을 고려할 수 있습니다. 
사용자의 `.dart_tool/` 디렉토리에서 도구에 대한 파일을 캐싱할 때는, 고유한 하위 디렉토리를 사용해야 합니다. 
충돌을 피하기 위해, `.dart_tool/<tool_package_name>/` 형태의 하위 디렉토리는 `<tool_package_name>`이라는 패키지에 예약되어 있습니다. 
도구가 [pub.dev 사이트]({{site.pub}})를 통해 배포되지 않는 경우, 
고유한 이름을 예약하기 위해 플레이스홀더 패키지를 게시하는 것을 고려할 수 있습니다.

**예시:** [`package:build`]({{site.pub-pkg}}/build)는 코드 생성 단계를 작성하기 위한 프레임워크를 제공합니다. 
이러한 빌드 단계를 실행할 때 파일은 `.dart_tool/build/`에 캐싱됩니다. 
이렇게 하면 나중에 빌드 단계를 다시 실행할 때 속도가 빨라집니다.

:::warning
`.dart_tool/`에서 파일을 캐시하려는 도구를 개발할 때 다음 사항을 확인하세요.

* 소유한 패키지의 이름을 딴 하위 디렉터리를 사용하고 있습니다. (`.dart_tool/<my_tool_package_name>/`)
* `.dart_tool/`는 일반적으로 `.gitignore`에 나열되므로, 파일은 소스 제어에 속하지 않습니다.
:::

[Markdown]: {{site.pub-pkg}}/markdown
