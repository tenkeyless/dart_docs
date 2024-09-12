---
title: dart pub outdated
# description: Use dart pub outdated to help you update your package dependencies.
description: 패키지 종속성을 업데이트하는 데 도움이 되는 dart pub outdated를 활용하세요.
---

_Outdated_ 는 [pub 도구](/tools/pub/cmd)의 명령 중 하나입니다.

```plaintext
$ dart pub outdated [options]
```

`dart pub outdated`를 사용하여, 오래된 [패키지 종속성][package dependencies]을 식별하고, 
업데이트 방법에 대한 조언을 받으세요. 
[종속성 관리 모범 사례][best practices]에는 최신 stable 패키지 버전을 사용하는 것이 포함되어 있으므로, 
최신 버그 수정 및 개선 사항을 얻을 수 있습니다.

## 개요 {:#overview}

다음은 `dart pub outdated`를 사용하여, 
소유한 패키지(앱이든 일반 패키지든)의 종속성을 업데이트하는 방법입니다.

1. 패키지에 소스 제어에 체크인된 `pubspec.lock` 파일이 없는 경우, 
   패키지의 최상위 디렉터리(패키지의 [`pubspec.yaml`](/tools/pub/pubspec) 파일이 있는 디렉터리)에서 
   **`dart pub get`을 실행**합니다.
2. **`dart pub outdated`를 실행**하여, 어떤 패키지 종속성이 오래되었는지 확인합니다. 
   영향을 받는 패키지를 기록해 두면, 
   나중에 해당 패키지를 사용하는 코드의 동작을 테스트할 수 있습니다.
3. 패키지를 업데이트하기 위해, `dart pub outdated`의 권장 사항을 따릅니다. 
   일부 업데이트는 `dart pub upgrade`만 실행하면 될 수 있습니다. 
   다른 업데이트는 `dart pub upgrade`를 실행하기 전에, 
   `pubspec.yaml`을 업데이트해야 할 수 있습니다.
4. **`dart pub outdated`를 실행**하여, 최신 호환 패키지 버전을 사용하고 있는지 확인합니다.
5. 패키지를 **테스트**하여 예상대로 작동하는지 확인합니다.

[전이적 종속성][transitive dependencies]로 인해 여전히 오래된 종속성이 있을 수 있습니다. 
원인을 파악하려면, [`dart pub deps`][]를 실행하고, 출력에서 ​​오래된 패키지의 이름을 검색해 보세요.


## 예제 {:#example}

다음은 여러 개의 오래된 종속성이 있는 예제에서, `dart pub outdated`를 실행하는 예입니다. 
세 가지 종속성(`args`, `http`, `path`)은 직접적이고, 하나(`meta`)는 전이적입니다. 
다음 예에서 알 수 있듯이, `dart pub outdated`는 명령줄에서 실행할 때, 기본적으로 출력을 색칠합니다.

{% include './_pub-outdated-output.html' %}

**Resolvable** 열은 오래된 각 종속성에 대해 업그레이드할 수 있는 버전을 보여줍니다. 
**빨간색이 아닌 값**이 있는 **가장 왼쪽 열**을 찾으면 자세한 정보를 얻을 수 있습니다. 
예를 들어, `args`는 1.6.0으로 _upgradable_ 하고 `http`는 0.12.1로 _resolvable_ 합니다. 
`path` 및 `meta` 패키지는 최신 버전은 아니지만, 
다른 모든 종속성을 고려할 때 가장 최신의 _resolvable_ 버전입니다.

:::tip
[pub.dev]({{site.pub}})에 게시된 패키지의 새 버전에서 변경된 사항을 보려면, 
패키지 페이지의 변경 로그를 살펴보세요. 
예를 들어, [`args`][] 및 [`http`][] 패키지 페이지에서 **Changelog** 탭을 볼 수 있습니다.
:::

upgradable으로 나열된 첫 번째 종속성(`args`)을 수정하려면, 
`dart pub upgrade`를 실행하기만 하면 됩니다.

```console
$ dart pub upgrade
Resolving dependencies...
> args 1.6.0 (was 1.4.4)
  ...
Changed 1 dependency!
```

해결 가능으로 나열된 두 번째 종속성(`http`)을 수정하려면, 
pubspec의 `http` 항목을 변경하여, 
**Resolvable** 열의 버전(또는 호환되는 상위 버전)을 사용할 수 있습니다. 
[캐럿 구문][caret syntax]에서는 **`^0.12.1`**입니다. 
`pubspec.yaml`에 대한 diff는 다음과 같습니다.

```diff
-  http: ^0.11.0
+  http: ^0.12.1
```

`pubspec.yaml`을 편집한 후, `dart pub upgrade`를 실행하여, `pubspec.lock` 파일을 업데이트합니다. 
그런 다음, `dart pub outdated`를 실행하여, 필요한 모든 변경을 했는지 확인할 수 있습니다. 
이 예에서 `path` 및 `meta` 패키지는, 다른 종속성에 의해 결정된 제약 조건으로 인해, 여전히 오래되었습니다.

```console
$ dart pub upgrade
...
$ dart pub outdated
Package Name  Current  Upgradable  Resolvable  Latest

direct dependencies:
path          1.6.2    1.6.2       1.6.2       1.7.0

dev_dependencies: all up-to-date

transitive dependencies:
meta          1.1.6    1.1.6       1.1.6       1.1.8

transitive dev_dependencies: all up-to-date

Dependencies are all on the latest resolvable versions.
Newer versions, while available, are not mutually compatible.
```

이러한 패키지가 오래된 이유를 알아보려면, 
`dart pub deps`를 실행하고 이러한 패키지의 종속성을 찾으세요.

```console
$ dart pub deps -s list
...
dependencies:
...
- terminal_tools 0.1.0
  - path 1.6.2
  - meta 1.1.6
...
```

이전 출력에서 ​​보듯이, 이 패키지는 `terminal_tools` 패키지에 종속되어 있으며, 
이 패키지는 이전 버전의 `path`와 `meta`에 종속되어 있습니다. 
`terminal_tools` 패키지가 업데이트되면, 이 패키지를 업데이트할 수 있을 것입니다.

:::important
패키지를 업데이트한 후에도 코드가 예상대로 작동하는지 테스트해 보세요.
:::


## 출력 컬럼 {:#output-columns}

`dart pub outdated`의 출력에는 각 오래된 종속성에 대한 버전 정보의 4개 열이 있습니다. 
다음은 Current, Upgradable, Resolvable, Latest의 4개 버전 열을 보여주는 
[예제](#example) 출력의 일부입니다.

{% include './_pub-outdated-output-columns.html' %}

Current
: `pubspec.lock`에 기록된 패키지에서 사용된 버전입니다. 
  패키지가 `pubspec.lock`에 없으면 값은 `-`입니다.

Upgradable
: `pubspec.yaml` 파일에서 허용하는 최신 버전입니다. 
  이것은 `dart pub upgrade`가 해결되는(resolves) 버전입니다. 
  **Current** 열의 값이 `-`이면 값은 `-`입니다.

Resolvable
: 다른 모든 종속성과 결합했을 때 해결할 수 있는 최신 버전입니다. 
  이 버전은 `pubspec.yaml`의 모든 버전 제약 조건이 제한되지 않은 경우, 
  `dart pub upgrade`가 제공하는 것과 일치합니다. 
  `-` 값은 패키지가 필요하지 않음을 의미합니다.

Latest
: `--prereleases` 옵션을 사용하지 않는 한, 사전 릴리스를 제외한 최신 버전의 패키지입니다.

예를 들어, 앱이 `foo` 및 `bar` 패키지에 종속되어 있지만, 
`bar`의 최신 버전은 `foo`의 이전 주요 버전만 허용한다고 가정해 보겠습니다. 
결과적으로 `foo`의 최신 _해결 가능(resolvable)_ 버전은 `foo`의 _최신(latest)_ 버전과 다릅니다.

`pubspec.yaml` 파일을 편집할 때, 
일반적으로 **dependencies** 및 **dev_dependencies** 섹션을 업데이트하여, 
각 패키지가 **Resolvable** 열의 버전을 사용하도록 합니다.


## 옵션 {:#options}

모든 pub 명령에 적용되는 옵션은 [전역 옵션](/tools/pub/cmd#global-options)을 참조하세요.

### `--[no-]dependency-overrides`

기본적으로, 패키지 제약 조건을 해결할 때, [`dependency_overrides`][]를 고려합니다.(`--dependency-overrides`) 
오버라이드를 고려하지 않으려면, `--no-dependency_overrides`를 사용합니다.

### `--[no-]dev-dependencies`

기본적으로, 패키지 제약 조건을 해결할 때, [dev 종속성][dev dependency]을 고려합니다.
(`--dev-dependencies`)
dev 종속성을 고려하지 않으려면, `--no-dev-dependencies`를 사용합니다.

### `--json`

JSON 형식으로 출력을 생성합니다.

### `--[no-]prereleases`

기본적으로, 마지막 패키지 버전을 결정할 때 사전 릴리스를 포함합니다. (`--prereleases`)
사전 릴리스를 고려하지 않으려면, `--no-prereleases`를 사용합니다.

### `--[no-]transitive`

기본적으로 [전이적 종속성][transitive dependencies]을 출력의 일부로 포함하지 않습니다.
(`--no-transitive`)
전이적 종속성을 포함하려면, `--transitive`를 사용합니다.

### `--[no-]up-to-date`

기본적으로, 최신 버전(`--no-up-to-date`)의 종속성은 포함하지 않습니다. 
최신 종속성을 포함하려면, `--up-to-date`를 사용합니다.


{% render 'pub-problems.md' %}

[`args`]: {{site.pub-pkg}}/args
[best practices]: /tools/pub/dependencies#best-practices
[caret syntax]: /tools/pub/dependencies#version-constraints
[dev dependency]: /tools/pub/dependencies#dev-dependencies
[`dependency_overrides`]: /tools/pub/dependencies#dependency-overrides
[package dependencies]: /tools/pub/dependencies
[`http`]: {{site.pub-pkg}}/http
[`dart pub deps`]: /tools/pub/cmd/pub-deps
[transitive dependencies]: /tools/pub/glossary#transitive-dependency
